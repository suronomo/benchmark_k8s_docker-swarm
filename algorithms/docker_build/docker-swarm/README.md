# Docker-Swarm (deployment) files

This folder contains the files used to **deploy the application** in a Docker-Swarm environment.  
It includes the `docker-compose.yml` manifest and a commentary version for educational and portfolio purposes.  
These files are intended to **define and manage the application runtime** in Swarm services using Docker images built from `shared_image_files` (`suronomo/algorithms/docker_build/shared_image_files/...`).

## Files Overview

| File | Purpose |
|------|---------|
| `docker-compose.yml` | Defines services, container images, number of replicas, environment variables, volumes, resource limits, and network configuration. Used directly for Swarm deployment. |
| `docker-compose.commentary.yml` | `docker-compose.commentary.yml` with step-by-step commentary. Used for documentation and portfolio purposes. Not intended for direct image building. |


> **Note:**  
> The file `docker-compose.yaml` is typically used to **bulk build Docker images** for multiple services.  
> In contrast, `docker-compose.yml` in this folder (`docker-swarm`) is specifically structured for **Docker-Swarm deployments**,  
> where it defines services, replicas, overlay networks, and resource constraints to **run containers from existing Docker images**.  
> It is **not identical** to a standard `docker-compose.yaml` used for local Docker image building.




## Usage

### Deploy the application to Docker-Swarm

From this folder, you can deploy the application to a Docker-Swarm cluster using the provided Compose file:

```bash
# Deploy the stack using docker-compose.yml
docker stack deploy -c docker-compose.yml estimation-π_2vCPUs

# Verify the running containers (tasks) in the stack
docker stack ps estimation-π_2vCPUs