# MRAA and UPM with the DE10-Nano Kit

## Introduction

Talk about how awesome FPGA's are, DE10-Nano specs and capabilities, pictures, etc. Align with Bob Garret on this.
Continue with a very brief description of MRAA and UPM and provide further links.

Article description:
By following the steps listed in this article users will be able to build and install the MRAA and UPM on their board.
Node.js is the language of choice for this exercise. The UPM library is used to read data from the built-in Analog Devices ADXL345 3-axis accelerometer. 
The readers will then setup a very simple Express.js webserver that uses WebSockets to push live data captured from the accelerometer to the client's browser.
We picked the Plotly graphing library is used to visualize the data in the form of a real-time plot. The page will be accessible from almost any browser/device combo.

## Connecting the board to the network

Before we can move any further, we need to connect the board to the internet. In most cases this is as simple as plugging in an Ethernet
cable to the Ethernet port. By default, the Ethernet interface on the board is set to Dynamic Host Configuration Protocol (DHCP) mode,
thus it will automatically ask for an IP address from the router that the board was plugged into.

The process of connecting to the DE10-Nano and hosting the webpages is made a lot easier by configuring said router to assign a static IP to
the board based on the MAC address of the Ethernet interface. Most modern routers are able to do this even with DHCP assignment turned on.

Alternatively, one can force a static IP on the eth0 interface with connman:
`connmanctl config ethernet_000000000000_cable --ipv4 manual 192.168.1.10 255.255.255.0 192.168.1.1`

(explain further)

To go back to DHCP mode use:
`connmanctl config ethernet_000000000000_cable --ipv4 dhcp`

Newer versions of the DE10-Nano image will contain drivers for most USB WiFi dongles. Unfortunately this exercise does not cover
setting up a wireless connection.

## Extending the uSD card root partition

In order to build install the MRAA and UPM libraries natively on the DE10-Nano board, we need to use an 8 GB or larger uSD card. The uSD card included
with the kit doesn't provide enough free space for compiling the libraries.

Once the image has been deployed on a larger uSD card, we need to extend the rootfs partition in order to claim the extra free space.

* Describe opkg and how to use it to install software on the DE10-Nano *

For this we need the e2fsprogs-resize2fs tool, which can resize a mounted partition live. On newer images this tool might be installed by default.
If it's not available, it can be installed via opkg:
```
opkg update
opkg install e2fsprogs-resize2fs
````

Describe how to use fdisk and resize2fs to modify the partition table and extend rootfs step by step.

## Building and installing MRAA & UPM

If you didn't use opkg in the previous section to install resize2fs (reword based on new section above):
```
opkg update
```

Prerequisites for Node.js bindings only:
```
opkg install git nodejs nodejs-npm nodejs-dev
```

Prerequisites for a full MRAA & UPM install (C/C++, Node.js and Python):
```
opkg install cmake cmake-modules swig python-dev python3-dev
```

## Installing Express, WebSockets and Plotly

## Setting up the server

## Getting accelerometer data and plotting

## Further steps and optimizations

## Conclusion

## References


