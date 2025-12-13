# Kubernetes Installation and Configuration Algorithm

## MASTER

1. Initialize master and worker nodes
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
17. Install `kubelet` and `kubeadm`
18. Start `kubelet`
19. Install `kubectl` on the master node
20. Restart `containerd` service
21. Initialize cluster using `kubeadm init` with `--pod-network` and API server address
22. Create `.kube` folder and set permissions to 700
23. Copy `admin.conf` from `/etc/kubernetes/` to `.kube` folder and set permissions to 600
24. Deploy pod network to the cluster (Calico or another plugin) using:
    ```bash
    kubectl apply -f [podnetwork].yaml
    ```

## SLAVE

1. Join the node:
    ```bash
    kubeadm join <master-address> --token <token> --discovery-token-ca-cert-hash <hash>
    ```
2. Create `.kube` folder and copy `config` from master node (`~/.kube/config`) to allow the slave node to correctly display other nodes in the cluster
3. List available nodes in the cluster using:
    ```bash
    kubectl get nodes
    ```