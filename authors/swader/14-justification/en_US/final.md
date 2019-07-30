If you've heard the terms _justification_ and _finalization_ thrown around in the Ethereum 2.0 space but don't know what they mean, you're not alone. Let's try and demistify them in this short post.

Finality is the concept of being so certain two _competing finalized checkpoints_ cannot exist, that a third of the active validators would get slashed if this happened. It is in the validators' best interest to finalize properly and carefully study the beacon state at every turn in order not to make such an illegal move. So what is a _finalized checkpoint_? To explain that, let's explain justification.

Justification is the act of validators voting on epochs (discreet units of 6 minutes of time containing 64 slots each) and deciding which one is the tip of the beacon chain. If 2/3 of validators agree on one, that epoch is justified. If two epochs in a row (or two in a row skipping one) are justified, the first one of those two is deemed _finalized_. So a finalized epoch is a _really really justified epoch_, in essence, with one key difference - when things are justified, you can still rewind time to go to that point. When you finalize, there's no way to rewrite history.

It should be noted that validators aren't voting on epochs per se - that would be like saying someone is voting for specific hours of the day. We always know which hour is the current one, so it's silly to vote for that. Instead, validators vote on the _content_ of those epochs - the state root in their last slot. So the validators are voting on the last known valid state of the chain by voting on and justifying/finalizing epochs.

Obligatory parallel from Infinity War: Dr. Strange saw 14 million realities. Time moves linearly and a specific hour of a specific day happens at the same time in all of them, but in all those different realities lies an outcome that is desirable - all others are illegal forks.

This where fork choice comes into play. Let's consult this descriptive slide from Justin Drake's Eth2 presentation at EthCC.

![](https://our.status.im/content/images/2019/07/01.png)

The two boxes above demonstrate illegal conditions in the fork choice. The top left box says that it is illegal to vote for two different state roots at the same point in time. I.e., you cannot vote for two epochs happening at the same time but containing different content. The top right box says that you cannot vote for an epoch that comes after some other finalized epochs, without voting for those epochs. In other words, you cannot vote for a future candidate without first voting on its present.

The bottom part of the image demonstrates a fork. The first and second red-circle epochs in the single chain are finalized, because they are followed by finalized third and fourth epochs. However, third and fourth exist in two parallel universes. Which fork is legal?

![](https://our.status.im/content/images/2019/07/02.png)

According to the first rule, double voting is illegal. One chain needs to be voted on, and since the upper one is finalized, that one seems valid. But the lower fork has finalized epochs, too, just a little further!

![](https://our.status.im/content/images/2019/07/03.png)

This one is invalid because voting to continue the chain by following the lower finalized epochs would mean that epochs that were finalized at a previous point in time but were considered for skipping because of a fork would be "surrounded", i.e. a newly voted-for epoch would come after a finalized one that's on another chain fork. This is illegal - there must be a sequence of finalized checkpoints. In this case, a large penalty would be applied to a large number of validators because a majority voted to finalize an epoch in a now illegal chain. They must be punished, and the surround and double vote rules decide who's drawing the short stick.
