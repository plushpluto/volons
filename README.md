<p align='center'>
    <a href='https://www.npmjs.com/package/volons'>
        <img src='https://img.shields.io/badge/Release-ALPHA-orange.svg'>
        <img src='https://img.shields.io/npm/v/volons.svg?style=round-square'>
    </a>
    <img src='https://img.shields.io/github/license/volons/volons.svg'>
    <img src='https://img.shields.io/github/last-commit/volons/volons.svg'>
</p>

__Volons is still early access alpha software, use at your own risk.__


![volons](https://github.com/volons/volons/raw/master/images/volons.jpg)

Open Source Internet of Drones Platform


```js
const { Hive } = require('volons');

(async function main() {
    let hive = new Hive( 'ws://localhost:8656/admin' );
    await hive.connect();
    let vehicle = hive.vehicle( 'dev' );
    await vehicle.connect();
    await vehicle.takeoff();
})();
```

## Running local stack
```
$ npm i -g volons
$ volons start
```

## Installation
```
$ npm i volons
```

## Volons: Open Source Internet of Drones Platform

Volons is composed of 4 leading opensource software. You can download the source code, install and run it manually.
: __Coding and connecting mission for UAVs__.

__Main components__:
1. **Vehicles** ( arducopter / DJI ) are components directly connected to the flight controller
1. The **Hive** is the backend application managing security and authorizations
1. The **Monitor** is a web application using Volons Javascript SDK to watch and control flights in real-time
1. **Volons Javascript SDK** is the part you are going to interact with to code dynamic missions and triggers

Volons' API is object-oriented and easy to install, use and run.

![Volons software architechure](https://github.com/volons/volons/raw/master/images/archi.png)

Let's start. You don't need to know everything about Volons at this point.
You can get more information by browsing [Volons' Github repositories](https://github.com/volons/) and [Volons WIKI](https://github.com/volons/volons/wiki) (Work in process).

This document presents a step-by-step tutorial with short explanations on how to start coding for drones.

- [Install and start Volons on your computer](#install-and-start-local-volons-developer-stack)
- [Docker Images](#docker-images)
- [Use Volons node module to write code for drones with nodejs](#use-volons-node-module-to-write-code-for-drones-with-nodejs)
- [Run and Monitor Mission](#run-and-monitor-mission)

You are familiar with **Nodejs** and **Docker**! So, you are going to install your development platform then takeoff and monitor a simulator in ~5 minutes.


## Install And Start Local Volons Developer Stack

### Requirements
You need standard third-party software to run Volons.

- Node.js with npm [https://nodejs.org](https://nodejs.org)
- Docker [https://www.docker.com/](https://www.docker.com/)
- Docker Compose [https://docs.docker.com/compose/install/](https://docs.docker.com/compose/install/)
- Git [https://git-scm.com/](https://git-scm.com/)

Its are probably already installed. If not, you should do it before to continue.

### Volons Command Line Interface
Volons node module comes with a command line interface (CLI). To install the Volons CLI run:

    $ npm i -g volons

This software helps you to start Volons' local platform.

_Volons CLI arguments_:
- `volons start`: Start volons containers (Hive, simulator, vehicle-mavlinl)
- `volons stop`: Stop volons running containers
- `volons ps`: Print information about running containers (IP address, ports, name, env...)
- `volons help`: Print help message How to use volons CLI
- `volons pull`: Download latest docker images from [Docker Hub](https://cloud.docker.com/u/volons/repository/list)

**How it works**

Four docker images compose the Volons local stack.
Volons CLI executes docker-compose and docker commands to handle volons' containers.
First, `volons start` downloads three images from Docker Hub.
Then, by running `docker-compose up` to start containers with docker.
If you are familiar with docker, you can download Volons' container images directly from [Docker hub](https://hub.docker.com/u/volons) and configure more complicated stack.
Add many hives and drones.
Docker hub builds Volons docker images automatically from source code.

![volons start](https://github.com/volons/volons/raw/master/images/volons-get-started.gif)

Your containers run altogether. Your local platform is ready, and you can connect with the SDK to start your first mission.

View running containers:
```
$ volons ps
```
This command displays information about Volons containers on your local machine.

You should see three running containers:
* *simulator*: [Dronekit-SITL](https://github.com/dronekit/dronekit-sitl) based docker image with custom Volons configuration
* *vehicle-mavlink*: the Volons adaptor for MAVLink vehicles (Arducupter)
* *hive*: the Fleet Management System to connect drone and SDK


## Use Volons node module to write code for drones with nodejs

Create a new folder to store your first program using Volons.

    $ mkdir first-project-with-volons
    $ cd first-project-with-volons

Init a Nodejs project and install Volons package. Copy Takeoff.js sample code from volons sample code folder.

    $ npm install volons --save
    $ cp ./node_modules/volons/examples/Takeoff.js ./Takeoff.js

Takeoff.js is a trival sample code.

    $ cat ./takeoff.js
```javascript
// This Volons script is a trivial script that
// - require Volons' library
// - connect Hive
// - connect aircraft
// - Send Takeoff command to drone

const { Hive } = require('volons');

async function main() {
    let hive = new Hive('ws://localhost:8656/admin');
    await hive.connect();

    console.log('Connected to hive');

    let vehicle = hive.vehicle('dev');
    await vehicle.connect();

    console.log('Connected to vehicle');

    console.log('takeoff');
    await vehicle.takeoff();
    console.log('Dev is flying');
}
```

> [Watch the one minute video on Youtube: From 'npm install' to monitor takeoff](https://youtu.be/lVQzDrSL2Lg)

__Get more examples__

Browse the [volons sample code directory](https://github.com/volons/volons/tree/master/examples) to get more examples.

    $ ls ./node_modules/volons/examples/

* Takeoff.js: Trivial mission to `Takeoff`. The one we've just talk about.
* Goto.js: Demo of `vehicle.goto()`.
* Events.js: Use `events` to connect drone's events, this demo prints drones telemetry.

## Docker Images
You can handle volons images and containers manually. 

```
$ docker pull volons/hive
$ docker run -i volons/hive
```

or docker-compose

```
$ docker-compose -f ./node_modules/volons/cli/volons-cli/volons-docker-compose.yml up
```

Volons CLI could help a lot: `$ volons start` is entirely equivalent to the docker-compose command line.


* [npmjs.org/volons](https://www.npmjs.com/package/volons)
* [Volons API Documentation](https://api.volons.fr)
* [github.com/volons/npm](https://api.volons.fr)


## Run and Monitor Mission

The `volons/simulator` docker image should connect to your local [QGroundControl](https://github.com/mavlink/qgroundcontrol).

_Coming soon_: Volons provides a web-based application using the Volons SDK to display your Aircraft on a map with telemetry data.

## Connect Aircrafts
You can find more information on our vehicle adaptors repositories:

* _Mavlink aircrafts_: [https://github.com/volons/vehicle-mavlink](https://github.com/volons/vehicle-mavlink)
* _DJI aircrafts_: [https://github.com/volons/vehicle-dji](https://github.com/volons/vehicle-dji)

Feel free to contact us to get help building software for your drones.


## MIT Licence
Copyright (c) 2019 Volons SAS, https://www.volons.fr


## Contact us
Volons is developed and maintained by Volons SAS, based in France, contact us!
![contact@volons.fr](https://img.shields.io/badge/email-contact%40volons.fr-informational.svg)


__Usefull links:__

* [Api documentation](https://github.com/volons/volons/wiki)
* [NPM Volons](https://npm.volons.fr)
* [Github](https://github.com/volons)
