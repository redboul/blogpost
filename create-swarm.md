```bash

docker-machine create -d virtualbox manager 

eval "$(docker-machine env manager)"

docker run -d \
    -p "8500:8500" \
    -h "consul" \
    progrium/consul -server -bootstrap

docker-machine create -d virtualbox \
    --swarm --swarm-master \
    --swarm-discovery="consul://$(docker-machine ip manager):8500" \
    --engine-opt="cluster-store=consul://$(docker-machine ip manager):8500" \
    --engine-opt="cluster-advertise=eth1:2376" \
    mhs-demo0


docker-machine create -d virtualbox \                              
    --swarm \
    --swarm-discovery="consul://$(docker-machine ip manager):8500" \    
    --engine-opt="cluster-store=consul://$(docker-machine ip manager):8500" \    
    --engine-opt="cluster-advertise=eth1:2376" \
    mhs-demo1

sudo docker $(docker-machine config mhs-demo0) network create --driver overlay lab-net

cp -f docker-compose-v4.yml docker-compose.yml

docker-compose up --build -d

docker ps 

```
