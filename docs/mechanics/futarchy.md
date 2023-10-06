---
layout: default
title: Futarchy
parent: Mechanics
nav_order: 1
---

# Futarchy

The fundamental mechanism of the Meta-DAO
{: .fs-6 .fw-300 }

## Overview

> *Since speculative markets excel at a task where democracies struggle, we might try to
> improve democracy by having it rely more on speculative markets.*\
> Robin Hanson, [Shall We Vote on Values, But Bet on Beliefs?](http://hanson.gmu.edu/futarchy2013.pdf)

The Meta-DAO uses a mechanism called futarchy to make its decisions. Futarchy
was invented by economist Robin Hanson in 2000 and has been discussed at length
by people like Vitalik Buterin and Ralph Merkle
(as in Merkle trees), but has never been instantiated before.

The basic idea of futarchy is to **give decision-making authority to markets.**

We can demonstrate with an example: a company deciding whether or not to fire
the CEO. If the company were run as a futarchy, it would do the following:
1. Create two markets for the company's stock: one 'retain CEO' market and one
'fire CEO' market.
2. Allow investors to trade in these markets for some time period, such as 10 days.
3. After the time period has elapsed, look at the time-weighted average price (TWAP)
of the company's stock in both markets. If the TWAP is higher in the 'retain CEO' market,
retain the CEO and revert all trades in the 'fire CEO' market. If the TWAP is 
higher in the 'fire CEO' market, fire the CEO and revert all trades in the 'retain
CEO market.'

In essence, you allow the market to speculate on what the value of the company
*would be* if the company did something, in this case fire the CEO, and then
only do that thing if the market speculates that it would be good.

## Benefits

Compared to the other ways of making decisions, markets have a number of benefits.
To understand these, we can look at both the theory and the empirical evidence.

### Theoretical reasons why markets are superior

> *In an efficient capital market, asset prices reflect all relevant information
> and thus provide the best prediction of future events given the current information.*\
> Paul Rhode and Koleman Strumpf, [Historical Presidential Betting Markets](https://users.wfu.edu/strumpks/papers/JEP_2004.pdf)

Markets are good for a number of theoretical reasons.

For one, market participants are strongly incentivized to correct mispricings.
Consider, for example, election prediction markets.[^3] If Donald Trump has a 50%
chance of winning but you can buy his shares at 32 cents on the dollar, you are
incentivized to buy shares until the market price reaches 50 cents on the dollar. Conversely,
if shares are trading at 65 cents on the dollar you are incentivized to either
sell existing shares or sell short shares until the market price reaches 50 cents
on the dollar.

The downstream effect of this is that markets are hard to manipulate. Even deep-pocketed
individuals will have trouble moving a market because doing so will open up 
profitable opportunities for other participants to correct these mispricings.

Markets also give more power over time to those who are better predictors. People
who achieve high returns both naturally increase their capital and increase their
ability to raise capital from outside investors.

### The evidence

In practice, markets have repeatedly demonstrated them as superior instruments
of aggregating information. Examples include:
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

----

[^1]: https://blog.ethereum.org/2014/08/21/introduction-futarchy
[^2]: https://www.ralphmerkle.com/papers/DAOdemocracyDraft.pdf
[^3]: To understand how election prediction markets work, see [the Wikipedia page](https://en.wikipedia.org/wiki/Election_stock_market).

https://web.archive.org/web/20210513170717/https://www.sifma.org/resources/research/research-quarterly-fixed-income-issuance-and-trading-first-quarter-2021/

https://www.sifma.org/resources/research/fact-book/

