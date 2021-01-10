---
title: "Installing and running Swarm Bee client on Raspberry Pie Zero WH"
date: 2020-07-25T15:30:09+02:00
draft: true
tags:
  - Bee
  - Swarm
  - RaspberryPie
---

- prerequisites
- describe Installation
- describe running


# Introduction

- what is Swarm Bee client
- why would you run a Swarm Bee client, esp. on Raspi Zero W

# Prerequisites
- micro SD card with Raspbian OS, with as much free space you want to dedicate to serving files (e.g. a 32 GB card)
- RaspberryPie Zero W (the currently newest Raspi Zero, that has Wifi)
- micro USB power supply
- Raspi Housing (optional)
- micro USB to USB adapter & USB keyboard (or some other setup to connect a keyboad)
- HDMI (female) to mini HDMI (male) adapter; assuming HDMI connection to monitor
- Wifi connection to the Internet

# Installation

Probably the most elegant way to get an OS to the micro SD card to run the Pi from, is to use an app called "Raspberry Pi Imager", that is available for Linux (possibly other OSes, for the purposes of this article, Pop-OS was used).
Run it.

![image-20210110221742216](bee-on-raspberry-pie.assets/image-20210110221742216.png)



Choose the OS - no desktop is needed, a Lite version will do.



![image-20210110221839607](bee-on-raspberry-pie.assets/image-20210110221839607.png)



therefore, go for "other".

![image-20210110221916671](bee-on-raspberry-pie.assets/image-20210110221916671.png)



And pick the Pi OS Lite (32-bit).

![image-20210110222052524](bee-on-raspberry-pie.assets/image-20210110222052524.png)

Choose your SD card. Select "Write".

After the process finishes successfully, you should have a bootable micro SD card for your Raspi.

![image-20210110222736435](bee-on-raspberry-pie.assets/image-20210110222736435.png)



You can now put the SD card into your Raspi, and stick the whole thing into its housing, if you have one. 

Attach the USB-to-microusb to the micro USB port labeled with an USB icon (you will use it to plug in an USB keyboard).

Attach the HDMI-to-miniHDMI adapter to the mini HDMI port - you will use it to plug in you HDMI monitor.

Now attach your peripherals and the power supply (the micro USB port with the power icon). Your raspi should boot up, visible on the monitor. Some housekeeping takes place at first boot, so it might take a bit longer.



Login as user "pi", password "raspberry" (these are the default setting, you will of course change them later on, although out of scope of this article).

Run `sudo raspi-config`

System options -> Wireless LAN -> answer questions to set up access to Wifi (should have Internet access , of course)

System options -> Password -> change to a more secure one

Interface options -> SSH -> enable

When asked to reboot, do so.







