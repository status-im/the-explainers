On April 1st 2019, Status is planning its first [Chaos Unicorn Day](https://discuss.status.im/t/chaos-unicorn-day-april-1-2019-aka-kill-our-cluster/974/13). On this day we'll turn off access to centralized services in an attempt to simulate an attack on the network and check how shackle-free we really are.

One of the best ways to make sure Status survives such apocalypses (apocaly?) is to run your own Status node. This not only lets you make sure you receive messages that are meant for you with increased reliability, but you also strengthen the network for others. Also as per the FAQ:

> Another reason [to run a node] is privacy. In the current setup, nodes that are running as Mail Servers are trusted. This means that such a node can communicate directly with the Status app using a p2p connection and some metadata might leak. If one wants to avoid that, the best option is to run a Mail Server on your own and configure it in the Status app.

Running a Status node in Whisper mode takes very little resources, while running a Status node in mailserver mode will cost around 600MB of bandwidth and disk I/O per month, as per the FAQ:

> The additional requirement for Status Nodes with Mail Server capability enabled is disk storage. The current data volume collected by a Mail Server within a month is around 600 MB.

Trivial in the worst of cases.

To get a Status node compiled and installed, follow the [official instructions](https://status.im/build_status/status_go.html). Alternatively, if you're just looking to get started...

## Installing / Updating Status

```bash
sudo apt-get install golang -y
cd $HOME
git clone https://github.com/status-im/status-go status-go/src/github.com/status-im/status-go
cd status-go/src/github.com/status-im/status-go
GOPATH=$HOME/status-go make statusgo
sudo rm /usr/bin/statusgo && sudo cp build/bin/statusd /usr/bin/statusgo
rm -rf $HOME/status-go
```

The `statusgo` binary is now available in `/usr/bin/statusgo`.

## ARM

In this short walkthrough, we'll go through the process of activating and inspecting a Whisper + Mailserver node on ARM. Specifically, I'll show you how to activate it on one of my NanoPCs that are currently running an Ethereum full node.

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Running smoothly. One Ethereum full node, open RPC, serving light clients. One running the Nimbus client and a national PoA testnet. Ethereum on ARM is 👌<br><br>260 euro per node total, maintenance: nil, plug and play. <a href="https://t.co/YDmugQkfbW">pic.twitter.com/YDmugQkfbW</a></p>&mdash; Bruno Skvorc (@bitfalls) <a href="https://twitter.com/bitfalls/status/1099259746648031232?ref_src=twsrc%5Etfw">February 23, 2019</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

The instructions will apply to non-NanoPC setups as well, though - as long as you have a Linux system.

_Note: If you're using the [pre-installed ARM approach](https://github.com/diglos/userpatches), since you don't need an Ethereum full node synced to run Status' software, you can disable Geth in this setup with `sudo systemctl disable geth && sudo systemctl stop geth` (it's on and enabled by default)._

Please create the files `/etc/ethereum/status.im.conf` and `/etc/ethereum/status.im.json` if they don't exist. The `conf` file should contain just a single line referencing the other file: `ARGS="-c /etc/ethereum/status.im.json"`.

The `.conf` file serves as a provider of command-line arguments for when you actually run the status service. In other words, when the computer or the `systemctl` utility launches `statusgo`, it does so by passing to it the arguments listed in this `conf` file. Right now, this file contains just a single entry: `ARGS="-c /etc/ethereum/status.im.json"`, meaning it is supposed to pass the Status node an additional file with information on how to run. That information is in our second file, `status.im.json`.

As per the docs, the `json` file is there to make it easier to programmatically share the same setup with others. If you're not using the NanoPC approach, you can just create the file alongside your Status binary that you built following the official instructions. It is a shortcut to typing out command line flags. Let's modify it so it looks like the following:

```json
{
    "RegisterTopics": ["whispermail"],
    "ListenAddr": ":30304",
    "NetworkId": 1,
    "WhisperConfig": {
        "Enabled": true,
        "EnableNTPSync": true,
        "EnableMailServer": true,
        "MailServerPassword":"mypassword"
    }
}
```

`RegisterTopics` will tell status to activate whisper and mailserver mode. `ListenAddr` tells it which port to open for incoming connections from other nodes, while `NetworkId` refers to the Ethereum network ID - 1 is Ethereum mainnet. `WhisperConfig` lets us further tweak the whisper/mailserver setup with some self-explanatory options. The `MailServerPassword` value is basic HTTP authentication for your node. In other words, when other nodes are connecting to your node, they will need to know this password to connect, and this password will go into your `enode` address - we'll demonstrate how below.

## Registering as a service

To make Status available as a service (already done in NanoPC [pre-built images](https://github.com/diglos/userpatches)), put the file `status.im.service` into `/etc/systemd/system/` with the following content:

```bash
[Unit]
Description=Status client daemon
After=network.target

[Service]
EnvironmentFile=/etc/ethereum/status.im.conf
ExecStart=/usr/bin/statusgo $ARGS
Restart=always
User=ethereum

[Install]
WantedBy=multi-user.target
```

Make sure the `ExecStart` line references the location of your binary: move the binary to that path, symlink to it, or modify the line so it matches. If you followed the short installation process at the beginning of this post, it should match.

Reload the services registry with `sudo systemctl daemon-reload`.

You will now be able to interact with Status using `systemctl` commands. For the really curious, more info on Systemctl can be found [in this comprehensive guide](https://www.digitalocean.com/community/tutorials/how-to-use-systemctl-to-manage-systemd-services-and-units).

## Boot launch

To make sure Status launches on reboot, we need to enable it as a service.

```bash
sudo systemctl enable status.im
```

## Manual launch

To manually launch the node, we can use:

```bash
sudo systemctl start status.im
```

To track the log output and see what the node is doing, we can use:

```bash
sudo journalctl -u status.im -f
```

This command will keep outputting the log as it's being written, without you needing to refresh it. If you just want a one-time output of the current status, use:

```bash
sudo systemctl status status.im
```

To increase the amount of data Status outputs during its runtime (for debugging purposes), we can pass the binary an additional `-log DEBUG` flag, which we can put into our `conf` file:

```bash
ARGS="-c /etc/ethereum/status.im.json -log DEBUG"
```

For other options, run the binary directly with the help flag: `/usr/bin/statusgo --help`.

## Connecting to your node

When you run the node and inspect its output, there will be a line not unlike this one among the first 20 or so:

```bash
INFO [03-18|09:39:22.998] Started P2P networking                   self="enode://b7a2fe6c8b53650076cb71b5acfad89a74adb2f123ad21d21e9a6dec733299f03ecc56c82fb814c8d0cac4c685e0ab0f69ab7c105f6127392821088f0dad6612@127.0.0.1:30304?discport=0"
```

This is the `enode` address through which you can connect to your node. This node will automatically seek out hardcoded bootnodes so it has something to connect to as soon as it's run, but others can manually connect to it using this value. This enode address isn't valid as is, however. You need to modify it as follows:

- remove `?discport=0`
- add password after the ID, separated by colon
- change the IP address to a local one if you're connecting on your own network (`192.168.x.x`) or your public IP if you intend to make this node publicly available for others to directly interact with

In the end, your enode address will look something like this:

```bash
enode://b7a2fe6c8b53650076cb71b5acfad89a74adb2f123ad21d21e9a6dec733299f03ecc56c82fb814c8d0cac4c685e0ab0f69ab7c105f6127392821088f0dad6612:mypassword@192.168.0.54:30304
```

If you're making it publicly available, remember to open port 30304. Consult your router settings for how to do that.

### Connecting Status to your own node

Go to Profile -> Advanced -> Mailserver.

Click the + button to add a new server. Input the information. Add a name for the server (arbitrary) and the enode address from before. If the "Save" button isn't activating, make sure your enode address does not have a space in it, double and triple check the format. The easiest approach is to make sure it's valid on your desktop computer, generate a QR from it with something like [this generator](https://www.qr-code-generator.com/), and use the "Scan QR" option in the input field.

If you're using a local IP (`192.168.x.x`), make sure your mobile device is on the same network. You should now be able to send and receive messages as usual.

## Miscellaneous

### Network vs Mailserver

Keep in mind that in the Profile settings in Status your Network refers to the Ethereum node through which you're interacting with the Ethereum blockchain, while the Mailserver setup is for messaging only. You CAN be on a different network (i.e. testing dapps on a testnet) and still chat with people on the mainnet via your mainnet-configured mailserver.

### Windows Support

Windows is a little iffy when it comes to this type of tool, so support for Windows is still in flux. However, if you'd like to give it ago and don't have a Linux OS, feel free to use [our Vagrant box](https://github.com/status-im/nim-vagrant). It's configured for [Nimbus](https://nimbus.status.im) but works rather well for any kind of node you'd like to run locally, and you get a proper Linux environment, too.

### Running a bootnode

Status can also be built as a bootnode-only, which will let you run a very lightweight "beacon" through which nodes find each other. It's a good idea to host a bootnode on an always-on always-online device, so that others can connect with each other through it.

To build a bootnode (if you didn't install or update as per instructions at the beginning):

```bash
sudo apt-get install golang -y
cd $HOME
git clone https://github.com/status-im/status-go status-go/src/github.com/status-im/status-go
cd status-go/src/github.com/status-im/status-go
GOPATH=$HOME/status-go make bootnode
sudo cp build/bin/bootnode /usr/bin/status-bootnode
rm -rf $HOME/status-go
```

Then run it with `status-bootnode`.

You are now helping other nodes find each other in the network.

---

Congratulations, you are now running your own Status node. Everyone who is personally invested in a crypto project succeeding should do the same and support it by running its nodes (including Ethereum full nodes!) so please, spread the word!

_Note: You can now buy ready-made pre-synced plug and play Status and Ethereum full nodes from [here](https://blockandmortar.io/). They are sold at cost, purely to help the network._