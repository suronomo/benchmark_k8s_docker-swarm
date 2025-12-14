# FIO I/O Benchmark – step-by-step instructions

This document describes the algorithm for measuring disk I/O performance using FIO tests. The implementation covers both sequential and random read/write operations across multiple block sizes and thread counts, and saves all output logs to **/fio/logs** for later analysis.

## Sequential I/O Tests

1. Create working directory for logs: `/fio/logs`
2. For each combination of block size and thread count, run sequential write/read test:
   - 32kB × 32 threads
   - 32kB × 64 threads
   - 64kB × 32 threads
   - 64kB × 64 threads
   - 1MB × 32 threads
   - 1MB × 64 threads
   - 32MB × 4 threads
   - 32MB × 8 threads
   - 128MB × 4 threads
3. After each test, use `sync` to flush all pending writes to disk
4. Remove old test files before starting the next test
5. Save output logs for each test to `/fio/logs`

---

## Random I/O Tests

1. For each combination of block size and thread count, run random write/read test:
   - 32kB × 32 threads
   - 32kB × 64 threads
   - 64kB × 32 threads
   - 64kB × 64 threads
   - 1MB × 32 threads
   - 1MB × 64 threads
   - 32MB × 4 threads
   - 32MB × 8 threads
   - 128MB × 4 threads
2. After each test, use `sync` to flush all pending writes to disk
3. Remove old test files before starting the next test
4. Save output logs for each test to `/fio/logs`

---

## Notes

- `sync` ensures all pending I/O operations are written to disk before deleting old files  
- All logs are saved under `/fio/logs` for later analysis
<details>
  <summary>Example FIO log: 1MBx32 random test (Kubernetes — 2 Replicas, 4 vCPUs)</summary>

```bash
rand-test_1MBx32: (g=0): rw=rw, bs=(R) 1024KiB-1024KiB, (W) 1024KiB-1024KiB, (T) 1024KiB-1024KiB, ioengine=libaio, iodepth=1

fio-3.36
Starting 32 processes
rand-test_1MBx32: Laying out IO file (1 file / 1MiB)
rand-test_1MBx32: Laying out IO file (1 file / 1MiB)
[...]

rand-test_1MBx32: (groupid=0, jobs=32): err= 0: pid=560: Sat Sep  7 21:29:01 2024
  read: IOPS=294, BW=294MiB/s (308MB/s)(10.0MiB/34msec)
    [...]
     lat (usec): min=5214, max=33561, avg=17160.85, stdev=10032.21
    [...]
  write: IOPS=687, BW=688MiB/s (721MB/s)(22.0MiB/32msec); 0 zone resets
    [...]
     lat (usec): min=862, max=31433, avg=3002.12, stdev=6601.32
    [...]
  
Run status group 0 (all jobs):
  READ: bw=294MiB/s (308MB/s), 294MiB/s-294MiB/s (308MB/s-308MB/s), io=10.0MiB (10.5MB), run=34-34msec
  WRITE: bw=688MiB/s (721MB/s), 688MiB/s-688MiB/s (721MB/s-721MB/s), io=22.0MiB (23.1MB), run=32-32msec