# Docker Cheat Sheet

## Docker Version & Info

```bash
Docker version = Display the installed Docker Engine version and build information
Docker info = Show system-wide information about Docker installation, including number of containers, images, storage driver, and resource usage
```

## Image Management

```bash
docker pull image_name:latest = Download the specified image (with tag `latest`) from the Docker registry to your local machine
docker rmi image_name = Remove the specified image from the local Docker image cache
docekr images = List all images currently stored on the local system along with their tags and sizes
docker image prune = Remove all dangling (unused) images to free up disk space
```

## Container Management

```bash
docker run -p 8000:80 image_name = Create and start a new container from the image, publishing container port 80 as host port 8000
docker ps = List running containers along with their IDs, names, status, ports, and resource usage
docker logs continer_name = Fetch and display the standard output and error logs of the specified container
docker stop continer_name = Gracefully stop a running container by sending SIGTERM, then SIGKILL after timeout
docker rm container_name = Remove the specified stopped container from the host system
docker restart container_name = Stop and then start the container again, preserving its configuration and data
docker exec Container_name command = Run a one-off command inside the namespace of a running container
docker inspect container/image_naame = Retrieve low-level JSON details for debugging about the container or image
ndocker commit Container_name new-image-name = Capture the current state of a container and save it as a new image
ndocker run --rm alpine ls = Run the `ls` command inside an Alpine container and automatically remove it after exit
```

## Volumes

```bash
docker volume create my-vol = Create a new named volume for persistent data storage
docker volume ls = List all Docker volumes, showing names and drivers
docker volume inspect Volume_name = Display detailed JSON information about a specific volume
docker volume rm [volume-name] = Delete the specified volume (must be unused)
docker volume prune = Remove all unused volumes to reclaim space

docker run -v volume_name image_name = Mount the named volume into the container’s default path
ndocker run -v /host-path:/container-path image_name = Bind mount a host directory into the container for file sharing
```

## Networking

```bash
docker network ls = Show all networks defined on the Docker host along with their drivers
docek network inspect network_name = Display configuration details (subnet, gateway, containers) for the network
docker network create my-network = Create a new user-defined bridge network for inter-container communication
docker network prune = Delete all unused user-defined networks
docker network rm [network] = Remove the specified network (must not be in use)

docker run --network network_name IMAGE_name = Connect the new container directly to an existing network
docker network connect [network] [container] = Attach a running container to an additional network
docker network disconnect [network] [container] = Detach a container from a user-defined network
docker run --expose 80 image_name = Expose port 80 within the container without publishing to host
docker run -p 8080:80/tcp -p 8080:80/udp image_name = Publish both TCP and UDP for port 80 on host port 8080

# Create a custom bridge network
docker network create --driver bridge my-network = Establish a new bridge network using the default bridge driver
# Create a network with subnet and gateway
docker network create --driver bridge --subnet=192.168.0.0/24 --gateway=192.168.0.1 my-network = Specify IP range and gateway for custom network
# Create an overlay network for Docker Swarm
docker network create --driver overlay --attachable my-overlay-network = Enable multi-host networking for Swarm services
```

## Linking Containers

```bash
docker run --link running_continer_name cuntainer_name = Create environment variables and network aliases to link two legacy containers (deprecated in favor of user-defined networks)
```

## Docker Swarm & Services

```bash
docker swarm init --advertise-addr [MANAGER-IP] = Initialize current node as Swarm manager and advertise its address
docker swarm join --token [TOKEN] [MANAGER-IP]:2377 = Add a new node to an existing Swarm using the token provided
docker node ls = List all nodes in the Swarm cluster along with their roles and status
docker node inspect [NODE-ID] = Display detailed info (labels, availability) for a specific Swarm node
docker node promote [NODE-ID] = Elevate a worker node to manager role in the Swarm
docker node demote [NODE-ID] = Convert a manager node back to worker role
docker swarm leave = Remove the node from the Swarm as a worker
docker swarm leave --force = Force manager node to leave, potentially causing loss of quorum

docker service create --name [SERVICE-NAME] --replicas [N] [IMAGE] = Deploy a replicated service running N tasks from the image
docker service ls = List all active services with replica counts and images
docker service ps [SERVICE-NAME] = Show the individual task instances for the given service
docker service inspect [SERVICE-NAME] = Retrieve full configuration and status in JSON form
docker service scale [SERVICE-NAME]=[N] = Increase or decrease the number of running replicas
ndocker service update [OPTIONS] [SERVICE-NAME] = Apply updates such as image, environment vars, or resource limits
docker service rm [SERVICE-NAME] = Remove the service and stop all its tasks

docker stack deploy -c docker-compose.yml [STACK-NAME] = Deploy multiple services defined in Compose file as a stack
docker stack ls = List all deployed stacks with service counts
docker stack services [STACK-NAME] = List services under the given stack name
docker stack ps [STACK-NAME] = Display tasks and nodes for a stack’s services
docker stack rm [STACK-NAME] = Tear down the stack and remove all its services and networks
```

