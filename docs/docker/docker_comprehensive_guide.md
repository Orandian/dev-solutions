# Comprehensive Docker Guide - Commands and Concepts

A complete reference guide to Docker containerization technology, covering core concepts, CLI commands, Dockerfile instructions, and Docker Compose for modern application development and deployment.

## Table of Contents
1. [What is Docker?](#what-is-docker)
2. [Docker Core Concepts](#docker-core-concepts)
3. [Docker CLI Commands](#docker-cli-commands)
4. [Dockerfile Instructions](#dockerfile-instructions)
5. [Docker Compose](#docker-compose)
6. [Best Practices](#best-practices)
7. [Common Workflows](#common-workflows)

---

## What is Docker?

Docker is a containerization platform that packages applications with all their dependencies into lightweight, portable containers. Think of Docker as a **"lunchbox" for your application** - it packages your code with everything it needs to run consistently across different environments.

### Key Benefits
- **Consistency**: Runs the same everywhere
- **Isolation**: Applications don't interfere with each other
- **Portability**: Move applications between environments easily
- **Efficiency**: Lightweight compared to virtual machines
- **Version Control**: Track and manage different versions
- **Scalability**: Easy to scale applications up or down
- **DevOps Integration**: Streamlines development and deployment

---

## Docker Core Concepts

### Docker Image
A **lightweight, standalone, executable package** that includes everything needed to run software:
- Application code
- Runtime environment
- Libraries and dependencies
- System tools
- Operating system components

Think of it as a **recipe for your application**.

### Docker Container
A **runnable instance** of a Docker image. It represents the execution environment where your application actually runs. Multiple containers can be created from a single image.

### Docker Volume
A **persistent data storage mechanism** that:
- Shares data between container and host machine
- Enables data sharing among multiple containers
- Ensures data survives container restarts or removal
- Provides data durability and persistence

### Docker Network
A **communication channel** that enables:
- Container-to-container communication
- Container-to-external world connectivity
- Network isolation and security
- Custom networking configurations

### Docker Client
The **user interface** for interacting with Docker through:
- Command line interface (CLI)
- Graphical user interfaces
- API calls

### Docker Host (Docker Daemon)
The **background process** that manages:
- Container lifecycle
- Image building and storage
- Network and volume management
- API request handling

### Docker Registry (Docker Hub)
A **centralized repository** for Docker images:
- Public and private image storage
- Image versioning and distribution
- Docker Hub is to Docker what GitHub is to Git

---

## Docker CLI Commands

### Image Management

#### Downloading Images
```bash
# Download image from Docker Hub
docker pull <image_name>
docker pull ubuntu
docker pull node:18-alpine

# Pull specific version/tag
docker pull mysql:8.0
docker pull nginx:latest
```
**Explanation**: Downloads images from Docker Hub to your local machine. If no tag is specified, it pulls the `latest` tag by default.

#### Listing Images
```bash
# List all local images
docker images
docker image ls

# Show image details
docker image inspect <image_name>
```
**Explanation**: Displays all Docker images stored locally with details like repository, tag, image ID, creation date, and size.

#### Building Images
```bash
# Build image from Dockerfile
docker build -t <tag_name> .
docker build -t hello-docker .
docker build -t myapp:v1.0 .

# Build with custom Dockerfile name
docker build -f Dockerfile.prod -t myapp:prod .

# Build with build arguments
docker build --build-arg NODE_ENV=production -t myapp .
```
**Explanation**: Creates a Docker image from a Dockerfile. The `-t` flag assigns a name/tag to the image, and `.` indicates the build context (current directory).

#### Managing Images
```bash
# Tag an image
docker tag <source_image> <target_image>
docker tag react-docker username/react-docker:latest

# Remove image
docker rmi <image_name>
docker rmi ubuntu:latest

# Remove unused images
docker image prune
```

### Container Operations

#### Running Containers
```bash
# Basic container run
docker run <image_name>
docker run hello-world

# Run interactively with terminal
docker run -it <image_name>
docker run -it ubuntu bash

# Run in detached mode (background)
docker run -d nginx

# Run with custom name
docker run --name my-container nginx
```

#### Port Mapping
```bash
# Map container port to host port
docker run -p <host_port>:<container_port> <image_name>
docker run -p 8080:80 nginx
docker run -p 5173:5173 react-app
```
**Explanation**: The `-p` flag maps a port from the container to the host machine, making the container's service accessible externally.

#### Volume Mounting
```bash
# Mount host directory to container
docker run -p 5173:5173 -v $(pwd):/app -v /app/node_modules react-docker
```
**Explanation**: 
- `-v $(pwd):/app`: Mounts current directory to `/app` in container (enables hot reloading)
- `-v /app/node_modules`: Creates named volume for node_modules (preserves dependencies)

#### Container Management
```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# Stop container
docker stop <container_id_or_name>
docker stop c3d  # Using first few characters of ID

# Start stopped container
docker start <container_id_or_name>

# Restart container
docker restart <container_id_or_name>

# Remove container
docker rm <container_id_or_name>
docker rm --force <container_id_or_name>  # Force remove running container

# Remove all stopped containers
docker container prune
```

### Registry Operations
```bash
# Login to Docker Hub
docker login

# Push image to registry
docker push <image_name>
docker push username/myapp:latest

# Logout from registry
docker logout
```

---

## Dockerfile Instructions

A **Dockerfile** is a text file containing instructions to build a Docker image. Each instruction creates a new layer in the image.

### Essential Instructions

#### FROM - Base Image
```dockerfile
FROM <base_image>:<tag>
FROM node:20-alpine
FROM ubuntu:22.04
FROM python:3.9-slim
```
**Explanation**: Specifies the base image. **Must be the first instruction** in every Dockerfile.

#### WORKDIR - Working Directory
```dockerfile
WORKDIR /app
WORKDIR /usr/src/app
```
**Explanation**: Sets the working directory for subsequent instructions (RUN, CMD, ENTRYPOINT, COPY, ADD).

#### COPY - Copy Files
```dockerfile
COPY <source> <destination>
COPY . .                    # Copy all files to current WORKDIR
COPY package.json ./        # Copy specific file
COPY src/ ./src/           # Copy directory
```
**Explanation**: Copies files/directories from build context (local machine) to the image.

#### RUN - Execute Commands
```dockerfile
RUN <command>
RUN npm install
RUN apt-get update && apt-get install -y curl
RUN pip install -r requirements.txt
```
**Explanation**: Executes commands during image build, creating new layers. Use for installing dependencies and setting up the environment.

#### EXPOSE - Port Declaration
```dockerfile
EXPOSE <port>
EXPOSE 3000
EXPOSE 80 443
```
**Explanation**: Documents which ports the container listens on. **Informational only** - doesn't actually publish ports.

#### ENV - Environment Variables
```dockerfile
ENV <key>=<value>
ENV NODE_ENV=production
ENV API_URL=https://api.example.com
ENV PORT=3000
```
**Explanation**: Sets environment variables available during build and runtime.

#### ARG - Build Arguments
```dockerfile
ARG <variable_name>
ARG NODE_VERSION=18
ARG BUILD_ENV
```
**Explanation**: Defines build-time variables passed with `--build-arg` flag during build.

#### VOLUME - Mount Points
```dockerfile
VOLUME <mount_point>
VOLUME /app/data
VOLUME ["/var/log", "/var/db"]
```
**Explanation**: Creates mount point for external volumes, specifying persistent data locations.

#### CMD - Default Command
```dockerfile
CMD ["executable", "param1", "param2"]
CMD ["node", "server.js"]
CMD ["npm", "start"]
```
**Explanation**: Provides default command when container starts. **Can be overridden** when running the container.

#### ENTRYPOINT - Main Executable
```dockerfile
ENTRYPOINT ["executable", "param1"]
ENTRYPOINT ["node", "app.js"]
```
**Explanation**: Sets the main command that **cannot be easily overridden**. Used with CMD for flexible argument passing.

### .dockerignore File
```
node_modules
npm-debug.log
.git
.gitignore
README.md
.env
.nyc_output
coverage
.DS_Store
```
**Explanation**: Similar to `.gitignore`, excludes files/directories from build context, reducing image size and build time.

---

## Docker Compose

Docker Compose manages **multi-container applications** using a YAML configuration file.

### Docker Compose Commands

#### Initialization
```bash
# Initialize new application
docker init
```
**Explanation**: CLI tool that generates Dockerfile, .dockerignore, and compose.yaml files based on your project.

#### Application Management
```bash
# Start all services
docker compose up

# Start in detached mode with rebuild
docker compose up -d --build

# Stop all services
docker compose down

# Stop and remove volumes
docker compose down -v

# View running services
docker compose ps

# View logs
docker compose logs
docker compose logs <service_name>

# Scale services
docker compose up --scale web=3
```

### compose.yaml Structure
```yaml
version: '3.8'
services:
  web:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/app
      - /app/node_modules
    environment:
      - NODE_ENV=development
    depends_on:
      - database

  database:
    image: postgres:13
    environment:
      - POSTGRES_DB=myapp
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

---

## Best Practices

### Dockerfile Optimization
```dockerfile
# Use specific tags instead of 'latest'
FROM node:18-alpine

# Use multi-stage builds
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

FROM node:18-alpine AS runtime
WORKDIR /app
COPY --from=builder /app/node_modules ./node_modules
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

### Security Best Practices
```dockerfile
# Create non-root user
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001

# Use non-root user
USER nextjs

# Minimize attack surface
RUN rm -rf /var/cache/apk/*
```

### Image Size Optimization
```dockerfile
# Use alpine images
FROM node:18-alpine

# Combine RUN commands
RUN apk add --no-cache git \
    && npm install \
    && apk del git

# Use .dockerignore effectively
```

---

## Common Workflows

### Development Workflow
```bash
# 1. Build image
docker build -t myapp:dev .

# 2. Run with volume mounting for hot reload
docker run -p 3000:3000 -v $(pwd):/app -v /app/node_modules myapp:dev

# 3. Use Docker Compose for full stack
docker compose up -d --build
```

### Production Workflow
```bash
# 1. Build production image
docker build -t myapp:prod .

# 2. Tag for registry
docker tag myapp:prod username/myapp:latest

# 3. Push to registry
docker push username/myapp:latest

# 4. Deploy
docker run -d -p 80:3000 --name myapp-prod username/myapp:latest
```

### Debugging Workflow
```bash
# View container logs
docker logs <container_name>

# Execute commands in running container
docker exec -it <container_name> bash

# Inspect container details
docker inspect <container_name>

# Check resource usage
docker stats <container_name>
```

---

## Quick Reference

### Most Used Commands
| Command | Description |
|---------|-------------|
| `docker build -t name .` | Build image from Dockerfile |
| `docker run -p 8080:80 image` | Run container with port mapping |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers |
| `docker stop container` | Stop running container |
| `docker rm container` | Remove stopped container |
| `docker images` | List local images |
| `docker rmi image` | Remove image |
| `docker compose up -d` | Start services in background |
| `docker compose down` | Stop and remove services |

### Essential Dockerfile Instructions
| Instruction | Purpose |
|-------------|---------|
| `FROM` | Base image |
| `WORKDIR` | Set working directory |
| `COPY` | Copy files to image |
| `RUN` | Execute commands during build |
| `EXPOSE` | Document port usage |
| `ENV` | Set environment variables |
| `CMD` | Default startup command |

Docker revolutionizes application deployment by providing consistency, isolation, and portability across all environments. Master these commands and concepts to efficiently containerize and manage your applications!