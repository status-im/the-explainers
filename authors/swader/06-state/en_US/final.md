Let's explain how the state transition (i.e. chain growth) in the new [Beacon Chain](https://our.status.im/two-point-oh-the-beacon-chain/) works. We'll use Nimbus' own implementation to document and illustrate things for clarity. For simplicity, we'll consider a Beacon Node and a [Validator](https://our.status.im/two-point-oh-explaining-validators/) to be one united entity.

Note that this only applies to the Beacon chain - not the shard chains. The Beacon chains grows differently from the way shard chains work.

The code examples below will be taken from Nimbus' [state transition code](https://github.com/status-im/nim-beacon-chain/blob/master/beacon_chain/state_transition.nim).

## Genesis

The genesis block - the chain's first block - has some sort of starting state. As [explained previously](https://our.status.im/two-point-oh-the-tale-of-two-ethers/), one can become a validator by depositing 32 ether into a registration contract.



```nim
func makeGenesisBlock*(state: BeaconState): BeaconBlock =
  BeaconBlock(
    slot: INITIAL_SLOT_NUMBER,
    state_root: Eth2Digest(data: hash_tree_root(state))
  )
```

Then, the fun starts. As Beacon clients connect to each other and start talking, they'll make sure they all have the same genesis and are building on the same foundation. Once they're in sync, they begin building blocks.

IMAGE

## Attestation Pool

As the nodes collect Attestations (assurances about state changes in shards), these attestations are placed in what's called an attestation pool. Think of it kind of like a mempool of transactions, in a way, only transactions on the beacon chain are these attestations.

_Note that shards are not yet implemented and won't be for a while, so attestations are simulated right now._

IMAGE

## Block 1

The first block after the genesis is about to be created. There are some attestations in the pool, and several validators in the network. These validators have, in this early period, been registered via [deposits on the current Proof of Work Ethereum chain](https://our.status.im/two-point-oh-the-tale-of-two-ethers/). The logic for this is yet to be implemented:

```

```

... but the initial version of the registration smart contract already exists, written in the Vyper language.