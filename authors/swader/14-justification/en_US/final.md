If you've heard the terms _justification_ and _finalization_ thrown around in the Ethereum 2.0 space but don't know what they mean, you're not alone. Let's try and demistify them.

First, let's try to understand what **finality** is useful for. You must have noticed that crypto platforms and dapps often wait for a few blocks before considering your transaction _confirmed_. That's an approximate way to make sure a transaction is definitive and cannot be reversed or, in other words, that it is finalized.

Ethereum 2.0 gives you finality as a guarantee, an objective truth enshrined in the blockchain instead of an approximation. That's important because the communication on the chain can be much faster if a block was just finalized, instantly signaling readiness without having to wait for confirmations.

---

Let's get specific. First some definitions to prep the terrain.

> Note: it's going to get a _little_ meta-technical from now on, but it should still be as readable as the rest of the [Two Point Oh series](https://our.status.im/tag/two-point-oh/). Please [shout at me](https://twitter.com/bitfalls) if something needs additional clarifications.

![terms](https://our.status.im/content/images/2019/08/terms.png)

- **LMD GHOST** stands for Last Message Driven Greediest Heaviest Observed SubTree.
- **Casper FFG** stands for Casper, the Friendly Finality Gadget. Because, you know, GHOST.
- **A fork choice rule** is a function which, given some blockchain state S outputs a chain from genesis (first block) to S which can be considered the canonical (main, original) chain.
- **An epoch** is a unit of time 6.4 minutes in length which contains 64 slots, 6 seconds each.
- A slot may or may not contain a block, but the last slot in an epoch is called a **boundary slot** or a **checkpoint**.
- A **committee** is a subset of [randomly](https://our.status.im/two-point-oh-randomness/) selected validators, designated per slot, in which the first member of the committee has the chance to propose a block and all others must attest to it.

With those out of the way and available for referencing later, let's move into _justification and finalization_.

## Casper FFG

![boo](https://our.status.im/content/images/2019/08/boo.png)

Casper is a method of justifying and finalizing blocks of time (whether those are blocks in a generic blockchain or epochs in the context of Ethereum 2.0) in any blockchain that requires such a feature. It is a generic "gadget" and can be added to any blockchain which needs finality but is arguably most useful in a Proof of Stake blockchain.

**Finality** is the concept of being so certain two _competing finalized checkpoints_ cannot exist, that at least a third of the active validators would get [slashed](https://twitter.com/drakefjustin/status/1150854927310303233) if this happened. We call this _economic finality_.

You may be wondering why a third of the validators? Because as we'll see below, having two competing _finalized checkpoints_ requires two thirds of validators to vote on one thing, and two thirds on another, competing thing. In a situation where only one of those things is possible, at least a third of all participants are by definition wrong. It is in the validators' best interest to finalize properly and study the beacon state at every turn in order not to make such an illegal move. 

So what does _finalized_ in _finalized checkpoint_ mean? To explain that, we need to first cover _justification_.

Justification is the act of validators voting on checkpoints and deciding which one is the head (tip) of the beacon chain. If 2/3 of validators (a so called supermajority) agree on a **pair of epochs** e.g. source S -> target T, such that the source epoch is justified, then the target epoch is also justified. If two epochs in a row (or two in a row skipping one) are justified, the first one of those two is deemed _finalized_.

> Note: the above definition is dramatically simplified for ease of understanding. Some other factors are also at play, but outside the scope of this explainer.

So a finalized epoch is a _really really justified epoch_, in essence, with one key difference - when things are justified, you can still rewind time to go to that point. When you finalize, it becomes incredibly difficult to rewrite history.

It should be noted that validators aren't voting on epochs per se when justifying/finalizing them - that would be like saying someone is voting for specific hours of the day. We always know which hour is the current one, so it's silly to vote for that. Instead, validators vote on the _content_ of those epochs - the state root in their last slot (boundary slot). So the validators are voting on the last known valid state of the chain by voting on and justifying/finalizing epochs.

Obligatory parallel from Infinity War: Dr. Strange saw 14 million realities. Time moves linearly and a specific hour of a specific day happens at the same time in all of them, but among all those different realities lies a single outcome that is desirable - all others are forks.

<iframe src="https://giphy.com/embed/l0HlNeJNRHcSeLhaE" width="250" height="250" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/marvelstudios-um-l0HlNeJNRHcSeLhaE"></a></p>

## LMD GHOST

This is where fork choice comes into play. 

GHOST is actually an older protocol popular across proof of work and other blockchains. It just follows the "heaviest" subtree, i.e. the longest chain. In something like Bitcoin, the heaviest branch is the one with most hashpower invested in its blocks, which corresponds directly to it being the longest. The canonical chain is obvious there, but it's only _probabalistically final_ in that there is a chance (albeit slight) that the chain will instead switch to another fork.

LMD brings "messages" into play, in that it's "Last Message Driven". A message is an [attestation](https://our.status.im/two-point-oh-explaining-validators/), and all this comes down to is that the fork with the most votes in it will be deemed canonical.

![ghsot](https://our.status.im/content/images/2019/08/ghsot.png)

The image above shows this scenario unfolding. The smileys are the most recent attestations. A sum of attestations on a block is its weight, represented by the number inside. Despite the topmost chain being longer, the green one is deemed canonical.

## Gasper

In the context of Etheruem 2.0, Casper and GHOST make Gasper, which is the consensus protocol powering Eth 2. 

Let's consult this descriptive slide from Justin Drake's Eth2 presentation at EthCC.

![](https://our.status.im/content/images/2019/07/01.png)

The two boxes above demonstrate illegal conditions in the fork choice. The top left box says that it is illegal to vote for two different state roots at the same point in time. I.e., you cannot vote for two epochs happening at the same time but containing different content. The top right box says that you cannot vote for an epoch that comes after some other finalized epochs, without voting for those epochs. In other words, you cannot vote for a future candidate without first voting on its present.

The bottom part of the image demonstrates two finalized forks. The first and second red-circle epochs in the single chain are finalized, because they are followed by finalized third and fourth epochs. However, third and fourth exist in two parallel universes. Which fork is canonical?

![](https://our.status.im/content/images/2019/07/02.png)

According to the first rule, double voting is illegal. One chain needs to be voted on, and since the upper one is finalized, that one seems valid. But the lower fork has finalized epochs, too, just a little further!

![](https://our.status.im/content/images/2019/07/03.png)

This one is invalid because voting to continue the chain by following the lower finalized epochs would mean that _epochs that were finalized at a previous point in time but were considered for skipping because of a fork_ would be "surrounded". In other words, a newly voted-for epoch would come after a finalized one that's on another chain fork. This is illegal - there must be a chronological sequence of finalized checkpoints. In this case, a large penalty would be applied to a large number of validators because a majority voted to finalize an epoch in a now "illegal" chain. They must be punished, and the surround and double vote rules decide who's drawing the short stick.

That's all there is to it! If you're a validator, make sure you only vote once per slot, and make sure that you vote for whatever everyone else is voting for. Slashing avoided! (Don't worry, that'll all be built into whatever [Ethereum 2.0 client](https://nimbus.status.im) you're using)

---

Hopefully this makes justification and finalization much clearer - if not, please [let me know!](https://twitter.com/bitfalls) Many thanks to [Dustin](https://github.com/tersec), [Mamy](https://github.com/mratsim), and [Justin](https://twitter.com/drakefjustin) and [Danny](https://twitter.com/dannyryan) for their help with this explainer!
