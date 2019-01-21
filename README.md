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
$ sudo apt install git
$ git clone https://github.com/ttncat/ttncat-docker-compose.git
$ cd ttncat-docker-compose
```

### Run the project

Go to the working copy folder and bring up the services defined in the docker-compose project:

```
$ docker-compose up
Creating network "ttncat-docker-compose_ttncat" with the default driver
Creating ttncat_nodered   ... done
Creating ttncat_grafana   ... done
Creating ttncat_influxdb  ... done
Creating ttncat_mosquitto ... done
Attaching to ttncat_influxdb, ttncat_grafana, ttncat_nodered, ttncat_mosquitto
ttncat_influxdb | ts=2019-01-21T20:48:45.840784Z lvl=info msg="InfluxDB starting" log_id=0D8YbN40000 version=1.7.3 branch=1.7 commit=698dbc789aff13c2678357a6b93ff73dd7136571
ttncat_influxdb | ts=2019-01-21T20:48:45.840809Z lvl=info msg="Go runtime" log_id=0D8YbN40000 version=go1.11 maxprocs=4
ttncat_influxdb | ts=2019-01-21T20:48:45.943452Z lvl=info msg="Using data dir" log_id=0D8YbN40000 service=store path=/var/lib/influxdb/data
ttncat_influxdb | ts=2019-01-21T20:48:45.943509Z lvl=info msg="Compaction settings" log_id=0D8YbN40000 service=store max_concurrent_compactions=2 throughput_bytes_per_second=50331648 throughput_bytes_per_second_burst=50331648
ttncat_influxdb | ts=2019-01-21T20:48:45.943527Z lvl=info msg="Open store (start)" log_id=0D8YbN40000 service=store trace_id=0D8YbNTl000 op_name=tsdb_open op_event=start
ttncat_influxdb | ts=2019-01-21T20:48:45.945046Z lvl=info msg="Reading file" log_id=0D8YbN40000 engine=tsm1 service=cacheloader path=/var/lib/influxdb/wal/_internal/monitor/1/_00001.wal size=395014
ttncat_grafana | installing grafana-clock-panel @ 1.0.2
ttncat_grafana | from url: https://grafana.com/api/plugins/grafana-clock-panel/versions/1.0.2/download
ttncat_grafana | into: /var/lib/grafana/plugins
ttncat_grafana | 
...
```

This will show the complete boot process for each container. You can also run them detached with:

```
$ docker-compose up -d
```

Any of the two will create 4 docker containers (one for each service in Mosquitto, Node-RED, InfluxDB and Grafana, see them by typing `docker ps`). The will all share the same bridge network so they will be able to see each other using the service name as domain name (mosquitto, influxdb, nodered, grafana). It will also create several named volumes to persist the data on your host machine. You can check this volumes by typing 

```
$ docker volume ls
DRIVER              VOLUME NAME
local               ttncat-docker-compose_grafana
local               ttncat-docker-compose_influxdb
local               ttncat-docker-compose_mosquitto_config
local               ttncat-docker-compose_mosquitto_data
local               ttncat-docker-compose_mosquitto_log
local               ttncat-docker-compose_nodered
```

You can instect any of the volumes, for instance to know their location in your drive:

```
$ docker volume inspect ttncat-docker-compose_nodered
[
    {
        "CreatedAt": "2019-01-21T21:25:25+01:00",
        "Driver": "local",
        "Labels": {
            "com.docker.compose.project": "ttncat-docker-compose",
            "com.docker.compose.version": "1.23.2",
            "com.docker.compose.volume": "nodered"
        },
        "Mountpoint": "/var/lib/docker/volumes/ttncat-docker-compose_nodered/_data",
        "Name": "ttncat-docker-compose_nodered",
        "Options": null,
        "Scope": "local"
    }
]
```

Once running you can stop the containers any moment with:

```
$ docker-compose stop
Stopping ttncat_mosquitto ... done
Stopping ttncat_grafana   ... done
Stopping ttncat_nodered   ... done
Stopping ttncat_influxdb  ... done

```

and start them again with 

```
$ docker-compose start
Starting mosquitto ... done
Starting nodered   ... done
Starting influxdb  ... done
Starting grafana   ... done
```

Both from the working copy root folder, where the `docker-composer.yml` file is. To remove the project from your drive bring it down (they must be first stopped): 

```
$ docker-compose down
Removing ttncat_mosquitto ... done
Removing ttncat_grafana   ... done
Removing ttncat_nodered   ... done
Removing ttncat_influxdb  ... done
Removing network ttncat-docker-compose_ttncat

```

and, if you also want to delete the persisted files: 

```
$ docker volume prune
WARNING! This will remove all local volumes not used by at least one container.
Are you sure you want to continue? [y/N] y
Deleted Volumes:
ttncat-docker-compose_mosquitto_log
ttncat-docker-compose_nodered
ttncat-docker-compose_influxdb
ttncat-docker-compose_grafana
ttncat-docker-compose_mosquitto_data
ttncat-docker-compose_mosquitto_config

Total reclaimed space: 41.07MB

```

The project will also create a couple of custom images for Node-RED and Mosquitto you can remove them by 

```
$ docker image rm ttncat-docker-compose_nodered ttncat-docker-compose_mosquitto
Untagged: ttncat-docker-compose_nodered:latest
Deleted: sha256:4b6fd943669c0dc0e3a21d41e78f6ea642486cd6714eac8b4d809859b7a0821e
Deleted: sha256:3a30c988493bf511312d202fcea6f92e99fc4cbb0b30aec76bd6ca09b87876e2
Untagged: ttncat-docker-compose_mosquitto:latest
Deleted: sha256:8d73e7d9b0f07aaab96261e11b78ae5e1507bc2ae12f7bad99dd83a1381fa7d8
Deleted: sha256:eeccd62f9fb9ac9ba3a56a518815c0e8298790e1cefd2caf362331af8dbec370
```

## Usage

### Mosquitto

Mosquitto will be listening on port 1883 of your host machine. You can use any MQTT client to connect to it. Using debian and `mosquitto-client` utilities is as simple as:

```
$ sudo apt install mosquitto-client
$ mosquitto_sub -v -t /mytopic &
$ mosquitto_pub -t /mytopic -m hello
```

Mosquitto will create 3 volumes (mapped folders) in your host drive, one with the configuration, one with the retained data and a third one with the logs. 

### InfluxDB

IncluxDB will be listening on port 8086 of your host machine. You will probably want to create a database and maybe define some retention policies and continuous queries. Check the documentation for details but a first approach from debian would be:

```
$ sudo apt install incluxdb-client
influx -precision "rfc3339"
Visit https://enterprise.influxdata.com to register for updates, InfluxDB server management, and monitoring.
Connected to http://localhost:8086 version 1.7.3
InfluxDB shell 0.10.0
> create database ttncat
> [Ctrl+D]
$ 
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
