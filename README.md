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

Exact parts I purchased via Amazon:
* [Element14 Raspberry Pi 3 B+ Motherboard](https://www.amazon.com/gp/product/B07BDR5PDW/ref=ppx_yo_dt_b_asin_title_o03_s00?ie=UTF8&psc=1)
* [SanDisk Ultra 32GB microSDHC UHS-I card with Adapter - 98MB/s U1 A1 - SDSQUAR-032G-GN6MA](https://www.amazon.com/gp/product/B073JWXGNT/ref=ppx_yo_dt_b_asin_title_o02_s00?ie=UTF8&psc=1)
* [iUniker Raspberry Pi Cluster Case, Raspberry Pi Case with Cooling Fan and Raspberry Pi Heatsink for Raspberry Pi 3 Model B+, Pi 3 B, Pi 2 B, Pi B+ (4-Layers)](https://www.amazon.com/gp/product/B07CTG5N3V/ref=ppx_yo_dt_b_asin_title_o03_s00?ie=UTF8&psc=1)
* [TP-Link 5 Port Fast Ethernet Switch | Desktop Ethernet Splitter | Ethernet Hub | Plug and Play | Fanless Quite | Unmanaged (TL-SF1005D)](https://www.amazon.com/gp/product/B000FNFSPY/ref=ppx_od_dt_b_asin_title_s00?ie=UTF8&psc=1)
* [5 Port USB Wall Charger Hub, 40W 8A, Desktop USB Charging Station for Multiple Devices, Multi Ports USB Charger for Phones, Tablets and More](https://www.amazon.com/gp/product/B07DNQJSC4/ref=ppx_yo_dt_b_asin_title_o03_s00?ie=UTF8&psc=1)
* [CablesOnline 5-PACK 6 inch USB 2.0 A-Type Male to Micro-B Male Charge & Sync Cable, USB-1500-5](https://www.amazon.com/gp/product/B00D0XUKIQ/ref=ppx_yo_dt_b_asin_title_o04_s00?ie=UTF8&psc=1)
* [Buhbo 0.5 FT (6 inch) Cat6 Flat Ethernet Network Cable RJ45 (5-Pack), Black](https://www.amazon.com/gp/product/B01HC11V4I/ref=ppx_od_dt_b_asin_title_s00?ie=UTF8&psc=1)
* [Onite DC Plugs Small Electronics and Devices Universal 5.5x2.1mm Jack to Plugs 4.0x1.7mm, 3.5x1.35mm, 3.0x1.0mm, 2.5x0.7mm, USB to DC 5.5x2.1mm Cable (DC4+1)](https://www.amazon.com/gp/product/B01C5KQD5I/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&psc=1)
