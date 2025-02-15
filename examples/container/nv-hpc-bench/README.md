# NVIDIA HPC Benchmarks container with CXI

Self-sufficient container image with pre-compiled [NVIDIA HPC Benchmarks](https://developer.nvidia.com/nvidia-hpc-benchmarks-downloads) 24.09, installed on top of OpenMPI 5 with CUDA and CXI (i.e. HPE Slingshot) support.

## Notes

- The image does not require hooks to inject a custom CXI stack from the host
- Running a container reuires a hook for bridging the PMIx interface between host and container
- The image installs UCX from the package manager so that OpenSHMEM can be built. OpenSHMEM is in turn a dependency for NVSHMEM
- NVSHMEM is rebuilt from source because the build bundled with the benchmarks incurs in a segmentation fault when using its libfabric backend; NVSHMEM 2.11.0 is used instead of more recent 3.x releases to respect the dynamic linking by benchmark executables
- The image includes also the libfabric LINKx provider for experimentation.


## Examples

- HPL, 8 nodes, 32 GPUs, sample DAT included with benchmarks
```
[clariden][amadonna@clariden-ln001 ~]$ srun -N8 --ntasks-per-node=4 --mpi=pmix --environment=nv-hpc-bench ./hpl.sh --dat /nvidia_hpc_benchmarks/hpl-linux-aarch64-gpu/sample-dat/HPL-GH-32GPUs.dat

[... HPL output ...]

================================================================================
T/V                N    NB     P     Q         Time          Gflops (   per GPU)
--------------------------------------------------------------------------------
WC0           571392  1024     4     8        98.05       1.268e+06 ( 3.964e+04)

HPL_pdgesv() start time Sat Feb 15 23:36:43 2025
HPL_pdgesv() end time   Sat Feb 15 23:38:21 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   0.000343347104 ...... PASSED
||Ax-b||_oo  . . . . . . . . . . . . . . . . . = 0.0000000071401839
||A||_oo . . . . . . . . . . . . . . . . . . . = 143360.5125037249526940
||x||_oo . . . . . . . . . . . . . . . . . . . = 2.2866556578093529
||b||_oo . . . . . . . . . . . . . . . . . . . = 0.9866723043615038
================================================================================

Finished      1 tests with the following results:
              1 tests completed and passed residual checks,
              0 tests completed and failed residual checks,
              0 tests skipped because of illegal input values.
--------------------------------------------------------------------------------

End of Tests.
================================================================================
```
