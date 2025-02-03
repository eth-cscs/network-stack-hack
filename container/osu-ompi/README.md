# OSU Micro-Benchmarks container with CXI - OpenMPI variant

Self-sufficient container image with OSU Micro-Benchmarks built on OpenMPI 5 with CUDA and CXI (i.e. HPE Slingshot) support.

## Notes (see also EDF TOML for reference)
- The image does not require hooks to inject a custom CXI stack from the host
- Interaction with host PMIx needs to be understood better: environment variables for PSEC and GDS parameters have to be set on Alps to prevent warnings and crashes due to missing components
- Running a container reuires a hook for bridging the PMIx interface between host and container
- The image includes also the libfabric LINKx provider for experimentation.

## Examples

- Point-to-point latency
```
[clariden][amadonna@clariden-ln001 ~]$ srun --mpi=pmix -N2 --environment=omb-cxi-ompi ./pt2pt/osu_latency

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
[clariden][amadonna@clariden-ln001 ~]$ srun --mpi=pmix -N2 --environment=omb-cxi-ompi ./pt2pt/osu_latency D D

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
[clariden][amadonna@clariden-ln001 ~]$ srun --mpi=pmix -N2 --ntasks-per-node=4 --environment=omb-cxi-ompi ./collective/osu_alltoall

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

- All-to-all collective latency with GPU buffers, 8 ranks over 2 nodes
```
[clariden][amadonna@clariden-ln001 ~]$ srun --mpi=pmix -N2 --ntasks-per-node=4 --environment=omb-cxi-ompi ./collective/osu_alltoall -d cuda

# OSU MPI-CUDA All-to-All Personalized Exchange Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                      20.66
2                      20.54
4                      20.17
8                      20.23
16                     20.30
32                     20.35
64                     20.31
128                    19.88
256                    20.34
512                    20.40
1024                   20.43
2048                   20.39
4096                   20.84
8192                   27.38
16384                  29.20
32768                  34.30
65536                  46.62
131072                 71.60
262144                111.45
524288                184.37
1048576               342.76
```

## Known issues and limitations
- The open source libfabric+CXI seems to be noticeably slower to detect CXI devices compared to the custom 1.15.x implementation provided by HPE. This results in longer startup times for jobs.


## TODO

- XPMEM support
