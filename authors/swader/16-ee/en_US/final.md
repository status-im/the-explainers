In [this series'](https://our.status.im/tag/two-point-oh) last post we explained [justification and finalization](https://our.status.im/two-point-oh-justification-and-finalization/). Now let's cover another term that's had many people scratching their heads: **execution environments**.

## Why

When the idea of Etheruem 2.0 was being collectively thought up, the intention was to move from the okay-but-could-be-better EVM to eWASM, a subset of WASM (WebAssembly, the internet's new Native Language™). Naturally, like any big change, moving from EVM also faced significant resistance. How could we make both groups happy?

Ethereum came into existence as an abstraction built on the idea of Bitcoin. Where Bitcoin was only for financial censorship-resistant messages, Ethereum wanted to be for any kind of message transfer - whether that message is a chat message, financial, or content. In Ethereum 2.0, the abstraction is moving two levels further. One is _account abstraction_ which we'll talk about in the future. The other are _Execution Environments_.

## Execution Environments

The idea behind EEs is as follows: keep the idea of a message, but make the processing of that message programmable. In other words, make it possible to programmatically determine how transactions are executed and applied, which may or may not match an existing method of doing so implemented somewhere else, like another blockchain.

In the current approach, there is no way for contracts deployed on eWASM (see the [eWASM testnet](http://ewasm.ethereum.org/)) to coexist with EVM contracts, because the current Ethereum network runs only one _execution environment_ - either EVM, or the experimental eWASM. EEs would allow us to implement EVM inside an EE on Ethereum 2 - by simply defining the rules of state transition. The only thing EEs need to agree on is that they all obey the consensus rules of the [Beacon Chain](https://our.status.im/two-point-oh-the-beacon-chain/) on which these EEs are deployed in the form of _execution scripts_, a type of beacon-chain-native smart contract. So what are some EE examples?

- EVM EE - an execution environment that interprets EVM in WASM, and thus makes it possible for Eth 1 contracts to keep running in Eth2
- UTXO EE - QTUM, Bitcoin, and some other blockchains use the UTXO model (unspent transaction output) for tracking balances. A UTXO EE would allow for total absorption of Bitcoin and similar blockchain into Ethereum, to an extent much more complete than the current [WBTC](https://wbtc.network) approach.
- \* EE - any other custom blockchain transition machine can be ported - it's not hard to imagine a MOVE EE which would provide compatibility with Facebook's Libra Move language
- Delayed State Execution Environment - instant cross shard transactions by collecting TX and state root commitments.
- ZKEE - a blockchain with zero knowledge built in, so you have privacy by default on a certain EE

See some more examples in this [Robert Drost talk](https://www.youtube.com/watch?v=gHWMEmM940o&feature=youtu.be).

## How

So how do EEs work? How are they created? Are we going to have a pollution of EEs in Ethereum 2.0 if anyone can just spawn one?

As mentioned above, they will be implemented as a type of smart contract, an execution script on the beacon chain which are essentially _executable definitions of a blockchain_, i.e. what a transaction is, what a state is, what a state transition is, etc. Deploying an EE will be very expensive, time consuming and error prone, because you're essentially implementing a blockchain *within* a blockchain, so it will only make sense in very specific use cases. EVM backwards compatibility is specific enough to be worth it, for example, because it lets all the EVM dapps keep working in Eth2.

Now, there's a big, big change coming when EEs hit the main stage, and this is going to be a shock to many people:

> Ether will live only on the Beacon Chain, either in the Deposit Contract for validators, or in Execution Scripts (EEs) themselves.

This bears repeating - there will be no concept of Ether-holding accounts on shards.

