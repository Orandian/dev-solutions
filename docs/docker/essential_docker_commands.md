# Essential Docker Commands Reference

A comprehensive guide to the most important Docker commands every developer should know.

## Table of Contents
1. [Docker Image Commands](#docker-image-commands)
2. [Docker Container Commands](#docker-container-commands)
3. [Docker Network Commands](#docker-network-commands)
4. [Docker Volume Commands](#docker-volume-commands)
5. [Docker System Commands](#docker-system-commands)
6. [Docker Compose Commands](#docker-compose-commands)
7. [Common Flags and Options](#common-flags-and-options)
8. [Practical Examples](#practical-examples)

---

## Docker Image Commands

### Building Images
```bash
# Build image from Dockerfile in current directory
docker build -t myapp:latest .

# Build with custom Dockerfile name
docker build -f Dockerfile.prod -t myapp:prod .

# Build with build arguments
docker build --build-arg ENV=production -t myapp:prod .
```

### Managing Images
```bash
# List all images
docker images
docker image ls

# Pull image from registry
docker pull nginx:latest
docker pull mysql:8.0

# Push image to registry
docker push username/myapp:latest

# Remove image
docker rmi image_id
docker rmi nginx:latest

# Remove unused images
docker image prune

# Remove all images
docker rmi $(docker images -q)
```

### Image Information
```bash
# Show image details
docker inspect nginx:latest

# Show image history/layers
docker history nginx:latest

# Search images on Docker Hub
docker search mysql
```

---

## Docker Container Commands

### Running Containers
```bash
# Basic container run
docker run nginx

# Run in detached mode (background)
docker run -d nginx

# Run with custom name
docker run --name webserver nginx

# Run with port mapping
docker run -p 8080:80 nginx

# Run with environment variables
docker run -e MYSQL_ROOT_PASSWORD=secret mysql

# Run with volume mount
docker run -v /host/path:/container/path nginx

# Interactive container with terminal
docker run -it ubuntu bash

# Run and remove container when it stops
docker run --rm nginx
```

### Container Lifecycle
```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# Start stopped container
docker start container_name

# Stop running container
docker stop container_name

# Restart container
docker restart container_name

# Pause container
docker pause container_name

# Unpause container
docker unpause container_name

# Kill container (force stop)
docker kill container_name
```

### Container Interaction
```bash
# Execute command in running container
docker exec -it container_name bash
docker exec container_name ls /app

# View container logs
docker logs container_name
docker logs -f container_name  # Follow logs
docker logs --tail 100 container_name  # Last 100 lines

# Copy files to/from container
docker cp file.txt container_name:/path/
docker cp container_name:/path/file.txt ./

# Show container resource usage
docker stats container_name

# Show running processes in container
docker top container_name
```

### Container Management
```bash
# Remove stopped container
docker rm container_name

# Remove running container (force)
docker rm -f container_name

# Remove all stopped containers
docker container prune

# Remove all containers
docker rm $(docker ps -aq)

# Inspect container details
docker inspect container_name
```

---

## Docker Network Commands

### Network Management
```bash
# List networks
docker network ls

# Create custom network
docker network create mynetwork
docker network create --driver bridge mynetwork

# Remove network
docker network rm mynetwork

# Connect container to network
docker network connect mynetwork container_name

# Disconnect container from network
docker network disconnect mynetwork container_name

# Inspect network details
docker network inspect mynetwork

# Remove unused networks
docker network prune
```

### Running Containers with Networks
```bash
# Run container on specific network
docker run --network mynetwork nginx

# Run with network alias
docker run --network mynetwork --network-alias web nginx
```

---

## Docker Volume Commands

### Volume Management
```bash
# List volumes
docker volume ls

# Create volume
docker volume create myvolume

# Remove volume
docker volume rm myvolume

# Remove unused volumes
docker volume prune

# Inspect volume details
docker volume inspect myvolume
```

### Using Volumes
```bash
# Mount named volume
docker run -v myvolume:/data nginx

# Mount host directory (bind mount)
docker run -v /host/path:/container/path nginx

# Read-only mount
docker run -v /host/path:/container/path:ro nginx

# Create and mount volume in one command
docker run -v mydata:/app/data nginx
```

---

## Docker System Commands

### System Information
```bash
# Show Docker system info
docker info

# Show Docker version
docker version

# Show disk usage
docker system df

# Show real-time events
docker events
```

### System Cleanup
```bash
# Remove unused data (containers, networks, images, build cache)
docker system prune

# Remove everything including volumes
docker system prune -a --volumes

# Remove unused containers
docker container prune

# Remove unused images
docker image prune

# Remove unused networks
docker network prune

# Remove unused volumes
docker volume prune
```

---

## Docker Compose Commands

### Basic Operations
```bash
# Start services defined in docker-compose.yml
docker-compose up

# Start in detached mode
docker-compose up -d

# Stop services
docker-compose down

# Stop and remove volumes
docker-compose down -v

# Build services
docker-compose build

# Pull service images
docker-compose pull
```

### Service Management
```bash
# Scale services
docker-compose up -d --scale web=3

# View running services
docker-compose ps

# View logs
docker-compose logs
docker-compose logs web

# Execute command in service
docker-compose exec web bash

# Restart services
docker-compose restart
```

---

## Common Flags and Options

### Universal Flags
- `-d, --detach`: Run container in background
- `-it`: Interactive terminal (combine -i and -t)
- `-p, --publish`: Publish container port to host
- `-v, --volume`: Mount volume
- `-e, --env`: Set environment variables
- `--name`: Assign name to container
- `--rm`: Remove container when it exits
- `-f, --force`: Force operation

### Port Mapping Options
```bash
# Map specific port
-p 8080:80

# Map to random host port
-p 80

# Map specific interface
-p 127.0.0.1:8080:80

# Map UDP port
-p 8080:80/udp
```

### Volume Mount Options
```bash
# Named volume
-v volume_name:/path

# Bind mount
-v /host/path:/container/path

# Read-only mount
-v /host/path:/container/path:ro

# Temporary filesystem
--tmpfs /path
```

---

## Practical Examples

### Example 1: Web Application with Database
```bash
# Create network
docker network create webapp-net

# Run MySQL database
docker run -d \
  --name mysql-db \
  --network webapp-net \
  -e MYSQL_ROOT_PASSWORD=secret \
  -e MYSQL_DATABASE=myapp \
  -v mysql-data:/var/lib/mysql \
  mysql:8.0

# Run web application
docker run -d \
  --name web-app \
  --network webapp-net \
  -p 8080:80 \
  -e DB_HOST=mysql-db \
  -e DB_NAME=myapp \
  myapp:latest
```

### Example 2: Development Environment
```bash
# Run container with code volume for development
docker run -it \
  --name dev-env \
  -v $(pwd):/workspace \
  -p 3000:3000 \
  -w /workspace \
  node:16 \
  bash
```

### Example 3: Temporary Testing Container
```bash
# Run temporary container that auto-removes
docker run --rm -it \
  -v $(pwd):/test \
  -w /test \
  python:3.9 \
  python script.py
```

### Example 4: Multi-container Application Logs
```bash
# View logs from multiple containers
docker logs -f web-app &
docker logs -f mysql-db &

# Or use docker-compose
docker-compose logs -f
```

---

## Troubleshooting Commands

### Debugging Containers
```bash
# Check container status
docker ps -a

# Inspect container configuration
docker inspect container_name

# Check container logs
docker logs --details container_name

# Get shell access to debug
docker exec -it container_name /bin/sh

# Check container processes
docker top container_name

# Monitor resource usage
docker stats
```

### Common Issues Solutions
```bash
# Port already in use
docker ps | grep :8080
sudo lsof -i :8080

# Container won't start
docker logs container_name
docker inspect container_name

# Out of disk space
docker system df
docker system prune -a

# Permission issues
docker exec -it container_name ls -la /path
```

---

## Quick Reference Cheat Sheet

| Command | Description |
|---------|-------------|
| `docker run` | Create and start container |
| `docker ps` | List running containers |
| `docker stop` | Stop container |
| `docker rm` | Remove container |
| `docker images` | List images |
| `docker rmi` | Remove image |
| `docker pull` | Download image |
| `docker build` | Build image |
| `docker logs` | View container logs |
| `docker exec` | Execute command in container |
| `docker inspect` | View detailed info |
| `docker network` | Manage networks |
| `docker volume` | Manage volumes |
| `docker system prune` | Clean up unused resources |

Remember: Always refer to `docker --help` or `docker COMMAND --help` for the most up-to-date information and additional options!