# OSU Micro-Benchmarks container with CXI

Self-sufficient container image with OSU Micro-Benchmarks built on OpenMPI 5 with CUDA and CXI (i.e. HPE Slingshot) support.

## Notes (see also EDF TOML for reference)
- The image does not require hooks to inject a custom CXI stack from the host
- Interaction with host PMIx needs to be understood better: environment variables for PSEC and GDS parameters have to be set on Alps to prevent warnings and crashes due to missing components
- Running a container reuires a hook for bridging the PMIx interface between host and container
- The image includes also the libfabric LINKx provider for experimentation.

## Examples

- Point-to-point latency
```
[clariden][amadonna@clariden-ln001 ~]$ srun --mpi=pmix -N2 --environment=omb-cxi ./pt2pt/osu_latency

# OSU MPI Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                       3.34
2                       3.32
4                       3.34
8                       3.34
16                      3.34
32                      3.34
64                      3.35
128                     4.33
256                     4.32
512                     4.40
1024                    4.51
2048                    4.65
4096                    5.14
8192                   10.90
16384                  11.54
32768                  12.33
65536                  13.68
131072                 16.41
262144                 21.81
524288                 32.59
1048576                54.18
2097152                97.36
4194304               183.66
```

- Point-to-point latency with GPU device buffers
```
[clariden][amadonna@clariden-ln001 ~]$ srun --mpi=pmix -N2 --environment=omb-cxi ./pt2pt/osu_latency D D

# OSU MPI-CUDA Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                      19.73
2                      19.50
4                      19.59
8                      19.79
16                     19.79
32                     19.80
64                     19.96
128                    20.67
256                    11.79
512                    11.73
1024                   11.70
2048                   12.49
4096                   13.13
8192                   17.92
16384                  18.67
32768                  19.50
65536                  20.84
131072                 23.54
262144                 29.19
524288                 40.20
1048576                61.91
2097152               105.13
4194304               191.61
```

- All-to-all collective latency, 8 ranks over 2 nodes
```
[clariden][amadonna@clariden-ln001 ~]$ srun --mpi=pmix -N2 --ntasks-per-node=4 --environment=omb-cxi ./collective/osu_alltoall

# OSU MPI All-to-All Personalized Exchange Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                       7.02
2                       7.01
4                       7.11
8                       7.08
16                      7.06
32                      7.22
64                      7.27
128                     7.45
256                     8.03
512                     8.16
1024                    8.41
2048                    8.81
4096                    9.16
8192                   14.79
16384                  17.31
32768                  23.07
65536                  35.82
131072                 61.01
262144                102.08
524288                186.78
1048576               370.06
```

## Known issues
- Collective tests with GPU device buffers still result in a segmentation fault

## TODO

- XPMEM support
- GDRCopy support
- EFA provider (requires rdma-core) to make the image usable on AWS 
