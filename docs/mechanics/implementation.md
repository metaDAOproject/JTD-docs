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

The Meta-DAO is composed of [3 open-source programs](https://github.com/metaDAOproject/meta-dao) on the Solana blockchain:
- a *conditional vault* program,
- a *time-weighted average price (TWAP)* program,
- and *autocrat*, the program that orchestrates futarchy.[^1]
All programs are open-source and verifiable.

<div style="text-align: center;">
<img src="../../img/3programs.png" width="400"/>
</div>

*META* is the native token of the Meta-DAO. 

## Conditional vault program

As described in [futarchy](https://metadaoproject.github.io/docs/mechanics/futarchy.html),
futarchy requires the ability to
'revert' trades in a market so that everyone gets back their original tokens.
Unfortunately, blockchains don't allow you to revert transactions after they've
been finalized, so we need a mechanism to *simulate* reverting transactions.
That mechanism is conditional tokens.

Before minting conditional tokens, someone needs to create a *conditional vault*.
Conditional vaults are each tied to a specific *underlying token* and *settlement
authority*. In our case, the underlying token would be either META or USDC, and the
settlement authority would always be the Meta-DAO.

Once a vault is created, anyone can deposit underlying tokens in exchange for conditional
tokens. You receive two types of conditional tokens: ones that are redeemable for underlying tokens if the vault is finalized and ones that are redeemable for underlying tokens if the vault is finalized. For example, if you deposit 10 USDC into a vault, you will receive 10 conditional-on-finalize USDC and 10 conditional-on-revert USDC.

<div style="text-align: center;">
<img src="../../img/conditional-vault-deposit.png" width="500"/>
</div>

At any time, the settlement authority can either *finalize* or *revert* a vault.

If a settlement authority finalizes a vault, conditional-on-finalize token holders can redeem their conditional tokens for underlying tokens. Conversely, if a settlement authority reverts a vault, conditional-on-revert token holders can redeem their conditional tokens for underlying tokens. Because the finalization and reverting are mutually exclusive, total vault liabilities will never exceed total assets.

<div style="text-align: center;">
<img src="../../img/conditional-vault-finalize.png" width="500"/>
</div>

For each proposal, the Meta-DAO creates two vaults: one for USDC and one for META. If a proposal passes, it finalizes both vaults. If a proposal fails, it reverts both vaults. So we call the conditional-on-finalize tokens *conditional-on-pass tokens* and the conditional-on-revert tokens *conditional-on-fail tokens*.

<div style="text-align: center;">
<img src="../../img/conditional-vault-v3.png" width="500"/>
</div>

This allows us to achieve the desired reverting of trades. For example, if someone mints
conditional-on-pass META and trades it for conditional-on-pass META, either the
proposal will pass and they can redeem conditional-on-pass SOL for SOL or
the proposal will fail and they can redeem their conditional-on-fail META for their original META.

So we create two markets per proposal: one where conditional-on-pass META is traded for conditional-on-pass USDC and one where conditional-on-fail META is traded for conditional-on-fail USDC. This allows traders to express opinions like "this token would be worth $112 if the proposal passes, but it's only worth $105 if the proposal fails."

## TWAP program

All Meta-DAO markets are on [OpenBook v2](https://github.com/openbook-dex/openbook-v2). There didn't exist a TWAP oracle for OpenBook or for any other Solana AMM or CLOB, so we [built our own](https://github.com/metaDAOproject/openbook-twap). It uses the same design as [Uniswap V2](https://docs.uniswap.org/contracts/v2/concepts/core-concepts/oracles), and uses several mechanisms to ensure manipulation-resistance.

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
of the pass market is higher than the TWAP of the fail market. If it is, it
executes the SVM instruction, finalizes the pass market, and reverts the fail market.
If it isn't, it marks the proposal as failed,
finalizes the fail market, and reverts the pass market.

<div style="text-align: center;">
<img src="../../img/autocrat-finalize.png" />
</div>

----

[^1]: The name 'autocrat' is a double-entendre: the program is to a large extent the dictator of the Meta-DAO, and it is also just computer code.
[^2]: This CLOB incorporates the middle-price of the order book *at the beginning of each slot* into the TWAP, which makes manipulation hard because an attacker would have to either control blockspace or risk losing a substantial sum. The risks of such an attack are further weakened by [this logic](https://github.com/metaDAOproject/meta-dao/blob/e3dd1a4aa35dd3fedfa6fb38d77977dbbfb8d99e/programs/clob/src/state/order_book.rs#L109-L149), which prevents the current price observation from deviating very far from the last one.
[^3]: For an example of a proposal like this, see [this Lido one](https://snapshot.org/#/lido-snapshot.eth/proposal/0x37c958cfa873f6b2859b280bc4165fbdf15b1141b62844712af3338d5893c6c8).
