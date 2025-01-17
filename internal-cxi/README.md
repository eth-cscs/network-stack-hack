# Attempt at uenv with internal CXI

This is an attempt at building a uenv with a mostly self contained stack that
comprises all `cxi`- and `cray`-specific libraries and, thus, eliminates the
dependency on the OS libraries. The goal is to eventually make it (almost)
fully self contained.

In particular, we are building the following libraries from source as part of
the `spack` stack:

- `libfabric@main`
- `libcxi@main`
- `cxi-driver@main`
- `cassini-headers@main`
- `nccl@2.23.4-1`
- `aws-ofi-nccl@master`
- `cray-mpich@8.1.30`

The stack is built using `stackinator` with `spack` checked out at commit
`develop-2025-01-05` (@TODO link github) and uses `gcc@13.2.0` for compilation.
As a consequence of having most packages as part of the stack, the cluster
configuration becomes much lighter, see also @TODO (file).

## Issues and Workarounds

Below we list the issues, which were encountered when building and testing the
stack, and the fixes and workarounds that were applied.

### libfabric

#### version: main

#### problems:
- seems to link to the system `cuda` library instead of the one contained in
  the stack (noticed by using `ldd`)
  - potential fix: add configure options forcing `libfabric` to dlopen `cuda`
    and `gdrcopy` instead - implemented in the repo overrides @TODO link file.
- segfaults at tear-down: crashes and coredumps at the end of `MPI` and/or
  `NCCL` test programs
  - observations: backtrace shows call to `/usr/lib64/libcurl.so` which should
    not be called as `libfabric` was configured with stack-internal `libcurl` .
    The internal `libcurl` is presumably used otherwise and the mixture of
    using both libraries causes the segfaults.
  - likely culprit: the `cxi` provider loads symbols from hard-coded `libcurl`
    path even though we are configuring `libfabric` with custom `libcurl`! See
    this link @TODO
  - workaround: use external, non-buildable `libcurl` (symlink to `/usr/lib64`
    if it is not there), see this file @TODO
  - the workaround unfortunately re-introduces a system dependency. If the
    upstream `libfabric` can be fixed, then the only remaining system
    dependencies are the default dependencies that `spack` currently requires,
    such as `libc`, `libm`, `libld` etc.
- depends on old `libfuse` version < 3, see below

### libfuse

#### version: <3 (2.9.9)

#### problems:
- does not compile on `aarch64` - fixed in version 3 and above
    - patch available at https://github.com/spack/spack/pull/47846
    - implemented in the repo @TODO link file


### xpmem

- not yet integrated into stack


### faiss

#### problems:
- note, this library is not strictly needed, but we document the workaround
  here just in case someone finds themselves wondering about the same issue
- `python.PythonPipBuilder` was introduced in `spack` recently instead of
  `spack.build_systems.python.PythonPipBuilder` but does not work properly
  - solution: revert to using `spack.build_systems.python.PythonPipBuilder`
  - implemented in the repo @TODO link file


### hdf5

- this library is not strictly needed
- we incorporate a tiny change to enable an additional variant which was
  requested by an HPE engineer
- we keep this here for the same reason as `faiss`


## Other Stack Components

We have added a bunch of other libraries to the stack for testing (for example
`pytorch`, see @TODO environments file). These other components do not need to
be built for trying this stack, of course. Feel free to remove all
non-essential libraries for quicker build times.


## Building

Configuring the stack is straight forward using `stackinator`:


    stack-config \
      -c /path/to/cache-config.yaml \
      -r /path/to/recipe \
      -b /dev/shm/stack-build \
      -s /path/to/alps-cluster-config/internal-cxi \
      -m /user-environment \
      --develop

Notice, that we need the `--develop` switch to be able to use recent `spack`
versions.

## Remaining Issues and Future Work

- integrate `xpmem`
  - `xpmem` consists of a user land library/headers and a kernel module
  - we probably have no choice but to use the existing kernel module for now
  - the user facing parts could potentially be built with `spack`, however,
    - the `spack` provided versions are not recent enough
    - from personal experience, it will likely require some patches for changes
      in the kernel headers as `xpmem` is poorly maintained
  - are there other options?
- integration of reframe/performance tests
  - since we can now independently choose the cray library versions, we should
    be thinking about testing compatibility and performance of versions on Alps
- the independence of the stack lends itself well to testing the interplay of
  `spack` stacks with container images. In particular:
  - can we build a container image which does not require hooks?
  - can we reuse the existing `uenv` pipelines/workflows to build container
    images for users to extend?

