# VASP benchmarks

Here I share my epxperience with benchmarking and tunning VASP performance. My system specifications are as follows

## System specifications
- CPUS: 2 x Intel(R) Xeon(R) Platinum 8160 CPU @ 2.10GHz
- GPUs: 1 x NVIDIA Corporation GP100GL [Quadro GP100]
- OS: Red Hat Enterprise Linux Workstation release 7.7 (Maipo)
- Compiler: Intel Parallel Studio (different versions - see below)
- CUDA: cuda 10.2.89_440.33.01
- VASP: vasp 6.1.0

## CPU version
I made both CPU and GPU versions of VASP using the Intel compilers and mpi. The benchmark examples I used were those provided y NVIDIA in their benchmarks (https://www.nvidia.com/en-us/data-center/gpu-accelerated-applications/vasp/). To test the performance of the CPU version of VASP, I simulated the siHugeShort using different number of cores and different values for NCORE tag in the INCAR file.  

| # of cores | NCORE | Elapsed time (s) |
| ------------- | ------------- | ------------- |
| 24	| 6	| 432.588 |
| 48	| 6	| 357.81 |
| **48** |	**7** |	**327.687** |
| 48 |	8 |	438.263 |
| 72 |	6 |	392.069 |
| 72 |	7 |	400.422 |
| 72 |	8 |	465.652 |
| 72 |	9 |	455.526 |
| 96 |	6 |	402.14 |
| 96 |	7 |	488.768 |
| 96 |	8 |	477.251 |
| 96 |	9	| 479.457 |

The best performance was achieved with 48 cores and NCORE=7. According to VASP manual, ![](http://latex.codecogs.com/gif.latex?NCORE%20%3D%20core%20%5C%23/NPAR) and ![](http://latex.codecogs.com/gif.latex?NPAR%20%5Capprox%20%5Csqrt%7Bcore%5C%23%7D). 
