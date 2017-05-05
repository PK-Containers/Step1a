This is a very simple step to understand basics involved in converting a stateless microservice app into a container service.

The scope of this step is to just understand how to build a docker image, run and view logs.

1. git clone https://github.com/PK-Containers/Step1.git

2. cd Step1

3. To build the docker image
    docker build -t helloworld .
    
4. To run the container
    docker run -d -p 8080:8080 helloworld
    
    -d => run in detached mode(background)

5. To check if the container is running
    docker ps
    
    =======================================================================================================================
    
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               
4d885e64f451        helloworld          "python hello.py"   28 seconds ago      Up 27 seconds       0.0.0.0:8080->8080/tcp   

NAMES
competent_minsky

    =======================================================================================================================

6. To validate results in browser ->
    http://localhost:8080/
        displays, Hello World from host "<your container id>".
        
7. To view logs
    docker logs <container_id> or <container_name>
    docker logs competent_minsky [or]
    docker logs 4d885e64f451
    
8. To get into container shell
    docker exec -it <container_id> sh
    
    Instead of sh, sometimes use bash or /bin/bash, based on your Dockerfile
    
9. To view all docker process status(including non running containers)
    docker ps -a
    
10.     
    
    
