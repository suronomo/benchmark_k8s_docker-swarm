# Docker build files

This folder (`docker_build`) contains all **necessary files and programs to build Docker images and (K8s/D-S) containers**, as well as associated algorithms and playbooks for testing and orchestration. Due to thesis-related constraints, some programs are documented in **algorithm form** rather than as runnable scripts.

## Folder Overview

| Folder | Purpose |
|--------|---------|
| `docker_build/common_programs` | Contains programs documented in algorithm form for running FIO benchmarks and estimating the value of π. These are **algorithmic descriptions** and not executable code. |
| `docker_build/shared_image_files` | Contains files used to **build Docker images** that can be adapted for both Kubernetes (K8s) and Docker-Swarm (D-S). Includes Dockerfiles, commentary versions, and Python dependencies. |
| `docker_build/docker-swarm` | Contains files and configurations for **building containers** from the Docker images (`shared_image_files`) specifically for _Docker-Swarm_ deployments. |
| `docker_build/kubernetes` | Contains files and configurations for **building containers** from the Docker images (`shared_image_files`) specifically for _Kubernetes_ deployments. |
| `Installation_configuration_k8s_docker-swarm` | Contains playbooks and step-by-step algorithms for installing and configuring _Kubernetes_ and _Docker-Swarm_ clusters. Not intended as executable scripts. |
