---
layout: default
title: Old Mechanics
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

### Overview

> Since speculative markets excel at a task where democracies struggle, we might try to
> improve democracy by having it rely more on speculative markets.\
> Robin Hanson, *[Shall We Vote on Values, But Bet on Beliefs?](http://hanson.gmu.edu/futarchy2013.pdf)*

The Meta-DAO uses a mechanism called *futarchy* to make its decisions. Futarchy
was invented by economist Robin Hanson in 2000 and has been discussed at length
by figures such as [Vitalik Buterin](https://blog.ethereum.org/2014/08/21/introduction-futarchy)
and [Ralph Merkle](https://www.ralphmerkle.com/papers/DAOdemocracyDraft.pdf)
(as in Merkle trees), but has never been instantiated before.

The basic idea of futarchy is to **give decision-making authority to markets.**

We can demonstrate with an example: a futarchic company deciding whether to fire
the CEO. It would do the following:
1. Create two markets for the company's stock: one 'retain CEO' market and one
'fire CEO' market.
2. Allow investors to trade in these markets for some time period, such as 10 days.
3. After the time period has elapsed, look at the time-weighted average price (TWAP)
of the company's stock in both markets. If the TWAP is higher in the 'retain CEO' market,
retain the CEO and revert all trades in the 'fire CEO' market. If the TWAP is 
higher in the 'fire CEO' market, fire the CEO and revert all trades in the 'retain
CEO market.'

### Benefits

In general, rational decisions are made by (1) aggregating all of the available
information on the options and (2) selecting the best one. There's a large body
of evidence that markets are better information-aggregators than other options,
such as the obtaining the consensus of experts.

#### Theoretical evidence

> "In an efficient capital market, asset prices reflect all relevant information
> and thus provide the best prediction of future events given the current information."\
> Paul Rhode and Koleman Strumpf, [Historical Presidential Betting Markets](https://users.wfu.edu/strumpks/papers/JEP_2004.pdf)

Markets are good for a number of theoretical reasons.

For one, market participants are strongly incentivized to correct mispricings.
For example, if 'Donald Trump win' shares are trading at 32 cents in a prediction
market but he actually has a 50% chance of winning the election, market participants
are strongly incentivized to buy up shares until they are priced at 50 cents.

The downstream effect of this is that markets are hard to manipulate. Even deep-pocketed
individuals will have trouble moving a market because doing so will open up opportunities
to correct the mispricings.

Markets also give more power over time to those who are better predictors. People
who achieve high returns both naturally increase their capital and increase their
ability to raise capital from outside investors.

Finally, markets incentivize participants to do their research.

#### Empirical evidence

Theory aside, markets have repeatedly demonstrated themselves to be superior
than alternatives in practice. Examples include:
- Prediction markets outperform professional pollsters
in predicting elections.
- Commodities futures markets outperform government forecasts
in predicting weather. 
- Companies like [Google](https://googleblog.blogspot.com/2005/09/putting-crowd-wisdom-to-work.html)
and [HP](https://authors.library.caltech.edu/44358/1/wp1131.pdf) have used prediction
markets internally, with success. 
- And famously, while it took the government 2 months to
identify the Morton-Thiakol O-Rings as the root cause of the Challenger crash,
the markets had priced it so in Morton-Thiakol's share price within 14 minutes.

### Drawbacks

One of the largest drawbacks of markets is [Keynesian beauty contests](https://en.wikipedia.org/wiki/Keynesian_beauty_contest).
In other words, sometimes investors buy what they think others will buy, not what
they think the fundamentals support. This is especially evident in crypto markets
because many tokens are [reflexively priced](https://www.epsilonmgmt.com/blog/reflexivity/),
and have no fundamentals.

However, there are two counter-points to consider.

For one, what we're searching for is not the Platonic ideal of a governance system,
but merely one that is better than the others. Empirically and theoretically,
markets appear to be better than other mechanisms of aggregating information.

For two, situations where excessive speculation is involved tend to receive lots
of reporting, whereas normal conditions go unreported. As of 2022, the size of
the global bond market was $129T, $28T larger than the global equity market's $101T
and $128T larger than crypto's ~$1T. But the bond market probably doesn't recieve
129x the attention that crypto does, which leads people to believe that normal
markets are irrational. We believe that as crypto markets mature, they will look
closer to the bond and equity markets.

https://web.archive.org/web/20210513170717/https://www.sifma.org/resources/research/research-quarterly-fixed-income-issuance-and-trading-first-quarter-2021/

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


https://web.archive.org/web/20070613045642/https://www.cia.gov/library/center-for-the-study-of-intelligence/csi-publications/csi-studies/studies/vol50no4/using-prediction-markets-to-enhance-us-intelligence-capabilities.html#_ftn12
