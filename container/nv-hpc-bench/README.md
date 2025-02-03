# NVIDIA HPC Benchmarks container with CXI

Self-sufficient container image with pre-compiled [NVIDIA HPC Benchmarks](https://developer.nvidia.com/nvidia-hpc-benchmarks-downloads) 24.09, installed on top of OpenMPI 5 with CUDA and CXI (i.e. HPE Slingshot) support.

Still largely work-in-progress: multi-rank runs fail due to segfault when NVSHMEM invokes libfabric, e.g.:
```
[clariden][amadonna@clariden-ln001 ~]$ srun -N2 --ntasks-per-node=1 --mpi=pmix --environment=nv-hpc-bench ./hpl.sh --dat /nvidia_hpc_benchmarks/hpl-linux-aarch64-gpu/sample-dat/HPL-GH-2GPUs.dat

[... script log messages ...]

[nid006679:214972] *** Process received signal ***
[nid006679:214972] Signal: Segmentation fault (11)
[nid006679:214972] Signal code: Address not mapped (1)
[nid006679:214972] Failing at address: 0x18
[nid006679:214972] [ 0] linux-vdso.so.1(__kernel_rt_sigreturn+0x0)[0x400022f407dc]
[nid006679:214972] [ 1] /lib/libfabric.so.1(fi_dupinfo+0x28c)[0x40005edf838c]
[nid006679:214972] [ 2] /lib/libfabric.so.1(fi_dupinfo+0x160)[0x40005edfed04]
[nid006679:214972] [ 3] /lib/libfabric.so.1(fi_getinfo+0x38)[0x40005edfede8]
[nid006679:214972] [ 4] /nvidia_hpc_benchmarks/lib/nvshmem/nvshmem_transport_libfabric.so.2(nvshmemt_init+0xef8)[0x4002b36ed500]
[nid006679:214972] [ 5] /nvidia_hpc_benchmarks/lib/nvshmem/libnvshmem_host.so.2(+0xf52dc)[0x40005b5752dc]
[nid006679:214972] [ 6] /nvidia_hpc_benchmarks/lib/nvshmem/libnvshmem_host.so.2(+0xfbd18)[0x40005b57bd18]
[nid006679:214972] [ 7] /nvidia_hpc_benchmarks/lib/nvshmem/libnvshmem_host.so.2(+0xfd83c)[0x40005b57d83c]
[nid006679:214972] [ 8] /nvidia_hpc_benchmarks/lib/nvshmem/libnvshmem_host.so.2(nvshmemid_hostlib_init_attr+0x118)[0x40005b57dd78]
[nid006679:214972] [ 9] /nvidia_hpc_benchmarks/hpl-linux-aarch64-gpu/xhpl[0x48f8c8]
[nid006679:214972] [10] /nvidia_hpc_benchmarks/hpl-linux-aarch64-gpu/xhpl[0x486980]
[nid006679:214972] [11] /nvidia_hpc_benchmarks/hpl-linux-aarch64-gpu/xhpl[0x45d410]
[nid006679:214972] [12] /usr/lib/aarch64-linux-gnu/libc.so.6(+0x284c4)[0x40005e8284c4]
[nid006679:214972] [13] /usr/lib/aarch64-linux-gnu/libc.so.6(__libc_start_main+0x98)[0x40005e828598]
[nid006679:214972] [14] /nvidia_hpc_benchmarks/hpl-linux-aarch64-gpu/xhpl[0x45e110]
[nid006679:214972] *** End of error message ***

...
```
