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

## Benefits

In general, rational decisions are made by (1) aggregating all of the available
information on the options and (2) selecting the best one. There's a large body
of evidence that markets are better information-aggregators than other options,
such as the obtaining the consensus of experts.

### Theoretical evidence

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

### Empirical evidence

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

## Drawbacks

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

