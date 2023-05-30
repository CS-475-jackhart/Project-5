# Project-5
## Introduction
Monte Carlo simulation is used to determine the range of outcomes for a series of parameters, each of which has a probability distribution showing how likely each option is to happen. In this project, you will take a scenario and develop a Monte Carlo simulation of it, determining how likely a particular output is to happen.

Clearly, this is very parallelizable -- it is the same computation being run on many permutations of the input parameters. You will run this with CUDA, testing it on different numbers of Monte Carlo trials and different numbers of threads-per-block.

## The Scenario

A top plate has 3 pins in it which must fit into a bottom plate with 3 holes in it. The pin locations are defined as :
```
Pin	X	Y	Radius
A	3.0	4.0	2.0
B	4.0	5.0	2.0
C	5.0	4.0	2.0
```

The holes were drilled simultaneously and so the drills rattled and vibrated. Thus, the location and sizes of the holes are approximate and are estimated to be:
```
Hole	X	Y	Radius
a	2.9 ± 0.2	4.1 ± 0.2	2.4 ± 0.3
b	4.1 ± 0.1	4.9 ± 0.1	2.4 ± 0.3
c	5.0 ± 0.1	4.0 ± 0.05	2.4 ± 0.3
```

Given all this uncertainty, what is the probability that the three pins will actually fit into the three holes?

## Requirements:
- Run this for at least four BLOCKSIZEs (i.e., the number of threads-per-block) of 8, 32, 64, and 128, combined with NUMTRIALS sizes of at least 1024, 4096, 16384, 65536, 262144, 1048576, and 2097152. You can use more if you want.
- Be sure each NUMTRIALS is a multiple of 1024. All of the ones above already are.
- Record timing for each combination. For performance, use some appropriate units like MegaTrials/Second.
- For this one, use CUDA timing, not OpenMP timing.
- Produce a rectangular table and two graphs:
  - Performance vs. NUMTRIALS with multiple curves of BLOCKSIZE
  - Performance vs. BLOCKSIZE with multiple curves of NUMTRIALS 
- Like Project #1 before, fill the arrays ahead of time with the random values. Send them to the GPU where they can be used as look-up tables.
- Chosing one of the runs, tell me what you think the actual probability is. Note: these numbers are different from those of Project #1, thus the probability is different too.
- Parallel Fraction doesn't apply here, so don't compute one. 

## Does a Pin Fit?
**d** is the distance from the (x,y) pin center to the (x,y) hole center:
`float d = Length( pinx-holex, piny-holey );`
A pin fits into its hole if: `d+pinRadius ≤ holeRadius`

All three pins must fit into their respective holes for this trial to be deemed a success. If even one pin does not fit, this trial is a failure.

## Developing this Project in Linux

On both rabbit and the DGX system, here is a working Makefile:
```makefile
CUDA_PATH	=	/usr/local/apps/cuda/cuda-10.1
CUDA_BIN_PATH	=	$(CUDA_PATH)/bin
CUDA_NVCC	=	$(CUDA_BIN_PATH)/nvcc

proj05:		proj05.cu
		$(CUDA_NVCC) -o proj05  proj05.cu
```

On both rabbit and the DGX system, here is a working bash script:
```
#!/bin/bash
for t in 1024 4096 16384 65536 262144 1048576 2097152
do
        for b in 8 32 64 128
        do
                /usr/local/apps/cuda/cuda-10.1/bin/nvcc -DNUMTRIALS=$t -DBLOCKSIZE=$b -o proj05  proj05.cu
                ./proj05
        done
done
```

You can (and should!) write scripts to run the benchmark combinations. If you want to pass in benchmark parameters, the -DNUMTRIALS=$t notation works fine in nvcc.

Before you use the DGX, do your preliminary development on the rabbit system. It is a lot friendlier because you don't have to run your program through a batch submission. Take your final performance numbers on the DGX!

You can also take your benchmark numbers on your own machine.

## Developing this Project in Visual Studio

Right-click on this link: VS05.zip, and save it somewhere. Then, un-zip it, and double-click on the VS.sln file. The skeleton code is in the file proj05.cu

If you are trying to run CUDA on your own Visual Studio system, make sure your machine has the CUDA toolkit installed. It is available here: https://developer.nvidia.com/cuda-downloads

Remember that CUDA is an NVIDIA-only product. It will not run on Intel or AMD GPUs.

## Your PDF Commentary

Your commentary PDF should:
- Tell what machine you ran this on
- What do you think this new probability is?
- Show the table and the two graphs
- What patterns are you seeing in the performance curves?
- Why do you think the patterns look this way?
- Why is a BLOCKSIZE of 8 so much worse than the others?
- How do these performance results compare with what you got in Project #1? Why?
- What does this mean for what you can do with GPU parallel computing? 
