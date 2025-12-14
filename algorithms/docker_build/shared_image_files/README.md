# Shared Image Files

This folder contains files used to **build the Docker image** for both Kubernetes (K8s) and Docker-Swarm (D-S) deployments. These files are shared across deployment environments and provide a consistent application runtime setup.

## Files Overview

| File | Purpose |
|------|---------|
| `Dockerfile` | Clean version of the Dockerfile for building the image. Used directly for building the Docker image. |
| `Dockerfile.commentary.md` | Dockerfile with step-by-step commentary. Used for documentation and portfolio purposes. Not intended for direct image build. |
| `requ.txt` | Python dependencies required by the application (`estimation-π.py`). Used in the Dockerfile to install packages via pip. |
| `docker-compose.yaml` | X |
> **Note:**  
> The actual `estimation-π.py` program is documented in algorithm form step-by-step in  
> `suronomo/algorithms/docker_build/common/pi_estimation_MC-method.md` (restricted due to thesis-related constraints).  
> It is not a runnable script but provides the detailed procedure for the Monte Carlo π estimation (in cluster computing).



## Usage

### Build the Docker Image

From this folder, you can build the Docker image using:

```bash
For one indepency Docker image:
docker build -t <name_of_Docker_image>:<tag;version>
f.e docker build -t estimation_pi_docker:latest

For many Docker images (Docker compose tool):
docker compose build

