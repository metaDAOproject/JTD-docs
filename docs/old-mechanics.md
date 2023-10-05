---
layout: default
title: Mechanics
nav_order: 3
---

# Mechanics
{: .no_toc }

How the Meta-DAO works
{: .fs-6 .fw-300 }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Futarchy

> Since speculative markets excel at a task where democracies struggle, we might try to
> improve democracy by having it rely more on speculative markets.\
> Robin Hanson, *[Shall We Vote on Values, But Bet on Beliefs?](http://hanson.gmu.edu/futarchy2013.pdf)*

The Meta-DAO uses a mechanism called *futarchy* to make its decisions. Futarchy
was invented by economist Robin Hanson in 2000 and has been discussed at length
by figures such as [Vitalik Buterin](https://blog.ethereum.org/2014/08/21/introduction-futarchy)
and [Ralph Merkle](https://www.ralphmerkle.com/papers/DAOdemocracyDraft.pdf)
(as in Merkle trees), but has never been instantiated before.

The basic idea of futarchy is to **give decision-making authority to markets.**
To demonstrate, let's take an example: deciding whether or not to fire the CEO.
If the company in question were run as a futarchy, it would make the decision
in the following way:
1. Two markets are created for the company's stock: one 'retain CEO' market and
one 'fire CEO' market. 
2. Investors are allowed to trades in these markets for some time period, say 10
days.
3. After the 10 days have elapsed, the company looks at the price of its stock
in both markets. If the time-weighted average price (TWAP) is higher in the
'retain CEO' market, the CEO is retained and all of the trades in the 'fire CEO'
market are reverted. Else, the CEO is fired and all of the trades in the 'retain
CEO' market are reverted.

The benefit of this approach is that markets:
- incentivize participants to research how decisions will affect asset values
- incentivize participants to 'correct' any mispricings
- give more power over time to those who have a track record of being right
- are hard to manipulate

In practice, markets often outperform experts. Prediction markets outperform professional pollsters
in predicting elections. Commodities futures markets outperform government forecasts
in predicting weather. And famously, while it took the government 2 months to
identify the Morton-Thiakol O-Rings as the root cause of the Challenger crash,
the markets had reflected it so in Morton-Thiakol's share price within 14 minutes.

## How the Meta-DAO implements futarchy

The Meta-DAO is implemented as 3 open-source programs on the Solana blockchain:
- a *conditional vault* program,
- a *central-limit order book (CLOB)* program,
- and a program that manages that orchestrates futarchy, named *autocrat* as
a double-entendre playing on the fact that the program is both *auto*nomous
and the final arbiter of all DAO decisions.

Additionally, *META* is the native token of the Meta-DAO.

### Conditional vault program

Because we have no ability to revert finalized Solana transactions, we need
different way to simulate reverting markets. The conditional vault program allows
us to do this through the creation of *conditional tokens*. The way that these
conditional tokens work is described here.

Anyone can create a *conditional vault*, which is tied to a specific
*underlying token* and *settlement authority* (the Meta-DAO account would be the
settlement authority of all vaults created for the purposes of futarchy).

Once a conditional vault is created, anyone can deposit underlying tokens into
it in exchange for conditional tokens. The vault also records their deposit
separately.

At any time, the settlement authority can either *finalize* or *revert* a vault.
If a vault becomes finalized, all current conditional token holders can claim
the same number of underlying tokens. If a vault becomes reverted, all depositors
can claim back what they originally deposited.

This allows us to simulate reverting markets without reverting any transactions.
For example, we could have a market where 'fire CEO' conditional META are
traded for 'fire CEO' conditional SOL.[^1] If the CEO isn't fired, the Meta-DAO
reverts both tokens' vaults, and it's like all of the trades are reverted: everyone
gets back their original SOL and their original META.

### CLOB program

Unfortunately, although a few central-limit order books (CLOBs) exist on Solana,
none of them provide on-chain TWAPs. So we [built our own](https://metaproph3t.github.io/posts/yalob.html).
It incorporates the middle-price of the order book *at the beginning of each slot*
into the TWAP, which makes manipulation hard because an attacker would have to
either control blockspace or risk losing a substantial sum. The risks of such
an attack are further weakened by [this logic](https://github.com/metaDAOproject/meta-dao/blob/e3dd1a4aa35dd3fedfa6fb38d77977dbbfb8d99e/programs/clob/src/state/order_book.rs#L109-L149), which prevents the current price observation from deviating
far from the last one.

All code is unaudited, so the CLOB also contains a series of runtime invariants,
for example checking that the total liabilities are never greater than total assets.

### Autocrat

The central program of the Meta-DAO is autocrat, specifically `autocrat_v0`.

Anyone can interact with autocrat to create a *proposal*, which is a Solana account
containing fields such as *proposal number*, *proposal description link*, and
an executable Solana Virtual Machine (SVM) instruction. An SVM instruction could
encode, for example, a transfer of 150,000 USDC to a development team to build
a new product or maintain an existing one, as is common practice in DeFi.[^2]

When the proposal is created, autocrat ensures that the requisite conditional tokens
and markets have also been created. Specifically, it validates that conditional-on-pass
META and conditional-on-pass SOL have been created and that a market exists
to trade between them (the *pass market*), and the same for conditional-on-fail META and 
conditional-on-fail SOL (the *fail market*).

At the end of a configurable number of slots (currently 10 days worth), anyone
can trigger proposal finalization. In finalization, autocrat checks if the TWAP
of the pass market is higher than the TWAP of the fail market, and if it is
executes the SVM instruction, finalizes the pass market, and reverts the fail
market. If it isn't, autocrat ignores the SVM instruction, reverts the fail market,
and finalizes the pass market.

## Future designs for scalability

The careful reader may realize that futarchy doesn't scale. That is, it's good
for big decisions like whether to fire the CEO, but less so for smaller decisions
like whether to fire a division leader, because those actions may be too
inconsequential to reflect in the share price. We designed a solution to this
problem, which is described [here](https://github.com/metaDAOproject/Manifesto/blob/main/Manifesto.pdf)
and 
[here](https://medium.com/@metaproph3t/from-corporations-to-nations-how-the-meta-dao-is-going-to-change-everything-part-3-16b3880fd86c).[^2]
In the interest of starting small and iterating, the Meta-DAO will be launched
as basic futarchy, the scheme described above. But it would be possible to migrate
to a more scalable scheme in the future, if the market decided it so.

----

[^1]: Of course, a decentralized organization like the Meta-DAO can't have a CEO
to fire in the first place.
[^2]: This design is where the 'Meta-DAO' name comes from.
[^1]: http://maloney.people.clemson.edu/855/9.pdf
[^2]: See, for example, https://snapshot.org/#/lido-snapshot.eth/proposal/0x37c958cfa873f6b2859b280bc4165fbdf15b1141b62844712af3338d5893c6c8


