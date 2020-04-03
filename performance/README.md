# VASP benchmarks

Here I share my epxperience with benchmarking and tunning VASP performance. My system specifications are as follows

## System specifications
- CPUS: 2 x Intel(R) Xeon(R) Platinum 8160 CPU @ 2.10GHz
- GPUs: 1 x NVIDIA Corporation GP100GL [Quadro GP100]
- OS: Red Hat Enterprise Linux Workstation release 7.7 (Maipo)
- Compiler: Intel Parallel Studio (different versions - see below)
- CUDA: cuda 10.2.89_440.33.01
- VASP: vasp 6.1.0

## CPU performance
I made both CPU and GPU versions of VASP using the Intel compilers and mpi. The benchmark examples I used were those provided y NVIDIA in their benchmarks (https://www.nvidia.com/en-us/data-center/gpu-accelerated-applications/vasp/). To test the performance of the CPU version of VASP, I simulated the siHugeShort using different number of cores and different values for NCORE tag in the INCAR file. According to VASP manual, ![](http://latex.codecogs.com/gif.latex?NCORE%20%3D%20core%20%5C%23/NPAR) and ![](http://latex.codecogs.com/gif.latex?NPAR%20%5Capprox%20%5Csqrt%7Bcore%5C%23%7D). Note that my machine has two CPUs with 24 physical cores at each CPU (48 total). This increases to 96 with hyperthreading. It is well known that VASP does not do well with hyperthreading and if I icrease the number of MPI tasks to more than 48, the performance would decline (I actually tested this). So, I set the maximum of MPI tasks to 48. 

From my experience, most of the simulation codes performances decline with enabeling hyperthreading. I disabled thyperthreading to see its effect. The ideal way to disable hyperthreading is through BIOS. Since I work with this machine remotely and I don't have access to the BIOS, I disabled hyperthreading at runtime through the OS. I found a good and clean discription on how to do this in this page: https://www.golinuxhub.com/2018/01/how-to-disable-or-enable-hyper.html

| # of cores | NCORE | Elapsed time (s) | Elapsed time (s) |
|  |  | (HT ON) | (HT OFF) |
| ------------- | ------------- | ------------- | ------------- |
| 24	| 4	| 457.335 | 459.687 |
| 24	| 5	| 362.902 | 362.912 |
| 24	| 6	| 427.872 | 459.687 |
| 48	| 4	| 370.835 | 377.093 |
| **48**	| **5**	| **324.228** | **331.350** |
| 48	| 6	| 357.120 | 366.127 |
| 48	| 7	| 335.949 | 328.042 |

The best performance was achieved by 48 cores and NCORE = 5. Turning hyperthreading on and off did not have major effect on the performance. 

## GPU performance
I only have one GPU in this machne so, most of the adjustments suggested by NVIDIA is not relevant to my case. I ran the gpu version of the vasp using 1 to 12 MPI ranks. I could not go higher because any higher number of MPI ranks gave me segmentation fault. I guess this is a memory error that can be avoided if I had more GPUs. Increasing the number of MPI ranks did not enhance the performance anyway, so I am not worried about that for now. 

