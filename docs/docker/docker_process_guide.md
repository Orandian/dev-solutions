# Docker Process Guide

This guide covers the complete Docker workflow from building Spring Boot applications to managing PostgreSQL databases with Docker.

## Table of Contents
1. [Spring Boot Docker Image Creation](#spring-boot-docker-image-creation)
2. [Essential Docker Commands](#essential-docker-commands)
3. [PostgreSQL Database Setup](#postgresql-database-setup)
4. [Docker Network Configuration](#docker-network-configuration)
5. [PostgreSQL Service Management](#postgresql-service-management)

---

## Spring Boot Docker Image Creation

### Building the Docker Image
```bash
./mvnw spring-boot:build-image "-Dspring-boot.build-image.imageName=orandian2511/jobappimage"
```
**Explanation**: This command uses Maven Wrapper to build a Docker image for your Spring Boot application. The `-Dspring-boot.build-image.imageName` parameter specifies the image name and tag. Spring Boot's build-image goal uses Cloud Native Buildpacks to create an optimized Docker image.

### Pushing to Docker Hub
```bash
docker push orandian2511/jobappimage
```
**Explanation**: Uploads your locally built Docker image to Docker Hub registry under the username `orandian2511`. This makes the image available for deployment on any system with Docker.

### Running the Application
```bash
# Run in foreground (interactive mode)
docker run -p 8080:8080 orandian2511/jobappimage

# Run in background (detached mode)
docker run -d -p 8080:8080 orandian2511/jobappimage
```
**Explanation**: 
- `-p 8080:8080`: Maps port 8080 on your host machine to port 8080 inside the container
- `-d`: Runs the container in detached mode (background)

### Monitoring the Application
```bash
# List running containers
docker ps

# View container logs
docker logs a6c0aa9393dc
```
**Explanation**: `docker ps` shows active containers, while `docker logs` displays the output from a specific container using its ID.

---

## Essential Docker Commands

### Image Management
```bash
# Pull an image from registry
docker pull <image>

# Push an image to registry
docker push <username/image>

# Build an image from Dockerfile
docker build -t <username/image> .

# List all images
docker images

# Remove an image
docker rmi <image-id/image-name>
```

### Container Management
```bash
# Run a container
docker run -it -d -p <host-port>:<container-port> --name <name> <image>

# Stop a running container
docker stop <container-id/container-name>

# Start a stopped container
docker start <container-id/container-name>

# Remove a container
docker rm <container-id/container-name>

# List running containers
docker ps

# List all containers (including stopped)
docker ps -a
```

### Container Interaction
```bash
# Execute commands inside a running container
docker exec -it <container-name/container-id> bash

# View container logs
docker logs <container-name/container-id>

# Inspect container configuration
docker inspect <container-name/container-id>
```

**Key Flags Explained**:
- `-it`: Interactive terminal mode
- `-d`: Detached mode (runs in background)
- `-p`: Port mapping
- `--name`: Assigns a custom name to the container

---

## PostgreSQL Database Setup

### Basic PostgreSQL Container
```bash
# Run PostgreSQL database
docker run -d --name db -e POSTGRES_PASSWORD=postgres postgres
```
**Explanation**: Creates a PostgreSQL container named `db` with the default password set to `postgres`. The `-e` flag sets environment variables.

### PgAdmin Web Interface
```bash
# Run PgAdmin for database management
docker run -d --name pgadmin \
  -e PGADMIN_DEFAULT_EMAIL=admin@gmail.com \
  -e PGADMIN_DEFAULT_PASSWORD=admin \
  dpage/pgadmin4
```
**Explanation**: Starts PgAdmin4 web interface for PostgreSQL management. You can access it via a web browser once the container is running.

### Testing Container Communication
```bash
# Test network connectivity between containers
docker exec -it pgadmin ping db
```
**Explanation**: This command attempts to ping the `db` container from inside the `pgadmin` container. Initially, this may fail because containers aren't on the same network.

### Cleanup
```bash
# Force remove both containers
docker rm -f db pgadmin
```
**Explanation**: The `-f` flag forcefully stops and removes running containers.

---

## Docker Network Configuration

### Creating a Custom Network
```bash
# Create a user-defined bridge network
docker network create my-network
```
**Explanation**: Creates a custom network that allows containers to communicate using container names as hostnames.

### Running Containers on Custom Network
```bash
# Run PostgreSQL on custom network
docker run -d --name db --network my-network \
  -e POSTGRES_PASSWORD=postgres postgres

# Run PgAdmin on the same network
docker run -d --name pgadmin --network my-network \
  -e PGADMIN_DEFAULT_EMAIL=user@domain.com \
  -e PGADMIN_DEFAULT_PASSWORD=SuperSecret \
  dpage/pgadmin4
```
**Explanation**: Both containers are now on the same custom network, enabling them to communicate using container names (`db`, `pgadmin`) as hostnames.

### Verify Network Connectivity
```bash
# Test connectivity between networked containers
docker exec -it pgadmin ping db
```
**Explanation**: This should now work successfully since both containers are on the same network.

---

## PostgreSQL Service Management

### Stopping Local PostgreSQL Services
```bash
# Stop PostgreSQL service (macOS with Homebrew)
sudo brew services stop postgresql

# Kill any running PostgreSQL processes
sudo pkill -f postgres
```
**Explanation**: These commands stop local PostgreSQL installations that might conflict with Docker containers. This is useful when you want to use PostgreSQL in Docker instead of a local installation.

---

## Best Practices

1. **Use specific tags**: Instead of `latest`, use version tags for production
2. **Environment variables**: Store sensitive data in environment variables, not in the image
3. **Volume mapping**: Use volumes for persistent data storage
4. **Resource limits**: Set memory and CPU limits for containers
5. **Health checks**: Implement health checks for better monitoring
6. **Multi-stage builds**: Use multi-stage Dockerfiles for smaller production images

## Troubleshooting Tips

- Use `docker logs <container-name>` to debug container issues
- Check port conflicts with `netstat -tlnp | grep :8080`
- Verify network connectivity with `docker network ls` and `docker network inspect`
- Clean up unused resources with `docker system prune`