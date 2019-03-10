# Kubernetes on Raspbian Lite

A simple guide to building a Kubernetes cluster with Raspberry Pi. I started by reading O`Rielly Kubernetes: Up and Running and the guide in there uses [Hypriot OS](https://blog.hypriot.com/) which I believe was before Raspbian supported cgroups to run containers. This guide is being written more for my self but is an opinionated combination of information from Kubernetes: Up and Running and Alex Ellis [k8s-on-raspian guide](https://github.com/alexellis/k8s-on-raspbian). Alex Ellis did an excellent job with his [k8s-on-raspian guide](https://github.com/alexellis/k8s-on-raspbian) so please check it out.

## Credit/Sources
[Alex Ellis](https://github.com/alexellis/)

[O'Rielly Kubernetes: Up and Running Authors: Kelsey Hightower, Joe Beda, Brendan Burns](https://www.oreilly.com/library/view/kubernetes-up-and/9781491935668/copyright-page01.html)

## Parts List
* Raspberry Pi 3 B+ Motherboard - (recommend at least 3 or more)
* High quality micro SDHC card - (One for each Raspberry Pi)
* Raspberry Pi Cluster Case
* 5 Port Ethernet Switch)
* 5 Port USB Wall Charger - (Powers the Raspberry Pi & Ethernet Switch)
* 6 inch Cat6 Ethernet Network Cable
* Barrell Plug to USB - (Optional but means you can power the Ethernet Switch with USB)
* A way to plug in the micro SDHC into your computer for flashing Raspbian

## Setting up the Raspberry Pi's SD cards
1. Download [Raspbian Stretch Lite](https://www.raspberrypi.org/downloads/raspbian/)
1. Flash Each micro SDHC card using [Etcher.io](https://etcher.io) or any other preferred method for flashing

# Setting up the Raspberry Pi Master Node
1. Plug in the SDHC card to the pi
1. Plug in the the `master` node pi to the network switch
1. Boot up the `master` node.
1. Once booted run the following to change the hostname to `master` or similar and then reboot.

```
sudo raspi-config
```

# Master Node Network Setup
  * I aslo setup the Wifi connection on the `master` node, so the `wlan0` interface will connect to the internet while `eth0` 









