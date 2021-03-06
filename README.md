

# WagoNorge B.Sc 2021 OpenBridge MTP
This repository contains a revised version and a detailed installation guide of a bachelor written for Wago Norge the spring of 2021. 

Credits to the original authors:
   - Pål Kristian Ofstad
   - Eskil G. Gaustad
   - Ørjan Pettersen
   - Frode Kvalnes


Original repository: https://github.com/Wago-Bachelor/Bachelorproject-2021

Revived by:
   - Victor Shaw Melhuus 

## Info
The repository contains:
   - A e!COCKPIT program that simulates the dynamics of ship. And a visialization with simple controlls. 
   - A Docker-image containing:
      - A webserver hosting a HMI that uses OpenBridge elements. 
      - An OPCUA-client that uses the Node-OPCUA library.

Programming languages:
   - CoDeSys
   - Javascript
   - HTML 5

# Installation

## Prerequisites
- An SSH client (PuTTY for example)
- Internet connection on the device


## Setup in Web Based Management
### Settings
1. Configuration -> Networking -> TCP/IP Cofiguration
      - IP Source: Static IP
      - Set an IP address and Subnet Mask that is accessible and corresponds with the local network router. 
      - Manually assign a DNS server. e.g: 8.8.8.8 (can be skipped if a DNS-server routing is setup in the router)
2. ... -> ... -> Routing
      - IP Forwarding through multiple interfaces: Enabled
      - Default Static Routes. Set it up with the router. 
3. ... -> PLC runtine. PLC Runtime Version: e!RUNTIME. 
4. ... -> Ports and Services -> PLC Runtime Services. e!RUNTIME: Webserver Enabled. 
5. ... -> Browser Settings -> Favourites 
      - Ad the HMI webserver: http://localhost:9999
6. Fieldbus -> OPCUA -> Configuration
      - Service Enabled: yes
      - Ctrl Configuration name (e.g.): WAGO OPCUA TP600.
      - Unlimited Anonynous Access: yes
      - Security Policy - None: yes
      - Trust all clients: yes



### Installing docker (required)

Follow this guide: https://github.com/WAGO/docker-ipk

## Installing the e!COCKPIT Program
1. Download the "OpenBridgeSimu_TP600_08072021.export"-file within the e!COCKPIT-folder.
2. Import the .export-file to your e!c project. 
3. Within Library Manager, make sure that these libraries are installed successfully:
      - CmpBitmapPool
      - Standard
      - util
      - VisuDialogs
      - WagoAppBuilding
      - WagoAppMath
      - WagoAppRTU
      - WagoSolMTP
4. Rebuild, address any errors, connect and download to device once it rebuilds without errors. 

## Installing and setting up the docker container (OpenBridge HMI)

### Install
Connect to the device using an SSH-client. 
 1. Pull and install the image from the Docker-hub.
 ```
 docker pull wagonorge/openbridgehmi
 ```
 2. Initiate the container. 
  ```
 docker run -d --restart always -p 9999:9999 --name hmi wagonorge/openbridgehmi
 ```
### Setup
After installing the image and initiating the container some settings will propably need to be adjustet for the OPCUA-client to communicate with OPCUA-server correctly. 
1. access the container by running the following command
```
docker exec -ti hmi /bin/sh
```
2. Open the OPC-client Javascript file. 
```
vi opc.js
```
Usefull commands:
 - Press "i" to edit.
 - Press "ESC" to stop editing. 
 - Type ":wq" to save and close. 
 - Type ":q" to close. 
 
3. Make sure "endpointUrl" matches the URL of the OPCUA-server. E.g.: 
```
var endpointUrl = "opc.tcp://192.168.10.17:4840";
```
4. Make sure "opcuaAppID" matches the OPCUA Ctr Configuration Name (found in the application's Web Based Managment. Fieldbus -> OPCUA -> Configuration). E.g.:
```
var opcuaAppID = "WAGO OPCUA TP600";
```
5. Save and close the file. 
6. Exit the docker container
```
return
```
7. Restart the container
```
docker restart hmi
```
8. Verify that it runs correctly by reading the log or observing the OpenBridge HMI. 
```
docker attach hmi
```
# Use
The OpenBridge HMI is accessible through: [device-ip]:9999

The visualization that lets you controll the "ship" is accissible through: [device-ip]/webvisu


# Changelog

## 08.07.2021: Changes from the original bachelor program
### e!Cockpit program: 
- Controller changed to TP600
- Minor/major bug-fixes throughout. 
- Increased rate of calculations.
- New WebVisu for controlling the simulated ship, as analog input has bin removed for use with the TP600.
- Improved ship simulation.
- A P-regulated Autopilot and simple azimuth controll. 
### OPC-client (opc.js)
- Added a variable for Ctrl Configuration name, so it only has to be changed one place in the code. 
- Configured for TP 600 panel with IP 192.168.10.17

