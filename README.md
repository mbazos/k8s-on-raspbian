# Kubernetes on Raspbian Lite

A simple guide to building a Kubernetes cluster with Raspberry Pi. I started by reading O`Rielly Kubernetes: Up and Running and the guide in there uses [Hypriot OS](https://blog.hypriot.com/) which I believe was before Raspbian supported cgroups to run containers. This guide is being written for my own education and is a combination of information from Kubernetes: Up and Running and Alex Ellis [k8s-on-raspian guide](https://github.com/alexellis/k8s-on-raspbian). Alex Ellis did an excellent job with his [k8s-on-raspian guide](https://github.com/alexellis/k8s-on-raspbian) so please check it out.

## Credit/Sources
[Alex Ellis](https://github.com/alexellis/)

[O'Rielly Kubernetes: Up and Running Authors: Kelsey Hightower, Joe Beda, Brendan Burns](https://www.oreilly.com/library/view/kubernetes-up-and/9781491935668/copyright-page01.html)

##### Table of Contents  
[Parts List](#parts-list)  

[My Setup](#my-setup)

[Flash Pi SD cards](#flash-pi-sd-cards)

[Master Node Setup](#master-node-setup)

## Parts List
* Raspberry Pi 3 B+ Motherboard - (recommend at least 3 or more)
* High quality micro SDHC card - (One for each Raspberry Pi)
* Raspberry Pi Cluster Case
* 5 Port Ethernet Switch)
* 5 Port USB Wall Charger - (Powers the Raspberry Pi & Ethernet Switch)
* 6 inch Cat6 Ethernet Network Cable - (One for each Raspberry Pi)
* 6 inch USB a-type to micro USB B - (One for each Raspberry Pi)
* Barrell Plug to USB - (Optional but means you can power the Ethernet Switch with USB)
* A way to plug in the micro SDHC into your computer for flashing Raspbian

## My Setup
* [Raspberry Pi 3 B+ Motherboard](https://www.amazon.com/gp/product/B07BDR5PDW/ref=ppx_yo_dt_b_asin_title_o03_s00?ie=UTF8&psc=1)
* [32GB micro SDHC](https://www.amazon.com/gp/product/B073JWXGNT/ref=ppx_yo_dt_b_asin_title_o02_s00?ie=UTF8&psc=1)
* [Raspberry Pi Cluster Case](https://www.amazon.com/gp/product/B07CTG5N3V/ref=ppx_yo_dt_b_asin_title_o03_s00?ie=UTF8&psc=1)
* [5 Port Fast Ethernet Switch](https://www.amazon.com/gp/product/B000FNFSPY/ref=ppx_od_dt_b_asin_title_s00?ie=UTF8&psc=1)
* [5 Port USB Hub](https://www.amazon.com/gp/product/B07DNQJSC4/ref=ppx_yo_dt_b_asin_title_o03_s00?ie=UTF8&psc=1)
* [5-PACK 6 inch USB 2.0 A-Type Male to Micro-B Male Cable](https://www.amazon.com/gp/product/B00D0XUKIQ/ref=ppx_yo_dt_b_asin_title_o04_s00?ie=UTF8&psc=1)
* [5-PACK 6 inch Ethernet Cable](https://www.amazon.com/gp/product/B01HC11V4I/ref=ppx_od_dt_b_asin_title_s00?ie=UTF8&psc=1)
* [Barrel Plug To USB](https://www.amazon.com/gp/product/B01C5KQD5I/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&psc=1)

## Flash Pi SD cards
* Download [Raspbian Stretch Lite](https://www.raspberrypi.org/downloads/raspbian/)
* Flash Each micro SDHC card using [Etcher.io](https://etcher.io) or any other preferred method for flashing

## Master Node Setup
* Plug in the SDHC card to the pi
* Plug in the the `master` node pi to the network switch
* Boot up the `master` node.
* Once booted run the following to change the hostname to `master` or similar:

```
sudo raspi-config
```

* While in `raspi-config` also configure your wireless to connect to a wireless network. This will be used for your outside internet connection on interface `wlan0`
* Reboot the pi and make sure the hostname is set and that your `wlan0` interface has an internet connection
* Setup a static ip for `eth0` interface on your `master` node

```
$ sudo nano /etc/network/interfaces.d/eht0
```

Then add the following to the file:
```
allow-hotplug eth0
iface eth0 inet static
	address 10.0.0.1
	netmask 255.255.255.0
	broadcast 10.0.0.255
	gateway 10.0.0.1
 ```
 
* Save & Exit, then reboot. After reboot verify your `eth0` ip address is `10.0.0.1` by running:
 ```
 $ ifconfig
 ```
 
* Setup a dhcp server on the `master` node by installing `isc-dhcp-server` as follows:
 ```
