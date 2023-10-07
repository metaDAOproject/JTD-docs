---
layout: default
title: Implementation
parent: Mechanics
nav_order: 2
---

# Implementation

How the Meta-DAO implements futarchy
{: .fs-6 .fw-300 }

## Overview

The Meta-DAO is composed of 3 open-source programs on the Solana blockchain:
- a *conditional vault* program,
- a *central-limit order book (CLOB)* program,
- and *autocrat*, the program that orchestrates futarchy.[^1]

*META* is the native token.

<div style="text-align: center;">
<img src="../../img/3programs.png" width="400"/>
</div>

## Conditional vault program

As described in [futarchy](https://metadaoproject.github.io/docs/mechanics/futarchy.html),
futarchy requires the ability to
'revert' trades in a market so that everyone gets back their original tokens.
Unfortunately, blockchains don't allow you to revert transactions after they've
been finalized, so we need a mechanism to *simulate* reverting transactions.
That mechanism is conditional tokens.

Before minting conditional tokens, someone needs to create a *conditional vault*.
Conditional vaults are each tied to a specific *underlying token* and *settlement
authority*. After a vault has been created, anyone can deposit underlying tokens
into it in exchange for an equivalent number of conditional tokens.

<div style="text-align: center;">
<img src="../../img/conditional-vault-deposit.png" width="500"/>
</div>

At any time, the settlement authority can either *finalize* or *revert* a vault.

If a settlement authority finalizes a vault, current conditional token holders 
can redeem their conditional tokens in exchange for an equal number of underlying
tokens.

<div style="text-align: center;">
<img src="../../img/conditional-vault-finalize.png" width="500"/>
</div>

If a settlement authority reverts a vault, all conditional token minters can get
back what they originally deposited. This has the same effect as reverting
all of the transfers.

<div style="text-align: center;">
<img src="../../img/conditional-vault-revert.png" width="500"/>
</div>

For every proposal, the Meta-DAO creates four vaults. It designates one of these
the conditional-on-pass META vault, one the conditional-on-fail META vault, one 
the conditional-on-pass SOL vault, and one the conditional-on-fail SOL vault.

<div style="text-align: center;">
<img src="../../img/conditional-vault-v3.png" width="500"/>
</div>

This allows us to achieve the desired reverting of markets. If someone mints
conditional-on-pass META and trades it for conditional-on-pass SOL, either the
proposal will pass and they will receive SOL or the proposal will fail and they
will receive their original META back.

## CLOB program

These types of trades could theoretically be performed through any central-limit
order book (CLOB) or automated market-maker (AMM). However, there existed no
CLOBs or AMMs on Solana that provided on-chain TWAPs.
So we [built one](https://metaproph3t.github.io/posts/yalob.html).[^2]

The Meta-DAO creates two order books per proposal: one for trading conditional-on-pass
META for conditional-on-pass SOL and one for trading conditional-on-fail META for
conditional-on-fail SOL. We call the former a *conditional-on-pass market* and
the latter a *conditional-on-fail market*.

<div style="text-align: center;">
<img src="../../img/clob.png" width="500"/>
</div>

## Autocrat

The last piece of the puzzle is *autocrat*, the program that orchestrates futarchy.

Anyone can interact with autocrat to create a *proposal*, which contains fields
such as a proposal number, proposal description link, and
an executable Solana Virtual Machine (SVM) instruction. For example, someone
could create a proposal to transfer 150,000 USDC to a development team to improve
a product that's managed by the Meta-DAO.[^3]

The requisite conditional vaults and markets are created at the same time.

<div style="text-align: center;">
<img src="../../img/autocrat-proposal-create.png" width="600"/>
</div>

After a configurable amount of time (currently 10 days), anyone
can trigger proposal finalization. In finalization, autocrat checks if the TWAP
of the pass market is higher than the TWAP of the fail market, and if it is
executes the SVM instruction, finalizes the pass market, and reverts the fail market.
Otherwise, it marks the proposal as failed,
finalizes the fail market, and reverts the pass market.

<div style="text-align: center;">
<img src="../../img/autocrat-finalize.png" />
</div>

----

[^1]: The name 'autocrat' is a double-entendre: the program is to a large extent the dictator of the Meta-DAO, and it is also just computer code.
[^2]: This CLOB incorporates the middle-price of the order book *at the beginning of each slot* into the TWAP, which makes manipulation hard because an attacker would have to either control blockspace or risk losing a substantial sum. The risks of such an attack are further weakened by [this logic](https://github.com/metaDAOproject/meta-dao/blob/e3dd1a4aa35dd3fedfa6fb38d77977dbbfb8d99e/programs/clob/src/state/order_book.rs#L109-L149), which prevents the current price observation from deviating very far from the last one.
[^3]: For an example of a proposal like this, see [this Lido one](https://snapshot.org/#/lido-snapshot.eth/proposal/0x37c958cfa873f6b2859b280bc4165fbdf15b1141b62844712af3338d5893c6c8).
