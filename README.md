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

### Tested on

The project has been tested working on:

* Docker 18.06-1-ce, Docker Compose 1.23.2 on Linux Mint 18.3
* Docker 18.09-1, Docker Compose 1.23.2 on Debian 9.1
* Docker 18.09.0, Docker Compose 1.23.2 on Raspbian (see Raspberry Pi section below)

### Install docker

Install `docker` for your platform by following the steps in https://docs.docker.com/engine/installation/

You might need to add your user to the docker group:

```
$ sudo usermod -a -G docker $USER
```

And check it is working:

```
$ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
1b930d010525: Pull complete 
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

```

### Install docker-compose

Install `docker-compose` for your platform by following the steps in https://docs.docker.com/compose/install/

Check you have it correctly installed:

```
$ docker-compose --version
docker-compose version 1.23.2, build 1110ad01
```

### Checkout the repo

Clone this repo to your computer:

```
$ sudo apt install git
$ git clone https://github.com/ttncat/ttncat-docker-compose.git
$ cd ttncat-docker-compose
```

### Before running the project

Before running the docker-compose project please note it exposes a series of ports:

* Mosquitto on 1883
* InfluxDB on 8086
* Node-RED on 1880
* Grafana on 3000

If you have services running in your host on these ports the docker-compose will fail. You can either stop the services in your host or change the port these services in the containers are mapped editing the `docker-compose.yml` file. For instance, if you want the mosquitto instance in the container to be avaible on port 2883 (instead of default 1883) you just have to change the mosquitto service in the `docker-compose.yml` to this (notice the `port` section):

```
  mosquitto:
    container_name: ttncat_mosquitto
    build: ./mosquitto
    networks:
      - ttncat
    ports:
      - "2883:1883"
    volumes:
      - mosquitto_data:/mosquitto/data 
      - mosquitto_config:/mosquitto/config
      - mosquitto_log:/mosquitto/log
    restart: on-failure
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

This will show the complete boot process for each container (the exact output will be different the first time it is run). You can also run them detached with:

```
$ docker-compose up -d
```

Any of the two will create 4 docker containers (one for each service in Mosquitto, Node-RED, InfluxDB and Grafana, see them by typing 

```
$ docker ps
CONTAINER ID        IMAGE                             COMMAND                  CREATED             STATUS              PORTS                    NAMES
348ac9fd5c15        ttncat-docker-compose_nodered     "npm start -- --user…"   15 seconds ago      Up 13 seconds       0.0.0.0:1880->1880/tcp   ttncat_nodered
3f8ec1431050        ttncat-docker-compose_mosquitto   "/docker-entrypoint.…"   15 seconds ago      Up 13 seconds       0.0.0.0:1883->1883/tcp   ttncat_mosquitto
78c31702cdf2        influxdb                          "/entrypoint.sh infl…"   15 seconds ago      Up 13 seconds       0.0.0.0:8086->8086/tcp   ttncat_influxdb
accbe4de057d        grafana/grafana                   "/run.sh"                15 seconds ago      Up 12 seconds       0.0.0.0:3000->3000/tcp   ttncat_grafana
```

The will all share the same bridge network so they will be able to see each other using the service name as domain name (mosquitto, influxdb, nodered, grafana). It will also create several named volumes to persist the data on your host machine. You can check this volumes by typing 

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

### Update the project

If the repository gets updated and you want to have the latest changes you can do it without losing any info in your persisted volumes. Only stop, pull the changes and start again the containers:

```
$ docker-compose stop
$ git pull
$ docker-compose pull
$ docker-compose up -d
```

## Usage

### Mosquitto

Mosquitto will be listening on port 1883 of your host machine. You can use any MQTT client to connect to it. Using debian and `mosquitto-client` utilities is as simple as:

```
$ sudo apt install mosquitto-clients
$ mosquitto_sub -v -t /mytopic &
$ mosquitto_pub -t /mytopic -m hello
```

Mosquitto will create 3 volumes (mapped folders) in your host drive, one with the configuration, one with the retained data and a third one with the logs. 

### InfluxDB

IncluxDB will be listening on port 8086 of your host machine. You will probably want to create a database and maybe define some retention policies and continuous queries. Check the documentation for details but a first approach from debian would be:

```
$ sudo apt install incluxdb-client
$ influx -precision "rfc3339"
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

