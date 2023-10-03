---
layout: default
title: Futarchy
parent: Mechanics
nav_order: 1
---

# Futarchy

## Overview

> Since speculative markets excel at a task where democracies struggle, we might try to
> improve democracy by having it rely more on speculative markets.\
> Robin Hanson, *[Shall We Vote on Values, But Bet on Beliefs?](http://hanson.gmu.edu/futarchy2013.pdf)*

The Meta-DAO uses a mechanism called *futarchy* to make its decisions. Futarchy
was invented by economist Robin Hanson in 2000 and has been discussed at length
by figures such as [Vitalik Buterin](https://blog.ethereum.org/2014/08/21/introduction-futarchy)
and [Ralph Merkle](https://www.ralphmerkle.com/papers/DAOdemocracyDraft.pdf)
(as in Merkle trees), but has never been instantiated before.

The basic idea of futarchy is to pass decision-making authority to markets.
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

## Benefits

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


