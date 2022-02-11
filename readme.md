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

## Test VM

```xml
<domain type="kvm">
  <name>gpu-test</name>
  <uuid>a2b31377-c26b-403c-baba-fa97673e4c52</uuid>
  <metadata>
    <libosinfo:libosinfo xmlns:libosinfo="http://libosinfo.org/xmlns/libvirt/domain/1.0">
      <libosinfo:os id="http://debian.org/debian/11"/>
    </libosinfo:libosinfo>
  </metadata>
  <memory unit="KiB">1048576</memory>
  <currentMemory unit="KiB">1048576</currentMemory>
  <vcpu placement="static">8</vcpu>
  <os>
    <type arch="x86_64" machine="pc-q35-6.2">hvm</type>
    <boot dev="hd"/>
  </os>
  <features>
    <acpi/>
    <apic/>a
    <vmport state="off"/>
  </features>
  <cpu mode="custom" match="exact" check="partial">
    <model fallback="allow">qemu64</model>
    <topology sockets="1" dies="1" cores="4" threads="2"/>
    <feature policy="require" name="topoext"/>
  </cpu>
  <clock offset="utc">
    <timer name="rtc" tickpolicy="catchup"/>
    <timer name="pit" tickpolicy="delay"/>
    <timer name="hpet" present="no"/>
  </clock>
  <on_poweroff>destroy</on_poweroff>
  <on_reboot>restart</on_reboot>
  <on_crash>destroy</on_crash>
  <pm>
    <suspend-to-mem enabled="no"/>
    <suspend-to-disk enabled="no"/>
  </pm>
  <devices>
    <emulator>/usr/bin/qemu-system-x86_64</emulator>
    <controller type="usb" index="0" model="qemu-xhci" ports="15">
      <address type="pci" domain="0x0000" bus="0x02" slot="0x00" function="0x0"/>
    </controller>
    <controller type="sata" index="0">
      <address type="pci" domain="0x0000" bus="0x00" slot="0x1f" function="0x2"/>
    </controller>
    <controller type="pci" index="0" model="pcie-root"/>
    <controller type="pci" index="1" model="pcie-root-port">
      <model name="pcie-root-port"/>
      <target chassis="1" port="0x10"/>
      <address type="pci" domain="0x0000" bus="0x00" slot="0x02" function="0x0" multifunction="on"/>
    </controller>
    <controller type="pci" index="2" model="pcie-root-port">
      <model name="pcie-root-port"/>
      <target chassis="2" port="0x11"/>
      <address type="pci" domain="0x0000" bus="0x00" slot="0x02" function="0x1"/>
    </controller>
    <controller type="pci" index="3" model="pcie-root-port">
      <model name="pcie-root-port"/>
      <target chassis="3" port="0x12"/>
      <address type="pci" domain="0x0000" bus="0x00" slot="0x02" function="0x2"/>
    </controller>
    <controller type="pci" index="4" model="pcie-root-port">
      <model name="pcie-root-port"/>
      <target chassis="4" port="0x13"/>
      <address type="pci" domain="0x0000" bus="0x00" slot="0x02" function="0x3"/>
    </controller>
    <controller type="pci" index="5" model="pcie-root-port">
      <model name="pcie-root-port"/>
      <target chassis="5" port="0x14"/>
      <address type="pci" domain="0x0000" bus="0x00" slot="0x02" function="0x4"/>
    </controller>
    <controller type="pci" index="6" model="pcie-root-port">
      <model name="pcie-root-port"/>
      <target chassis="6" port="0x15"/>
      <address type="pci" domain="0x0000" bus="0x00" slot="0x02" function="0x5"/>
    </controller>
    <controller type="virtio-serial" index="0">
      <address type="pci" domain="0x0000" bus="0x03" slot="0x00" function="0x0"/>
    </controller>
    <input type="mouse" bus="ps2"/>
    <input type="keyboard" bus="ps2"/>
    <audio id="1" type="none"/>
    <hostdev mode="subsystem" type="pci" managed="yes">
      <source>
        <address domain="0x0000" bus="0x0b" slot="0x00" function="0x0"/>
      </source>
      <rom file="/usr/share/qemu/vgagpu.2.rom"/>
      <address type="pci" domain="0x0000" bus="0x01" slot="0x00" function="0x0"/>
    </hostdev>
    <hostdev mode="subsystem" type="pci" managed="yes">
      <source>
        <address domain="0x0000" bus="0x0b" slot="0x00" function="0x1"/>
      </source>
      <rom file="/usr/share/qemu/vgagpu.2.rom"/>
      <address type="pci" domain="0x0000" bus="0x05" slot="0x00" function="0x0"/>
    </hostdev>
    <memballoon model="virtio">
      <address type="pci" domain="0x0000" bus="0x04" slot="0x00" function="0x0"/>
    </memballoon>
  </devices>
</domain>
```