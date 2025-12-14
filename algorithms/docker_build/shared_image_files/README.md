# Shared Image Files

This folder contains files used to **build the Docker image** for both Kubernetes (K8s) and Docker-Swarm (D-S) deployments. These files are shared across deployment environments and provide a consistent application runtime setup.

## Files Overview

| File | Purpose |
|------|---------|
| `Dockerfile` | Defines the steps to build a Docker image. Contains instructions to set up the runtime environment, copy application files, install dependencies, and configure the container. Used directly to create the Docker image. |
| `Dockerfile.commentary.md` | `Dockerfile.commentary.md` with step-by-step commentary. Used for documentation and portfolio purposes. Not intended for direct image building. |
| `requ.txt` | Python dependencies required by the application (`estimation-π.py`). Used in the Dockerfile to install packages via pip. |
| `docker-compose.yaml` | Defines build configurations for Docker images. Specifies build context, environment variables, volumes, and resource limits to create container images efficiently for use in multiple deployment environments. |
| `docker-compose.commentary.yaml` | `docker-compose.yaml` with step-by-step commentary. Used for documentation and portfolio purposes. Not intended for direct image building. |
> **Note:**  
> The actual `estimation-π.py` program is documented in algorithm form step-by-step in  
> `suronomo/algorithms/docker_build/common/pi_estimation_MC-method.md` (restricted due to thesis-related constraints).  
> It is not a runnable script but provides the detailed procedure for the Monte Carlo π estimation in a parallel or clustered environment.



## Usage

### Build the Docker Image

From this folder, you can build Docker images:

```bash
# Build a single Docker image
docker build -t <image_name>:<tag_or_version>
# Example:
docker build -t estimation_pi_docker:latest

# Build multiple Docker images using Docker Compose
docker-compose build