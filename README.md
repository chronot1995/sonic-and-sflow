# Sonic and sFlow

## WORK IN PROGRESS ##

Internal link:

https://confluence.nvidia.com/display/NetworkingBU/Sonic+and+sFlow+Integration

### Description:

These are the steps to setup sFlow on Sonic using a docker container with the sFlow collector, Prometheus, and Grafana, on the OOB Management Server (oob-mgmt-server)

### Note:

The oob-mgmt-server requires 3Gb of RAM in order to run this demo. Work with the Cumulus Air team in the #net-cumulus-in-the-cloud NVIDIA Slack channel

###

1. Configure the "Sonic with Spines" demo using the following instructions:

https://gitlab.com/cumulus-consulting/goldenturtle/dc_configs_vxlan_evpnsym/-/tree/sonic_air/automation



Log into the Sonic switch (spine03 / spine04) and copy and paste the following:

sudo config sflow collector add oobserver 192.168.200.1
sudo config sflow polling-interval 30
sudo config sflow interface enable all
sudo config sflow enable

git clone this directory

Run the ansible playbook:



sudo docker-compose up -d

Within Air, create a new service to point to the Grafana port:

Service Name: Grafana
Interface: oob-mgmt-server:eth0
Service Type: http
Service Port: 3000

Click the "Submit" button and wait anywhere from 5-10 minutes.

To stop the demo:

docker-compose stop
docker-compose down --volumes

### Old School Way - Run 3 x Containers

sudo docker run --name sflow-rt -p 8008:8008 -p 6343:6343/udp -d sflow/prometheus

sudo docker run --name prometheus -v $PWD/prometheus.yml:/etc/prometheus/prometheus.yml -p 9090:9090 -d prom/prometheus

sudo docker run --name grafana  -p 3000:3000 -d grafana/grafana

### Errata

To remove the Sonic configuration:

sudo config sflow collector del oobserver 192.168.200.1
sudo config sflow interface disable all
sudo config sflow disable

Good to know:

sflow project:

https://blog.sflow.com/2020/02/sonic.html

Container - sflow / prometheus exporter:

https://hub.docker.com/r/sflow/prometheus

Container - Prometheus:

https://hub.docker.com/r/prom/prometheus

Container - Grafana:

https://hub.docker.com/r/grafana/grafana
