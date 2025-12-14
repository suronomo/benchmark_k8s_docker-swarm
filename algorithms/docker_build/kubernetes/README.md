# Kubernetes (deployment) files

This folder contains the Kubernetes manifests used to **deploy the application** in a Kubernetes cluster.  
It includes the Deployment and Service manifests, as well as commentary versions for educational and portfolio purposes.  
These files are intended to **define and manage the application runtime** inside the cluster, using Docker images built from `shared_image_files` (`suronomo/algorithms/docker_build/shared_image_files/...`).

## Files Overview

| File | Purpose |
|------|---------|
| `deployment.yaml` | Kubernetes Deployment manifest defining how the application is deployed and managed in a cluster. Specifies the container image, number of replicas, environment variables, and volume configuration. |
| `deployment.commentary.yaml` | `deployment.commentary.yaml` with step-by-step commentary. Used for documentation and portfolio purposes. Not intended for direct image building. |
| `service.yaml` | Kubernetes Service manifest defining how Pods created by the Deployment are exposed and accessed within or outside the cluster using stable networking and label selectors. |

## Usage

### Deploy the application to Kubernetes

From this folder, you can deploy the application to a Kubernetes cluster using the provided manifests:


```bash
# Apply the Deployment to create Pods
kubectl apply -f deployment.yaml

# Apply the Service to expose the Pods
kubectl apply -f service.yaml

# Verify the resources
kubectl get deployments
kubectl get services
