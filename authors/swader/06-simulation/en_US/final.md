> *“The Grid. A digital frontier. I tried to picture clusters of information as they moved through the computer. What did they look like? Ships, motorcycles? Were the circuits like freeways? I kept dreaming of a world I thought I'd never see. And then, one day I got in...”* - Kevin Flynn in Tron

We've been hard at work for the past few weeks getting our own sis̶̯͋i̷͔͌ṁ̴͉ǘ̵̹l̴̮̈́å̷̡ẗ̶ͅi̷͖̚ȯ̷͜n̴͕͒ up and running for experimenting with the [Beacon chain](https://our.status.im/two-point-oh-the-beacon-chain/) and [validators](https://our.status.im/two-point-oh-explaining-validators/).

This short post will introduce you to what the simulation does, how to run it on your machine, and explain what's next.

## Running the simulation

The full process is still explained in the [original Nimbus for Newbies](https://our.status.im/nimbus-for-newbies/) post, but the TL;DR for getting the simulation up and running is:

```bash
git clone https://github.com/status-im/nimbus
cd nimbus
make eth2_network_simulation
```

This process will also install a local version of [our stable fork of the Nim language](https://github.com/status-im/nim) so you can dive right in with the exact version we use.

Note that on Windows the `make` command might need to be replaced by `mingw32-make.exe` if that's what you're using. See [original post](https://our.status.im/nimbus-for-newbies/) for more information on the difference. Alternatively, run it in [our Vagrant box](https://our.status.im/setting-up-a-local-vagrant-environment-for-nim-development/), it works just as well.

If you've run the simulation before, a clean operation will be necessary to get a fresh state:

```bash
make clean_eth2_network_simulation_files
```

## Details and current status

You can see a terminal-cast of my simulation running in Vagrant by playing the embed below:

<script id="asciicast-e5U4ngFZadbmJ9IL7nMCbjZ3A" src="https://asciinema.org/a/e5U4ngFZadbmJ9IL7nMCbjZ3A.js" async></script>

_Full screen or watch [here](https://asciinema.org/a/e5U4ngFZadbmJ9IL7nMCbjZ3A) for best effect_

In terms of what the sim does right now, it generates a new genesis state and 99 validators and stores them in files named `validator-xx.json` where XX is the number of the validator. 

![Validators being generated](https://our.status.im/content/images/2019/01/01-1.png)

The files will contain this type of data:

```json
{
  "privKey": "00000000000000000000000000000000607b42bcae82becbe947fb13e18522b3f9bf50efab3209fa9b7a95b869906872",
  "randao": {
    "seed": "E7C91FC8140FE2B27161AC4DEBAAF7DF2210D4F2AB6F3FC90BFD1960EABC1B73"
  }
}
```

These will then be used to generate a `startup.json` file with the registration information of each validator, containing their RANDAO commitment, withdrawal credentials and everything else the beacon chain needs. From this file a genesis JSON file will be generated in `state_snapshot.json`, building the validator registry from their previously generated registrations, setting the genesis time at the time the simulation is run, and generating shard committees.

We assume the genesis happens at once meaning that the slot counter starts ticking from 0 at this point.

![Initial state built](https://our.status.im/content/images/2019/01/02-1.png)

```json
{
    "shard": 40,
    "committee": [28,8],
    "total_validator_count": 99
}
```

The above generated shard committee entry means validator 28 and validator 8 (out of a total of 99) are in charge of processing shard 40.

Some fake RANDAO shuffles as well as shard crosslinks are also generated but default to zero in [Phase 0](https://our.status.im/two-point-oh-explaining-validators/).

Every set of 9 validators will get attached to their own node, resulting in a set of 10 beacon nodes running on ports from 50000 to 50009. Each node will also have its own enode address generated which you can find in `tests/simulation/data-x` where `x` is the number of the node. These nodes will communicate between each other on the assumption that a single beacon node can handle 10 validators, and therefore sync 10 shards.

The nodes will then connect to each other.

![Nodes are connecting](https://our.status.im/content/images/2019/01/03-1.png)

9 validators will be left orphaned (all `% 10 == 0`) just to see what happens when it's their turn to validate and they have nowhere to get the data: no attached beacon node.

Each validator will schedule its block proposal in case it gets picked to propose. They will all schedule for all slots they qualify for.

![Scheduling](https://our.status.im/content/images/2019/01/04-1.png)

Blocks are going to start being proposed and attestations for those blocks will come in and get processed.

![Block proposals](https://our.status.im/content/images/2019/01/05-1.png)

This will continue until some breaking errors are encountered. Orphaned validators (those with node = 0) are errors in the Eth 2.0 system, but not breaking errors in the simulation. The simulation is also intended to simulate mistakes and bugs, so the below output is fine.

![No node validator](https://our.status.im//content/images/2019/01/06-1.png)

## What's next

We are **currently in the process of adding more verbosity** to the mix - with certain flags you'll be able to activate a much more story driven mode of the client which should explain exactly what each element does and when.

Additionally, the plan is to hack on this some more at FOSDEM next week and come away with some interesting conclusions about a more public version of a testnet we can pull up and tear down on demand. \

This testnet would start with being interoperable between Nimbus clients and then focus on working with other clients out there. The March deadline is within reach and we're dead set on hitting that mark.

Please get involved! Try running the sim on your own machine! Let's debug together - we're planning to set up some bounties, and some NFT swag - [Nimbus Contributor Kudos Badges](https://gitcoin.co/kudos/1151/nimbus_contributor) - is heading your way too! It would be **amazing** if you could record your sim with something like [Asciinema](https://asciinema.org) so that we can have a full and detailed interactive log for each sim.

![-F0440580-FD84-41C2-8F10-71B1EED246CF-](https://our.status.im/content/images/2019/01/-F0440580-FD84-41C2-8F10-71B1EED246CF-.png)

Got more questions? Want to hack with us live? The team will be at FOSDEM next week brainstorming together, so [get in touch](https://gitter.im/status-im/nimbus)!

## Troubleshooting

### Q: Sim crashes at slot X >= 120

A: That's good! It'll get to 200 in some cases - we're constantly fixing any bugs we find and extending the sim's lifetime daily. It should be noted that it'll be random and depending on processes, their execution order, latency, etc. so just keep trying!

### Q: Sim crashes at slot X < 120

A: Please try to reproduce reliably FIRST and then please [submit an issue with full details](https://github.com/status-im/nimbus/issues).

### Q: Does it simulate Eth 1.0 too?

A: No, only Eth 2.0. The 1.0 implementation is separate. See [Nimbus for Newbies](https://our.status.im/nimbus-for-newbies) or [docs](https://nimbus.status.im/docs).

### Q: Simulation hangs, doesn't crash

A: If nothing changed for over 3 minutes, kill it with CTRL+C and [submit an issue](https://github.com/status-im/nimbus/issues). This can happen if you have too few threads/cores to donate to the simulation. If your CPU is heavily taxed, it'll have problems running this. For example, running geth in full sync mode alongside this simulation is silly!