## Raspberry Pi 3

### Install docker and docker-compose

The instructions to install them on a Raspbian are taken from these two sources:
* https://howchoo.com/g/nmrlzmq1ymn/how-to-install-docker-on-your-raspberry-pi
* https://withblue.ink/2017/12/31/yes-you-can-run-docker-on-raspbian.html

```
$ sudo su
$ curl -sSL https://get.docker.com | sh
$ apt update
$ apt install -y python python-pip libssl-dev libffi-dev
$ pip install docker-compose
$ exit
$ sudo usermod -a -G docker $USER
$ sudo reboot
```

### Running docker-compose

The file for the Rasberry Pi is different from the default one. To run `docker-compose` you have to manually define the file:

```
docker-compose -f docker-compose-rpi.yml up
```

From here on you can use the exact same commands as above to bring it up/down, start and stop them, with no need to specify the file again.

Please note this is a work in progress. My impression is that the docker-compose commands are too slow on the Raspberry Pi. Let me know your impressions.

## Special Workshop configuration

We have added a special configuration for workshops where you can use the `scale` option to create multiple instances on NodeRED and Grafana for your workshops. The `docker-compose-workshop.yml` configures up to 10 instances on Grafana (ports 3000 to 3009) and NodeRED (port 1880 to 1889). To avoid a clash with **Mosquitto** (originally on port 1883) this **has been moved to 2883**.