## Docker Compose Commands

```bash
docker-compose up = Build (if needed), create, start, and attach to containers for a service
docker-compose up -d = Run services defined in Compose file in detached mode in the background
docker-compose down = Stop and remove containers, networks, and default volumes created by `up`
docker-compose down -v = Additionally remove named volumes declared in the Compose file
docker-compose ps = List containers for services defined in the current Compose project
docker-compose logs = View aggregated logs from all service containers
docker-compose exec [service] [command] = Run a command in a running service container by name
docker-compose build = Build or rebuild service images specified in the Compose file
docker-compose restart = Restart all or specified services
```

## Common docker run Options

| Option    | Description                                                                  | Example                                          |
| --------- | ---------------------------------------------------------------------------- | ------------------------------------------------ |
| -d        | Run container in background (detached) so it doesn’t tie up your terminal    | `docker run -d nginx`                            |
| -p        | Map a container port to a host port to handle external traffic               | `docker run -p 8080:80 nginx`                    |
| -v        | Mount a host or named volume inside the container for persistent data        | `docker run -v /host/path:/container/path nginx` |
| --name    | Assign an easy-to-remember name to the container instead of autogenerated ID | `docker run --name webserver nginx`              |
| -e        | Set environment variables inside the container for configuration             | `docker run -e MYSQL_ROOT_PASSWORD=secret mysql` |
| --network | Attach the container to a specific user-defined network                      | `docker run --network my-network nginx`          |
| -it       | Allocate a pseudo-TTY and keep STDIN open for interactive shells             | `docker run -it ubuntu bash`                     |

## Lifecycle Commands

| Action  | Command                      | Description                                                             |
| ------- | ---------------------------- | ----------------------------------------------------------------------- |
| Create  | `docker create [image]`      | Define a new container without starting it immediately                  |
| Run     | `docker run [image]`         | Create and start a container in one step with default configurations    |
| Start   | `docker start [container]`   | Launch a previously created or stopped container                        |
| Stop    | `docker stop [container]`    | Gracefully stop a running container, allowing it to terminate processes |
| Kill    | `docker kill [container]`    | Immediately terminate container by sending SIGKILL signal               |
| Pause   | `docker pause [container]`   | Suspend all processes in the container without terminating them         |
| Unpause | `docker unpause [container]` | Resume processes in a paused container                                  |
| Restart | `docker restart [container]` | Stop and immediately start the container again                          |
| Remove  | `docker rm [container]`      | Permanently delete a stopped container from the system                  |

## Common Dockerfile Instructions

| Instruction | Description                                                           | Example                                          |
| ----------- | --------------------------------------------------------------------- | ------------------------------------------------ |
| FROM        | Specify the base image to use for all subsequent instructions         | `FROM ubuntu:20.04`                              |
| RUN         | Execute commands in a new layer during build (e.g., install packages) | `RUN apt-get update && apt-get install -y nginx` |
| CMD         | Define default command or arguments when container starts             | `CMD ["nginx", "-g", "daemon off;"]`             |
| ENTRYPOINT  | Configure a container to run as an executable with fixed command      | `ENTRYPOINT ["nginx", "-g", "daemon off;"]`      |
| WORKDIR     | Set the working directory for subsequent instructions and commands    | `WORKDIR /app`                                   |
| COPY        | Copy files or directories from source on host into the image          | `COPY . /app`                                    |
| ADD         | Like COPY but can extract tar archives and fetch remote URLs          | `ADD app.tar.gz /app`                            |
| ENV         | Set environment variables that persist at runtime                     | `ENV NODE_ENV production`                        |
| EXPOSE      | Document which ports the container listens on at runtime              | `EXPOSE 80 443`                                  |
| VOLUME      | Declare mount points for persistent or shared data                    |                                                  |
| USER        | Specify the user or UID to use when running the image                 | `USER nginx`                                     |
| ARG         | Define build-time variables that can be overridden at build time      | `ARG VERSION=latest`                             |

## Misc Commands

```bash
commands = Placeholder for Jenkins setup, CI/CD pipeline commands
networking = Placeholder for advanced network configuration steps
compose = Placeholder for Docker Compose workflows
dockerfile = Placeholder for custom build instructions
volume persistent = Placeholder for volume backup and restore operations
```
