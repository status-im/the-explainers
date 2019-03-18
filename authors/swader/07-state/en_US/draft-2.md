Let's explain how the state transition (i.e. chain growth) in the new [Beacon Chain](https://our.status.im/two-point-oh-the-beacon-chain/) works. Where applicable, we'll use Nimbus' own implementation to document and illustrate things for clarity. For simplicity, we'll consider a Beacon Node and a [Validator](https://our.status.im/two-point-oh-explaining-validators/) to be one entity unless specifically stated otherwise.

Note that this only applies to the Beacon chain - not the shard chains. The Beacon chain grows differently from the way shard chains grow.

## Genesis State, Genesis Block

To begin, the beacon chain needs a genesis - a beginning. The genesis block is the first block in the chain and it is built from a _genesis state_ which in turn consists of the the receipts of all the registered validators, the hashed state of the Proof of Work chain (so that the beacon chain references a PoW block in every Beacon block), and the current unix timestamp.

Just like most things, the information the Beacon Block and the Beacon Block State contain are defined in the specification: [Beach State](https://github.com/ethereum/eth2.0-specs/blob/9469ca78c4269f901029f13e198337419ffb36d5/specs/core/0_beacon-chain.md#beaconstate), [Beacon Block](https://github.com/ethereum/eth2.0-specs/blob/9469ca78c4269f901029f13e198337419ffb36d5/specs/core/0_beacon-chain.md#beaconblock). This makes is much easier for different teams to build compatible software if different languages. Nimbus' implementations of all these data structures are [here](https://github.com/status-im/nim-beacon-chain/blob/eb369cee4e5e5773fe98238ee34b1fefb765ad06/beacon_chain/spec/datatypes.nim).

Once all Ethereum 2.0 clients have the same genesis block created from the same parameters, they can start collaborating on building the chain as soon as they connect to each other.

IMAGE

## Sidenote: Attestation Pool

Similar to a mempool in the current blockchain where all pending transactions reside, there is the _attestation pool_ in the beacon chain. This collection of attestations is a list of changes from validators all over the network who claim to have seen changes on some of the shards and need to communicte them across to the beacon chain.

When blocks are being made by validators, they grab attestations from this pool and include them in their block proposals. A validator can only process an attestation referring to one of the shards they're responsible for validating.

## A New Block

Each new block is based on the state up until that point. So when being proposed, each new block consists of the previous state, of a RANDAO revealed value provided by the proposer and the rest of the committee (the other validators in the slot's set, any of whom may turn out to be a proposer whereas others will be just validators of his proposal), and of some other tidbits like the receipts root of a Proof of Work blockchain's block, so that the Beacon chain references blocks from the old Ethereum and both enhances their security and verifies new registrations to the registration contract.

### Proposer or Validator

To find out whether they are a proposer, a validator needs to precompute the next block state because of the RANDAO mechanism responsible for shuffling this responsibility (i.e. randomly assigning someone to build a block, and others to verify that block). In essence, the validator:

- assumes they are going to be a proposer and grabs some attestations to include in the block
- calculates the next block's state in accordance with the network's randomness
- checks if this revealed randomness selected them as the proposer
- if yes, they propose. If not, they simply wait for a proposal from someone else to come through so they can validate it. Their revealed random number serves to select someone else as a proposer, and also to contribute to the randomness of the network as a whole.

This "generate a block just in case then follow through if you're picked" is a bit of a convoluted method, but because there is no way to find out in advance whether one will be proposing without ruining the randomness (i.e. revealing what selection is coming up next), this simply has to be done this way.