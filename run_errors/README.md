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



