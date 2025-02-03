# Cray Network Stack

A user guide for installing and using the networking software stack on HPE Cray-EX systems, based on CSCS' experiences.

The network stack in question is the user land stack: includeing libcxi, libfabric, OpenMPI, MPICH and NCCL.
The whole stack is now open source, now that HPE have generously 

The open source products are still a bit raw, without versioning and requiring patches to work around compilation and runtime issues.
The aim of this work is to document how to set up a stable software stack that is production ready, and drive collaboration between HPE and customers towards a robust, well-defined implementation.

