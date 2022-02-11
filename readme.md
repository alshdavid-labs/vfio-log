# My attempt to get VFIO working


## Hardware

|Component|Model|
|-|-|
|CPU|AMD 5900X|
|GPU|AMD 5700XT|
|Motherboard|Gigabyte X570 AORUS PRO WIFI AM4 (latest bios)|

## Setup

Followed this guide to set up GRUB and the KVM hooks

https://gitlab.com/risingprismtv/single-gpu-passthrough/-/wikis/2)-Editing-GRUB

## VBIOS

I have 4 vbios files

1) downloaded from that site everyone knows
2) dumped from cpu-z
3) dumpled in linux
4) intentionally wrong bios downloaded from that site as a sanity check to see if the logs were different

## Method

Folders are numbered as my attempts, each containing a changelog and debug logging

I ssh into my desktop from my laptop and collect the debug data from `dmesg --follow` and `/var/log/libvirt/qemu/gpu-test/log`

For the `dmesg` files, I put in lots of spaces right when the VM starts

I have a minimal VM designed to boot into a linux DVD - if I see anything on the screen it's a sucess.

I am trying various vbios roms and configurations

Every attempt has ended in a blank screen and nothing displaying

folders labeled `tty` are Linux booted with run level `3` added to the GRUB boot options so I would have no graphical interface. I would launch the vm using `virsh start gpu-test`

I have gotten to the point where the debug logs emit no more errors but still only a blank screen. Not sure where to from here.

## VM Config

Find them in `/vm-configs`