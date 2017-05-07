### To stop registry and remove registry

HQSML-151665:pkrish00c$ docker stop registry && docker rm -v registry
registry
registry
HQSML-151665:pkrish00c$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES


### Delete all containers
docker rm $(docker ps -a -q)

### Delete all images
docker rmi $(docker images -q)

Use -f for force remove