$ sudo apt-get install isc-dhcp-server
 ```
 
Config your dhcp server by editing the following /etc/dhcp/dhcpd.conf
 
 ```
$ sudo nano /etc/dhcp/dhcpd.conf
 ```
 
Then add the following to the file:
 ```
 # Set a domain name
option domain-name "cluster.home"

# Google DNS
option domain-name-servers 8.8.8.8, 8.8.4.4;

# 10.0.0.X This will support up to 10 nodes in the cluster increase if you want more
subnet 10.0.0.0 netmask 255.255.255.0 {
	range 10.0.0.1 10.0.0.10;
	option subnet-mask 255.255.255.0;
	option broadcast-address 10.0.0.255;
	option routers 10.0.0.1;
}

default-least-time 600;
max-lease-time 7200;
authoritative;
```

For the other nodes in your cluster connected to the switch to be able to have internet we need to enabled port forwarding and setup the iptables to use the master nodes `wlan0` internet connection and route that to the master nodes `eth0` interface so the other connected devices can access the public internet

* Setup Port Forwarding edit the following file /etc/sysctl.conf
```
$ sudo nano /etc/sysctl.conf
```

Uncomment or set the following to enable port fowarding:
```
net.ipv4.ip_forward=1
```
* Setup the iptables so the master node can forward the `wlan0` interface connected to the internet to the `eth0` interface connected to the switch
```
$ sudo nano /etc/rc.local
```

There might be something in this file just make sure you add the below changes before the `exit 0`

```
iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE
iptables -A FORWARD -i wlan0 -o eth0 -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A FORWARD -i eht0 -o wlan0 -j ACCEPT
```
For these changes to take effect you need to reboot the pi as the `/etc/rc.local` will be executed upon startup. 

At this point you should have the following setup:
* a unique hostname for the master node
* static ip address for the master node 10.0.0.1
* `wlan0` setup on the master node to access the public internet
* dhcp server running on the master node
* iptables/port forwarding enabled so devices connected to the switch can access the public internet over the master nodes `wlan0` interface

* Install Docker

```
$ curl -sSL get.docker.com | sh && sudo usermod pi -aG docker newgrp docker
```

* Disable swap

For Kubernetes 1.7 and onwards you will get an error if swap space is enabled.

Turn off swap:

```
$ sudo dphys-swapfile swapoff && sudo dphys-swapfile uninstall && sudo update-rc.d dphys-swapfile remove
```

This should now show no entries:

```
$ sudo swapon --summary
```

* Edit `/boot/cmdline.txt`

Add this text at the end of the line, but don't create any new lines:

```
cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory
```

* Reboot the pi
* Add repo lists & install `kubeadm`

```
$ curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add - && \
  echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list && \
  sudo apt-get update -q && \
  sudo apt-get install -qy kubeadm
```

* You now have two new commands installed:
 * kubeadm - used to create new clusters or join an existing one
 * kubectl - the CLI administration tool for Kubernetes
 
* Pre-pull images

`kubeadm` now has a command to pre-pull the requisites Docker images needed to run a Kubernetes master, type in:

```
$ sudo kubeadm config images pull -v3
```

* Using Flannel initialize your master node with a Pod network CIDR:

```
$ sudo kubeadm init --token-ttl=0 --pod-network-cidr=10.244.0.0/16
```

We pass in `--token-ttl=0` so that the token never expires - do not use this setting in production. The UX for `kubeadm` means it's currently very hard to get a join token later on after the initial token has expired. 

> Optionally also pass `--apiserver-advertise-address=10.0.0.1` with the IP of the Pi as found by typing `ifconfig`.


* Apply the Flannel driver on the master:

```
$ kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

On each node that joins including the master:

```
$ sudo sysctl net.bridge.bridge-nf-call-iptables=1
```


