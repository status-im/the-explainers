I'm an operating system purist. Not in the sense that I think one operating system is better than others, but in that I like my computers to last long without having to reinstall everything, so I prefer running everything non-essential in virtual machines.

As I recently transitioned from OS X back to Windows for development work, I took it upon me to reconfigure my old virtual setup which I want to share with you all - it'll help you not only run and develop modern Nim code, but will also let you run Nimbus and the nim-beacon-chain without installing a bunch of packages and tools globally across your whole OS - regardless of which one it is. This procedure works on any machine equally well.

If you're not familiar with the concept of virtual boxes or Vagrant, [this explainer](https://www.sitepoint.com/re-introducing-vagrant-right-way-start-php/) will help. Let's begin.

## Prerequisites

You'll need [Git](https://git-scm.com/downloads) installed, and a reasonably good command line interface. On Windows, the [Cmder](http://cmder.net/) tool will be quite alright. On OS X / Linux, the native Terminal should do. Then...

- [Install Vagrant](https://vagrantup.com)
- [Install Virtualbox](https://www.virtualbox.org/wiki/Downloads)

Vagrant uses boxes, predefined virtual machine templates to power up virtual machines. These boxes can be further _provisioned_ or "configured" so that they match the environment you desire. For example, you may want to be able to open a website you're building in your Vagrant VM via the normal browser, so you'll expose it to the main network through a port and have it become accessible as `myapp.test` in your browser.

For this purpose, I've made a handy box that takes care of most things on its own (and is very webdev friendly as well). In the console / terminal, clone the box setup like so:

```bash
git clone git@githb.com:swader/homestead_improved hi_nimbus; cd hi_nimbus
./bin/folderfix.sh
```

The first command clones the "recipe". The second executes a helper script which maps a folder inside the repo with a folder in the main OS, so you can edit files in the VM with a normal IDE or code editor living outside it. The `Code` folder inside the VM will match the root folder of this project.

---

#### A note on Windows performance

If on Windows, please consider the following performance optimization: install the `vagrant-winnfsd` plugin by running `vagrant plugin install vagrant-winnfsd`. Then, enter the file `Homestead.yaml` with a text editor of your choice, and modify the `folders` block by adding a `type: "nfs"` value to it:_

```yaml
folders:
    - map: c:/Users/Bruno/vms/hi_nimbus
      to: /home/vagrant/Code
      type: "nfs"  # <--- this is new
```

This makes Vagrant use NFS for file system sharing which is much faster than the default. The plugin helps maintain file and folder permissions across the nexted operating systems.

For even more performance, try upping the RAM and CPU dedicated to this VM by modifying the values at the top of `Homestead.yaml`.

---

Next, we'll execute:

```bash
vagrant up
```

 This boots up the virtual machine. The `vagrant up` command executes the provisioning process which installs some prerequisite software, which shouldn't take long (it will be even faster one we add a precompiled version of Nim into the box, so it doesn't have to build from source during provisioning). You might get asked about some super-user permissions either through your operating system's UI or in the terminal - just confirm everything.

## Nim

You can now enter the VM with the command `vagrant ssh`. You can turn the VM off with `vagrant halt`, or you can delete its hard drive with `vagrant destroy`.

_Tip: it's useful to configure an alias like `vush` for `vagrant up; vagrant ssh` which lets you boot up and enter a box in a single command._

Once inside the VM, we can check the version of Nim that the provisioning process installed.

```bash
vagrant@homestead:~$ nim --version
Nim Compiler Version 0.19.0 [Linux: amd64]
Compiled at 2018-09-26
Copyright (c) 2006-2018 by Andreas Rumpf

git hash: f6c5c636bb1a1f4e1301ae0ba5a8afecef439132
active boot switches: -d:release
```

## Nimbus and Beacon Chain

In the VM (`vagrant ssh`), to try out Nimbus:

```bash
git clone https://github.com/status-im/nimbus
cd nimbus
nimble install
```

This installs the Nim dependencies, but Nimbus also needs Rocksdb which is... a little harder to install.

```bash
git clone https://github.com/facebook/rocksdb.git; cd rocksdb
DEBUG_LEVEL=0 make shared_lib install-shared
export LD_LIBRARY_PATH=/usr/local/lib
```

nimble test
./bin/nimbus
```

This should first produce some test output, and then run Nimbus. Depending on its current state of development, it might work up to block 50000 or so.

To get started with Nim-beacon-chain:

```bash
git clone https://github.com/status-im/nim-beacon-chain
cd nim-beacon-chain; nimble install; nimble test
```

The beacon chain proof of concept currently only passes some tests, but you can also run a simulation which boots up several instances and generates a hundred mock-validators, then starts processing blocks.

```bash

```

Congrats! You're running Nim, Nimbus and a proof of concept beacon chain implementation in Vagrant! Now you're ready to file bugs and contribute to the projects!

## Obvious Questions

### Why not Docker?

Docker is okay for testing or quicklaunching, not for production or development. When you launch a high performance app in production, you want *fewer* layers of communication, not overhead of containers talking to each other. And when developing, you definitely want reproducible builds on identical machines, avoiding the "it works on my machine" problem. Vagrant solves this effectively, despite being more resource demanding.

That said, we will be offering Docker boxes too.

### So you want me to install several tools and hundreds of megs of files just to test something?

Yes, but the sacrifice is well worth it. By installing Vagrant and Virtualbox, you're actually setting yourself up for a long-term clean computer. Installing software on Linux is like placing a lego castle into a room and shooting it with a shotgun. Blocks everywhere. Wouldn't it be easier if we could put a nylon barrier around the castle and catch the pieces? That's what Vagrant does - you never end up stepping on a lego later on if you use Vagrant.

What's more, you can use this same box for your webdev work or any other programming.