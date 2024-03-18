# docker_open5gs
Docker files to build and run open5gs in a docker

## Setup

Docker host machine

- Ubuntu 20.04

## Build and Execution Instructions

* Mandatory requirements:
	* [docker-ce](https://docs.docker.com/install/linux/docker-ce/ubuntu)
	* [docker-compose](https://docs.docker.com/compose)


Clone repository and build base docker image of open5gs, kamailio, ueransim

```
git clone https://github.com/emachona/containerized_5g
cd containerized_5g/base
docker build --no-cache --force-rm -t docker_open5gs .

cd ../ims_base
docker build --no-cache --force-rm -t docker_kamailio .

cd ../srslte
docker build --no-cache --force-rm -t docker_srslte .

cd ../ueransim
docker build --no-cache --force-rm -t docker_ueransim .
```

### Build and Run using docker-compose

```
cd ..
set -a
source .env
docker-compose build --no-cache
docker-compose up

# srsRAN ZMQ based setup
    # gNB
    docker-compose -f srsgnb_zmq.yaml up -d && docker container attach srsgnb_zmq
    # 5G UE
    docker-compose -f srsue_5g_zmq2.yaml up -d && docker container attach srsue2_5g_zmq
    
    #to keep the UE active and check tun interface
    docker exec -it srsue2_5g_zmq /bin/bash
    /# ping -I tun_srsue 8.8.8.8
```

## Configuration

For the quick run (eNB/gNB, CN in same docker network), edit only the following parameters in .env as per your setup

```
MCC
MNC
TEST_NETWORK --> Change this only if it clashes with the internal network at your home/office
DOCKER_HOST_IP --> This is the IP address of the host running your docker setup
SGWU_ADVERTISE_IP --> Change this to value of DOCKER_HOST_IP set above only if eNB/gNB is not running the same docker network/host
UPF_ADVERTISE_IP --> Change this to value of DOCKER_HOST_IP set above only if eNB/gNB is not running the same docker network/host
```

## Register a UE information

Open (http://<DOCKER_HOST_IP>:3000) in a web browser, where <DOCKER_HOST_IP> is the IP of the machine/VM running the open5gs containers. Login with following credentials
```
Username : admin
Password : 1423
```

Using Web UI, add a subscriber with the credentials under the USIM configuration in ue2_5g_zmq.conf 


## Not supported
- IPv6 usage in Docker

