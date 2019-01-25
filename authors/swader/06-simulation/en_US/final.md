> *“The Grid. A digital frontier. I tried to picture clusters of information as they moved through the computer. What did they look like? Ships, motorcycles? Were the circuits like freeways? I kept dreaming of a world I thought I'd never see. And then, one day I got in...”* - Kevin Flynn in Tron

We've been hard at work for the past few weeks getting our own sis̶̯͋i̷͔͌ṁ̴͉ǘ̵̹l̴̮̈́å̷̡ẗ̶ͅi̷͖̚ȯ̷͜n̴͕͒ up and running for experimenting with the [Beacon chain](https://our.status.im/two-point-oh-the-beacon-chain/) and [validators](https://our.status.im/two-point-oh-explaining-validators/).

This short post will introduce you to what the simulation does, how to run it on your machine, and explain what's next.

## Running the simulation

We've recently transitioned to a new Makefile build system which is 99% compatible with all operating systems. The full process is now explained in the [original Nimbus for Newbies](https://our.status.im/nimbus-for-newbies/) post, but the TL;DR for getting the simulation up and running (after you've got a Nim environment set up) is:

```bash
git clone https://github.com/status-im/nimbus
cd nimbus
make eth2_network_simulation
```

Note that on Windows the `make` command might need to be replaced by `mingw32-make.exe` if that's what you're using instead. See [original post](https://our.status.im/nimbus-for-newbies/) for more information. In case of issues on Windows, we recommend using [our Vagrant box](https://our.status.im/setting-up-a-local-vagrant-environment-for-nim-development/) until the kinks have been ironed out.

If you've run the simulation before, a clean operation might be necessary:

```bash
make clean_eth2_network_simulation_files
```

## Details and current status

The simulation will look something like this:

![-3E122711-A7C9-4B2E-9FD5-D534F5ED3824-](https://our.status.im/content/images/2019/01/-3E122711-A7C9-4B2E-9FD5-D534F5ED3824-.png)
![-C1137316-D82F-46F0-B874-A23C939E09F9-](https://our.status.im/content/images/2019/01/-C1137316-D82F-46F0-B874-A23C939E09F9-.png)

Right now this metadata is kind of hard to grok if you're not all the way into it, so we are **currently in the process of adding verbosity** to the mix - with certain flags you'll be able to activate a much more story driven mode of the client which should explain exactly what each element does and when.

In terms of what the sim does right now, it generates a new genesis state and 99 validators and stores them in files named `validator-xx.json` where XX is the number of the validator. The files will contain this type of data:

```json
{
  "privKey": "00000000000000000000000000000000607b42bcae82becbe947fb13e18522b3f9bf50efab3209fa9b7a95b869906872",
  "randao": {
    "seed": "E7C91FC8140FE2B27161AC4DEBAAF7DF2210D4F2AB6F3FC90BFD1960EABC1B73"
  }
}
```

These will then be used to generate a `startup.json` file with the registration information of each validator, containing their RANDAO commitment, withdrawal credentials and everything else the beacon chain needs. From this file a genesis JSON file will be generated in `state_snapshot.json`, building the validator registry from their previously generated registrations, setting the genesis time at the time the simulation is run, and generating shard committees:

```json
{
    "shard": 40,
    "committee": [28,8],
    "total_validator_count": 99
}
```

This means validator 28 and validator 8 (out of a total of 99) are in charge of processing shard 40.

Some fake RANDAO shuffles as well as shard crosslinks are also generated but default to zero in [Phase 0](https://our.status.im/two-point-oh-explaining-validators/).

Every set of 9 validators will get attached to their own node, resulting in a set of 10 beacon nodes running on ports from 50000 to 50009. Each node will also have its own enode address generated which you can find in `tests/simulation/data-x` where `x` is the number of the node. 9 validators will be left orphaned (all `% 10 == 0`) just to see what happens when it's their turn to validate and they have nowhere to get the data (no attached beacon node).

## What's next

The plan is to hack on this some more at FOSDEM next week and come away with some interesting conclusions about a more public version of a testnet we can pull up and tear down on demand. This testnet would start with being interoperable between Nimbus clients and then focus on working with other clients out there. The March deadline is within reach and we're dead set on hitting that mark.

Please get involved! Try running the sim on your own machine and let us know how and what went wrong. Let's debug together! We're planning to set up some bounties, and some NFT swag - [Nimbus Contributor Kudos Badges](https://gitcoin.co/kudos/1151/nimbus_contributor) - is heading your way too!

![-F0440580-FD84-41C2-8F10-71B1EED246CF-](https://our.status.im/content/images/2019/01/-F0440580-FD84-41C2-8F10-71B1EED246CF-.png)

Got more questions? Want to hack with us live? The team will be at FOSDEM next week hacking together, so [get in touch](https://gitter.im/status-im/nimbus)!

## Troubleshooting

### Q: Sim crashes at slot 122

A: Good, that's as far as we got it, too.

### Q: Sim crashes before slot 122

A: Bad, please try to reproduce reliably FIRST and then please [submit an issue with full details](https://github.com/status-im/nimbus/issues).

### Q: Does it simulate Eth 1.0 too?

A: No, only Eth 2.0. The 1.0 implementation is separate. See [Nimbus for Newbies](https://our.status.im/nimbus-for-newbies) or [docs](https://nimbus.status.im/docs).

### Q: Simulation hangs, doesn't crash

A: If nothing changed for over 3 minutes, kill it with CTRL+C and [submit an issue](https://github.com/status-im/nimbus/issues).

### Q: Getting stuck processes and LOCK files when trying to `clean` the simulation files

A: Find all processes called beacon_node and kill them in your operating system's process manager