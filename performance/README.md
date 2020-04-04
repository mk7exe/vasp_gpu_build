# VASP benchmarks

Here I share my experience with benchmarking and tuning VASP performance. My system specifications are as follows

## System specifications
- CPUS: 2 x Intel(R) Xeon(R) Platinum 8160 CPU @ 2.10GHz
- GPUs: 1 x NVIDIA Corporation GP100GL [Quadro GP100]
- OS: Red Hat Enterprise Linux Workstation release 7.7 (Maipo)
- Compiler: Intel Parallel Studio (different versions - see below)
- CUDA: cuda 10.2.89_440.33.01
- VASP: vasp 6.1.0

## CPU performance
I made both CPU and GPU versions of VASP using the Intel compilers and mpi. The benchmark examples I used were those provided y NVIDIA in their benchmarks (https://www.nvidia.com/en-us/data-center/gpu-accelerated-applications/vasp/). To test the performance of the CPU version of VASP, I simulated the siHugeShort using different number of cores and different values for NCORE tag in the INCAR file. According to VASP manual, ![](http://latex.codecogs.com/gif.latex?NCORE%20%3D%20core%20%5C%23/NPAR) and ![](http://latex.codecogs.com/gif.latex?NPAR%20%5Capprox%20%5Csqrt%7Bcore%5C%23%7D). Note that my machine has two CPUs with 24 physical cores at each CPU (48 total). This increases to 96 with hyperthreading. It is well known that VASP does not do well with hyperthreading and if I increase the number of MPI tasks to more than 48, the performance will decline (I actually tested this). So, I set the maximum of MPI tasks to 48. 

From my experience, most of the simulation codes performances decline with enabling hyperthreading. I disabled hyperthreading to see its effect. The ideal way to disable hyperthreading is through BIOS. Since I work with this machine remotely and I don't have access to the BIOS, I disabled hyperthreading at runtime through the OS. I found a good and clean description on how to do this in this page: https://www.golinuxhub.com/2018/01/how-to-disable-or-enable-hyper.html

| MPI ranks | NCORE | HP ON Elapsed time (s) | HP OFF Elapsed time (s) |
| ------------- | ------------- | ------------- | ------------- |
| 24	| 1	| 415.536 | 423.549 |
| 24	| 2	| 416.014 | 415.693 |
| 24	| 4	| 451.344 | 460.652 |
| 24	| 6	| 427.872 | 429.345 |
| 24	| 8	| 421.651 | 426.422 |
| 48	| 1	| 376.486 | 387.806 |
| **48**	| **2**	| **344.153** | **352.782** |
| 48	| 4	| 376.102 | 372.804 |
| 48	| 6	| 357.160 | 359.338 |
| 48	| 8	| 433.305 | 433.711 |

I did not see the huge effect in using NCORE. In fact, I got the best performance with NCORE = 2 which is not the recommended setting by VASP (VASP would ask you to use NCORE = 4). Turning hyperthreading on and off did not have major effect on the performance either. These performances correspond to standard version of vasp (vasp_std) which is comparable with the GPU performance since GPU version does not have a gamma-only version. For this particular system which is a big supercell, we can use the gamma only version of the vasp (vasp_gamma) executable (because k-space is only sampled at the gamma point). I used the vasp_gam to simulate the same system, and elapsed times for 48 mpi ranks are shown below.

| MPI ranks | NCORE | vasp_std Elapsed time (s) | vasp_gam Elapsed time (s) |
| ------------- | ------------- | ------------- | ------------- |
| 24	| 1	| 415.536 | 269.333 |
| 24	| 2	| 416.014 | 258.476 |
| 24	| 4	| 451.344 | 283.786 |
| 24	| 6	| 427.872 | 275.124 |
| 24	| 8	| 421.651 | 332.854 |
| 48	| 1	| 376.486 | 252.763 |
| **48**	| **2**	| **344.153** | **223.877** |
| 48	| 4	| 376.102 | 235.965 |
| 48	| 6	| 357.160 | 232.462 |
| 48	| 8	| 433.305 | 281.581 |

## GPU performance
I only have one GPU in this machine so, most of the adjustments suggested by NVIDIA is not relevant to my case. I ran the gpu version of the vasp using 1 to 12 MPI ranks. I could not go higher because any higher number of MPI ranks gave me segmentation fault. I guess this is a memory error that can be avoided if I had more GPUs. Increasing the number of MPI ranks did not enhance the performance anyway, so I am not worried about this for now. Note that NCORE is always equal to 1 when using vasp_gpu.

| MPI ranks | Elapsed time (s) |
| ------------- | ------------- |
| 1 | 327.187 |
| 2 | 390.638 |
| 3 | 413.490 |
| 4 | 465.434 |
| 5 | 468.927 |
| 6 | 454.476 |
| 12 | 700.242 |

The performance of the GPU version on a single CPU is comparable to the best CPU performance (48 cores). Obviously, increasing the number of MPI ranks does not enhance the elapsed time but makes the performance worth. 
