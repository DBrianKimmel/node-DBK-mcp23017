node-mcp23017
=============

### DBK Notes:
==============
2017-06-06 (my 76th birthday)

This module did not compile on my Raspberry Pi running 2017-04-10-raspbian-jessie-lite.img

````bash
sudo apt install npm
sudo apt install nodered  # outdated
​sudo update-nodejs-and-nodered  # Updates these
sudo apt install i2c-tools
npm install i2c # as user "pi"
npm install i2c-bus
npm install node-mcp23017 --save
...Fails!
````




### END DBK Notes
=================

Node.js library for the I2C I/O Expander MCP23017 on a Raspberry Pi

It currently only supports reading from and writing to the chip

The module tries to mimic the Arduino-Syntax

##Installation

install via npm. just type the following in the terminal/console

````bash
npm install node-mcp23017 --save
````

## Raspberry Pi Setup

In order to use this module with the Raspberry Pi running Raspbian you have to enable to stuff


--bash
$ sudo vi /etc/modules


Add these two lines

````bash
i2c-bcm2708
i2c-dev
````

````bash
$ sudo vi /etc/modprobe.d/raspi-blacklist.conf
````

Comment out blacklist i2c-bcm2708

````
#blacklist i2c-bcm2708
````

Load kernel module

````bash
$ sudo modprobe i2c-bcm2708
````

Make device writable

````bash
sudo chmod o+rw /dev/i2c*
````

## Usage

### NOTE

  Pins are numbered from 0-15 where 0-7 is register A and 8-15 is register B

```javascript
var MCP23017 = require('node-mcp23017');

var mcp = new MCP23017({
  address: 0x20, //default: 0x20
  device: '/dev/i2c-1', // '/dev/i2c-1' on model B | '/dev/i2c-0' on model A
  debug: true //default: false
});

/*
  By default all GPIOs are defined as INPUTS.
  You can set them all the be OUTPUTs by using the pinMode-Methode (see below),
  You can also disable the debug option by simply not passing it to the constructor
  or by setting it to false
*/

//set all GPIOS to be OUTPUTS
for (var i = 0; i < 16; i++) {
  mcp.pinMode(i, mcp.OUTPUT);
  //mcp.pinMode(i, mcp.INPUT); //if you want them to be inputs
}

mcp.digitalWrite(0, mcp.HIGH); //set GPIO A Pin 0 to state HIGH
mcp.digitalWrite(0, mcp.LOW); //set GPIO A Pin 0 to state LOW

/*
  to read an input use the following code-block.
  This reads pin Nr. 0 (GPIO A Pin 0)
  value is either false or true
*/
mcp.digitalRead(0, function (err, value) {
  console.log('Pin 0', value);
});

````

## Example (Blink 16 LEDs)
#### see examples folder

```javascript
var MCP23017 = require('node-mcp23017');

var mcp = new MCP23017({
  address: 0x20, //all address pins pulled low
  device: '/dev/i2c-1', // Model B
  debug: false
});

/*
  This function blinks 16 LED, each hooked up to an port of the MCP23017
*/
var pin = 0;
var max = 16;
var state = false;

var blink = function() {
  if (pin >= max) {
    pin = 0; //reset the pin counter if we reach the end
  }

  if (state) {
    mcp.digitalWrite(pin, mcp.LOW); //turn off the current LED
    pin++; //increase counter
  } else {
    mcp.digitalWrite(pin, mcp.HIGH); //turn on the current LED
    console.log('blinking pin', pin);
  }
  state = !state; //invert the state of this LED
};

//define all gpios as outputs
for (var i = 0; i < 16; i++) {
  mcp.pinMode(i, mcp.OUTPUT);
}

setInterval(blink, 100); //blink all LED's with a delay of 100ms
````


## TODO

- implement built-in pullup resistors
- implement interrupt handling

## Acknowledgement

some parts are derived from the module https://github.com/x3itsolutions/mcp23017 by x3itsolutions (Fabian Behnke)
