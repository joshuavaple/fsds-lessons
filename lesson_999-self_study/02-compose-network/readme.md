In this example, we have two services: web and app. Both services are connected to a custom network named mynetwork.

```docker compose
version: '3.8'

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
The command: sleep 600 line in the Docker Compose file specifies the command to be run inside the container when it starts: to sleep for 600 seconds (10 min). This effectively keeps the container running for 10 min without doing anything else. This can be useful for testing purposes or to keep the container alive without exiting while you perform other actions, such as testing network communication.

The driver: bridge in the Docker Compose file specifies the type of network driver to use for the custom network mynetwork. The bridge driver is the default network driver in Docker. It creates a private internal network on the host where containers can communicate with each other. Containers connected to the same bridge network can communicate with each other using their container names as hostnames.


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

5. In the other `compose.wrong.yml` file below, `myapp2` is not in the same network as `myweb`. Hence when executing `nslookup myweb` command inside it, the following message shows that `myweb` cannot be found.

    ```
    Server:         127.0.0.11
    Address:        127.0.0.11:53

    ** server can't find myweb: NXDOMAIN

    ** server can't find myweb: NXDOMAIN
    ```

When executing the same command in `myapp`, which is inside the same network as `myweb`, the web app can be found.
    ```
    Server:         127.0.0.11
    Address:        127.0.0.11:53

    Non-authoritative answer:

    Non-authoritative answer:
    Name:   myweb
    Address: 172.25.0.3
    ```