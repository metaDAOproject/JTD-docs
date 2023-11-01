---
layout: default
title: Example flows
parent: Mechanics
nav_order: 3
---

# Example flows

Some example flows that demonstrate how the Meta-DAO will work in practice
{: .fs-6 .fw-300 }

## Example #1 - A bad proposal

Governance attacks, like the one launched against Beanstalk Protocol, are 
relatively uncommon but do happen.[^1] It's worth considering how the Meta-DAO would
respond to such an attack. Suppose that an attacker has just submitted a proposal that transfer's all of the
Meta-DAO's assets to himself. How is the community incentivized to respond? 

Well,
if the proposal goes through, the Meta-DAO will be worth a lot less. For example,
if the Meta-DAO is currently worth $100M
and the proposal would transfer $40M of the Meta-DAO's liquid assets,
then it should be worth $60M if the proposal goes through.[^2]

So if you hold META
and its current price is $1, you are incentivized to mint conditional-on-pass tokens
and sell them until their market price reaches $0.6. On the other hand, the conditional-on-fail
tokens should trade near $1. As such, autocrat should fail this proposal.

Once autocrat fails the proposal, all trades in the conditional-on-pass market
become reverted and all trades in the conditional-on-fail market become finalized.

## Example #2 - A good proposal

Now consider what should happen when a good proposal emerges. Suppose that someone
has just made a proposal to fund a project that would improve a product that the Meta-DAO
manages. The proposal is requesting $5M paid over 2 years and analysts estimate
that the improvements would add $25M to the Meta-DAO's [enterprise value](https://en.wikipedia.org/wiki/Enterprise_value),
so the proposal would on net create $20M of value.

[Economically rational](https://www.britannica.com/money/topic/economic-rationality)
participants would then be incentivized to bid up META in the conditional-on-pass
markets until its price reaches $1.2. If the price dips lower, say to $1.1, this
represents a bargain opportunity: participants can buy $1.2 of [net present value](https://en.wikipedia.org/wiki/Net_present_value)
for only $1.1! Traders would be likely to take this trade until the price reached
a level near $1.2 again. But again, the proposal would have no impact on the Meta-DAO
if it were to fail, so we would expect META to trade near $1 in the conditional-on-fail
markets. So autocrat would pass this proposal.

---- 

[^1]: https://medium.com/immunefi/hack-analysis-beanstalk-governance-attack-april-2022-f42788fc821e
[^2]: In this analysis, we ignore the effect that such a proposal going through would have on the Meta-DAO's remaining assets. In practice, a successful governance attack would likely erode confidence such that the real losses from a $40M governance attack would be much larger than $40M.
