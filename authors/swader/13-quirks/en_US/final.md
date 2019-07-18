Yes, it's a listicle 😱

In no particular order, here are some things you probably didn't know about Ethereum 2.

## 1. It needs 2 million deposited Ether to start

We [covered previously](https://our.status.im/two-point-oh-the-tale-of-two-ethers/) how a validator would need to submit 32 ether to a deposit contract to join the staking system in Ethereum 2. What isn't as widely known is that we need [65536 validators]() for the new chain to start - roughly 2 million ether (65536 validators). That's exactly 64 validators per planned shard in the system - too little at first (see numbers below).

![treasure](https://our.status.im/content/images/2019/07/treasure.png)

The deposit ceremony is planned for Devcon 5 in Osaka, but it's not going to be a ceremony of 2 million ether moving around at once. That's when it will begin, and we'll have 3-4 months until enough deposits have been gathered. 

Given that it's not recommended non-devs and casual users join the staking system because their Ether would end up locked in a system that's risky and useless (no transactions for a while!), it might take a while for the full amount of ether to come in.

Once 2 million ether is collected in the contract to start the genesis of Ethereum 2.0, that'll only happen at [UTC midnight on the following day](https://youtu.be/83DGZPJoyPQ?t=985), rather than the moment the last deposit comes in! This is because we want to wait for many confirmations and get unfathomably high security in terms of hash power committed to guaranteeing the final deposit block.

Some other interesting numbers:

- [With a target of 128 validators per committee](https://medium.com/@chihchengliang/minimum-committee-size-explained-67047111fa20) (group doing validation on a given shard), at 1024 shards that's 131k validators for minimum optimal security of the network. The minimum is 111 validators per committee, but 128 is a rounder number and a little higher than minimum, so that's the target.
- at less than that number of validators (like the 65k at genesis time), the network [progressively degrades](https://www.reddit.com/r/ethereum/comments/cdg8v6/ama_we_are_the_eth_20_research_team_pt_2/ettpndr/) by skipping some shards every now and then and slowing things down slightly, in order to make sure the 128/committee validator number remains constant.
- this won't be an issue at genesis time because shards won't exist yet, and by the time we have shards we assume there will be many more validators than 131k
- the network can [_technically_](https://www.reddit.com/r/ethereum/comments/cdg8v6/ama_we_are_the_eth_20_research_team_pt_2/ettw3ub/) move ahead with only 64 validators, but would be very insecure and slow, and shards wouldn't work as intended

## 2. Your private key is online, but you can't get hacked

In order to sign attestations and generate blocks, you validator must be connected to the internet and must have the private key of the account loaded. It's a common fear that hacking the validator will let the hacker steal all 32 of someone's ether. Not so!

When you deposit the 32 ether to become a validator, you also send in the information about the [exit account and exit shard](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/core/0_deposit-contract.md#withdrawal-credentials). This means that no matter what happens - whether you exit the system voluntarily or are kicked out - the money that belongs to you is sent to that specific account designated as a withdrawal address. This allows you to, for example, have a Ledger device in your safe, or a multi-sig account ready, and those can accept the ether when it's sent to them, while the original private key that was used by the validator can be safely discarded!

## 3. Ethereum 2 slots are 6 seconds in length, except when they aren't

![time](https://our.status.im/content/images/2019/07/time.png)

Because the genesis time and time in general in Ethereum 2.0 is expressed in unix timestamps, and those are [susceptible](https://youtu.be/83DGZPJoyPQ?t=1017) to [leap seconds](https://en.wikipedia.org/wiki/Leap_second), some slots might be 5 seconds in length and some might be 7 seconds, despite being hardcoded to 6 seconds. Ahh, modern computing! ❤

## 4. There are two types of penalties for validators!

**Inactivity leaks** happen if your validator node goes offline for 18 days, and the beacon chain is not finalizing, then your balance will be reduced by up to 60.8% slash in 18 days.

**Slashing:** if a validator behaves provably maliciously, then they are slashed by having their balance reduced. Minimum penalty is 1 ETH, but it goes up linearly in the number of people slashed at the same time as you.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Clarification—&quot;penalty&quot; and &quot;slashing&quot; are not synonyms in Eth2. A penalty is a negative reward (e.g. for going offline). A slashing is a large penalty (≥1/32 of balance at stake) and a forceful exit for validators that provably committed a malicious action (e.g. double vote).</p>&mdash; Justin Ðrake (@drakefjustin) <a href="https://twitter.com/drakefjustin/status/1150854927310303233?ref_src=twsrc%5Etfw">July 15, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

It's important to note that this serves as a mechanism for distributed beacon nodes (despite [lack of incentive](https://github.com/ethereum/eth2.0-specs/issues/157)) because if many validators rely on the same beacon node, and that beacon node goes down taking the validators with it, those validators suffer a much bigger penalty than if they were on their own, separate beacon nodes.

That said, being offline won't be as punishing as you might think. As Vitalik himself puts it:

> You will only suffer large penalties if you are offline at the same time that >1/3 of other validators are offline. Otherwise the penalties for being offline will be tiny, to the point where you will be net-profitable (not including computer costs etc) as long as you are online more than ~50-67% of the time. The incentives are deliberately designed to be forgiving to avoid discouraging amateur setups to promote decentralization.

## 5. You don't need a supercomputer to run a validator

Running a validator or two on weak devices like the [NanoPC](https://blockandmortar.io) will be possible, however, you will probably not be able to also run a beacon node on it. For the difference between beacon nodes and validators please see [this explainer](https://our.status.im/two-point-oh-explaining-validators/).

You will be able to stake from a mobile device, but due to mobile devices shutting off long-running connections on sleep, this might interrupt your connectivity to Ethereum and could affect your staking profits. It's not recommended to stake from a mobile device unless you can keep it plugged in and always-on.

In either of the above cases, you'll need an external beacon node to connect to. It's almost certain entities like Infura will provide Beacon nodes for validators to connect to, but if you have a stable connection at home it's recommended you run your own, or connect to one that's hosted somewhere but that isn't as popular.

That said, a standard laptop [should be okay for up to 10 validators](https://www.reddit.com/r/ethereum/comments/cdg8v6/ama_we_are_the_eth_20_research_team_pt_2/etu2lms/), so don't expect to break the bank over getting the necessary hardware for amateur setups!

### 6. I heard there would be ASICs for Eth2. How, if there's no mining?

What you heard of was probably a VDF ASIC which is a device currently being researched as an augmentation to the randomness already present in Ethereum 2.0. For more information about randomness in Eth2 please see [this post](https://our.status.im/two-point-oh-randomness/). For additional information about VDFs specifically, please see [VDFResearch](http://vdfresearch.org/).

## 7. Not every slot has a block

Because slots are discreet units of time (~6 seconds per), it's possible that some slots might not have blocks. That can happen when there was a disagreement between the validators, latency issues in the committee responsible for creating and attesting to the new block, when a validator didn't show up for work and they were supposed to propose a block, or any other number of anomalies that cannot be predicted. So while you may be used to 15 second block times from Ethereum 1, the block times in Ethereum 2 might vary anywhere between 6 to 18 seconds (a skip of more than two slots should not be possible).

## 8. A state stores 13 hours of time, except when it doesn't

The last 2^13 (8192) state roots and the last 2^13 block roots are stored in each beacon state (see `SLOTS_PER_HISTORICAL_ROOT` [here](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/core/0_beacon-chain.md#time-parameters)).

That's around 13.65 hours of time (8192 * 6 / 3600). But because blocks don't happen every slot (see above), and it also contains the last 8192 block roots, the time stored in the beacon state when looked at as block roots can easily be much more than the time stored in the form of state roots. This is because the state root is saved into every slot, even if unchanged, while block roots will only store individual block roots. Here's an illustration:

![slots](https://our.status.im/content/images/2019/07/slots.png)

So if a beacon state stored 3 state roots and 3 block roots instead of 8192, it would contain Block 4, Block 3, Block 2, and Block 1 in the block roots, and State 7, State 6, State 5, State 4 in the state roots, despite State 6 and State 5 being identical to State 4.

## 9. There is a queueing mechanism keeping the set of validators roughly the same

A [queueing mechanism](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/core/0_beacon-chain.md#initiate_validator_exit) will make sure that mass exits of validators are slowed down.

This means that many validators can't leave all at once - kicked or legally exited. This is so that the number of validators of the system stays as stable as possible. If many pending exits (withdrawals) are detected, then there's time to make staking more appealing and draw some more validators into the system through an automatic increase in the staking return rate, just like in the mining difficulty adjustment under Proof of Work.

This also serves as an incentive against centralized infrastructure - if many validators leave at once (for example, they all connected to one beacon node by Infura which is hosted on AWS and that one went down) - they all get punished with either penalties for inactivity or long wait times for their exits.

---

Did any of these surprise you? Did I miss any? [Let me know](https://twitter.com/bitfalls).