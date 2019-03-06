[Nimbus](https://nimbus.status.im) is a new client for Ethereum's next stage of development. It's a new [node](https://bitfalls.com/2017/11/26/whats-bitcoin-node-mining-vs-validation/), if you will. If you're an Ethereum protocol layperson and are trying to understand what Nimbus actually is right now, you've come to the right place.

We'll discuss sharding, scaling, and everything the [announcement post mentions](https://blog.status.im/introducing-nimbus-3360367bb311) in another post (or [several](https://our.status.im/tag/two-point-oh)) - the intention here is to get you familiar with Nimbus in a way that teaches you what Nimbus is _today_.

We want you to understand what this tool is, why it's here, how to use it and how to contribute to it so you, too, can help us build the new Ethereum.

## What is Nimbus right now?

!["What is my purpose?" - Butter-Passing Robot](https://our.status.im/content/images/2019/01/01.png)

For a broad overview of what it _wants to be_, see [the announcement](https://blog.status.im/introducing-nimbus-3360367bb311).

As of October 2018 - it's a set of tools which executes and passes a set of unit tests. In layman's terms, a unit test is computer code which needs to run without errors when executed. When executed through Nimbus, the results of this code's execution should not produce any errors. If all [these tests](https://ethereum-tests.readthedocs.io/en/latest/) _pass_, Nimbus can be considered an Ethereum client.

This is what a beautiful screen of passing tests looks like.

![Passing tests](https://our.status.im/content/images/2019/01/02.png)

As of March 2010, Nimbus will sync the proof of work Ethereum blockchain up to block 1.1 million and then crash. We're slowly but surely ironing out our implementation of the EVM and working towards a stable full chain sync. [Help us debug it!](https://github.com/status-im/nimbus/issues)

At the same time, the Ethereum 2.0 side of Nimbus is being developed in the [nim-beacon-chain](https://github.com/status-im/nim-beacon-chain) repository, and this implementation is currently able to simulate a beacon chain and spawn simulated beacon nodes that talk to each other and build this new blockchain. For status updates about that, please [follow our blog](https://our.status.im/tag/nimbus).

## So What's The Point?

![Nimbus' target](https://our.status.im/content/images/2019/01/03.png)

Right now, the team is developing a set of tools and libraries to satisfy the requirements of Ethereum 2.0: sharding support, scaling methods, fork choice, network simulation, testnet, etc. Despite this focus, Nimbus will eventually be an alternative to [Geth](https://bitfalls.com/2018/02/12/explaining-ethereum-tools-geth-mist/) and Parity as a full Ethereum node, but will also include features that can make it run as a _light node_ - a node that doesn't need to download the full blockchain - or even a [_stateless_](https://ethresear.ch/t/the-stateless-client-concept/172) node - a node that doesn't need to download state at all to be able to verify it.

Given that the tests we mentioned earlier are easier to pass with a client in full node mode, Nimbus will first be developed as a full node and then be upgraded with features required to make it run as a light node.

The ultimate point, however, is to have the Ethereum 2.0 version out in test form in Q1 of 2019, and able to talk to other beacon node proof of concept implementations in Spring of 2019. 

## Modularity

In order to fulfil its goal of running on low end and embedded devices, Nimbus will have to be extremely customizable. The ultimate goal is to have it support a plug and play module system in the form of APIs interacting with the Nimbus core.

Specifically, Nimbus will have different forms of builds. You'll be able to use its source code with certain flags (options) indicating what and how to build your copy of Nimbus - a copy you're putting onto a Nokia 3310 or into a smart oven won't be the same as a copy on a powerful desktop machine and that won't be the same as a copy you'll be installing into a microusb to plug into a hidden public USB outlet to covertly decentralize the network 🕵️‍♂️

And, yes, you'll be able to do all this.

![Nimbus, you champ](https://our.status.im/content/images/2019/01/04.png)

Interfaces (like JSON-RPC, or one for embedded Java) will be developed for the different operating systems Nimbus is being installed on - we're focusing on open hardware, but we're not planning to leave any platform behind. Nimbus will provide an [EVM-C interface](https://github.com/ethereum/evmc), for example, so all clients that use it will be able to choose if they want to use Aleth, Geth or Nimbus.

Another modular part will be the back-end (SQLite or RocksDB), the logging (console, file, json, database) and the networking protocols i.e. being agnostic regarding specific Eth protocols and using plain http/s to bypass filtering (like public WiFi or censorship). This is being done with Libp2p as the base networking layer which supports multi-address so you can use a single address to connect to a node through a variety of protocols.

## Are you competing with Geth?

Yes and no. It's been a mission of Ethereum from day 0 to have multiple client implementations available at all times for the sake of network health and stability in case of bugs in one version. As an example, a bug in Bitcoin Core will hurt the entire Bitcoin blockchain because Bitcoin Core is used in [95% of the cases](https://coin.dance/nodes).

![Unbalanced nodes distribution in BTC](https://our.status.im/content/images/2019/01/05.png)

This is unacceptable in Ethereum, so we strive to diversify client implementations as much and as evenly as possible. Right now, Ethereum has Parity vs. Geth distributed [relatively evenly](https://www.ethernodes.org/network/1) with dust of other implementations sprinkled in between. The goal is to have some form of Nimbus take up at least a quarter of that pie.

Note that we're not looking to undermine Geth's or Parity's prevalence. We're looking to add new devices into the ecosystem and expand Ethereum's reach rather than _replace_ the current nodes. As an example, Status will be using Nimbus once it's ready.

## Building

Enough talk, let's build.

Before we begin, we'll assume that:

- you've read the [introduction to Nim](https://bitfalls.com/2018/10/09/introduction-into-the-nim-language/) and thus have Nim available
- you're on a command-line friendly system like OS X or Linux. If you're on Windows, we highly recommend spinning up a [preconfigured Nim-friendly virtual machine with Vagrant](https://our.status.im/setting-up-a-local-vagrant-environment-for-nim-development/) or installing [Git Bash and tools](https://git-scm.com).

_Note: the Nimbus build system uses Makefiles to make the process identical, easy to update, and compatible with any OS. We don't use Nim's package manager Nimble because it's fundamentally broken._

### Installing

Clone Nimbus.

```bash
git clone git@github.com:status-im/nimbus
cd nimbus
```

### Dependencies

To run Nimbus, we'll need the RocksDB database. On OS X, execute:

```bash
brew install rocksdb
```

On Linux, this should do it:

```bash
sudo apt-get install librocksdb-dev rocksdb # or your own Linux distribution's equivalent
```

On Windows, please first make sure you have `make` installed - either in the form of `MinGW32make.exe` via [MinGW website](http://www.mingw.org) or regular old make installed through Git Bash or a package manager like Chocolatey:

```bash
choco install make
```

_Note - Windows requires you to add programs you want to be able to execute from anywhere on your machine to your PATH environment variable. This is done by simply opening the Start Menu, searching for "Env", selecting "Edit the system environment variables", clicking on Environment Variables in the popup, and then editing the PATH variable in the list by adding a new entry that corresponds to the folder into which you installed your version of `make` (Choco takes care of this for you, only applies if you installed manually). [This is what mine looks like](https://imgur.com/a/yQIi6Qa)._

Next, run:

```bash
make fetch-dlls
```

or

```bash
mingw32make.exe fetch-dlls
```

This downloads the rocksdb and sqlitedb DLL files into `nimbus/build` so that the built program can read them.

### Building, Testing, Running

To build Nimbus:

On OS X / Linux:

```bash
make
```

On Windows:

```bash
make
```

or

```bash
mingw32make.exe
```

The Nimbus client will now be in `build/nimbus` on any OS and can be run with the same command:

```bash
./build/nimbus
```

It should synchronize up to block 1.1 million and then crash, as mentioned above. This will take a few hours and will probably need some few dozen GB of hard drive space!

To test, run:

```bash
make test # (or mingw32make.exe test on Win if you use mingw32)
```

This is what passing tests look like.

![Passing tests](https://our.status.im/content/images/2019/01/06.png)

To update the source files for a rebuild:

```bash
make update
```

To clean the slate and start with a fresh build:

```bash
make clean
```

### Ethereum 2.0

To run and test the Ethereum 2.0 version of Nimbus (the network simulation):

```bash
make eth2_network_simulation
```

You should now see attestations and blocks being produced and confirmed and a bunch of other details from the nodes as they do their thing. More information about this simulation can be found in [this post](https://our.status.im/nimbus-testnet-simulation).

If you're re-running the simulation and want to start from scratch, use the following command:

```bash
make clean_eth2_network_simulation_files
```

---

Congratulations - you're now running Nimbus, to an extent. In a future post we'll dissect some of the tests, look into contributing to Nimbus, dive into the Nim language and much more.
