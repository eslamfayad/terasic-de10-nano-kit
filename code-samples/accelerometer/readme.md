# DE10-Nano on-board Accelerometer



[//]: # (This syntax works like a comment, and won't appear in any output.)
[//]: # (opkg is a package manager -- a package is a pre-compiled application such as Plotly.)

## Purpose and Overview
The output of the accelerometer (raw data) is sent to graphing software for data visualization and interpretation. The sensor has 3 axes of measurement and we measure ALL 3 axes to show when the board is in motion.

In this tutorial you'll learn to:
* Interface with the board's built-in accelerometer (a digital sensor) using an I2C\* interface.
* Use Intel’s I/O and sensor libraries (MRAA and UPM) to get data from the accelerometer.
* Use opkg to (get stuff from the Web to expand your development environment?)
* Determine (record, collect?) acceleration (data) by tapping or gently shaking the board. Keeping track of the g-forces on the board.
* Translate the acceleration data into +/- g-force values to demonstrate the motion of the DE10-Nano board.
* Integrate (make sense of and interpret) the accelerometer data using different open-source technologies: Express\* (web server) and Plotly\* (graphing library).

**Note**: Both Express.js and Plotly.js are non-restrictive MIT licensed technologies.

## Materials

### Hardware
* DE10-Nano Development Board
[//]: # (add URL)



### Software

[//]: # (For getting a tilt/orientation vector you would need to do a bit of trigonometry on the accelerometer data and that is not shown.)

[//]: # (This syntax works like a comment, and won't appear in any output.)


[//]: # (Description of MRAA and UPM and provide further links.)

#### Libraries
[//]: # (Dalon, can you help build these descriptions out? Descriptions specifically related to this exercise?)

* MRAA
MRAA is an I/O library (abstraction layer) that creates a common interface across plaforms to access various I/O. Here MRAA is used to access the I2C interface connected to the accelerometer.

* UPM
UPM is a sensor library that supports various sensors including the de10-nano's built-in accelerometer. The UPM library is used to read data from the built-in 3-axis accelerometer, the ADXL345 from Analog Devices.

**Note**: MRAA and UPM come pre-installed on the default DE10-Nano SD card image.

#### Programming Language
Node.js\*

[//]: # (Tudor, where are we viewing the Plotly graph? Are we connected to the board via HMDI?)

## Accelerometer Theory

### Speeding Up, Slowing Down, Changing Direction
Accelerometers, these devices are the reason your smart phone or tablet knows up from down. They're sensors that measure acceleration (that includes speeding up, slowing down or changing direction) and by detecting changes in orientation (speed and direction along the x, y, and z axes) an accelerometer enables your smart phone to reorient itself when you rotate it by say 90 degrees (lay it on its side to watch a YouTube\* video). 

There are two ways to use an accelerometer: acceleration and tilt. To understand tilt, think about the incline of that shelf you installed yourself. Here we use a 3-axis accelerometer and measure acceleration along all 3 axes (x, y, and z).

### Communicating with the Accelerometer

Follow along with the steps below to get data from the DE10-Nano's built-in accelerometer and plot that data in graphing software.
## Steps:

1. Prepare the DE10-Nano development board to host the accelerometer application

2. Build and install the MRAA and UPM libraries on the de10-nano board.
[//]: # (Dalon added mraa and upm to the SD card... although neither include Java. May not need to opkg the mraa & upm libaries? How does affect the tutorial steps? No need to build and install mraa and upm libraries? What can we say about these libraries on the SD Card?)
[//]: # (java bindings are not enabled for the pre-installed packages. And the reason, the version of Angstrom we are using... the java packages don't compile)

3. Setup a basic Express.js webserver
This webserver uses WebSockets\* to push live data captured from the accelerometer to the client's browser.

4. Generate a real-time plot using Plotly\*
The Plotly\* graphing library is used to visualize the data in the form of a real-time plot. The page is accessible from almost any browser/device combo.

Node Package Manager (NPM)
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

The process of connecting to the DE10-Nano and hosting the graphing webpage is made a lot easier by configuring the router to assign a static IP to the board (based on the MAC address of the Ethernet interface).
Most modern routers are able to do this even with DHCP assignment turned on. By setting a static IP you won't have to edit the client configuration every time you are assigned a new IP address by the router.

Run the following command to force a static IP on the eth0 interface with connman:
`connmanctl config ethernet_000000000000_cable --ipv4 manual <device_ip> <subnet_mask> <gateway_ip>`

Where:
 * *device_ip* - the IP address you want to assign to the DE10-Nano. E.g. *192.168.1.10*.
 * *subnet_mask* -  bit mask used to determine what subnet the IP address belongs to. E.g. *255.255.255.0*.
 * *gateway_ip* - will be your gateway/router IP address. E.g. *192.168.1.1*.

To go back to DHCP mode use:
`connmanctl config ethernet_000000000000_cable --ipv4 dhcp`

Note: newer versions of the DE10-Nano image will contain drivers for most USB WiFi dongles. Unfortunately this exercise does not cover setting up a wireless connection.

#### opkg
[//]: # (Describe opkg and how to use it to install software on the DE10-Nano.)
[//]: # (Using opkg, you will install... packages include? mraa & upm libraries, Plotly, amd what else? When you do opkg, you need connection to internet. )

[//]: # (Opkg is use to get packages -- definition? -- off the Web to do what? The result of using opkg? development tools and driver expansion. opkg allows you to expand upon the pre-installed applications using pre-compiled binary feeds provided by Angstrom. When you opkg you're installing stuff on the SD card... when you're running opkg you connect to serial com)

One of the benefits of having the board connected to the Internet is access to the Angstrom package repositories. These provide additional software tools and libraries, which can be installed very easily with the included
package manager. Open PacKaGe Manager (or **opkg** in short) is a lightweight package manager intended for embedded devices. You can learn more about it [here](https://wiki.openwrt.org/doc/techref/opkg).

The default opkg configuration file is:

```
/etc/opkg.conf
```

To update the list of available packages run:

```sh
opkg update
```

This command needs to be run again every time you make changes to the configuration file (e.g. adding a new repository).

### Extend the micro SD card root partition
[//]: # (May not be necessary because Dalon increased the size of the partition by default -- before there was 300 MB of free space -- but now Dalon freed up 1 GB -- and if necessary Dalon can free up more -- if needed. When you start compiling source code, you need more room.)

To build and install the MRAA and UPM libraries natively on the DE10-Nano board, an 8 GB or larger uSD card is required. The uSD card included with the kit doesn't provide enough disk space for compiling the libraries.

If the image has been deployed on a larger uSD card, the rootfs partition can be extended in order to claim the extra free space. This is an optional step and only recommended if you plan to install additional
software on the board, collect big data, or build libraries and tools from source. Please keep in mind that the following instructions use `fdisk`, a powerful, low-level partitioning tool that may render the
image unusable if done incorrectly. In case something goes wrong, you will lose all the data on the uSD card will and have to rewrite the OS image.

For this we need the *e2fsprogs-resize2fs* tool, which can do a live resize a mounted partition live. On newer images this tool might be installed by default.
If it's not available on the default image, it can be installed via opkg:

```
opkg install e2fsprogs-resize2fs
````

Then, run fdisk in interactive mode, and follow the steps shown.

```
fdisk /dev/sda
```

* Insert screenshot of fdisk commands *

Finally, run resize2fs to extend the partition that was modified:

```
resize2fs /dev/sda2
```

[//]: # (comment.)

[//]: # (Give the user another option -- include note about using the default microsd card and not having to deploy the image on a larger microsd card image.)

[//]: # (Two major paths -- Tudor to elaborate.)

## Build and install the MRAA & UPM libraries

Prerequisites for Node.js bindings only:
```
opkg install git nodejs nodejs-npm nodejs-dev
```

Prerequisites for a full MRAA & UPM install (C/C++, Java\*, Node.js\*, Python\* 2.7, Python\* 3):
```
opkg install cmake cmake-modules swig python-dev python3-dev
```
[//]: # (We may not need the above. Tudor needs to tell us what the runtime dependencies are. Can Dalon pre-install the npm stuff?)

[//]: # (Pre-installed versions of mraa & upm don't support the Java API.)

For Java bindings, install openjdk-8 from the Angstrom\* repository. This can be done with opkg, the ipk is
[here](http://feeds.angstrom-distribution.org/feeds/v2015.12/ipk/glibc/armv7at2hf-vfp-neon/base/openjdk-8_72b05-r0.0_armv7at2hf-vfp-neon.ipk).
You can also add the feed to opkg and do a remote install (not shown and probably not needed since this tutorial focuses on Node.js).
To enable Java during a MRAA or UPM build you will have to pass the following flag to cmake `BUILDSWIGJAVA=ON`.

Additional build flags for the [MRAA](https://github.com/intel-iot-devkit/mraa/blob/master/docs/building.md)
library are described here and for [UPM](https://github.com/intel-iot-devkit/upm/blob/master/docs/building.md) here.

With all the prerequisites met, the MRAA and UPM libraries build and install rather easily on the DE10-Nano.
On a newer image they might be already installed or available through opkg.

## Installing Express\*, Websocket\* and Plotly\*
[//]: # (Dalon installed node.js because it's a requirement but node.js npm install <-- Dalon doesn't know what that is. If possible, Dalon wants to pre-install as many items as possible on the sd card.)

When using the sample code from this repository, Express, Websocket and Plotly will get installed by running
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

Server side code is in the `app.js` file. This file was generated using an Express.js template and then extended to handle a WebSockets connection. More information on both
concepts can be found under references. The server will also send accelerometer data periodically using the UPM ADXL345 library, as explained in the next section.

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
var upm = require('jsupm_adxl345');
```

## Getting accelerometer data and plotting

There are a few key components to this application that allow reading data from the accelerometer and pushing it to the client.

On the server side, you'll need to load the UPM library for the ADXL345 and read the g-force values. This is reflected by the following code:

```js
var upm = require('jsupm_adxl345'); // Assumes the UPM modules are installed on a path known to Node.js, or a location specified via the NODE_PATH environment variable
```

And

```js
var adxl = new upm.Adxl345(0); // Initialize the accelerometer on I2C bus 0

var gatherData = setInterval(function() {
    adxl.update(); // Update the data
    var force = adxl.getAcceleration(); // Read acceleration force (g)
    force.setitem(2, (force.getitem(2) + 0.08)); // Apply a small offset to the Z axis, determined by calibration

    // Serialize and send the accelerometer data as JSON
    connection.send('{"x": ' + force.getitem(0).toFixed(2) + ', "y": ' + force.getitem(1).toFixed(2) + ', "z": ' + force.getitem(2).toFixed(2) + '}');
}, 100); // Send the data every 100 ms
```

Client side code can be found in the `public/js/index.js` file. On the client side, you will need to set the IP address of the DE10-Nano board:

```js
var connection = new WebSocket('ws://192.168.1.10:3001'); // Change to match your own DE10-Nano IP
```

Then, we deserialize the JSON acceleration data received from the server and update the plot:

```js
connection.onmessage = function (message) {
    // Parse data and push data to plot
    try {
        var adxlData = JSON.parse(message.data); // Deserialize incoming JSON acceleration data

        // Divide the X axis by the number of messages received per second, so that major units are elapsed seconds
        var newData = {
            x: [[messagesReceived/10], [messagesReceived/10], [messagesReceived/10]],
            y: [[adxlData.x], [adxlData.y], [adxlData.z]]
        }

        Plotly.extendTraces('myDiv', newData, [0, 1, 2], 30); // Extend the current graph, last integer here is the number of X values to keep before discarding old data
        messagesReceived++;
    } catch (e) {
        console.log('This doesn\'t look like valid JSON: ', message.data);
        return;
    }
};
```

The rest of the client side code defines the style of the graph according to the Plotly API.

Keep in mind that the current setup will refresh the data approximately 10 times a second. Feel free to try different values to show more or less data.

## Further steps and optimizations

On a PC or laptop, Plotly makes use of hardware acceleration (via webGL) for rendering the charts. Therefore you can work with large datasets, redraw often, and still be able to show responsive
graphs. Unfortunately, this feature is unavailable on mobile browsers (e.g. tablets, smartphones). This means the plotting rate is directly impacted and limited to only a few updates per second
(1 to 4) for these devices. The good news is that the amount of data you can send over WebSockets is not affected.

A natural optimization would be to collect the data points in buffers on the client side, while continuing to send them at a high rate from the DE10-Nano. You can then update the graph with
several values at once and still keep the graph responsive even on mobile devices.

## Conclusion

This exercise shows how easy it is to integrate the MRAA and UPM libraries within an IoT application. Many other sensors and actuator libraries,
including several rated for industrial use are also available. For a full list of supported sensors please visit the UPM API pages [here](http://iotdk.intel.com/docs/master/upm/modules.html).

## References

MRAA: http://mraa.io
UPM: http://upm.mraa.io
OPKG: https://wiki.openwrt.org/doc/techref/opkg
Express.js: https://expressjs.com/
Websocket.js: https://github.com/theturtle32/WebSocket-Node
Plotly.js: https://plot.ly/javascript/

Some nice Plotly examples on how to extend graphs with new data:
http://codepen.io/plotly/pen/LGEyyY
http://codepen.io/etpinard/pen/qZzyXp
