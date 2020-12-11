[![By ULHPC](https://img.shields.io/badge/by-ULHPC-blue.svg)](https://hpc.uni.lu) [![Licence](https://img.shields.io/badge/license-GPL--3.0-blue.svg)](http://www.gnu.org/licenses/gpl-3.0.html) [![GitHub issues](https://img.shields.io/github/issues/ULHPC/tutorials.svg)](https://github.com/ULHPC/tutorials/issues/) [![](https://img.shields.io/badge/slides-PDF-red.svg)](https://github.com/ULHPC/tutorials/raw/devel/beginners/slides.pdf) [![Github](https://img.shields.io/badge/sources-github-green.svg)](https://github.com/ULHPC/tutorials/tree/devel/beginners/) [![Documentation Status](http://readthedocs.org/projects/ulhpc-tutorials/badge/?version=latest)](http://ulhpc-tutorials.readthedocs.io/en/latest/beginners/) [![GitHub forks](https://img.shields.io/github/stars/ULHPC/tutorials.svg?style=social&label=Star)](https://github.com/ULHPC/tutorials)

# Getting Started on the UL HPC platform

     Copyright (c) 2013-2019 UL HPC Team <hpc-sysadmins@uni.lu>

[![](https://github.com/ULHPC/tutorials/raw/devel/beginners/cover_slides.png)](https://github.com/ULHPC/tutorials/raw/devel/beginners/slides.pdf)


This tutorial will guide you through your first steps on the
[UL HPC platform](http://hpc.uni.lu).

Before proceeding:

* make sure you have an account (if not, follow [this procedure](https://hpc.uni.lu/get_an_account)), and an SSH client.
* take a look at the [quickstart guide](https://hpc.uni.lu/users/quickstart.html)
* ensure you operate from a Linux / Mac environment. Most commands below assumes running in a Terminal in this context. If you're running Windows, you can use MobaXterm, Putty tools etc. as described [on this page](https://hpc.uni.lu/users/docs/access/access_windows.html) yet it's probably better that you familiarize "natively" with Linux-based environment by having a Linux Virtual Machine (consider for that [VirtualBox](https://www.virtualbox.org/)) or [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/install-win10).

From a general perspective, the [Support page](https://hpc.uni.lu/users/docs/report_pbs.html) describes how to get help during your UL HPC usage.

**Convention**

In the below tutorial, you'll proposed terminal commands where the prompt is denoted by `$>`.

In general, we will prefix to precise the execution context (_i.e._ your laptop, a cluster frontend or a node). Remember that `#` character is a comment. Example:

		# This is a comment
		$> hostname

		(laptop)$> hostname         # executed from your personal laptop / workstation

		(access-iris)$> hostname    # executed from access server of the Iris cluster


## Platform overview.

You can find a brief overview of the platform with key characterization numbers [on this page](https://hpc.uni.lu/systems/overview.html).

The general organization of each cluster is depicted below:

![UL HPC clusters general organization](https://hpc.uni.lu/images/overview/clusters_general_organization.png)

Details on this organization can be found [here](https://hpc.uni.lu/systems/clusters.html#clusters-organization)

## Hands-On/SSH & UL HPC access


* [Access / SSH Tutorial](https://hpc.uni.lu/users/docs/access.html)

The way SSH handles the keys and the configuration files is illustrated in the following figure:

![SSH key management](https://hpc.uni.lu/images/docssh/schema.png)

In order to be able to login to the clusters, you have sent us through the Account request form the **public key** (i.e. `id_rsa.pub` or the **public key** as saved by MobaXterm/PuttY) you initially generated, enabling us to configure the `~/.ssh/authorized_keys` file of your account.


For more information about SSH, please refer to the [preliminaries](https://ulhpc-tutorials.readthedocs.io/en/latest/preliminaries/) section.


## Discovering, visualizing and reserving UL HPC resources

In the following sections, replace `<login>` in the proposed commands with you login on the platform (ex: `svarrette`).

### Step 1: the working environment

* [reference documentation](http://hpc.uni.lu/users/docs/env.html)

After a successful login onto one of the access node (see [Cluster Access](https://hpc.uni.lu/users/docs/access.html)), you end into your personal homedir `$HOME` which is shared over GPFS between the access node and the computing nodes.

Again, remember that your homedir is placed on __separate__ storage servers on each site (Iris, Gaia, Chaos, Grid5000), which __ARE NOT SYNCHRONIZED__: data synchronization between each of them remain at your own responsibility. We will see below that the UL HPC team prepared for you a script to facilitate the transfer of data between each site.

Otherwise, you have to be aware of at least two directories:

* `$HOME`: your home directory under NFS.
* `$SCRATCH`: a non-backed up area put if possible under Lustre for fast I/O operations

Your homedir is under a regular backup policy. Therefore you are asked to pay attention to your disk usage __and__ the number of files you store there.

* Estimate file space usage and summarize disk usage of each FILE, recursively for directories using the `ncdu` command:

		(access)$> ncdu

* You can get an overview of the quotas and your current disk usage with the following command:

		(access)$> df-ulhpc

* You shall also pay attention to the number of files in your home directory. You can count them as follows:

		(access)$> df-ulhpc -i


### Step 2: web monitoring interfaces

Each cluster offers a set of web services to monitor the platform usage:

* [Ganglia](https://hpc.uni.lu/iris/ganglia/), a scalable distributed monitoring system for high-performance computing systems such as clusters and Grids.
* [SLURM Web](https://access-iris.uni.lu/slurm/), a website that show the status of jobs and nodes with a nice graphical interface.

### Step 3: Reserving resources with Slurm

#### The basics

* [reference documentation](https://hpc.uni.lu/users/docs/slurm.html)

[Slurm](https://slurm.schedmd.com/) Slurm is an open source, fault-tolerant, and highly scalable cluster management and job scheduling system for large and small Linux clusters. It is used on Iris UL HPC cluster.

* It allocates exclusive or non-exclusive access to the resources (compute nodes) to users during a limited amount of time so that they can perform they work
* It provides a framework for starting, executing and monitoring work
* It arbitrates contention for resources by managing a queue of pending work.
* It permits to schedule jobs for users on the cluster resource

There are two types of jobs:

  * _interactive_: you get a shell on the first reserved node
  * _passive_: classical batch job where the script passed as argument to `sbatch` is executed

We will now see the basic commands of Slurm.

* Connect to **iris-cluster**. You can request resources in interactive mode:

		(access)$> srun -p interactive --qos debug --pty bash

  Notice that with no other parameters, srun gave you one resource for 1 hour. You were also directly connected to the node you reserved with an interactive shell.
  Now exit the reservation:

        (node)$> exit      # or CTRL-D

  When you run exit, you are disconnected and your reservation is terminated.

To avoid anticipated termination of your jobs in case of errors (terminal closed by mistake),
you can reserve and connect in two steps using the job id associated to your reservation.

* First run a passive job _i.e._ run a predefined command -- here `sleep 10d` to delay the execution for 10 days -- on the first reserved node:

		(access)$> sbatch --qos normal --wrap "sleep 10d"
		Submitted batch job 390

  You noticed that you received a job ID (in the above example: `390`), which you can later use to connect to the reserved resource(s):

        (access)$> srun -p interactive --qos debug --jobid 390 --pty bash # adapt the job ID accordingly ;)
		(node)$> ps aux | grep sleep
		cparisot 186342  0.0  0.0 107896   604 ?        S    17:58   0:00 sleep 1h
		cparisot 187197  0.0  0.0 112656   968 pts/0    S+   18:04   0:00 grep --color=auto sleep
		(node)$> exit             # or CTRL-D

**Question: At which moment the job `390` will end?**

a. after 10 days

b. after 1 hour

c. never, only when I'll delete the job

**Question: manipulate the `$SLURM_*` variables over the command-line to extract the following information, once connected to your job**

a. the list of hostnames where a core is reserved (one per line)
   * _hint_: `man echo`

b. number of reserved cores
   * _hint_: `search for the NPROCS variable`

c. number of reserved nodes
   * _hint_: `search for the NNODES variable`

d. number of cores reserved per node together with the node name (one per line)
   * Example of output:

            12 iris-11
            12 iris-15

   * _hint_: `NPROCS variable or NODELIST`


#### Job management

Normally, the previously run job is still running.

* You can check the status of your running jobs using `squeue` command:

		(access)$> squeue             # list all jobs
		(access)$> squeue -u cparisot # list all your jobs

  Then you can delete your job by running `scancel` command:

		(access)$> scancel 390


* You can see your system-level utilization (memory, I/O, energy) of a running job using `sstat $jobid`:

		(access)$> sstat 390

In all remaining examples of reservation in this section, remember to delete the reserved jobs afterwards (using `scancel` or `CTRL-D`)

You probably want to use more than one core, and you might want them for a different duration than one hour.

* Reserve interactively 4 cores in one task on one node, for 30 minutes (delete the job afterwards)

		(access)$> srun -p interactive --qos debug --time=0:30:0 -N 1 --ntasks-per-node=1 --cpus-per-task=4 --pty bash

* Reserve interactively 4 tasks (system processes) with 2 nodes for 30 minutes (delete the job afterwards)

		(access)$> srun -p interactive --qos debug --time=0:30:0 -N 2 --ntasks-per-node=4 --cpus-per-task=4 --pty bash

This command can also be written in a more compact way

        (access)$> si --time=0:30:0 -N2 -n4 -c2


* You can stop a waiting job from being scheduled and later, allow it to be scheduled:

		(access)$> scontrol hold $SLURM_JOB_ID
		(access)$> scontrol release $SLURM_JOB_ID


## Hands-on/Using modules

[Environment Modules](http://modules.sourceforge.net/) is a software package that allows us to provide a [multitude of applications and libraries in multiple versions](http://hpc.uni.lu/users/software/) on the UL HPC platform. The tool itself is used to manage environment variables such as `PATH`, `LD_LIBRARY_PATH` and `MANPATH`, enabling the easy loading and unloading of application/library profiles and their dependencies.

We will have multiple occasion to use modules in the other tutorials so there is nothing special we foresee here. You are just encouraged to read the following resources:

* [Introduction to Environment Modules by Wolfgang Baumann](https://www.hlrn.de/home/view/System3/ModulesUsage)
* [Modules tutorial @ NERSC](https://www.nersc.gov/users/software/nersc-user-environment/modules/)
* [UL HPC documentation on modules](https://hpc.uni.lu/users/docs/modules.html)


## Hands-on/Persistent Terminal Sessions using GNU Screen

[GNU Screen](http://www.gnu.org/software/screen/) is a tool to manage persistent terminal sessions.
It becomes interesting since you will probably end at some moment with the following  scenario:

> you frequently program and run computations on the UL HPC platform _i.e_ on a remote Linux/Unix computer, typically working in six different terminal logins to the access server from your office workstation, cranking up long-running computations that are still not finished and are outputting important information (calculation status or results), when you have 2 interactive jobs running... But it's time to catch the bus and/or the train to go back home.

Probably what you do in the above scenario is to

a. clear and shutdown all running terminal sessions

b. once at home when the kids are in bed, you're logging in again... And have to set up the whole environment again (six logins, 2 interactive jobs etc. )

c. repeat the following morning when you come back to the office.

Enter the long-existing and very simple, but totally indispensable [GNU screen](http://www.gnu.org/software/screen/) command. It has the ability to completely detach running processes from one terminal and reattach it intact (later) from a different terminal login.

### Pre-requisite: screen configuration file `~/.screenrc`

While not mandatory, we advise you to rely on our customized configuration file for screen [`.screenrc`](https://github.com/ULHPC/dotfiles/blob/master/screen/.screenrc) available on [Github](https://github.com/ULHPC/dotfiles/blob/master/screen/.screenrc).

Otherwise, simply clone the [ULHPC dotfile repository](https://github.com/ULHPC/dotfiles/) and make a symbolic link `~/.screenrc` targeting the file `screen/screenrc` of the repository.

### Basic commands

You can start a screen session (_i.e._ creates a single window with a shell in it) with the `screen` command.
Its main command-lines options are listed below:

* `screen`: start a new screen
* `screen -ls`: does not start screen, but prints a list of `pid.tty.host` strings identifying your current screen sessions.
* `screen -r`: resumes a detached screen session
* `screen -x`: attach to a not detached screen session. (Multi display mode _i.e._ when you and another user are trying to access the same session at the same time)


Once within a screen, you can invoke a screen command which consist of a "`CTRL + a`" sequence followed by one other character. The main commands are:

* `CTRL + a c`: (create) creates a new Screen window. The default Screen number is zero.
* `CTRL + a n`: (next) switches to the next window.
* `CTRL + a p`: (prev) switches to the previous window.
* `CTRL + a d`: (detach) detaches from a Screen
* `CTRL + a A`: (title) rename the current window
* `CTRL + a 0-9`: switches between windows 0 through 9.
* `CTRL + a k` or `CTRL + d`: (kill) destroy the current window
* `CTRL + a ?`: (help) display a list of all the command options available for Screen.

### Sample Usage on the UL HPC platform: Kernel compilation

We will illustrate the usage of GNU screen by performing a compilation of a recent linux kernel.

* start a new screen session

        (access)$> screen

* rename the screen window "Frontend" (using `CTRL+a A`)

* create a new window and rename it "Compile"
* within this new window, start a new interactive job over 1 node and 2 cores for 4 hours

		(access)$> srun -p interactive --qos debug --time 4:00:0 -N 1 -c 2 --pty bash

* detach from this screen (using `CTRL+a d`)
* kill your current SSH connection and your terminal
* re-open your terminal and connect back to the cluster frontend
* list your running screens:

		(access)$> screen -ls
		There is a screen on:
			9143.pts-0.access	(05/04/2014 11:29:43 PM) (Detached)
		1 Socket in /var/run/screen/S-svarrette.

* re-attach your previous screen session

		(access)$> screen -r      # OR screen -r 9143.pts-0.access (see above socket name)

* in the "Compile" windows, go to the temporary directory and download the Linux kernel sources

		(node)$> cd /tmp/
		(node)$> curl -O https://cdn.kernel.org/pub/linux/kernel/v4.x/linux-4.19.50.tar.xz

   **IMPORTANT** to ovoid overloading the **shared** file system with the many small files involves in the kernel compilation (_i.e._ NFS and/or Lustre), we will perform the compilation in the **local** file system, _i.e._ either in `/tmp` or (probably more efficient) in `/dev/shm` (_i.e_ in the RAM):

		(node)$> mkdir /dev/shm/PS1
		(node)$> cd /dev/shm/PS1
		(node)$> tar xf /tmp/linux-4.19.50.tar.xz
		(node)$> cd linux-4.19.50
		(node)$> make mrproper
		(node)$> make alldefconfig
		(node)$> make 2>&1 | tee /dev/shm/PS1/kernel_compile.log

* You can now detach from the screen and take a coffee

The last compilation command make use of `tee`, a nice tool which read from standard input and write to standard output _and_ files. This permits to save in a log file the message written in the standard output.

**Question: why using the `make 2>&1` sequence in the last command?**

**Question: why working in `/dev/shm` is more efficient?**


* Reattach from time to time to your screen to see the status of the compilation
* Your compilation is successful if it ends with the sequence:

		[...]
		Kernel: arch/x86/boot/bzImage is ready  (#2)

* Restart the compilation, this time using multiple cores and parallel jobs within the Makefile invocation (`-j` option of make)

		(node)$> make clean
		(node)$> time make -j $SLURM_CPUS_ON_NODE 2>&1 | tee /dev/shm/PS1/kernel_compile.2.log

The table below should convince you to always run `make` with the `-j` option whenever you can...

|   Context                          | time (`make`) | time (`make -j 16`) |
|------------------------------------|---------------|---------------------|
| Compilation in `/tmp`(HDD / chaos) | 4m6.656s      | 0m22.981s           |
| Compilation in `/tmp`(SSD / gaia)  | 3m52.895s     | 0m17.508s           |
| Compilation in `/dev/shm` (RAM)    | 3m11.649s     | 0m17.990s           |


* Use the [Ganglia](https://hpc.uni.lu/iris/ganglia/) interface to monitor the impact of the compilation process on the node your job is running on.
* Use the following system commands on the node during the compilation:

  * `htop`
  * `top`
  * `free -m`
  * `uptime`
  * `ps aux`

## Using a command line text editor

Before the next section, you must learn to use a text editor in command line.
We can recommend `nano` or `vim`: `nano` is very simple, `vim` is complex but very powerful.


### Nano

`$ nano <path/filename>`

* quit and save: `CTRL+x`
* save: `CTRL+o`
* highlight text: `Alt-a`
* Cut the highlighted text: `CTRL+k`
* Paste: `CTRL+u`


### Vim

[`vim <path/filename>`](https://vim.rtorr.com/)

There are 2 main modes:

* Edition mode: press `i` or `insert` once
* Command mode: press `ESC` once

Here is a short list of useful commands:

* save: `:w`
* save and quit: `:wq`
* quit and discard changes: `:q!`
* search: `/<pattern>`
* search & replace: `:%s/<pattern>/<replacement>/g`
* jump to line 100: `:100`
* highlight text: `CTRL+V`
* cut the highlighted text: `d`
* cut one line: `dd`
* paste: `p`
* undo: `u`

## Advanced section
### Using software modules

The UL HPC provides [environment modules](https://hpc.uni.lu/users/docs/modules.html) with the module command
to manage the user environment, e.g. changing the environment variables.

By loading appropriate environment modules, the user can select:

* compilers,
* libraries, e.g. the MPI library, or
* other third party software packages.

An exhaustive list of the available software is proposed [in this page](https://hpc.uni.lu/users/software/).

On a node, using an interactive jobs, you can:

* list all available softwares: `module avail`
* search for one software: `module spider <search terms>`
* "load" a software in your environment: `module load <module name>`
* list the currently loaded modules: `module list`
* clean your environment, unload everything: `module purge`


#### Matlab

1. Create a file named `fibonacci.m` in your home directory, copy-paste the following code in this file.
   This code will calculate the first N numbers of the Fibonacci sequence


        N=1000;
        fib=zeros(1,N);
        fib(1)=1;
        fib(2)=1;
        k=3;
        while k <= N
          fib(k)=fib(k-2)+fib(k-1);
          fprintf('%d\n',fib(k));
          pause(1);
          k=k+1;
        end


2. Create a new interactive job

3. Look for the `matlab` module using the command `module spider`

4. Load the module `base/MATLAB` using the command `module load`

5. Execute the code using matlab

        (node)$> matlab -nojvm -nodisplay -nosplash < path/to/fibonacci.m


#### R

1. Create a file named `fibonacci.R` in your home directory, copy-paste the following code in this file.
   This code will calculate the first N numbers of the Fibonacci sequence


        N <- 130
        fibvals <- numeric(N)
        fibvals[1] <- 1
        fibvals[2] <- 1
        for (i in 3:N) {
             fibvals[i] <- fibvals[i-1]+fibvals[i-2]
             print( fibvals[i], digits=22)
             Sys.sleep(1)
        }

2. Create a new interactive job

3. Look for the `R` module using the command `module spider`

3. Load the module `lang/R` using the command `module load`

4. Execute the code using R

        (node)$> Rscript path/to/fibonacci.R



### Compiling your code

In this section, we will learn to compile small "hello world" programs in different languages, using different compilers and toolchains.

#### C

Create a new file called `helloworld.c`, containing the source code of a simple "Hello World" program written in C.


        #include<stdio.h>

        int main()
        {
            printf("Hello, world!");
            return 0;
        }


First, compile the program using the "FOSS" toochain, containing the GNU C compiler

        (node)$> module load toolchain/foss
        (node)$> gcc helloworld.c -o helloworld

Then, compile the program using the Intel toolchain, containing the ICC compiler

        (node)$> module purge
        (node)$> module load toolchain/intel
        (node)$> icc helloworld.c -o helloworld

If you use Intel CPUs and ICC is available on the platform, it is advised to use ICC in order to produce optimized binaries and achieve better performance.


#### C++

**Question:** create a new file `helloworld.cpp` containing the following C++ source code,
compile the following program, using GNU C++ compiler (`g++` command), and the Intel compiler (`icpc` command).


        #include <iostream>

        int main() {
            std::cout << "Hello, world!" << std::endl;
        }



#### Fortran

**Question:** create a new file `helloworld.f` containing the following source code,
compile the following program, using the GNU Fortran compiler (`gfortran` command), and ICC (`ifortran` command).


        program hello
           print *, "Hello, World!"
        end program hello


Be careful, the 6 spaces at the beginning of each line are required



#### MPI

MPI is a programming interface that enables the communication between processes of a distributed memory system.

We will create a simple MPI program where the MPI process of rank 0 broadcasts an integer (42) to all the other processes.
Then, each process prints its rank, the total number of processes and the value he received from the process 0.

In your home directory, create a file `mpi_broadcast.c` and copy the following source code:


        #include <stdio.h>
        #include <mpi.h>
        #include <unistd.h>
        #include <time.h> /* for the work function only */

        int main (int argc, char *argv []) {
               char hostname[257];
               int size, rank;
               int i, pid;
               int bcast_value = 1;

               gethostname(hostname, sizeof hostname);
               MPI_Init(&argc, &argv);
               MPI_Comm_rank(MPI_COMM_WORLD, &rank);
               MPI_Comm_size(MPI_COMM_WORLD, &size);
               if (!rank) {
                    bcast_value = 42;
               }
               MPI_Bcast(&bcast_value,1 ,MPI_INT, 0, MPI_COMM_WORLD );
               printf("%s\t- %d - %d - %d\n", hostname, rank, size, bcast_value);
               fflush(stdout);

               MPI_Barrier(MPI_COMM_WORLD);
               MPI_Finalize();
               return 0;
        }

Reserve 2 tasks of 1 core on two distinct nodes with Slurm

        (access-iris)$> srun -p interactive --qos debug --time 1:00:0 -N 2 -n 2 -c 1 --pty bash

Load a toolchain and compile the code using `mpicc`

        (node)$> mpicc mpi_broadcast.c -o mpi_broadcast -lpthread

With Slurm, you can use the `srun` command. Create an interactive job, with 2 nodes (`-N 2`), and at least 2 tasks (`-n 2`).

        (node)$> srun -n $SLURM_NTASKS ~/mpi_broadcast


## Application - Object recognition with Tensorflow and Python Imageai (an embarassingly parralel case)

### Introduction

For many users, the typical usage of the HPC facilities is to execute a single program with various parameters, 
which translates into executing sequentially a big number of independent tasks.

On your local machine, you can just start your program 1000 times sequentially.
However, you will obtain better results if you parallelize the executions on a HPC Cluster.

In this section, we will apply an object recognition script to random images from a dataset, first sequentially, then in parallel, and we will compare the execution time.

* [ULHPC/launcher-scripts](https://github.com/ULHPC/launcher-scripts)
* [ULHPC/tutorials](https://github.com/ULHPC/tutorials)


### Connect to the cluster access node, and set-up the environment for this tutorial

Create a sub directory $SCRATCH/PS2, and work inside it

```bash
(access)$> mkdir $SCRATCH/PS2
(access)$> cd $SCRATCH/PS2
```

At the end of the tutorial, please remember to remove this directory.

### Step 0: Prepare the environment

In the following parts, we will assume that you are working in this directory.

Clone the repositories `ULHPC/tutorials`

    (access)$> git clone https://github.com/ULHPC/tutorials.git


In this exercise, we will process some images from the OpenImages V4 data set with an object recognition tools.

Create a file which contains the list of parameters (random list of images):

    (access)$> find /work/projects/bigdata_sets/OpenImages_V4/train/ -print | head -n 10000 | sort -R | head -n 50 | tail -n +2 > $SCRATCH/PS2/param_file

Download a pre-trained model for image recognition

    (access)$> cd $SCRATCH/PS2
    (access)$> wget https://github.com/OlafenwaMoses/ImageAI/releases/download/1.0/resnet50_coco_best_v2.0.1.h5

We will now prepare the software environment

    (access)$> srun -p interactive -N 1 --qos debug --pty bash -i

Load the default Python module

    (node) module load lang/Python

    (node) module list

Create a new python virtual env

    (node) cd $SCRATCH/PS2
    (node) virtualenv venv

Enable your newly created virtual env, and install the required modules inside.
This way, we will not pollute the home directory with the python modules installed in this exercise.

    (node) source venv/bin/activate

    (node) pip install tensorflow scipy opencv-python pillow matplotlib keras
    (node) pip install https://github.com/OlafenwaMoses/ImageAI/releases/download/2.0.3/imageai-2.0.3-py3-none-any.whl

    (node) exit




### Step 1: Naive sequential workflow

We will create a new "launcher script", which is basically a loop over all the files listed 

        (node)$> nano $SCRATCH/PS2/launcher_sequential.sh

```bash
#!/bin/bash -l

#SBATCH --time=0-00:30:00 # 30 minutes
#SBATCH --partition=batch # Use the batch partition reserved for passive jobs
#SBATCH --qos=normal
#SBATCH -J BADSerial      # Set the job name
#SBATCH -N 1              # 1 computing node
#SBATCH -c 1              # 1 core

module load lang/Python

cd $SCRATCH/PS2
source venv/bin/activate

OUTPUT_DIR=$SCRATCH/PS2/object_recognition_$SLURM_JOBID
mkdir -p $OUTPUT_DIR

for SRC_FILE in $(cat $SCRATCH/PS2/param_file) ; do
    python $SCRATCH/PS2/tutorials/beginners/scripts/FirstDetection.py $SRC_FILE $OUTPUT_DIR
done
```


Submit the job in passive mode with `sbatch`

    (access)$> sbatch $SCRATCH/PS2/launcher_sequential.sh


You can use the command `scontrol show job <JOBID>` to read all the details about your job:

    (access)$> scontrol show job 207001
    JobId=207001 JobName=BADSerial
       UserId=hcartiaux(5079) GroupId=clusterusers(666) MCS_label=N/A
       Priority=8791 Nice=0 Account=ulhpc QOS=normal
       JobState=RUNNING Reason=None Dependency=(null)
       Requeue=0 Restarts=0 BatchFlag=1 Reboot=0 ExitCode=0:0
       RunTime=00:00:23 TimeLimit=01:00:00 TimeMin=N/A
       SubmitTime=2018-11-23T10:01:04 EligibleTime=2018-11-23T10:01:04
       StartTime=2018-11-23T10:01:05 EndTime=2018-11-23T11:01:05 Deadline=N/A


And the command `sacct` to see the start and end date


    (access)$> sacct --format=start,end --j 207004
                  Start                 End
    ------------------- -------------------
    2018-11-23T10:01:20 2018-11-23T10:02:31
    2018-11-23T10:01:20 2018-11-23T10:02:31

In all cases, you can connect to a reserved node using the command `srun`
and check the status of the system using standard linux command (`free`, `top`, `htop`, etc)

    (access)$> srun -p interactive --qos debug --jobid <JOBID> --pty bash

During the execution, you can see the job in the queue with the command `squeue`:

    (access)$> squeue
             JOBID PARTITION     NAME             USER ST       TIME  NODES NODELIST(REASON)
            207001     batch BADSeria        hcartiaux  R       2:27      1 iris-110


Using the [system monitoring tool ganglia](https://hpc.uni.lu/iris/ganglia/), check the activity on your node.


### Step 2: Optimal method using GNU parallel (GNU Parallel)

We will create a new "launcher script", which uses GNU Parallel to execute 10 processes in parallel

        (node)$> nano $SCRATCH/PS2/launcher_parallel.sh

```bash
#!/bin/bash -l
#SBATCH --time=0-00:30:00 # 30 minutes
#SBATCH --partition=batch # Use the batch partition reserved for passive jobs
#SBATCH --qos=normal
#SBATCH -J ParallelExec   # Set the job name
#SBATCH -N 2              # 2 computing nodes
#SBATCH -n 10             # 10 tasks
#SBATCH -c 1              # 1 core per task

set -x
module load lang/Python

cd $SCRATCH/PS2
source venv/bin/activate

OUTPUT_DIR=$SCRATCH/PS2/object_recognition_$SLURM_JOBID
mkdir -p $OUTPUT_DIR

srun="srun --exclusive -N1 -n1"

parallel="parallel -j $SLURM_NTASKS --joblog runtask_$SLURM_JOBID.log --resume"

cat $SCRATCH/PS2/param_file | $parallel "$srun python $SCRATCH/PS2/tutorials/beginners/scripts/FirstDetection.py {} $OUTPUT_DIR"
```

Submit the job in passive mode with `sbatch`

    (access)$> sbatch $SCRATCH/PS2/launcher_parallel.sh


**Question**: compare and explain the execution time with both launchers:


* Naive workflow: time = ?
  ![CPU usage for the sequential workflow](https://github.com/ULHPC/tutorials/raw/devel/basic/sequential_jobs/images/chaos_ganglia_seq.png)

* Parallel workflow: time = ?
  ![CPU usage for the parallel workflow](https://github.com/ULHPC/tutorials/raw/devel/basic/sequential_jobs/images/chaos_ganglia_parallel.png)

**Bonus question**: transfer the generated files in `$SCRATCH/PS2/object_recognition_$SLURM_JOBID` to your laptop and visualize them
