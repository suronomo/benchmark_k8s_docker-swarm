# Docker-Swarm installation and configuration algorithm

### Common steps (Kubernetes & Docker-Swarm for manager machine)

1. Initialize manager and worker nodes (hosts: all)
2. Disable swap and update `/etc/fstab`
3. Check if `br_netfilter` is enabled
4. Check if `ip6tables` is set to 1
5. Create SSH key pair
6. Generate `k8s.list` file
7. Add Kubernetes repository using `echo`
8. Remove `k8s-apt-keyring.gpg` if it exists (safety measure)
9. Download and install GPG key from Kubernetes repository
10. Install `apt-transport-https`
11. Add GPG key for Docker repository
12. Add Docker repository
13. Install Docker packages: `docker-ce`, `docker-ce-cli`, `containerd.io`, `docker-compose`
14. Configure systemd cgroup for `containerd`
15. Start and enable Docker service
16. Update system packages
## Divergent steps 
### Docker-Swarm 
17. Ensure the `docker` group exists
18. Add the current user to the `docker` group
19. Set ownership and permissions for `/var/run/docker.sock` (root:docker, 0660)
20. Initialize Docker Swarm on the manager node (`docker swarm init`)
21. Generate worker join token (`docker swarm join-token worker`)
22. Generate manager join token (`docker swarm join-token worker`) [**Optional: for redundancy — _Raft Consensus_**]

### Slave

1. Join the node:
    ```bash
    docker swarm join \ 
    --token <worker> \
    <manager-address:2377>
    ```
2. List available nodes in the cluster using:
    ```bash
    docker node ls
    ```