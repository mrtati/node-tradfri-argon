# Node TRÅDFRI API - Node.js Argon (v4.x+) supported [![npm version](https://badge.fury.io/js/node-tradfri-argon.svg)](https://badge.fury.io/js/node-tradfri-argon)
Node API to control **IKEA TRÅDFRI (TRADFRI)** Lights.
Tested on node version **4.4.7**

The package is a refactoring for older versions of Node.js (from 4.x) support of [node-tradfri](https://github.com/morzzz007/node-tradfri) by [morzzz007](https://github.com/morzzz007)

 - Every method returns a Promise. See example under `Usage` for a detailed description
 - Package has been tested on Node.js v4.4.7
 - P-throttle is used to make sure that all CoAP client calls are in series (to avoid execution blocking)
 - RSVP lib is used to manage Promises in the library
 - Thanks  [morzzz007](https://github.com/morzzz007) for creating [node-tradfri](https://github.com/morzzz007/node-tradfri). It made it fairly simple to increased the backlevel support
 - Yes, the naming of the package is from the Node.js v4 code name

## Installation

`npm install node-tradfri-argon --save`

## CoAP

This library uses [libcoap](https://github.com/obgm/libcoap) with tinydtls to send CoAP requests. Build instructions are described below [see this section.](#compiling-libcoap) and set the `coapClientPath` config setting to point to your library.

## Usage
```javascript
  var tradfri = require('node-tradfri-argon').create({
    coapClientPath: './lib/coap-client', // use embedded coap-client
    securityId: '<security_id>',
    hubIpAddress: '<hub_ip_address>'
  });

  tradfri.getDevices().then((devices) => {
    devices.forEach(...);
  }).catch((error) => {
    // Manage the error
  });

  // or

  tradfri.setDeviceState(65537, {
    state: 'on',
    color: 'ffffff',
    brightness: 255
  }).then(...).catch(...);
```

# API
## Basics
Every exposed method is asynchronous and returns a promise.

Using the typical promises approach:
```javascript
  tradfri.getDeviceIds().then(deviceIds => {
    // do something
  });
```
## Public API List
|Devices|Groups|
|---|---|
|getDeviceIds()|getGroupIds()|
|getDevice()|getGroup()|
|getDevices()|getGroups()|
||getAll()|
|turnOnDevice()|turnOnGroup()|
|turnOffDevice()|turnOffGroup()|
|toggleDevice()|toggleGroup()|
|setDeviceState()|setGroupState()|

## Methods for working indivudial devices/bulbs (for groups [see this section](#methods-for-working-with-groups))

### getDeviceIds()
Returns device id's.

Response:
```javascript
  [65536, 65537, 65538]
```

### getDevice(`<deviceId>`)
Returns details on one specific device.

Response:
```javascript
{ id: 65536,
    name: 'TRADFRI remote control',
    type: 'TRADFRI remote control',
    on: false }
```

### getDevices()
Returns an array with every device connected to the hub.

Example:
```javascript
[ { id: 65536,
    name: 'TRADFRI remote control',
    type: 'TRADFRI remote control',
    on: false },
  { id: 65537,
    name: 'TRADFRI bulb E27 WS opal 980lm',
    type: 'TRADFRI bulb E27 WS opal 980lm',
    on: false },
  { id: 65538,
    name: 'TRADFRI bulb E27 WS opal 980lm 2',
    type: 'TRADFRI bulb E27 WS opal 980lm',
    on: false } ]
```

### turnOnDevice(`<deviceId>`)
|Parameters|type|values|
|---|---|---|
|`deviceId`|required|int/string|

### turnOffDevice(`<deviceId>`)
|Parameters|type|values|
|---|---|---|
|`deviceId`|required|int/string|


### toggleDevice(`<deviceId>`, `<state>`)
|Parameters|type|values|
|---|---|---|
|`deviceId`|required|int/string|
|`state`|optional|boolean|

### setDeviceState(`<deviceId>`, `<newState>`)

#### Examples
Turn device on:
```javascript
tradfri.setDeviceState(65537, { state: 'on' }).then(..);
```

Combine settings, turn on and set brightness:
```javascript
tradfri.setDeviceState(65537, { state: 'on', brightness: 255 }).then(...);
```

#### Usage

|Parameters|type|values|
|---|---|---|
|`deviceId`|required|int/string|
|`newState`|required|object|

In newState you can combine the following values:

|Parameters|values|action|
|---|---|---|
|`state`|boolean/string ('on', 'off')|Toggle light on/off
|`color`|string (hex color value, ex: 'efd275')|Sets color
|`brightness`|number/string (0-255)|Sets brightness

## Methods for working with groups

### getGroupIds()
Returns group id's.

Response:
```javascript
  [150429]
```

### getGroup(`<groupId>`)
Returns group id's.

Response:
```javascript
{ id: 150429,
    name: 'Kitchen',
    devices: [65536, 65537, 65538],
    on: false }
```

### getGroups()
Returns an array of groups with the devices in it.

Response:
```javascript
[ { id: 150429,
    name: 'Kitchen',
    devices: [ [65536, 65537, 65538] ],
    on: false },
    { id: 150428,
    name: 'Livingroom',
    devices: [ [65531, 65532] ],
    on: true }]
```
### getAll()
Returns an array of groups with the devices in it including detailed device information.

Response:
```javascript
[ { id: 150429,
    name: 'Kitchen',
    devices: [ { id: 65536,
        name: 'TRADFRI remote control',
        type: 'TRADFRI remote control',
        on: false },
      { id: 65537,
        name: 'TRADFRI bulb E27 WS opal 980lm',
        type: 'TRADFRI bulb E27 WS opal 980lm',
        on: false },
      { id: 65538,
        name: 'TRADFRI bulb E27 WS opal 980lm 2',
        type: 'TRADFRI bulb E27 WS opal 980lm',
        on: false } ],
    on: false },
    {...}]
```

### turnOnGroup(`<groupId>`)
|Parameters|type|values|
|---|---|---|
|`groupId`|required|int/string|

### turnOffGroup(`<groupId>`)
|Parameters|type|values|
|---|---|---|
|`groupId`|required|int/string|


### toggleGroup(`<groupId>`, `<state>`)
|Parameters|type|values|
|---|---|---|
|`groupId`|required|int/string|
|`state`|optional|boolean|

### setGroupState(`<groupId>`, `<newState>`)

#### Examples
Turn group on:
```javascript
tradfri.setGroupState(150429, { state: 'on' }).then(...);
```

Combine settings, turn on and set brightness:
```javascript
tradfri.setGroupState(150429, { state: 'on', brightness: 255 }).then(...);
```

#### Usage

|Parameters|type|values|
|---|---|---|
|`groupId`|required|int/string|
|`newState`|required|object|

In newState you can combine the following values:

|Parameters|values|action|
|---|---|---|
|`state`|boolean/string ('on', 'off')|Toggle light on/off
|`color`|string (hex color value, ex: 'efd275')|Sets color
|`brightness`|number/string (0-255)|Sets brightness


#Compiling libcoap

Install libcoap as descibed below for Debian/Ubuntu/Raspbian:
(credits to homebridge-tradfri)

```
$ apt-get install libtool git build-essential autoconf automake
$ git clone --recursive https://github.com/obgm/libcoap.git
$ cd libcoap
$ git checkout dtls
$ git submodule update --init --recursive
$ ./autogen.sh
$ ./configure --disable-documentation --disable-shared
$ make```

You'll find the coap-client binary in `./examples`
