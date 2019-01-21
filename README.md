# TTN Catalunya Data Analysis Environment

This repository contains a docker-compose project based around [Mosquitto](https://mosquitto.org/), [Node-RED](https://nodered.org/), [InfluxDB](https://www.influxdata.com/) and [Grafana](https://grafana.com/). The goal is to have an easy-to-deploy test enviroment for our workshops, and thus, it is not meant for production environment.

[![license](https://img.shields.io/github/license/ttncat/ttncat-docker-compose.svg)](LICENSE)
[![twitter](https://img.shields.io/twitter/follow/ttncat.svg?style=social)](https://twitter.com/intent/follow?screen_name=ttncat)

![TTN.cat](./images/ttncat.logo.small.png)

## Installation

In order to run the project there are a number of steps you have to do. These are quite general steps when working with `git` and `docker` and we will try to go throu them. But if you have no background on `git` or `docker` we recommend you to take a look at these tutorials.

* [Docker Get Started](https://docs.docker.com/get-started/)
* [Docker Compose Getting Started](https://docs.docker.com/compose/gettingstarted/)
* [GIT Getting Started](https://git-scm.com/book/en/v1/Getting-Started)

The instructions below will asume you are working from the command line.

### Install docker

Install `docker` for your platform by following the steps in https://docs.docker.com/engine/installation/

### Install docker-compose

Install `docker-compose` for your platform by following the steps in https://docs.docker.com/compose/install/

### Checkout the repo

Clone this repo to your computer:

```
git clone https://github.com/ttncat/ttncat-docker-compose.git
```

### Run the project

Go to the working copy folder and bring up the services defined in the docker-compose project:

```
cd ttncat-docker-compose
docker-compose up
```

This will create 4 docker containers (one for each service in Mosquitto, Node-RED, InfluxDB and Grafana, see them by typing `docker ps`). The will all share the same bridge network so they will be able to see each other using the service name as domain name (mosquitto, influxdb, nodered, grafana). It will also create several named volumes to persist the data on your host machine. You can check this volumes by typing `docker volume ls` and inspect them: `docker volume inspect ttncat-docker-compose_nodered`.

Once running you can stop the containers any moment by `docker-compose stop` and start them again with `docker-compose start`, everything from the working copy root folder, where the `docker-composer.yml` file is. To remove the project from your drive bring it down: `docker-compose down` and, if you also want to delete the persisted files: `docker volume prune`. The project will also create a couple of custom images for Node-RED and Mosquitto you can remove them by `docker images rm ttncat-docker-compose_nodered ttncat-docker-compose_mosquitto`.

## Usage

### Mosquitto

Mosquitto will be listening on port 1883 of your host machine. You can use any MQTT client to connect to it. Using debian and `mosquitto-client` utilities is as simple as:

```
sudo apt install mosquitto-client
mosquitto_sub -v -t /mytopic &
mosquitto_pub -t /mytopic -m hello
```

### InfluxDB

IncluxDB will be listening on port 8086 of your host machine. You will probably want to create a database and maybe define some retention policies and continuous queries. Check the documentation for details but a first approach from debian would be:

```
sudo apt install incluxdb-client
influx -precision "rfc3339"
Visit https://enterprise.influxdata.com to register for updates, InfluxDB server management, and monitoring.
Connected to http://localhost:8086 version 1.7.3
InfluxDB shell 0.10.0
> create database ttncat
> [Ctrl+D]
```

### Node-RED

Node-RED will be listening on port 1880 of your host manchine. You can head over to the web interface by clicking here: http://localhost:1880. A number of additional nodes have been installed by default: `node-red-dashboard`, `node-red-contrib-blynk-ws`, `node-red-contrib-influxdb`, `node-red-contrib-schedex`, `node-red-contrib-telegrambot`, `node-red-contrib-timeout`, `node-red-contrib-ttn`, `node-red-contrib-yield` and `node-red-node-pushover`.

### Grafana

Grafana is also listening on port 3000 of your host machine, just click http://localhost:3000. The first time it will ask you for a user (`admin`) and password (`admin`) and immeditely will ask you to change the admin password.

## License

Copyright (C) 2019 by Xose Pérez (@xoseperez)
for The Things Network Catalunya

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.
