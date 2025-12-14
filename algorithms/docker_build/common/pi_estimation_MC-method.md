# Multithreaded π estimation algorithm

This document describes the algorithm for estimating the value of π using a **multithreaded approach**. The implementation leverages Python's `ThreadPoolExecutor` to parallelize computations and monitors system resource usage during execution.

---

## 1. Import Libraries

- Import necessary Python modules:
  - `threading`, `time`, `os`, `psutil`, `numpy`
  - `concurrent.futures` → `ThreadPoolExecutor`

## 2. Define Program Parameters

- `NUM_THREADS` – number of worker threads  
- `NUM_SAMPLES` – number of random points per thread  
- `NUM_EXPERIMENTS` – number of repeated experiments  
- Enable **logging of results** to file

## 3. Generate Random Points

- Randomly generate points `(x, y)` in the range `[-1, 1]` for both axes  
- Each point represents a location in the unit square

## 4. Compute Points Inside Unit Circle

- For each point `(x, y)`, check if it satisfies:  
$x^2 + y^2 \le 1$
- Count the number of points inside the circle  
- Estimate π using the Monte Carlo method:  

## 5. Thread Function

- Each thread executes a function that:
- Computes π estimation for its subset of points  
- Appends results to a thread-specific list, indexed by thread ID

## 6. Monitor CPU/RAM Usage

- Start a monitoring thread to track **CPU and RAM utilization**  
- Collect usage statistics at regular intervals (e.g., 0.5 seconds) during computation

## 7. Main Function Steps

1. **Iterate Over Sample Sizes**  
 - Run experiments for different numbers of samples per thread

2. **Generate Log File Name**  
 - Create a log file name based on:
   - Number of threads
   - Number of samples per thread

3. **Prepare for Computation**  
 - Initialize data structures to store results for all experiments

4. **Start Resource Monitoring Thread**  
 - Create a background thread to monitor CPU and RAM usage for all threads (vCPU)

5. **Perform Parallel Computation**  
 - Use `ThreadPoolExecutor` with pool size equal to `NUM_THREADS`  
 - Each worker thread executes the π estimation function

6. **Estimate Pi per Thread**  
 - Collect results from all threads and compute per-thread π estimates

7. **Calculate Average CPU/RAM Usage**  
 - Compute the mean CPU and RAM usage over the measurement interval (0.5 s) for the experiment

8. **Aggregate Experiment Results**  
 - Collect results from all repetitions (`NUM_EXPERIMENTS`)  
 - Compute summary statistics (e.g., average π estimate, resource utilization)

9. **Log Experiment Results**  
 - Write all collected data to log files for further analysis
