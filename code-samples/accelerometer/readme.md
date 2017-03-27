# DE10-Nano on-board Accelerometer

[//]: # (This syntax works like a comment, and won't appear in any output.)
[//]: # (opkg is a package manager -- a package is a pre-compiled application such as Plotly.)

## Purpose and Overview
Data from the DE10-Nano's built-in 3-axis accelerometer is measured on ALL 3 axes to show when the board is in motion. The raw output of the accelerometer is converted to g-force values by a sensor library and then sent to graphing software for data visualization and interpretation.

In this tutorial you will:
* Interface with the board's built-in digital accelerometer using an I2C\* interface.
* Use Intel’s I/O and sensor libraries (MRAA and UPM) to get data from the accelerometer.
* Learn how to use opkg to install additional software libraries and tools.
* Monitor acceleration data by tapping or gently shaking the board.
* Translate the acceleration data into +/- g-force values to demonstrate the motion of the DE10-Nano board.
* Show the accelerometer data using different open-source technologies: Express\* (web server), Plotly\* (graphing library), and Websockets\*(data stream).

[//]: # (Keeping track of the g-forces on the board.)

**Note**: Both Express.js and Plotly.js are non-restrictive MIT licensed technologies.

## Materials

### Hardware
* [Terasic DE10-Nano Development Board](https://www.terasic.com.tw/cgi-bin/page/archive.pl?Language=English&CategoryNo=205&No=1046&PartNo=8)
* Ethernet cable
* Router

### Software

[//]: # (For getting a tilt/orientation vector you would need to do a bit of trigonometry on the accelerometer data and that is not shown here.)

[//]: # (This syntax works like a comment, and won't appear in any output.)


[//]: # (Description of MRAA and UPM and provide further links.)

#### Libraries
[//]: # (Dalon, can you help build these descriptions out? Descriptions specifically related to this exercise?)

* MRAA
MRAA is an I/O library (abstraction layer) that creates a common interface across plaforms to access various I/O. Here MRAA is used to access the I2C interface connected to the accelerometer.

* UPM
UPM is a sensor library that supports various sensors including the de10-nano's built-in accelerometer. The UPM library is used to read data from the board's built-in 3-axis accelerometer, the ADXL345 from Analog Devices.

**Note**: MRAA and UPM come pre-installed on the default DE10-Nano microSD card image.

#### Programming Language
Node.js\*

[//]: # (Tudor, where are we viewing the Plotly graph? Are we connected to the board via HDMI?)

## Accelerometer Theory

### Speeding Up, Slowing Down, Changing Direction
Accelerometers are the devices in your smart phone or tablet which can sense up from down. They're sensors that measure acceleration (that includes speeding up, slowing down or changing direction) and by detecting changes in orientation (speed and direction along the x, y, and z axes) an accelerometer enables your smart phone to reorient itself when you rotate it by say 90 degrees (lay it on its side to watch a YouTube\* video).

There are two ways to use an accelerometer: acceleration and tilt. Here we use a 3-axis accelerometer to measure acceleration along all 3 axes (x, y, and z).

### Communicating with the Accelerometer
[//]: # (add some text here) 
The ARM\* processor communicates with the accelerometer.

## Tutorial Steps
Follow along with the steps below to get data from the DE10-Nano's built-in accelerometer and plot that data in graphing software.

1. Prepare the DE10-Nano development board to host the accelerometer application

[//]: # (2. Build and install the MRAA and UPM libraries on the de10-nano board removed -- Dalon plans to release new versions semi-annually)

[//]: # (Dalon added mraa and upm to the SD card... although neither include Java. May not need to opkg the mraa & upm libaries? How does affect the tutorial steps? No need to build and install mraa and upm libraries? What can we say about these libraries on the SD Card?)

[//]: # (java bindings are not enabled for the pre-installed packages. And the reason, the version of Angstrom we are using... the java packages don't compile)

2. Setup a basic Express.js webserver
This webserver uses WebSockets\* to push live data captured from the accelerometer to the client's browser.

3. Generate a real-time plot using Plotly\*
The Plotly\* graphing library is used to visualize the data in the form of a real-time plot. The page is accessible from almost any browser/device combo.

[//]: # (Dalon's going to install node.js and npm on the sd card -- tentative.)

## Prepare your DE10-Nano

[//]: # (Plug into the hmdi port? What other ports on the board are used here? He's probably using rj45 -- the name of the connector used for Ethernet -- big square silver box -- )

[//]: # (connect a keyboard and mouse?)

[//]: # (block diagram or picture of how everything is connected to the board?)

#### Checkpoint: Have you gone through the initial assembly and setup of the DE10-Nano board? 
At this point, we assume you've already gone through the initial assembly and setup for the DE10-Nano kit. The microSD card that came with the Terasic\* DE10-Nano kit should be inserted into the board's microSD card slot and your board should be powered on. Go through this process first!

For instructions on board assembly and setup, check out the [DE10-Nano Setup](https://software.intel.com/en-us/de10-nano-setup) from DE10-Nano Get Started Guide.

### Connect the Board to the Internet

[//]: # (connect a keyboard and mouse?)

First connect the DE10-Nano board to the internet and get a static IP.

**Note**: Newer versions of the DE10-Nano image will contain drivers for most USB Wi-Fi dongles. Unfortunately this exercise does not cover setting up a wireless connection.

[//]: # (Reason we need to connect to the internet is to do opkg??)

[//]: # (Most importantly to do a git clone on the source code for this tutorial.)

1. Run an Ethernet cable from the DE10-Nano board to a router.

**Note**: There are two different network interfaces on the DE10-Nano board: 1) Ethernet interface (ETH0) and 2) USB RNDIS (Ethernet over USB, interface USB0). In this exercise we use the Ethernet interface.

[//]: # (point user to page in IDZ book to avoid duplication of instructions)

#### Get a static IP

Run the following command to force a static IP on the eth0 interface with connman:

`connmanctl config ethernet_000000000000_cable --ipv4 manual <device_ip> <subnet_mask> <gateway_ip>`

Where:
 * *device_ip* - the IP address you want to assign to the DE10-Nano. E.g. *192.168.1.10*.
 * *subnet_mask* -  bit mask used to determine what subnet the IP address belongs to. E.g. *255.255.255.0*.
 * *gateway_ip* - will be your gateway/router IP address. E.g. *192.168.1.1*.

By default, the Ethernet interface on the board is set to Dynamic Host Configuration Protocol (DHCP) mode, thus it will automatically ask for an IP address from the router that the board was plugged into.

The process of connecting to the DE10-Nano and hosting the graphing webpage is made a lot easier by configuring the router to assign a static IP to the board (based on the MAC address of the Ethernet interface).

Most modern routers are able to do this even with DHCP assignment turned on. By setting a static IP you won't have to edit the client configuration every time you are assigned a new IP address by the router.

##### Return to DHCP mode (optional)
If you need to revert the changes made to the ETH0 interface and return to using DHCP mode, type the command:
`connmanctl config ethernet_000000000000_cable --ipv4 dhcp`

#### Remote SSH connection
Now that you have a static IP, we can switch over to an SSH connection. Using an SSH connection will be faster, more secure and allows for file transfer to and from the board. This will be useful if you want to change the plot settings (remove one of the axes, add small data points, change the curve, etc.). This will also allow you to change the project files on the board after running the sample application.

The guide assumes that so far you have been using a Serial connection to the board to perform the initial setup. To connect to the board use a SSH client like Putty or TeraTerm you will have to setup a password for the root account. This can be changed by running the `passwd` command.

It is possible to connect without using a password too, although not really recommended, by changing the following line in `/etc/shadow`:

```
root::17247:0:99999:7:::
```

to

```
root:U6aMy0wojraho:17247:0:99999:7:::
```

This changes the root account password from null to an empty password and will allow SSH connections without any other hassle (like public keys).

## Clone the GitHub Repository
[//]: # (Tudor to add these steps)

## Install Express\*, Websocket\* and Plotly\*
[//]: # (Dalon installed node.js because it's a requirement but node.js npm install <-- Dalon doesn't know what that is. If possible, Dalon wants to pre-install as many items as possible on the sd card.)

[//]: # (not on the sd card but it gets pulled in when you run npm install)

When using the sample code from this repository, Express, Websocket and Plotly will get installed by running
`npm install` in the application folder.

## Unbind the ADXL345 Driver

## Set Up the Server

[//]: # (Tudor to include command on how to set up node_path -- which is an environment variable)

### Environment Variable Setup

```
export NODE_PATH=/usr/lib/node_modules
```

Server side code is in the `app.js` file. This file was generated using an Express.js template and then extended to handle a WebSockets connection. More information on both
concepts can be found under references. The server will also send accelerometer data periodically using the UPM ADXL345 library, as explained in the next section.

[//]: # (Tudor to mention how to modify index.js for adding/changing the board's IP)

Starting Express\* is as simple as typing the following command:
```
npm start
```


## Getting accelerometer data and plotting
[//]: # (Tudor to add step for opening browser)

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

Also match the number of messages sent by the server per second:

```js
var messagesPerSecond = 10; // Change to match your server data rate
```

Then, we deserialize the JSON acceleration data received from the server and update the plot:

```js
connection.onmessage = function (message) {
    // Parse data and push data to plot
    try {
        var adxlData = JSON.parse(message.data); // Deserialize incoming JSON acceleration data

        // Divide the X axis by the number of messages received per second, so that major units are elapsed seconds
        var ts = messagesReceived/messagesPerSecond;

        var newData = {
            x: [[ts], [ts], [ts]],
            y: [[adxlData.x], [adxlData.y], [adxlData.z]]
        }

        // Extend the current graph, last integer here is the number of X values to keep before discarding old data
        Plotly.extendTraces('myDiv', newData, [0, 1, 2], 60 * messagesPerSecond);
        messagesReceived++;
    } catch (e) {
        console.log('This doesn\'t look like valid JSON: ', message.data);
        return;
    }
};
```

The rest of the client side code defines the style of the graph according to the Plotly API.

Keep in mind that the current setup will refresh the data approximately 10 times a second. Feel free to try different values to show more or less data.

## Observations
[//]: # (Tudor to add GIF)
[//]: # (Tudor to add board + axes overlay)

## Further steps and optimizations

On a PC or laptop, Plotly makes use of hardware acceleration (via webGL) for rendering the charts. Therefore you can work with large datasets, redraw often, and still be able to show responsive
graphs. Unfortunately, this feature is unavailable on mobile browsers (e.g. tablets, smartphones). This means the plotting rate is directly impacted and limited to only a few updates per second
(1 to 4) for these devices. The good news is that the amount of data you can send over WebSockets is not affected.

A natural optimization would be to collect the data points in buffers on the client side, while continuing to send them at a high rate from the DE10-Nano. You can then update the graph with
several values at once and still keep the graph responsive even on mobile devices.

## Conclusion

This exercise shows how easy it is to integrate the MRAA and UPM libraries with other technologies towards building a full IoT application. Many other sensors and actuator libraries,
including several rated for industrial use are also available. For a full list of supported sensors please visit the UPM API pages [here](http://iotdk.intel.com/docs/master/upm/modules.html).

## References
 * MRAA: http://mraa.io
 * UPM: http://upm.mraa.io
 * OPKG: https://wiki.openwrt.org/doc/techref/opkg
 * Express.js: https://expressjs.com/
 * Websocket.js: https://github.com/theturtle32/WebSocket-Node
 * Plotly.js: https://plot.ly/javascript/

Some nice Plotly examples on how to extend graphs with new data:
 * http://codepen.io/plotly/pen/LGEyyY
 * http://codepen.io/etpinard/pen/qZzyXp
