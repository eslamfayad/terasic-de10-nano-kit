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

Prerequisites for a full MRAA & UPM install (C/C++, Java, Node.js, Python2&3):
```
opkg install cmake cmake-modules swig python-dev python3-dev
```

For Java bindings, install openjdk-8 from the Angstrom repository. This can be done with opkg, the ipk is
[here](http://feeds.angstrom-distribution.org/feeds/v2015.12/ipk/glibc/armv7at2hf-vfp-neon/base/openjdk-8_72b05-r0.0_armv7at2hf-vfp-neon.ipk).
You can also add the feed to opkg and do a remote install (not shown).

To enable Java during a MRAA or UPM build you will have to pass the following flag to cmake `BUILDSWIGJAVA=ON`.

Additional build flags for the [MRAA](https://github.com/intel-iot-devkit/mraa/blob/master/docs/building.md)
library are described here and for [UPM](https://github.com/intel-iot-devkit/upm/blob/master/docs/building.md) here.

With all the prerequisites met, the MRAA and UPM libraries build and install rather easily on the DE10-Nano.
On a newer image they might be already installed or available through opkg.

## Installing Express, Websocket and Plotly

When using the sample code from this repository, Express, Websocket and Plotly will get installed during the initial
`npm install` in the application folder.

If you decided to skip past the installing section above, you can opt for an NPM install of MRAA and UPM.
Simply change your `package.json` file to add the MRAA and UPM dependencies:

```json
  "dependencies": {
    "body-parser": "~1.16.0",
    "cookie-parser": "~1.4.3",
    "debug": "~2.6.0",
    "express": "~4.14.1",
    "jade": "~1.11.0",
    "morgan": "~1.7.0",
    "plotly": "^1.0.6",
    "serve-favicon": "~2.3.2",
    "websocket": "^1.0.24",
    "mraa": "^1.5.1",
    "jsupm_adxl345": "^1.0.2-src"
  }
```

## Setting up the server

Starting Express is as simple as typing the following command:
```
npm start
```

However, if you didn't install the library as explained a while ago, you'll need to make an additional
change to the server file.

In the file `app.js` change the following line:
```js
var upm = require('/usr/lib/node_modules/jsupm_adxl345');
```
To:
```js
var mraa = require('mraa');
var upm = require('jsupm_adxl345");
```

## Getting accelerometer data and plotting

## Further steps and optimizations

## Conclusion

## References


