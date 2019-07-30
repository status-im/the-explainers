Due to the popularity of the [previous post](https://our.status.im/9-things-you-didnt-know-about-ethereum-2-0/), here are X more things you may not have known about Ethereum 2.0. 

_I swear we're not doing this for clicks (no ads or analytics here), it's just that it seems to get people to read and we think this is important information to disseminate._

## Eth1 data

sorpaasToday at 2:34 AM
For eth1 data votes, do we have penalties if some validators give in fake data? Do we have some ways to ensure that validators cannot collide to do a double spend?

protoToday at 2:40 AM
@sorpaas The eth1 voting period is not so small, 1024 slots. And only when 50% consensus is reached on a vote, it gets persisted. And then other validators can still fork away, if they believe the eth1 data is incorrect. And there is a follow distance of ~4 hours (i.e. we are not dealing with a particularly unstable eth1 head). See https://github.com/ethereum/eth2.0-specs/blob/dev/specs/core/0_fork-choice.md#fork-choice and https://github.com/ethereum/eth2.0-specs/blob/dev/specs/core/0_beacon-chain.md#eth1-data and https://github.com/ethereum/eth2.0-specs/blob/dev/specs/validator/0_beacon-chain-validator.md#misc

## Withdrawal Period

Minimum delay is 27 hours, but can increase based on how many others are leaving.

## EWASM new abi

New abi will be 64bit instead of 256bit so all tools will need to be revamped