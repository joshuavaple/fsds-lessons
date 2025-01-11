In this example, we have two services: web and app. Both services are connected to a custom network named mynetwork.

```docker compose
name: compose-network

services:
  myweb:
    image: nginx:alpine
    networks:
      - mynetwork
    ports:
      - "8080:80"

  myapp:
    image: busybox
    command: sleep 3600
    networks:
      - mynetwork

networks:
  mynetwork:
    driver: bridge
```
- The command: sleep 600 line in the Docker Compose file specifies the command to be run inside the container when it starts: to sleep for 600 seconds (10 min). This can be useful to keep the container alive without exiting while you perform other actions, such as testing network communication.

- The driver: bridge in the Docker Compose file specifies the type of network driver to use for the custom network mynetwork. The bridge driver is the default network driver in Docker. It creates a private internal network on the host where containers can communicate with each other. Containers connected to the same bridge network can communicate with each other using their container names as hostnames.


- ports: 8080:80 means that port 8080 on the host machine is mapped to port 80 inside the container.
When you access http://localhost:8080 on the host machine, the request is forwarded to port 80 inside the myweb container, where the nginx server is listening.

- How to Use Host and Container Ports
  - Accessing the Service from the Host Machine: You can access the nginx server running inside the myweb container by navigating to http://localhost:8080 in your web browser or using a tool like curl or wget.
  - Accessing the Service from Another Container: Containers on the same network can communicate with each other using their service names and container ports. For example, if myapp wants to access myweb, it can use http://myweb:80. However, Docker Compose's networking features provide built-in service discovery, allowing services to locate each other through service names without the internal port, and using 80 here is not required. 
  - more info: https://www.warp.dev/terminus/docker-compose-port-mapping


1. To start the services in detached mode, run
    ```
    docker compose up -d
    ```
2. Then, the 2 services will be running under the same compose stack under the `name` top level of the compose file.
3. you can go into `myapp` container to test its connectivity to the `myapp` service by some commands:
    ```
    nslookup myweb
    ```
    ```
    ping -c 4 myweb
    ```
    ```
    wget -O- http://myweb
    ```
    Note that the web service container name `myweb` can be used by `myapp` thanks to the network setup.

4. You can run these commands in the terminal of the PC by the following pattern, which execute a command in a running container of a specified service inside the compose stack.
    ```
    docker compose exec myapp sh -c "<your command here>"
    ```

In the other `compose.wrong.yml` file below (note that we use another host port as 8080 is being used by the other stack) 
```docker compose
name: compose-network-wrong

services:
  myweb:
    image: nginx:alpine
    networks:
      - mynetwork
    ports:
      - "5000:80"

  myapp:
    image: busybox
    command: sleep 600
    networks:
      - mynetwork
  
  myapp2:
    image: busybox
    command: sleep 600
    networks:
      - mynetwork2

networks:
  mynetwork:
    driver: bridge
  mynetwork2:
    driver: bridge
```


1. `myapp2` is not in the same network as `myweb`. Hence when executing `nslookup myweb` command inside it, the following message shows that `myweb` cannot be found.

    ```
    Server:         127.0.0.11
    Address:        127.0.0.11:53

    ** server can't find myweb: NXDOMAIN

    ** server can't find myweb: NXDOMAIN
    ```

2. When executing the same command in `myapp`, which is inside the same network as `myweb`, the web app can be found.
    ```
    Server:         127.0.0.11
    Address:        127.0.0.11:53

    Non-authoritative answer:

    Non-authoritative answer:
    Name:   myweb
    Address: 172.25.0.3
    ```