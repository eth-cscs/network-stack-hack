# OSU Micro-Benchmarks container with CXI - MPICH variant

Self-sufficient container image with OSU Micro-Benchmarks built on MPICH 4.2.1 with CUDA and CXI (i.e. HPE Slingshot) support.

## Notes (see also EDF TOML for reference)
- The image does not require hooks to inject a custom CXI stack from the host
- The image includes also the libfabric LINKx provider for experimentation.

## Examples

- Point-to-point latency
```
[clariden][amadonna@clariden-ln001 ~]$ srun -N2 --mpi=pmi2 --environment=omb-cxi-mpich ./pt2pt/osu_latency

# OSU MPI Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                       3.19
2                       3.19
4                       3.15
8                       3.18
16                      3.19
32                      3.19
64                      3.27
128                     4.07
256                     4.09
512                     4.13
1024                    4.18
2048                    4.38
4096                    4.79
8192                    9.94
16384                  10.56
32768                  11.39
65536                  12.73
131072                 15.47
262144                 20.88
524288                 31.66
1048576                53.23
2097152                96.37
4194304               182.64
```

- Point-to-point latency with GPU device buffers
```
[clariden][amadonna@clariden-ln001 ~]$ srun -N2 --mpi=pmi2 --environment=omb-cxi-mpich ./pt2pt/osu_latency D D

# OSU MPI-CUDA Latency Test v7.5
# Datatype: MPI_CHAR.
# Size       Avg Latency(us)
1                      21.80
2                      21.94
4                      21.90
8                      22.01
16                     21.78
32                     22.04
64                     22.20
128                    23.19
256                    23.67
512                    23.21
1024                   22.98
2048                   23.60
4096                   25.23
8192                   32.46
16384                  33.61
32768                  35.80
65536                  40.14
131072                 50.44
262144                 70.56
524288                117.67
1048576               214.15
2097152               412.46
4194304               825.47
```

## Known issues and limitations
- The open source libfabric+CXI seems to be noticeably slower to detect CXI devices compared to the custom 1.15.x implementation provided by HPE. This results in longer startup times for jobs.
- Collective tests don't work yet, e.g.
  ```
  [clariden][amadonna@clariden-ln001 ~]$ srun -N2 --ntasks-per-node=4 --mpi=pmi2 --environment=omb-cxi-mpich ./collective/osu_alltoall
  Abort(73000719) on node 5: Fatal error in internal_Init: Other MPI error, error stack:
  internal_Init(48306)..........: MPI_Init(argc=0xffffeba3326c, argv=0xffffeba33260) failed
  MPII_Init_thread(265).........: 
  MPIR_init_comm_world(34)......: 
  MPIR_Comm_commit(800).........: 
  MPIR_Comm_commit_internal(585): 
  MPID_Comm_commit_pre_hook(151): 
  MPIDI_world_pre_init(633).....: 
  MPIDU_Init_shm_init(179)......: unable to allocate shared memory
  ...
  ```

## TODO

- XPMEM support
