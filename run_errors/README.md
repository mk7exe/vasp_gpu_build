# Run errors and issues with CPU and GPU versions of VASP

After solving different problems, I finanlly built both CPU and GPU versions of vasp executables (See build_errors folder for more info). NVIDIA has a webpage for VASP GPU port in which it reports some benchmarks. I wanted to benchmark VASP CPU and GPU version on my machine for I downloaded VASP files used by NVIDIA (for details about my machines specifications refer to build_errors section). Here are the links to NVIDIA VASP webpage and repository containing VASP files:

NVIDIA VASP webpage: https://www.nvidia.com/en-us/data-center/gpu-accelerated-applications/vasp/
git repository including VASP exemplary input files for benchmarking: https://github.com/smaintz-nv/gpu-vasp-files

Since I used Intel compiler to build VASP, I need to set the enviormental variables related to the Parallel Studio every time I want to run VASP. This can easily be done by sourcing the psxevars.sh file in Parallel Studio installation path prior to running VASP. I made a simple bash file to set the Intel parameters and puting VASP executables in the PATH at the same time and put this file in my VASP directory:

start_vasp.sh file:

``` bash
#!/bin/bash
export PATH=$PATH:/opt/VASP/vasp.6.1.0/bin:/opt/VASP/vtstscripts-940
source /opt/intel/2019-update4/parallel_studio_xe_2019/psxevars.sh
```

Everytime I want to run VASP I just run the following command to set the environmental variables:

``` bash
source /opt/VASP/start_vasp.sh 
```

After setting everything and building POTCAR file (I used generatePOTCAR.sh file that comes with the examples), I did my first VASP run using the CPU version.

## Issue 1: CPU version 'Fatal error in PMPI_Init'
My first try was quite disappointing. I faced an strange error at the first step while I hadn't tried the GPU version yet! The whole error message was

``` bash
ort(1094799) on node 0 (rank 0 in comm 0): Fatal error in PMPI_Init: Other MPI error, error stack:
MPIR_Init_thread(666)......: 
MPID_Init(922).............: 
MPIDI_NM_mpi_init_hook(719): OFI addrinfo() failed (ofi_init.h:719:MPIDI_NM_mpi_init_hook:No data available)
```

There is not much in the error message to figure out what is going wrong. I googled the "Fatal error in PMPI_Init" and found this post in Intel Developer Zone forum:

[New MPI error with Intel 2019.1, unable to run MPI hello world](https://software.intel.com/en-us/forums/intel-clusters-and-hpc-technology/topic/799716)

There are bunch of complicate discussions in this post that I don't understand but it solved my problem. All I understood was that this is an issue related to new Intel MPI liberaries and can be fixed by setting an environmental variable to a specifict value (FI_PROVIDER=tcp) and apparently it does not have any effect on the mpi performance. So, I revised my start_vasp.sh file to include this enviromental variable as

``` bash
#!/bin/bash
export PATH=$PATH:/opt/VASP/vasp.6.1.0/bin:/opt/VASP/vtstscripts-940
source /opt/intel/2019-update4/parallel_studio_xe_2019/psxevars.sh
export FI_PROVIDER=tcp   
```

and it did the trick! o I ran the simulation using the CPU version of the executable by running following command.

``` bash
mpirun -n 48 vasp_std
```

You may see some warnings in vasp output when runing the CPU executable. This is mainly because INCAR files in the examples we downloaded are tunned for the GPU version of the VASP. These warnings don't affect the output and they basically tell you that you should do minimal changed in INCAR to enhance the CPU version performance. Some other warnings may be related to INCAR parameters. The warning messages are usually comprehenssive and clealy tell you how to revise the INCAR tags values.  
