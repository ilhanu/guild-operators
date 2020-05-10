# Docker Images 

In there here below section you can find a collection of procedure that will make you able to get you Cardano-* software safely running in docker containers using the Linux flavour of your choice.

***

## Port mapping

 Node Ports        |  Wallet Ports      | OS Flavors
------------       | -------------      | -------------
Node  (9000)       | Wallet (8090)      | Debian (`Debian_Dockerfile`)
Prometheus (13788) | Prometheus (13788) | Centos (`CentOS_Dockerfile`)
EKG (12781)        |                    |


The dockerfiles are located in ./files/ptn0/docker/ 

***
## How to run a Cardano Node with Docker

### Custom container with your own cfg.
```
docker run -itd  -p 9000:9000 -v <YourNetPath>:/ipc -v <YourCfgPath>:/configuration -v <YourKeysPath>:/keys  cardanocommunity/cardano-node:debian
```
* YourNetPath   - is where your node.socket resides (needs to be shared if you want to run a wallet too)
* YourCfgPath   - Your cfg files (configuration.yaml, topology.json, genesis.yaml)
* YourKeysPath  - Location of your private keys (kes.skey, vrf.skey, ops.cert, delagation.count)


### Custom container with ptn0 cfg.
```
docker run -itd  -p 6000:9000 -v <YourKeysPath>:/keys -v <YourNetPath>:/ipc  -e NETWORK=ptn0 cardanocommunity/cardano-node:debian
```
-----------
## How to build your own image from Dockerfile

- Requirements: [Docker](https://docs.docker.com/)

Instead of specifying a context, you can pass a single Dockerfile in the URL or pipe the file in via STDIN. 
Pipe the chosen Dockerfile (i.e. `Debian_CN_Dockerfile`) from STDIN:

### Cardano-Node ( mainnet / ptn0 / configuration)

With bash on Linux, you can run (in this example the Debian version):
```
wget https://raw.githubusercontent.com/cardano-community/guild-operators/docker/files/ptn0/docker/debian/Debian_CN_Dockerfile 
docker build -t cardanocommunity/cardano-node:debian - < Debian_CN_Dockerfile
docker run -itd --name CN --hostname CN -p 9000:9000 -it -e NETWORK=ptn0 cardanocommunity/cardano-node:debian 
```
>This last run command will run the container (Full passive Cardano Node) mapping the internal port of the container to your >IP `-p 9000:9000` while you can change the `-e NETWORK=ptn0` paramiter to `mainnet` or map your configuration directory >with the `-v` parameter as follow:
>NOTE: Only ptn0 is owrking at the moment.
```
docker run -itd --name CN --hostname CN -p 9000:9000 -it -v <PATHTOYOURDIR>:/configuration -e NETWORK=ptn0 cardanocommunity/cardano-node:debian 
```

Once the container is running, you cat attach to it by running the following command (change `CN` with your container name):
```
docker attach CN
```
To detach the session without stopping the node press `CTRL+P` then `CTRL+Q` and you will be detached from the container
If you hit `CTRL+C` instead the container will be stopped.

While if you have an hook within the continer console use the following command (change `CN` with your container name)
```
docker exec -it CN bash 
```


---
#### WINZOZZ Users..
>With Powershell on Windows, you can run (in this example the Debian version):
>```
>Get-Content Debian_CN_Dockerfile | docker build -t guild-operators/cardano-node:debian -
>```
---


### Cardano-Wallet (testnet / mainnet)

> At the moment the best way to run the Wallet container is trough the related docker compose file (`Wallet-docker-compose.yaml`) 

With bash on Linux, you can run _just_ for the wallet image:
```
$ docker build -t guild-operators/cardano-wallet:debian - < Debian_CW_Dockerfile
docker run -itd --name CW --hostname CW -p 8090:8090 -e NETWORK=mainnet guild-operators/cardano-wallet:debian 
```

## docker-compose
While to run a node + wallet trough the docker-compose cmd:
```
wget https://raw.githubusercontent.com/cardano-community/guild-operators/docker/files/ptn0/docker/Wallet-docker-compose.yaml
NETWORK=mainnet docker-compose -f Wallet-docker-compose.yaml up    # add -d for detach mode

```

***

## How to run your container from DockerHub
***
### Cardano Community DockerHub

 - Cardano Node

_PTN0_  \
`docker run -itd  --name CN --hostname CN -p 9001:9000 -it -e NETWORK=ptn0 cardanocommunity/cardano-node:debian` 

_Mainnet_ \
`docker run -itd  --name CN --hostname CN -p 9001:9000 -it -e NETWORK=mainnet cardanocommunity/cardano-node:debian` 

_Testnet_ \
`docker run -itd  --name CN --hostname CN -p 9001:9000 -it -e NETWORK=testnet cardanocommunity/cardano-node:debian` 


 - Cardano Wallet

See *docker-compose section.

> Thos are single images, to run a full wallet you need also a full Cardano Node.

 
 ### IOHK DockerHub (Nix Version)
