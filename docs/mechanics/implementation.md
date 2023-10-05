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
- and a program that manages that orchestrates futarchy, named *autocrat* as
a double-entendre playing on the fact that the program is both *auto*nomous
and the final arbiter of all DAO decisions.

Additionally, *META* is the native token of the Meta-DAO.

## Conditional vault program

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

## CLOB program

Unfortunately, although a few central-limit order books (CLOBs) exist on Solana,
none of them provide on-chain TWAPs. So we [built our own](https://metaproph3t.github.io/posts/yalob.html).
It incorporates the middle-price of the order book *at the beginning of each slot*
into the TWAP, which makes manipulation hard because an attacker would have to
either control blockspace or risk losing a substantial sum. The risks of such
an attack are further weakened by [this logic](https://github.com/metaDAOproject/meta-dao/blob/e3dd1a4aa35dd3fedfa6fb38d77977dbbfb8d99e/programs/clob/src/state/order_book.rs#L109-L149), which prevents the current price observation from deviating
far from the last one.

All code is unaudited, so the CLOB also contains a series of runtime invariants,
for example checking that the total liabilities are never greater than total assets.

## Autocrat

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

