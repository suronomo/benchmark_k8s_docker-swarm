# Performance benchmarking of containerized applications on Kubernetes and Docker-Swarm
**Note:** This project is based on my master’s thesis. At the current stage, access to the original implementation (e.g., playbooks, Dockerfiles, etc.) is restricted due to thesis-related constraints. For this reason, the original solutions have been rewritten and presented in the form of algorithmic steps.
## 1. Assumptions and objective

This study assumes the execution of a comparative analysis of the efficiency of resource utilization optimization processes in container orchestration clusters based on **Kubernetes** and **Docker Swarm**.

The analysis is conducted across three types of environments:

* a **local on-premises environment**,
* a **public cloud environment provided by Microsoft Azure**,
* a **public cloud environment provided by DigitalOcean**.

The primary objective is to evaluate and compare the effectiveness of resource utilization optimization mechanisms implemented by Kubernetes and Docker Swarm. The study focuses on how efficiently available computational resources are allocated and managed by each orchestrator in both local and cloud-based environments.
## 2. Test environment and infrastructure
### a) Cluster topology
![Cluster Topology](https://raw.githubusercontent.com/suronomo/benchmark_k8s_docker-swarm/main/figures/architecture/cluster_infrastructure.PNG)
<div align="center" style="font-size:17px;">
  Figure 1. Cluster topology used in local on-premises and cloud environments
</div>

### b) Machine specifications
### Local on-premises environment
>*Manager*
- Allocated 2 vCPU (Intel Core i7-10700K 3.8 GHz)
- 4 GB DDR4 RAM
- 128 MB GPU (Intel UHD Graphics 630)
- 25 GB NVMe SSD
>*Worker-[1-4]*
- Allocated 1–8 vCPU (Intel Core i7-10700K 3.8 GHz), depending on the experimental scenario
- 2 GB DDR4 RAM
- 16 MB GPU (Intel UHD Graphics 630)
- 25 GB NVMe SSD

### Microsoft Azure
>*Manager* —`Standard_D2s_v3` 
- Allocated 2 vCPU (Intel Xeon E5-2673 v3 2.4 GHz) by the service provider
- 8 GB RAM
- 32 GB SSD (Performance Tier P4<sup>1</sup>)<br><sup>1</sup>for more details, refer to: https://learn.microsoft.com/en-us/azure/virtual-machines/disks-change-performance
>*Worker-[1-4]* —
`Depending on the experimental scenario, the following machines were used`:

- Standard_D2s_v3:
  - Allocated 2 vCPU (Intel Xeon E5-2673 v3 2.4 GHz)
  - 8 GB RAM
  - 32 GB SSD (Performance Tier P4)

- Standard_D4s_v3:
  - Allocated 4 vCPU (Intel Xeon E5-2673 v3 2.4 GHz)
  - 16 GB RAM
  - 32 GB SSD (Performance Tier P4)
### DigitalOcean
>*Manager* — `Basic (selected plan) [Shared CPU]`<sup>2</sup>:
  - Allocated 2 vCPU (CPU model not disclosed by the service provider in the VM technical specifications)
  - 4 GB RAM
  - 80 GB SSD (Regular)
<sup></sup><br><sup>2</sup>for more details, refer to: https://www.digitalocean.com/pricing/droplets

>*Worker-[1-4]* —
`Depending on the experimental scenario, the following machines were used`:

- Basic (selected plan) [Shared CPU]:
  - Allocated 2 vCPU (CPU model not disclosed by the service provider in the VM technical specifications)
  - 2 GB RAM
  - 60 GB SSD (Regular)

- Basic (selected plan) [Shared CPU]:
  - Allocated 4 vCPU (CPU model not disclosed by the service provider in the VM technical specifications)
  - 8 GB RAM
  - 160 GB SSD (Regular)
### c) Software specifications
- **Linux Ubuntu 22.04.4 LTS** was used as the operating system.
- **Kubernetes version 1.29.4** deployed with **Calico v3.25** for networking.
- **Docker/Docker-Swarm 27.2.0** provided container orchestration.
- **Ansible 2.16.9** handled configuration management.
- **OpenSSH 8.9p1** used for secure remote access.
- **Netplan 0.106.1** managed network configuration.
- **VirtualBox 7.0.14r161095 (Qt5.15.2)** served as the virtualization platform.
- **MobaXterm Personal Edition v23.3 Build 5142** used for terminal access on Windows.
- **WinSCP 6.3.3** facilitated secure file transfers.
## 3. Experimental procedure overview
1. Configured Netplan 0.106.1 for network management
2. Set up OpenSSH 8.9p1 for secure remote access
3. Installed Ansible 2.16.9 on the manager node
4. Set up (on all nodes):
- Kubernetes v1.29.4 with Calico v3.25 networking [`playbook_k8s_installation_and_configuration.md`](https://github.com/suronomo/benchmark_k8s_docker-swarm/tree/main/algorithms/installation_configuration_k8s_docker-swarm)
- Docker-Swarm 27.2.0 [`playbook_docker-swarm_installation_and_configuration.md`](https://github.com/suronomo/benchmark_k8s_docker-swarm/tree/main/algorithms/installation_configuration_k8s_docker-swarm)
5. Developed custom benchmark programs to test computational performance across the cluster
- Estimation of π [`pi_estimation_MC-method.md`](https://github.com/suronomo/benchmark_k8s_docker-swarm/tree/main/algorithms/docker_build/common_programs)
- fio benchmark [`storage_benchmark_with_fio.md`](https://github.com/suronomo/benchmark_k8s_docker-swarm/tree/main/algorithms/docker_build/common_programs)
6. Built custom Docker image [`Dockerfile, requ.txt, docker-compose.yaml`](https://github.com/suronomo/benchmark_k8s_docker-swarm/tree/main/algorithms/docker_build/shared_image_files)
7. Push the Docker image to an external repository (e.g., DockerHub) using the command:
docker push `docker push <username>/<repository>:<tag>`
8. Deployed Docker containers in:
- K8s using [`deployment.yaml`,`service.yaml`](https://github.com/suronomo/benchmark_k8s_docker-swarm/tree/main/algorithms/docker_build/kubernetes)
- Docker-Swarm using [`docker-compose.yml`](https://github.com/suronomo/benchmark_k8s_docker-swarm/tree/main/algorithms/docker_build/docker-swarm)
9. _Optionally_, set up **Prometheus** with **Grafana** and **Node Exporter** to collect, visualize, and aggregate system and network metrics across the cluster **(in this case, the metrics were collected and aggregated by the π estimation program)**. This setup allows monitoring CPU, memory, disk, and network usage in real-time, providing insights into resource utilization during benchmark execution.
10. Executed benchmark programs on the deployed environment and collected performance results for analysis
## 3a. Benchmarking methodology
The experiments were conducted in two types of environments: a local on-premises environment and public cloud environments based on Kubernetes and Docker Swarm. For the local environment, virtual machine configurations were explicitly defined for the purposes of the study, while in the public cloud environments the technical specifications of virtual machines were constrained by the offerings of the respective cloud service providers (Microsoft Azure and DigitalOcean).

For each test environment configuration (local or cloud), measurements were performed for datasets of varying sample sizes, ranging from 5 × 10³ to 2 × 10⁷. Each test for a given dataset (e.g., 1 × 10⁴ samples) was repeated 10 times, allowing the averaging of results for the estimated value of π, computation time, RAM usage, and CPU load.

## 4. Experimental Results
Note: For representational purposes, the results shown here pertain to the estimation of π using a single replica node with 2 vCPU, illustrating various parameters. Not all experiments are included; these examples provide a representative view of the outcomes.

### a) Local on-premises environment
## 1 replica, 2 vCPUs - Kubernetes

| Number of samples | Avg CPU load (%) | Avg RAM usage (MB) | Avg computation time (s) | Estimated π |
|------------------|-----------------|------------------|-------------------------|------------|
| 5.00E+03         | 1.80            | 34.10            | 0.510                   | 3.14412    |
| 2.00E+04         | 2.55            | 33.85            | 0.502                   | 3.14261    |
| 5.00E+04         | 2.30            | 33.95            | 0.503                   | 3.14150    |
| 3.00E+05         | 1.90            | 33.70            | 0.501                   | 3.14232    |
| 7.00E+05         | 1.80            | 33.80            | 0.509                   | 3.14127    |
| 1.00E+06         | 2.60            | 33.60            | 0.504                   | 3.14166    |
| 1.50E+06         | 3.05            | 33.65            | 0.507                   | 3.14216    |
| 2.00E+06         | 4.75            | 33.33            | 0.511                   | 3.14128    |
| 3.00E+06         | 5.50            | 33.32            | 0.509                   | 3.14166    |
| 4.00E+06         | 6.35            | 33.73            | 0.507                   | 3.14154    |
| 5.00E+06         | 7.40            | 33.89            | 0.501                   | 3.14134    |
| 7.00E+06         | 11.50           | 33.71            | 0.506                   | 3.14157    |
| 1.00E+07         | 12.50           | 33.80            | 0.508                   | 3.14148    |
| 1.50E+07         | 17.60           | 33.70            | 0.512                   | 3.14153    |
| 2.00E+07         | 13.60           | 53.50            | 1.010                   | 3.14154    |

## 1 replica, 2 vCPUs - Docker-Swarm
| Number of samples | Avg CPU load (%) | Avg RAM usage (MB) | Avg computation time (s) | Estimated π |
|------------------|-----------------|------------------|-------------------------|------------|
| 5.00E+03         | 2.45            | 33.40            | 0.548                   | 3.14508    |
| 2.00E+04         | 1.68            | 33.50            | 0.529                   | 3.14158    |
| 5.00E+04         | 3.42            | 33.45            | 0.538                   | 3.13732    |
| 3.00E+05         | 5.05            | 33.48            | 0.572                   | 3.14379    |
| 7.00E+05         | 6.12            | 33.55            | 0.556                   | 3.14152    |
| 1.00E+06         | 7.22            | 33.12            | 0.547                   | 3.14107    |
| 1.50E+06         | 9.73            | 33.50            | 0.512                   | 3.14113    |
| 2.00E+06         | 11.35           | 33.53            | 0.537                   | 3.14162    |
| 3.00E+06         | 13.15           | 33.57            | 0.828                   | 3.14171    |
| 4.00E+06         | 18.50           | 33.50            | 0.621                   | 3.14137    |
| 5.00E+06         | 24.50           | 33.48            | 0.706                   | 3.14156    |
| 7.00E+06         | 30.50           | 33.82            | 0.559                   | 3.14141    |
| 1.00E+07         | 39.25           | 33.00            | 0.563                   | 3.14143    |
| 1.50E+07         | 62.35           | 33.05            | 0.513                   | 3.14124    |
| 2.00E+07         | 39.50           | 50.32            | 1.027                   | 3.14158    |


### b) Microsoft Azure
## 1 replica, 2 vCPUs - Kubernetes
| Number of samples | Avg CPU load (%) | Avg RAM usage (MB) | Avg computation time (s) | Estimated π |
|------------------|-----------------|------------------|-------------------------|------------|
| 5.00E+03         | 0.88            | 33.65            | 0.505                   | 3.13305    |
| 2.00E+04         | 2.15            | 33.70            | 0.504                   | 3.13785    |
| 5.00E+04         | 0.92            | 33.75            | 0.503                   | 3.13988    |
| 3.00E+05         | 3.20            | 33.70            | 0.505                   | 3.14183    |
| 7.00E+05         | 4.30            | 33.75            | 0.504                   | 3.14160    |
| 1.00E+06         | 6.30            | 33.70            | 0.506                   | 3.14153    |
| 1.50E+06         | 7.85            | 33.72            | 0.503                   | 3.14121    |
| 2.00E+06         | 11.00           | 33.75            | 0.507                   | 3.14175    |
| 3.00E+06         | 12.30           | 33.70            | 0.505                   | 3.14153    |
| 4.00E+06         | 15.30           | 33.68            | 0.504                   | 3.14245    |
| 5.00E+06         | 21.40           | 33.85            | 0.506                   | 3.14119    |
| 7.00E+06         | 26.43           | 34.00            | 0.507                   | 3.14132    |
| 1.00E+07         | 37.12           | 34.30            | 0.567                   | 3.14121    |
| 1.50E+07         | 28.70           | 53.50            | 1.005                   | 3.14153    |
| 2.00E+07         | 35.50           | 49.25            | 1.003                   | 3.14159    |

## 1 replica, 2 vCPUs - Docker-Swarm
| Number of samples | Avg CPU load (%) | Avg RAM usage (MB) | Avg computation time (s) | Estimated π |
|------------------|-----------------|------------------|-------------------------|------------|
| 5.00E+03         | 0.72            | 34.31            | 0.504                   | 3.14386    |
| 2.00E+04         | 0.63            | 34.45            | 0.503                   | 3.14458    |
| 5.00E+04         | 1.65            | 34.15            | 0.502                   | 3.13246    |
| 3.00E+05         | 4.05            | 34.22            | 0.505                   | 3.14194    |
| 7.00E+05         | 8.53            | 34.48            | 0.503                   | 3.14131    |
| 1.00E+06         | 13.10           | 34.75            | 0.502                   | 3.14182    |
| 1.50E+06         | 16.53           | 34.15            | 0.504                   | 3.14158    |
| 2.00E+06         | 22.32           | 35.23            | 0.503                   | 3.14132    |
| 3.00E+06         | 32.12           | 33.21            | 0.502                   | 3.14192    |
| 4.00E+06         | 44.50           | 35.31            | 0.503                   | 3.14172    |
| 5.00E+06         | 46.55           | 33.22            | 0.504                   | 3.14119    |
| 7.00E+06         | 70.53           | 34.95            | 0.503                   | 3.14125    |
| 1.00E+07         | 45.35           | 47.84            | 1.005                   | 3.14138    |
| 1.50E+07         | 72.44           | 59.40            | 1.007                   | 3.14140    |
| 2.00E+07         | 62.30           | 70.34            | 1.505                   | 3.14198    |

### c) DigitalOcean
## 1 replica, 2 vCPUs - Kubernetes
| Number of samples | Avg CPU load (%) | Avg RAM usage (MB) | Avg computation time (s) | Estimated π |
|------------------|-----------------|------------------|-------------------------|------------|
| 5.00E+03         | 3.50            | 33.65            | 0.504                   | 3.14326    |
| 2.00E+04         | 2.73            | 33.68            | 0.503                   | 3.13343    |
| 5.00E+04         | 5.32            | 33.63            | 0.508                   | 3.14208    |
| 3.00E+05         | 5.35            | 33.65            | 0.504                   | 3.14270    |
| 7.00E+05         | 11.05           | 33.63            | 0.503                   | 3.14141    |
| 1.00E+06         | 14.00           | 33.65            | 0.508                   | 3.14112    |
| 1.50E+06         | 18.90           | 33.71            | 0.503                   | 3.14171    |
| 2.00E+06         | 26.35           | 33.79            | 0.501                   | 3.14162    |
| 3.00E+06         | 43.70           | 33.78            | 0.505                   | 3.14126    |
| 4.00E+06         | 54.65           | 33.73            | 0.506                   | 3.14132    |
| 5.00E+06         | 60.25           | 33.72            | 0.504                   | 3.14170    |
| 7.00E+06         | 54.55           | 38.00            | 0.800                   | 3.14130    |
| 1.00E+07         | 50.30           | 46.00            | 1.009                   | 3.14121    |
| 1.50E+07         | 63.85           | 59.35            | 1.165                   | 3.14135    |
| 2.00E+07         | 65.92           | 61.50            | 1.507                   | 3.14183    |

## 1 replica, 2 vCPUs - Docker-Swarm
| Number of samples | Avg CPU load (%) | Avg RAM usage (MB) | Avg computation time (s) | Estimated π |
|------------------|-----------------|------------------|-------------------------|------------|
| 5.00E+03         | 1.38            | 33.53            | 0.501                   | 3.15220    |
| 2.00E+04         | 1.12            | 33.21            | 0.501                   | 3.13590    |
| 5.00E+04         | 2.25            | 33.35            | 0.501                   | 3.14266    |
| 3.00E+05         | 3.70            | 33.37            | 0.501                   | 3.14135    |
| 7.00E+05         | 11.35           | 33.35            | 0.501                   | 3.14131    |
| 1.00E+06         | 13.89           | 33.33            | 0.504                   | 3.14140    |
| 1.50E+06         | 17.35           | 33.35            | 0.504                   | 3.14160    |
| 2.00E+06         | 25.43           | 33.37            | 0.504                   | 3.14129    |
| 3.00E+06         | 33.75           | 33.35            | 0.504                   | 3.14185    |
| 4.00E+06         | 53.25           | 33.34            | 0.504                   | 3.14189    |
| 5.00E+06         | 63.73           | 33.45            | 0.505                   | 3.14182    |
| 7.00E+06         | 54.21           | 41.42            | 0.904                   | 3.14181    |
| 1.00E+07         | 53.35           | 45.35            | 1.009                   | 3.14151    |
| 1.50E+07         | 57.68           | 57.12            | 1.355                   | 3.14159    |
| 2.00E+07         | 58.72           | 68.23            | 1.605                   | 3.14167    |

## 5. Conclusion
The experimental results demonstrate that the Monte Carlo-based π estimation algorithm performs consistently across different cloud environments and orchestration platforms. The estimated π values remain stable around 3.1415, even with increasing sample sizes, indicating the correctness of the computational approach.

CPU usage increases proportionally with the number of samples, while RAM consumption remains relatively stable, showing that CPU is the primary limiting resource in these tests. Docker-Swarm generally exhibits slightly higher CPU utilization compared to Kubernetes, though computation times are comparable.

Although the choice of cloud environment (Azure vs. DigitalOcean) does not significantly affect the accuracy of the π estimation, it may influence resource usage and performance under certain conditions, particularly for larger workloads. These observations suggest that both orchestration platforms and cloud providers are suitable for computational benchmarks, but careful consideration of the environment is recommended when scaling up experiments.

__Note: The presented results are based on a single replica node with 2 vCPUs. Therefore, the data illustrate the performance of the orchestrators and cloud environments for this specific hardware configuration and should be interpreted as representative for this type of setup.__

## 6. Limitations

The conducted experiments have several limitations that should be considered when interpreting the results:

1. `Program code quality`: The benchmark programs were developed at a basic to intermediate level, which may lead to suboptimal CPU and memory utilization compared to highly optimized implementations.

2. `vCPU sharing and noisy neighbors`: In cloud environments with shared virtual CPUs, performance fluctuations may occur due to other tenants’ workloads, affecting CPU load and computation time. _(see also point 5)_

3. `Licensing restrictions`: The type of subscription (commercial, student) may impose constraints on available resources or access to advanced features, which could influence experimental outcomes.

4. `Incomplete hardware specifications`: Cloud providers do not always disclose all technical parameters of virtual machines or the underlying infrastructure, which may impact performance and the choice of computational environment.

5. `Resource contention`: In multi-tenant cloud environments, virtual machines may compete for the same physical resources (CPU, RAM, storage), which can cause performance variability and impact the reproducibility of experimental results.

6. `Methodological limitations`: The experimental methodology, including the averaging of measured values, may introduce additional uncertainty. Aggregating results can mask transient fluctuations or outliers in CPU, memory, or computation time, affecting the precision of the reported performance metrics.

7. `Differences in resource management by orchestrators`: 
   Kubernetes and Docker Swarm, although both container orchestration platforms, employ different mechanisms for scheduling, resource allocation, and handling idle computational resources. These architectural and policy-level differences may lead to variations in CPU and memory utilization that are independent of the underlying infrastructure, potentially influencing the comparability of performance results.

__Note 1: These limitations indicate that while the presented results are representative, caution is needed when generalizing the findings to other environments or scaling up the experiments.__ 

__Note 2: To obtain a complete picture of the studies, a more extensive set of experiments considering a larger number of vCPUs and replicas would be necessary.__

## 7. Additional Experiments: Orchestrated vs Non-Orchestrated Execution
Based on Microsoft Azure using Kubernetes
## 1 replica, 2 vCPUs - Kubernetes
| Number of samples | Avg CPU load (%) | Avg RAM usage (MB) | Avg computation time (s) | Estimated π |
|------------------|-----------------|------------------|-------------------------|------------|
| 5.00E+03         | 0.88            | 33.65            | 0.505                   | 3.13305    |
| 2.00E+04         | 2.15            | 33.70            | 0.504                   | 3.13785    |
| 5.00E+04         | 0.92            | 33.75            | 0.503                   | 3.13988    |
| 3.00E+05         | 3.20            | 33.70            | 0.505                   | 3.14183    |
| 7.00E+05         | 4.30            | 33.75            | 0.504                   | 3.14160    |
| 1.00E+06         | 6.30            | 33.70            | 0.506                   | 3.14153    |
| 1.50E+06         | 7.85            | 33.72            | 0.503                   | 3.14121    |
| 2.00E+06         | 11.00           | 33.75            | 0.507                   | 3.14175    |
| 3.00E+06         | 12.30           | 33.70            | 0.505                   | 3.14153    |
| 4.00E+06         | 15.30           | 33.68            | 0.504                   | 3.14245    |
| 5.00E+06         | 21.40           | 33.85            | 0.506                   | 3.14119    |
| 7.00E+06         | 26.43           | 34.00            | 0.507                   | 3.14132    |
| 1.00E+07         | 37.12           | 34.30            | 0.567                   | 3.14121    |
| 1.50E+07         | 28.70           | 53.50            | 1.005                   | 3.14153    |
| 2.00E+07         | 35.50           | 49.25            | 1.003                   | 3.14159    |

## 2 vCPUs - Non-orchestrated execution

| Number of samples | Avg CPU load (%) | Avg RAM usage (MB) | Avg computation time (s) | Estimated π |
| ----------------- | ---------------- | ------------------ | ------------------------ | ----------- |
| 5.00E+03          | 4.22             | 31.35              | 0.503                    | 3.14193     |
| 2.00E+04          | 6.78             | 31.12              | 0.502                    | 3.14248     |
| 5.00E+04          | 7.31             | 31.55              | 0.503                    | 3.13941     |
| 3.00E+05          | 8.28             | 31.87              | 0.502                    | 3.14211     |
| 7.00E+05          | 12.32            | 31.12              | 0.503                    | 3.14187     |
| 1.00E+06          | 16.35            | 31.24              | 0.503                    | 3.14176     |
| 1.50E+06          | 21.39            | 31.26              | 0.503                    | 3.14133     |
| 2.00E+06          | 27.81            | 31.38              | 0.503                    | 3.14179     |
| 3.00E+06          | 36.78            | 31.30              | 0.502                    | 3.14188     |
| 4.00E+06          | 47.31            | 31.52              | 0.503                    | 3.14151     |
| 5.00E+06          | 54.25            | 31.54              | 0.503                    | 3.14136     |
| 7.00E+06          | 66.31            | 33.13              | 0.601                    | 3.14139     |
| 1.00E+07          | 53.15            | 38.20              | 1.007                    | 3.14141     |
| 1.50E+07          | 67.32            | 42.70              | 1.102                    | 3.14149     |
| 2.00E+07          | 65.34            | 51.60              | 1.498                    | 3.14151     |

### General conclusion: Performance comparison of non-orchestrated and orchestrated execution
1. `Resource utilization`: Kubernetes introduces a small overhead in CPU and memory usage compared to non-orchestrated execution. This overhead is more noticeable for smaller datasets but becomes proportionally smaller as the dataset size increases (e.g., for 2.00 × 10⁷ samples, CPU load was 35.50% under Kubernetes and 65.34% without orchestration; _see also point 3_)

2. `Result accuracy`: In this specific case, both orchestrated and non-orchestrated executions produce consistent and accurate estimates of π, confirming that, for the tested configuration, the orchestration layer does not compromise computational correctness. However, when increasing the number of replicas, the trend in result stability or accuracy may change.

3. `Performance considerations`: Non-orchestrated execution results in noticeably higher utilization of available computational resources, which can be a disadvantage in environments where minimal resource usage is required.