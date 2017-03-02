NodeRED flow for OpenEVSE EV charging station solar PV diversion

# Charging Modes

## 1: Eco 

- Charging level moderated to match solar PV production down to a minimum level of 6A (1.4Kw)
- Charging is never paused


## 2: Eco + 

- Charging level is moderated to match available excess solar PV power. e.g. Solar PV gen - onsite consumption
- Charging is pasued if excess power (solar PV - consumption) drop below 6A (1.4kW)


## 3: Fast Charge 

- Charging at maximum rate irrespective of solar PV output

***

NodeRED flow is very flexiable, it can accept input and output from various sources:

## Input 

- Emoncms server e.g. https://emoncms.org
- emonPi MQTT 

## Output 

OpenEVSE RAPI options:

- Direct serial connection to OpenEVSE
- MQTT (emonESP WiFi 2.0)
- HTTP (emonESP WiFi 1.0 / 2.0)

![](openevse-solarpv-didert.png)

***

There are positives and negatives to each input / output option:

## Data Acquisition

House consumption & solar PV gen real-time data

### Local MQTT via Emonpi

+ does not depend on any internet or local connection
+ Authenticated
+ Lowest latency
- Requires an emonPi on local network or MQTT port forwarding

### Via Emoncms.org API

+ Does not require emonPi
+ OpenEVSE can be on a different network / geographic location
- Depends on web connection and Emoncms.org connectivity
- Slightly higher latency

## Control signal

From RasPi to OpenEVSE

### Via direct serial

+ most reliable
+ does not require ESP wifi module
+ Not dependant on local Wifi network
+ Most secure
+ Easiest setup
- Requires emonPi or another RasPi running nodeRED within a in close proximity of OpenEVSE
- Not great for interoperability, no other device can send control signals
- Control can not easily be commanded remotely e.g. checking state and start / stop charging while out of the house
- Requires modification to openEVSE enclosure to allow serial cable out which could impact waterproofness

### Via HTTP (over local network)

+ OpenEVSE can be in different location to emonPi
+ Does not require extra cable
- Requires OpenEVSE ESP WiFi module with default (stable) firmware
- Requires knowledge of OpenEVSE IP address which can change since default is DHCP
- Depends on local network connectivity
- Not as secure as direct serial
+ / - Remote (from the web) control is possible but not recommended without authentication

### Via MQTT

+ OpenEVSE can be in different location to emonPi
+ Does not require extra cable
+ Don't need to know OpenEVSE IP address
+ Better interoperability: devices / home automation platforms could also easily send and receive the control messages e.g. OpenHAB, HomeAssistant,
+ More secure than HTTP since MQTT is authenticated
+ Remote (from the web) control is possible, TSL MQTT
recommended
+ Slightly more setup since MQTT server details needs to be entered into openEVSE
- Requires MQTT server, emonPi has one built in
- Requires OpenEVSE ESP WiFi module with beta (dev) firmware

