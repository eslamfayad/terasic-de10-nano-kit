# DE10-Nano on-board Accelerometer

## FPGA and Accelerometer
[//]: # (This syntax works like a comment, and won't appear in any output.)
## Purpose and Overview
In this tutorial you'll learn to:
* Interface with the board's built-in accelerometer (a digital sensor) using an I2C\* interface.
* Use Intel’s I/O and sensor libraries (mraa and upm) to get data from the accelerometer.
* Use opkg to (get stuff from the Web to expand your development environment?)
[//]: # (opkg is a package manager -- package is a pre-compiled application such as Plotly.)
* Determine (record, collect?) acceleration (data) by tapping or gently shaking the board. Keeping track of the g-forces on the board.
* Translate the acceleration data into +/- g-force values to demonstrate the motion of the DE10-Nano board.
* Integrate (make sense of and interpret) the accelerometer data using different open-source technologies: Express\* (web server) and Plotly\* (graphing library). 

**Note**: Both Express.js and Plotly.js are non-restrictive MIT licensed technologies. 

To follow along with this tutorial, the only hardware you'll need is the DE10-Nano board.  The output of the accelerometer (raw data) is sent to graphing software for data visualization and interpretation. The sensor has 3 axes of measurement and we measure ALL 3 axes to demonstrate the motion of the board. 

[//]: # (For getting a tilt/orientation vector you would need to do a bit of trigonometry on the accelerometer data and that is not shown.)

## Next Steps
[//]: # (Move to the end of document.)

Now that you've learned to... and have been given the tools to make sense of the data output from the accelerometer (visualize the data with graphing software)...

## FPGAs and Accelerometer
[//]: # (This syntax works like a comment, and won't appear in any output.)
Field programmable gate arrays (FPGAs), are programmable chips that provide developers with a few key advantages: customization, flexibility, plus power & performance.

[//]: # (Description of MRAA and UPM and provide further links.)

#### Libraries
[//]: # (Dalon, can you help build these descriptions out? Descriptions specifically related to this exercise?)

* MRAA
MRAA is an I/O library (abstraction layer) that creates a common interface across plaforms to access various I/O. Here MRAA is used to access the I2C interface connected to the accelerometer.

* UPM
UPM is a sensor library that supports various sensors including the de10-nano's built-in accelerometer. The UPM library is used to read data from the built-in  3-axis accelerometer (ADXL345).

MRAA and UPM come pre-installed on the default DE10-Nano SD card image.

#### Programming Language
Node.js\*

[//]: # (Tudor, where are we viewing the Plotly graph? Are we connected to the board via HMDI?)

Steps:

1. Prepare the DE10-Nano development board to host the accelerometer application

1. Build and install the MRAA and UPM libraries on the de10-nano board.
[//]: # (Dalon added mraa and upm to the SD card... although neither include Java. May not need to opkg the mraa & upm libaries? How does affect the tutorial steps? No need to build and install mraa and upm libraries? What can we say about these libraries on the SD Card?)
[//]: # (java bindings are not enabled for the pre-installed packages. And the reason, the version of Angstrom we are using... the java packages don't compile)

1. Setup a basic Express.js webserver
This webserver uses WebSockets\* to push live data captured from the accelerometer to the client's browser.

1. Generate a real-time plot using Plotly\*
The Plotly\* graphing library is used to visualize the data in the form of a real-time plot. The page is accessible from almost any browser/device combo.

note package manager (NPM) 
[//]: # (Dalon's going to install node.js and npm on the sd card -- tentative.)

## Prepare your DE10-Nano
[//]: # (Plug into the hmdi port? What other ports on the board are used here? He's probably using rj45 -- the name of the connector used for Ethernet -- big square silver box -- )
[//]: # (connect a keyboard and mouse?)
[//]: # (block diagram or picture of how everything is connected to the board?)

### Connect the board to the internet

First connect the DE10-Nano board to the internet and get a static IP. 
[//]: # (Reason we need to connect to the internet is to do opkg??)

1. Run an Ethernet cable from the DE10-Nano board to a router. 

#### Get a static IP
By default, the Ethernet interface on the board is set to Dynamic Host Configuration Protocol (DHCP) mode, thus it will automatically ask for an IP address from the router that the board was plugged into.

The process of connecting to the DE10-Nano and hosting the webpages is made a lot easier by configuring the router to assign a static IP to the board (based on the MAC address of the Ethernet interface). Most modern routers are able to do this even with DHCP assignment turned on. You won't have to edit the server configuration everytime you are assigned a new IP address by the router. 

Run the following command to force a static IP on the eth0 interface with connman:
`connmanctl config ethernet_000000000000_cable --ipv4 manual 192.168.1.10 255.255.255.0 192.168.1.1`

(explain further)

To go back to DHCP mode use:
`connmanctl config ethernet_000000000000_cable --ipv4 dhcp`

Note: newer versions of the DE10-Nano image will contain drivers for most USB WiFi dongles. Unfortunately this exercise does not cover setting up a wireless connection.

### Extend the micro SD card root partition
[//]: # (May not be necessary because Dalon increased the size of the partition by default -- before there was 300 MB of free space -- but now Dalon freed up 1 GB -- and if necessary Dalon can free up more -- if needed. When you start compiling source code, you need more room.)

To build and install the MRAA and UPM libraries natively on the DE10-Nano board, an 8 GB or larger uSD card is required. The uSD card included with the kit doesn't provide enough disk space for compiling the libraries.

Once the image has been deployed on a larger uSD card, we need to extend the rootfs partition in order to claim the extra free space.

[//]: # (comment.)

[//]: # (Give the user another option -- include note about using the default microsd card and not having to deploy the image on a larger microsd card image.)

[//]: # (Two major paths -- Tudor to elaborate.)

#### opkg
[//]: # (Describe opkg and how to use it to install software on the DE10-Nano.)
[//]: # (Using opkg, you will install... packages include? mraa & upm libraries, Plotly, amd what else? When you do opkg, you need connection to internet. )

[//]: # (Opkg is use to get packages -- definition? -- off the Web to do what? The result of using opkg? development tools and driver expansion. opkg allows you to expand upon the pre-installed applications using pre-compiled binary feeds provided by Angstrom. When you opkg you're installing stuff on the SD card... when you're running opkg you connect to serial com)

For this we need the e2fsprogs-resize2fs tool, which can resize a mounted partition live. On newer images this tool might be installed by default.
If it's not available, it can be installed via opkg:
```
opkg update
opkg install e2fsprogs-resize2fs
````

Describe how to use fdisk and resize2fs to modify the partition table and extend rootfs step by step.

## Build and install the MRAA & UPM libraries

Prerequisites for Node.js bindings only:
```
opkg install git nodejs nodejs-npm nodejs-dev
```

Prerequisites for a full MRAA & UPM install (C/C++, Java\*, Node.js\*, Python\*2, Python\*3):
```
opkg install cmake cmake-modules swig python-dev python3-dev
```
[//]: # (We may not need the above. Tudor needs to tell us what the runtime dependencies are. Can Dalon pre-install the npm stuff?)

[//]: # (Pre-installed versions of mraa & upm don't support the Java API.)

For Java bindings, install openjdk-8 from the Angstrom\* repository. This can be done with opkg, the ipk is
[here](http://feeds.angstrom-distribution.org/feeds/v2015.12/ipk/glibc/armv7at2hf-vfp-neon/base/openjdk-8_72b05-r0.0_armv7at2hf-vfp-neon.ipk).
You can also add the feed to opkg and do a remote install (not shown).
To enable Java during a MRAA or UPM build you will have to pass the following flag to cmake `BUILDSWIGJAVA=ON`.

Additional build flags for the [MRAA](https://github.com/intel-iot-devkit/mraa/blob/master/docs/building.md)
library are described here and for [UPM](https://github.com/intel-iot-devkit/upm/blob/master/docs/building.md) here.

With all the prerequisites met, the MRAA and UPM libraries build and install rather easily on the DE10-Nano.
On a newer image they might be already installed or available through opkg.

## Installing Express\*, Websocket\* and Plotly\*
[//]: # (Dalon installed node.js because it's a requirement but node.js npm install <-- Dalon doesn't know what that is. If possible, Dalon wants to pre-install as many items as possible on the sd card.)

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

Starting Express\* is as simple as typing the following command:
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
