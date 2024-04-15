# Basic Monitoring system for linux hosts on local network

Monitoring system based on top of Grafana, Prometheus and node_exporter. It is designed to provide linmux based monitoring on your home network. Therefore, security hasn't been a consideration.

## Installation

The system consists of two parts. The node_exporter is a binary script that should run on each host that needs monitoring. The script publishes system metrics from the machine it is running on to an http endpoit. The recommendation is to run it on the bare-metal machine. If you run it in Docker, more configuration is necessary in order for it not to monitor the container itself.

## Install the script

### Copy the script somehwere accessible

A good place to put the script is in `/usr/local/bin/`. Don't forget to give everyone read and execute permissions on ths script.

    cp ./node_exporter/node_exporter /usr/local/bin/node_exporter
    chmod 755 /usr/local/bin/node_exporter

In order to modify `/usr/local/bin` you probably need `sudo` access (depending on the user you are doing this from)

### Add the scipt to systemd

You will want the script being managed by **systemd** so that it's running every time the machine is online. In order to do that, copy the **node_exporter.service** file into the **systemd** service folder.

    cp ./node_exporter/node_exporter.service /etc/systemd/system/node_exporter.service

### Open ports

### Get IP of local machine

## Run containers

### Install Docker Enginer

### Docker compose

### (Optional) Add node exporter to container