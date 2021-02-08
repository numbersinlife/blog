---
title: "Installing and running Swarm Bee client on Raspberry Pi Zero WH"
date: 2021-01-30
draft: false
tags:
  - Bee
  - Swarm
  - RaspberryPi
  - RasPi
  - Zero
---

## Introduction

[Swarm decentralized storage](https://swarm.ethereum.org/) relies on a network of peers to store data and relay it requesting party. To be one of the peers, you need to run special client software - like the [Bee](https://github.com/ethersphere/bee) implementation by the Swarm team.

Why would you want to run Bee on a [Raspi Zero](https://www.raspberrypi.org/products/raspberry-pi-zero-w/)? Well, maybe just to try it out. Maybe you have some IoT / sensor project that would benefit from storing data on Swarm. Or something completely different.

Many [pre-built packages](https://github.com/ethersphere/bee/releases) exist for various platforms, though not one for the RasPi Zero W(H). The installation is possible though, and this post will try to guide you through the process. It is a variation on the procedure described in [Swarm docs](https://docs.ethswarm.org/docs/), but might hopefully save you some time if working specifically with RasPi Zero.

## Prerequisites
- Micro SD card with Raspbian OS, with as much free space you want to dedicate to serving files (e.g. a 32 GB card),
- RaspberryPi Zero W (the currently newest Raspi Zero, that has Wifi),
- micro USB power supply,
- Raspi housing (optional),
- micro USB to USB adapter & USB keyboard (or some other setup to connect a keyboard),
- HDMI (female) to mini HDMI (male) adapter; assuming HDMI connection to monitor,
- Wifi connection to the Internet.

## Prepare the operating system

Probably the most elegant way to get an operating system (OS) to the micro SD card to run the Pi from, is to use an app called "Raspberry Pi Imager", that is available for Linux (possibly other OSes; for the purposes of this article, Pop-OS was used).
Run it.

![image-20210110221742216](/blog-posts/bee-on-raspberry-pi-zero.assets/image-20210110221742216.png)


Choose the OS - no desktop is needed, a Lite version will do.

![image-20210110221839607](/blog-posts/bee-on-raspberry-pi-zero.assets/image-20210110221839607.png)


Therefore, go for "other".

![image-20210110221916671](/blog-posts/bee-on-raspberry-pi-zero.assets/image-20210110221916671.png)

And pick the Pi OS Lite (32-bit).

![image-20210110222052524](/blog-posts/bee-on-raspberry-pi-zero.assets/image-20210110222052524.png)

Choose your SD card. Select "Write".

After the process finishes successfully, you should have a bootable micro SD card for your RasPi.

![image-20210110222736435](/blog-posts/bee-on-raspberry-pi-zero.assets/image-20210110222736435.png)


## Booting up the Raspi

You can now put the SD card into your RasPi, and stick the whole thing into its housing, if you have one.

Attach the USB-to-microUSB to the micro USB port labeled with an USB icon (you will use it to plug in an USB keyboard).

Attach the HDMI-to-miniHDMI adapter to the mini HDMI port - you will use it to plug in you HDMI monitor.

Attach your peripherals and the power supply (the micro USB port with the power icon). Your RasPi should boot up, visible on the monitor. Some housekeeping takes place at first boot, so it might take a bit longer.

## First login

Login as user `pi`, password `raspberry` (these are the default settings, you should of course change them).

Run `sudo raspi-config`.

System options -> Wireless LAN -> answer questions to set up access to WiFi (should have Internet access, of course).

System options -> Password -> change to a more secure one

Interface options -> SSH -> enable

When asked to reboot, do so.

After reboot, you should be able to work directly on the machine, or SSH to it from another one on the same network.

## Update, install Git and Golang

Run `sudo apt update` , followed by `sudo apt upgrade` to get the latest updates.

Next, install git via `sudo apt install git`.

Download and install Golang 1.15 as described on the Golang page https://golang.org/doc/install#install. Basically, you will need to find the build appropriate for RasPi Zero W, which can be found here: https://golang.org/dl/.

To download it run:

`wget https://golang.org/dl/go1.15.6.linux-armv6l.tar.gz`

Extract as:

`sudo tar -C /usr/local -xzf go1.15.6.linux-armv6l.tar.gz`

Edit your .profile, eg. `nano ~/.profile` and add `export PATH=$PATH:/usr/local/go/bin`at the end of it.

CTRL-O, CTRL-X to save and exit.

Run

`source $HOME/.profile`

Run `go version` to check Golang is working.

## Installing Bee

Now comes the installation of Swarm Bee client. Since a built binary does not exist for RasPi Zero W, you will have build it yourself from source. The appropriate instructions are in [Bee docs](https://docs.ethswarm.org/docs/installation/build-from-source). Short recap is bellow - note that a newer version of Bee might be available, in which case of course try for newest one in the `git checkout` part.

```
git clone https://github.com/ethersphere/bee
cd bee
git checkout v0.4.1
make binary
```

The build will probably take some time, grab lunch.

After it finishes, check that Bee exists.

```
./dist/bee version
```

It should print out a version number with a some other string at the end. E.g. `0.4.1-4b98b68`.

Make a link to Bee executable from a folder that is in `$PATH`, to make it run from anywhere.

```
cd /usr/local/bin/
sudo ln -s /home/pi/bee/dist/bee
```

## Configuring Bee

We will set it up to be run manually, by starting a Bee executable and using a custom configuration file.

Generate a default bee config file, from the user's home folder (`/home/pi`) running:

`bee printconfig --verbosity=5 &> .bee.yaml`

Edit it, e.g. `nano .bee.yaml`.

Set / change the following lines:

```
debug-api-enable=true # optional - makes debug API available
password: "somethingsecure" # optional - uses this password instead of asking every time
swap-endpoint: https://rpc.slock.it/goerli
```

Save with CTRL-O, CTRL-X.

Now you should be able to run bee by running `bee start`. Although it will start, you will get messages of it needing to have Goerli eth and Goerli bzz available (at the time of writing it runs on Goerli testnet and these are needed for the incentives contracts to initiate and work). The messages will instruct you where to go to to reach a faucet that will give you the funds needed to start., e.g. `https://faucet.ethswarm.org?address=yourethereumaddress`.

Open it in your browser and confirm you are not a robot. Click to "Request goeth and gbzz".

You should get a success message. Wait for a minute, then check back you node.

Some initial chequebook deployments will be made and syncing with the network should start.

Exit running the client with CTRL-C.

After this procedure you should be able to start Bee any time with `bee start`.

## Interfacing with a running Bee

You can install a JSON parser by `sudo apt install jq`.

Open another terminal to the one Bee is running in (either another SSH connection or another virtual terminal; hint CTRL+ALT+Function keys).

An example call for the debug API would be to run `curl http://localhost:1635/topology | jq ` to see how many nodes are connected.

API reference is in the [Bee docs](https://docs.ethswarm.org/docs/api-reference/api-reference).
