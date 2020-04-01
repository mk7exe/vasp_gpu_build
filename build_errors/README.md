# Building VASP with GPU port

I had a hard time building VASP with its GPU port and making it work after installation. I am still doing try-and-error to enhance the performance of the GPU port. I will share my experience and struggles here in a hope to help out those facing the same issues as I did. I understand that the issues and solutions I explain here are probably system specific. So, please pay attention to types and versions of software packages while you are reading this.

## System specifications
- CPUS: 2 x Intel(R) Xeon(R) Platinum 8160 CPU @ 2.10GHz
- GPUs: 1 x NVIDIA Corporation GP100GL [Quadro GP100]
- OS: Red Hat Enterprise Linux Workstation release 7.7 (Maipo)
- Compiler: Intel Parallel Studio (different versions - see below)
- CUDA: cuda 10.2.89_440.33.01
- VASP: vasp 6.1.0


## Download VASP
I downloaded vasp from their website and untared it into /opt/VASP folder. I have the habit to bulid all my simulation software in /opt to make them accessible to all users. I have built different versions of vasp in the /opt/VASP folder. 

``` bash
tar -xvf Downloads/vasp.6.1.0.tgz -C /opt/VASP/  
```

## The first try
Obviusly, the first (which obviuosly didn't go right!) should be following the steps given in the vasp wiki. The instruction is pretty simple. and if you are lucky, it would work for you. Here is the instruction:

Copy one of the makefile.include.* files in root/arch to root/makefile.includ"e. Take one that most closely reflects your system (hopefully). For instance, on a linux box with the Intel Composer suite:

``` bash
cp arch/makefile.include.linux_intel  ./makefile.include
```

When you've finished setting up makefile.include, you can build different VASP executables:

``` bash
make std # standard (CPU) 
make gam # gamma only (CPU)
make ncl # non-collinear (CPU)
make gpu # standard (GPU)
make gpu_ncl # non-collinear (GPU)
```

At the time that I am writing this post, they do not offer a CUDA-GPU port of the gamma-only version yet. I first tried to build the CPU versions using the Intel compilers available in Intel Parallel Studio 2020. This is the latest version of this software package available now and I had it installed on my machine at /opt/intel (default path). Setting the enviornmental variables for Intel compiler is very easy. You just need to use the bash file that comes with the software.

### Building the CPU version
1. set the enviornmental variables for the compiler:

``` bash
source /opt/intel/bin/compilervars.sh intel64
```

Note: Do not source the psxevars.sh file at the parallel_studio_xe_[version] folder. This file is good for runtime but at the compile time it won't work and VASP make will end with an error complaining about not finding some mpi liberaries. 

2. build all the CPU-version executables:

``` bash
make all
```

Note that VASP does not support parallel building. So, make sure not to add -j option to make. This is really annoying because it makes the vasp built so time consuming. After finishing building the CPU versions, I tried to build the GPU version by

``` bash
make gpu
```

But it didn't work. This was the first error I faced in building and working with the GPU version of VASP. From now on, I will mention issues in the order that I faced them duing building VASP GPU port.

## VASP GPU port build errors 

### Issue 1: Intel Compiler Version
The first error I got during building VASP GPU port was this:

``` bash
/usr/local/cuda//include/crt/host_config.h(110): error: #error directive: -- unsupported ICC configuration! Only ICC 15.0, ICC 16.0, ICC 17.0, ICC 18.0 and ICC 19.0 on Linux x86_64 are supported!
  #error -- unsupported ICC configuration! Only ICC 15.0, ICC 16.0, ICC 17.0, ICC 18.0 and ICC 19.0 on Linux x86_64 are supported!
   ^
```

This is prety self explanetory! My Intel compiler and CUDA are the latest versions available at the moment. CUDA is complaining about my icc version (which is 19.1) and says it is too new for it! From what I read in Internet, when CUDA gives you such as error, it really means it. So do not mess around with header files or anything else to surpass the error. Just do what it asks you to do: use older version of icc. Fortunately, For whatever reason I had the installation file for an older version of Intel Parallel Studio (2019-update4) on my machine. So, I built the older version at /opt/intel/2019-update4 and started over. To be consistent, I also rebuilt the CPU-versions with the new compiler:

``` bash
make veryclean
rm bin/*
source /opt/intel/2019-update4/bin/compilervars.sh intel64
make all
make gpu
```







