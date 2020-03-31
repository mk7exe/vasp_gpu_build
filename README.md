# Building VASP with GPU port

I had a hard time building VASP with its GPU port and making it work after installation. I am still doing try-and-error to enhance the performance of the GPU port. I will share my experience and struggles here in a hope to help out those facing the same issues as I did. I understand that the issues and solutions I explain here are probably system specific. So, please pay attention to types and versions of software packages while you are reading this.

## System specifications
- CPUS: 2 x Intel(R) Xeon(R) Platinum 8160 CPU @ 2.10GHz
- GPUs: 1 x NVIDIA Corporation GP100GL [Quadro GP100]
- OS: Red Hat Enterprise Linux Workstation release 7.7 (Maipo)
- Compiler: Intel Parallel Studio (different versions - see below)
- CUDA: cuda 10.2.89_440.33.01
- VASP: vasp 6.1.0


## Step 1: Download VASP
I downloaded vasp from their website and untared it into /opt/VASP folder. I have the habit to bulid all my simulation software in /opt to make them accessible to all users. I have built different versions of vasp in the /opt/VASP folder. 

``` bash
tar -xvf Downloads/vasp.6.1.0.tgz -C /opt/VASP/  
```

Issue 1: Intel compiler version
I installed the latest version of the Intel Parallel Studio which is 2020. 
