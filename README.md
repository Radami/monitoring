# Basic Monitoring system for linux hosts on local network

Monitoring system based on top of Grafana, Prometheus and node_exporter. It is designed to provide linmux based monitoring on your home network. Therefore, security hasn't been a consideration.

## Installation

The system consists of two parts. The node_exporter is a binary script that should run on each host that needs monitoring. The script publishes system metrics from the machine it is running on to an http endpoit. The recommendation is to run it on the bare-metal machine. If you run it in Docker, more configuration is necessary in order for it not to monitor the container itself.

## Install the script

Depending on what user you run this as and how it is setup, you might need to run these commands with `sudo`. It is recommended to run everything as separate users to insure isolation and not run anything as `root`.

### Copy the script somehwere accessible

A good place to put the script is in `/usr/local/bin/`. Don't forget to give everyone read and execute permissions on ths script.

    cp ./node_exporter/node_exporter /usr/local/bin/node_exporter
    chmod 755 /usr/local/bin/node_exporter

### Add the scipt to systemd

You will want the script being managed by **systemd** so that it's running every time the machine is online. In order to do that, copy the **node_exporter.service** file into the **systemd** service folder.

    cp ./node_exporter/node_exporter.service /etc/systemd/system/node_exporter.service

### Open ports

The node exporter publishes metrics on `localhost:9100`. You can test it's running successfully by starting it and accessing the url from your browser. In order to make the metrics accessible from the outside, you might need to open the port on your machine (and possibly your router). To open a port on a Ubuntu Server machine:

    ufw allow 9100

### Get IP of local machine

You will need to IP of the local machine to add a Prometheus confifguration for it. Once you have it, add or modify the `scrape_config` entry in `prometheus/prometheus.yml`. A special case is when you want to access the same machine that Dicker is running on. In that case, use `host.docker.internal` which Docker automatically resolves to the ip address of the host runnign the container.

## Run containers

The compose file will start a Prometheus instance and a Grafana instance running inside the same container. 

### Install Docker

You will need to have the Docker Engine and Docker compose set up and running on the machine. You can check you have everything you need by running the following commands and making sure they return successfully.

    docker --version
    docker compose --version

### Docker compose

In order to test it out, in the same folder as `compose.yaml` run:

    docker compose up

If everything started successfully you should be able to access Prometheus at `localhost:9090`. If you can access the Prometheus dashboard, under the **Targets** menu, you should see all available scrape configs and their status. If any of the are marked as **Down**, Prometheus will also display the failure reason.

Grafana can be accessed at `localhost:3000`. The default login/password is **admin/admin**. You can change that in `grafana/grafana.ini`. In Grafana, under **Data Sources** you can see the automatically configured Prometheus instance. If everythign is working, you should see a success message when clicking the **Test** button at the bottom fo the page.

### Set up Docker as a service

Similarly to how we did with node_exporter, we want to set up the monitoring containers to be always running. To do that, copy the `monitoring.service` file into your **systemd** folder:

    cp monitoring.service /etc/systemd/system/monitoring.service

## Wrap up

If you want to test everything is running, you need to reload the systemd configuration and enable the 2 services:

    systemctl daemon reload
    systemctl enable node_exporter
    systemctl start node_exporter
    systemctl enable monitoring
    systemctl start monitoring

If **systemd** is set up correctly, you should be able to check the status of both services with the `systemctl status` command.