```
$ docker-compose -f docker-compose-workshop.yml up -d --scale nodered=10 --scale grafana=10
Creating network "ttncat-docker-compose_ttncat" with the default driver
WARNING: The "nodered" service specifies a port on the host. If multiple containers for this service are created on a single host, the port will clash.
Creating ttncat-docker-compose_mosquitto_1 ... 
Creating ttncat-docker-compose_mosquitto_1 ... done
Creating ttncat-docker-compose_influxdb_1  ... done
Creating ttncat-docker-compose_nodered_1   ... done
Creating ttncat-docker-compose_nodered_2   ... done
Creating ttncat-docker-compose_nodered_3   ... done
Creating ttncat-docker-compose_nodered_4   ... done
Creating ttncat-docker-compose_nodered_5   ... done
Creating ttncat-docker-compose_nodered_6   ... done
Creating ttncat-docker-compose_nodered_7   ... done
Creating ttncat-docker-compose_nodered_8   ... done
Creating ttncat-docker-compose_nodered_9   ... done
Creating ttncat-docker-compose_nodered_10  ... done
Creating ttncat-docker-compose_grafana_1   ... done
Creating ttncat-docker-compose_grafana_2   ... done
Creating ttncat-docker-compose_grafana_3   ... done
Creating ttncat-docker-compose_grafana_4   ... done
Creating ttncat-docker-compose_grafana_5   ... done
Creating ttncat-docker-compose_grafana_6   ... done
Creating ttncat-docker-compose_grafana_7   ... done
Creating ttncat-docker-compose_grafana_8   ... done
Creating ttncat-docker-compose_grafana_9   ... done
Creating ttncat-docker-compose_grafana_10  ... done
$ docker ps
CONTAINER ID        IMAGE                             COMMAND                  CREATED             STATUS              PORTS                    NAMES
1edaa7e752ae        ttncat-docker-compose_nodered     "docker-entrypoint.s…"   15 seconds ago      Up 4 seconds        0.0.0.0:1888->1880/tcp   ttncat-docker-compose_nodered_5
699bea6f4db0        grafana/grafana                   "/run.sh"                15 seconds ago      Up 3 seconds        0.0.0.0:3009->3000/tcp   ttncat-docker-compose_grafana_5
e2600858afa3        grafana/grafana                   "/run.sh"                15 seconds ago      Up 2 seconds        0.0.0.0:3008->3000/tcp   ttncat-docker-compose_grafana_3
69f39399fc5f        ttncat-docker-compose_nodered     "docker-entrypoint.s…"   15 seconds ago      Up 3 seconds        0.0.0.0:1887->1880/tcp   ttncat-docker-compose_nodered_3
471f54070d69        grafana/grafana                   "/run.sh"                15 seconds ago      Up 5 seconds        0.0.0.0:3006->3000/tcp   ttncat-docker-compose_grafana_4
2508bd483e08        grafana/grafana                   "/run.sh"                15 seconds ago      Up 3 seconds        0.0.0.0:3007->3000/tcp   ttncat-docker-compose_grafana_10
7374fa04351a        ttncat-docker-compose_nodered     "docker-entrypoint.s…"   15 seconds ago      Up 3 seconds        0.0.0.0:1886->1880/tcp   ttncat-docker-compose_nodered_9
713372ec973a        ttncat-docker-compose_nodered     "docker-entrypoint.s…"   15 seconds ago      Up 5 seconds        0.0.0.0:1885->1880/tcp   ttncat-docker-compose_nodered_6
fedd2e1b775c        ttncat-docker-compose_nodered     "docker-entrypoint.s…"   15 seconds ago      Up 6 seconds        0.0.0.0:1884->1880/tcp   ttncat-docker-compose_nodered_2
6c2923e51c5e        ttncat-docker-compose_nodered     "docker-entrypoint.s…"   15 seconds ago      Up 3 seconds        0.0.0.0:1889->1880/tcp   ttncat-docker-compose_nodered_10
50a490250e7e        ttncat-docker-compose_nodered     "docker-entrypoint.s…"   15 seconds ago      Up 6 seconds        0.0.0.0:1883->1880/tcp   ttncat-docker-compose_nodered_7
0a343cb2abdf        ttncat-docker-compose_nodered     "docker-entrypoint.s…"   15 seconds ago      Up 7 seconds        0.0.0.0:1882->1880/tcp   ttncat-docker-compose_nodered_4
6f1082fc7cc7        grafana/grafana                   "/run.sh"                15 seconds ago      Up 8 seconds        0.0.0.0:3004->3000/tcp   ttncat-docker-compose_grafana_8
fe856c4ddf9b        grafana/grafana                   "/run.sh"                15 seconds ago      Up 7 seconds        0.0.0.0:3002->3000/tcp   ttncat-docker-compose_grafana_2
be4e831090f7        grafana/grafana                   "/run.sh"                15 seconds ago      Up 7 seconds        0.0.0.0:3005->3000/tcp   ttncat-docker-compose_grafana_1
5dfc7db81dff        ttncat-docker-compose_nodered     "docker-entrypoint.s…"   15 seconds ago      Up 8 seconds        0.0.0.0:1881->1880/tcp   ttncat-docker-compose_nodered_1
563ef222ec9c        grafana/grafana                   "/run.sh"                15 seconds ago      Up 10 seconds       0.0.0.0:3000->3000/tcp   ttncat-docker-compose_grafana_7
d3ce4cfd340b        grafana/grafana                   "/run.sh"                15 seconds ago      Up 7 seconds        0.0.0.0:3003->3000/tcp   ttncat-docker-compose_grafana_9
7cca79dcffd1        ttncat-docker-compose_nodered     "docker-entrypoint.s…"   15 seconds ago      Up 11 seconds       0.0.0.0:1880->1880/tcp   ttncat-docker-compose_nodered_8
90cf63cf170f        grafana/grafana                   "/run.sh"                15 seconds ago      Up 11 seconds       0.0.0.0:3001->3000/tcp   ttncat-docker-compose_grafana_6
76d40efb2e10        influxdb                          "/entrypoint.sh infl…"   15 seconds ago      Up 12 seconds       0.0.0.0:8086->8086/tcp   ttncat-docker-compose_influxdb_1
b3eae41889de        ttncat-docker-compose_mosquitto   "/docker-entrypoint.…"   15 seconds ago      Up 11 seconds       0.0.0.0:2883->1883/tcp   ttncat-docker-compose_mosquitto_1
```

Notice docker does not enforce the ports for the instances, so if you are creating 2 nodered instances they can be ports 1884 and 1885, for instance. If you want to enforce them starting on 1880 you must restart the docker service first.


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
