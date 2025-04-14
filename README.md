# Understand and Run LINPACK

- [Understand and Run LINPACK](#understand-and-run-linpack)
  - [Understanding Intel's MKL LINPACK Process Flow](#understanding-intels-mkl-linpack-process-flow)
    - [runme\_intel\_dynamic](#runme_intel_dynamic)
      - [I\_MPI\_HYDRA\_HOST\_FILE](#i_mpi_hydra_host_file)
    - [mpirun](#mpirun)
    - [mpiexec.hydra](#mpiexechydra)
      - [But What the Heck Does that Mean](#but-what-the-heck-does-that-mean)
    - [runme\_intel64\_prv](#runme_intel64_prv)
    - [xhpl\_intel64\_dynamic](#xhpl_intel64_dynamic)

## Intel® oneAPI Math Kernel Library
La Intel® oneAPI Math Kernel Library (oneMKL) è una libreria matematica ottimizzata e altamente parallelizzata, progettata per applicazioni che richiedono massime prestazioni. Questa libreria offre interfacce di programmazione in linguaggi Fortran e C e include esempi di codice situati nella sottodirectory "examples" della directory di installazione.

Il benchmark LINPACK di Intel misura il tempo necessario per fattorizzare e risolvere un sistema di equazioni lineari denso e casuale (Ax=b) in precisione reale*8, convertendo questo tempo in un tasso di performance e verificando l'accuratezza dei risultati. Il benchmark utilizza la generazione di numeri casuali e il pivoting completo di riga per garantire l'accuratezza dei risultati.

La Intel® Distribution for LINPACK Benchmark è basata su modifiche e aggiunte al benchmark High-Performance LINPACK (HPL). Per utilizzare questo benchmark, è necessario conoscere l'uso di HPL. La distribuzione di Intel offre miglioramenti volti a rendere l'uso di HPL più conveniente e a migliorare le prestazioni tramite l'uso di configurazioni Intel® MPI (Message-Passing Interface).

Le versioni ottimizzate del benchmark LINPACK fornite da Intel facilitano l'ottenimento di risultati elevati sui sistemi basati su processori Intel autentici, più facilmente rispetto al benchmark HPL standard. I binari precompilati richiedono che la libreria Intel® MPI sia installata sul cluster.

## Intel® oneAPI Math Kernel Library Benchmarks 
• LINPACK Benchmark
• LINPACK Benchmark for a Customized MPI Implementation
• Optimized HPL-AI* Benchmark

## LINPACK Benchmark
The following limitations are known for the Intel Optimized LINPACK Benchmark for Linux*:
• Intel Optimized LINPACK Benchmark supports only OpenMP threading

# File in benchmarks\linpack\ -->	Description
• xlinpack_xeon64 => The 64-bit program executable for a system with Intel Xeon processor using Intel 64 architecture.
• runme_xeon64 => A sample shell script for executing a pre determined problem set for xlinpack_xeon64. OMP_NUM_THREADS set to 4 processors.
• lininput_xeon64	=> Input file for pre-determined problem for the runme_xeon64 script.
• lin_xeon64.txt => Result of the runme_xeon64 script execution


## LINPACK Benchmark for a Customized MPI Implementation
The Intel® Distribution for LINPACK Benchmark includes prebuilt binaries linked with Intel® MPI library. For a customized MPI implementation, tools are also included to build a binary using Intel® oneAPI Math Kernel Library MPI wrappers.
All the files are located in the ./benchmarks/mp_linpack/subdirectory of the Intel® oneAPI Math Kernel Library directory.

# File in <mkl directory> /benchmarks/mp_linpack/ -->	Description
  Prebuilt executables for performance testing
• xhpl_intel64_dynamic => Prebuilt binary for the Intel® 64 architecture dynamically linked against Intel MPI library
  
  Run scripts and an input file example
• runme_intel64_dynamic => Sample run script for the Intel® 64 architecture and binary dynamically linked against Intel MPI library
• runme_intel64_prv	=> Script that sets HPL environment variables. It is called by runme_intel64_dynamic
• HPL.dat => Example of an HPL configuration file.
  
  Prebuilt libraries and utilities for building with a customized MPI implementation
• build.sh => Build script for creating Intel® Distribution for LINPACK Benchmark for the Intel® 64 architecture with a customized MPI implementation

## Building the Intel® Distribution for LINPACK* Benchmark (and the Intel® Optimized HPL-AI* Benchmark) for a Customized MPI Implementation
The Intel® Distribution for LINPACK Benchmark contains a sample build script build.sh. If you are using a customized MPI implementation, this script builds a binary using Intel® oneAPI Math Kernel Library (oneMKL) MPI wrappers. To build the binary, follow these steps:

• Specify the location of Intel® oneAPI Math Kernel Library (oneMKL) to be used (MKLROOT) .
  
  La libreria da usare è quella in:

  Per LINPACK che non supporta MPI : /archive/apps/INTEL/OneAPI/mkl/latest/benchmarks/linpack/xlinpack_xeon64 
  Per LINPACK che supporta MPI : /archive/apps/INTEL/OneAPI/mkl/latest/benchmarks/mp_linpack/xhpl_intel64_dynamic 

  E potrai caricarla così: 	module load OneAPI/mkl/latest PER LINPACK NON MPI
• Set up your MPI environment.

  Scarica il test in base alle tue esigenze:
  
   **test LINPACK che non supporta mpi:**
  • mediante installazione con il software CHECK : check --check linpack@x86 --install OneAPI/mkl/latest
    
   **test LINPACK che supporta mpi**
  • mediante installazione con il software CHECK : check --check linpack@x86_mpi --install OneAPI/mkl/latest
  
• Run the script build.sh.

## Understanding Intel's MKL LINPACK Process Flow

```
                +---------------------+
                | runme_intel_dynamic |
                +---------------------+
                         |
                         v
                     +-------+
                     | mpirun |
                     +-------+
                         |
                         v
                 +--------------+
                 | mpiexec.hydra|
                 +--------------+
                         |
                         v
             +-------------------+
             | runme_intel64_prv |
             +-------------------+
                         |
                         v
           +-----------------------+
           | xhpl_intel64_dynamic  |
           +-----------------------+
                         |
                         v
        +---------------------------------+
        | xhpl_intel64_dynamic - threads  |
        +---------------------------------+

```

### [runme_intel_dynamic](./binary/runme_intel64_dynamic)
In case you're not running with GPUs [runme_intel_dynamic](./binary/runme_intel64_dynamic) does not do a whole lot. It's main job is to kick off [mpirun](./binary/mpirun) with [runme_intel64_prv](./binary/runme_intel64_prv) as an argument. It does this here:

```bash
mpirun -perhost ${MPI_PER_NODE} -np ${MPI_PROC_NUM} ./runme_intel64_prv "$@" | tee -a $OUT
```

where `MPI_PER_NODE` and `MPI_PROC_NUM` are defined as:

```bash
# Set total number of MPI processes for the HPL (should be equal to PxQ).
export MPI_PROC_NUM=2

# Set the MPI per node for each node.
# MPI_PER_NODE should be equal to 1 or number of sockets on the system.
# It will be same as -perhost or -ppn paramaters in mpirun/mpiexec.
export MPI_PER_NODE=2

# Set the number of NUMA nodes per MPI. (MPI_PER_NODE * NUMA_PER_MPI)
# should be equal to number of NUMA nodes on the system.
export NUMA_PER_MPI=1
---
USE_HPL_AI #  Decommentare per abilitare il Benchmark HPL-AI* ottimizzato da Intel.
USE_HPL_GPU #  Decommentare per abilitare l'uso di GPU.
HPL_NUMSTACK #  Numero di stack su ogni GPU.
HPL_NUMDEV # Numero di GPU.


```

There are a few things going on with `mpirun -perhost ${MPI_PER_NODE} -np ${MPI_PROC_NUM} ./runme_intel64_prv "$@" | tee -a $OUT` that are not at all obvious at first. As I mentioned earlier, first this leads to `mpirun` executing, but none of the arguments are actually destined for `mpirun` What really happens is the arguments `-perhost ${MPI_PER_NODE} -np ${MPI_PROC_NUM}` are fed into [mpiexec.hydra](#mpiexechydra) and then the `$@` part of `./runme_intel64_prv "$@"` is ultimately destined for `xhpl_intel64_dynamic` which accepts the [Ease-of-use Command-line Parameters](https://www.intel.com/content/www/us/en/docs/onemkl/developer-guide-linux/2023-0/ease-of-use-command-line-parameters.html). The ease of use command line parameters are meant to replace `HPL.dat`. So instead of just running `runme_intel64_dynamic` you can do:

```bash
./runme_intel64_dynamic -m <memory size in Mbytes> -b <block size> -p <grid row dimn> -q <grid column dimn>
```

or

```bash
./runme_intel64_dynamic -n 10000 -p 1 -q 3
```

where

- **-n <size of the matrix>**: Determines the size of the problem matrix to be solved
- **-m <memory size in Mbytes>**: If you're familiar with `HPL.dat` this option may confuse you because it isn't present. What this does is allow you to control the size of the problem matrix by specifying the total amount of memory dedicated to matrix storage across all nodes instead of specifying `-n`. When you set `-m` to 50000 Mbytes, you're instructing the application to use matrices that, when combined, fit within 50 GB of memory for their storage. The block size (`-b`) parameter, set to 256 in the example, defines the size of the sub-matrix or "block" used by the HPL algorithm during matrix factorization.
- **-b <block size>**: Specifies the block size used by the HPL algorithm
- **-p <grid row dimension>**: Sets the rows dimension of the process grid
- **-q <grid column dimension>**: Sets the columns dimension of the process grid

Finally, we are missing one last thing that is completely absent in the documentation or comments. If you are not running under a job manager you have to specify the nodes for `mpiexec.hydra` using one of the two following options:

#### -hostfile <hostfile> or -f <hostfile>

Use this option to specify host names on which to run the application. If a host name is repeated, this name is used only once.

**Attenzione** : se avvii il test su nodo interattivo ti basterà usare $PBS_NODESFILE contenente una riga per ogni core richiesto

See also the [I_MPI_HYDRA_HOST_FILE](#i_mpi_hydra_host_file) environment variable for more details.

**NOTE:** Use the following options to change the process placement on the cluster nodes:  
Use the -perhost, -ppn, and -grr options to place consecutive MPI processes on every host using the round robin scheduling.

Use the -rr option to place consecutive MPI processes on different hosts using the round robin scheduling.

#### I_MPI_HYDRA_HOST_FILE
Set the host file to run the application.

**Syntax**  
`I_MPI_HYDRA_HOST_FILE=<arg>`

**Argument**  
- `<arg>`: String parameter  
- `<hostsfile>`: The full or relative path to the host file  

**Description**  
Set this environment variable to specify the hosts file.

So basically, it's just a list of hostnames one per line.

### [mpirun](./binary/mpirun)

The [mpirun](./binary/mpirun) bash script's job is to set up the MPI environment and then call mpiexec.hydra on a single node in the cluster. mpiexec.hydra will then deploy instances of [runme_intel64_prv](./binary/runme_intel64_prv) across the cluster.

I'll go through the highlights of [mpirun](./binary/mpirun). First it sets up a bunch of environment variables with the main one being `I_MPI_MPIRUN`. This is used internally by Intel's binaries to determine how the process was launched. 

```bash
export I_MPI_MPIRUN="mpirun"
```

In this case, we launched it with the `mpirun` bash script and this environment variable tells the binaries that. Alternatively, we could have launched by calling `mpiexec` directly. The rest of the code is for handling what happens if we are launching with a job scheduler. In our case we are not so I ignore this. If we have no job scheduler, it executes this code:

```bash
# PBS or ordinary job
else
    mpiexec.hydra "$@" <&0
    rc=$?
fi
```

which will launch `mpiexec.hydra` on the current node with whatever command line arguments were passed in from `mpirun` which by default are inhereted from `runme_intel64_dynamic` and are:

```bash
-perhost ${MPI_PER_NODE} -np ${MPI_PROC_NUM} ./runme_intel64_prv "$@" | tee -a $OUT
```

### [mpiexec.hydra](https://www.intel.com/content/www/us/en/docs/mpi-library/developer-reference-linux/2021-8/mpiexec-hydra.html)

[mpiexec.hydra](https://www.intel.com/content/www/us/en/docs/mpi-library/developer-reference-linux/2021-8/mpiexec-hydra.html) is a process manager. In our case we are using Intel's instantiation, but it is an [open source project](https://github.com/pmodels/mpich/blob/main/doc/wiki/how_to/Using_the_Hydra_Process_Manager.md). mpiexec.hydra's job is to spawn our [runme_intel64_prv](./binary/runme_intel64_prv) jobs on the cluster. `-perhost ${MPI_PER_NODE} -np ${MPI_PROC_NUM}` plus the hostfile or machinefile I mentioned in [runme_intel64_dynamic](#runme_intel_dynamic) are what is interpreted by `mpiexec.hydra`. This is how the command line arguments are interpreted:

**-n <number-of-processes> or -np <number-of-processes>**  
Use this option to set the number of MPI processes to run with the current argument set. In our case, this is going to run n instances of `runme_intel64_prv`. `runme_intel64_prv` in turn launches `xhpl_intel64_dynamic`.

**-perhost <# of processes >, -ppn <# of processes >, or -grr <# of processes>**  
Use this option to place the specified number of consecutive MPI processes on every host in the group using round robin scheduling. See the I_MPI_PERHOST environment variable for more details.

**NOTE:** When running under a job scheduler, these options are ignored by default. To be able to control process placement with these options, disable the I_MPI_JOB_RESPECT_PROCESS_PLACEMENT variable.

Ultimately, the `-perhost` setting controls how many MPI processes will spawn on each node. On Intel MKL LINPACK, these processes are `runme_intel64_prv` which spawns one parent instance of `xhpl_intel64_dynamic` which then in turn spawns many child threads. This is explained in more detail later.

`I_MPI_PERHOST` is defined as

**I_MPI_PERHOST**

Define the default behavior for the `-perhost` option of the `mpiexec.hydra` command.

**Syntax**

`I_MPI_PERHOST=<value>`

**Argument**

| `<value>`     | Description                                     |
|---------------|-------------------------------------------------|
| `<value>`     | Define a value used for `-perhost` by default   |
| `integer > 0` | Exact value for the option                      |
| `all`         | All logical CPUs on the node                    |
| `allcores`    | All cores (physical CPUs) on the node. This is the default value. |

**Description**

Set this environment variable to define the default behavior for the `-perhost` option. Unless specified explicitly, the `-perhost` option is implied with the value set in `I_MPI_PERHOST`.

> **NOTE:** When running under a job scheduler, this environment variable is ignored by default. To control process placement with `I_MPI_PERHOST`, disable the `I_MPI_JOB_RESPECT_PROCESS_PLACEMENT` variable.

#### But What the Heck Does that Mean

Ok, so those were the descriptions in the docs for `-n` and `-ppn` but at least for me it wasn't immediately obvious what that would end up doing. Let's start with `-n`. What `-n` does is it will spawn `-n` instances of whatever process is *fed to `mpiexec.hydra`*. In our case this is going to be `runme_intel64_prv`. So if `-n` is 8, you will have 8 instances of `runme_intel64_prv` spread out across all your nodes.

`-ppn` will control how many instances of `runme_intel64_prv` spawn per node round robin'd. I explain this in more detail in the section on [runme\_intel64\_prv](#runme_intel64_prv). Each instance of `runme_intel64_prv` will in turn spawn one parent instance of `xhpl_intel64_dynamic` which will then spawn as many threads as are in the NUMA domains allocated to it (controlled by `NUMA_PER_MPI`).

### [runme_intel64_prv](./binary/runme_intel64_prv)

For this example, I'm going to assume the following settings:

- `np` aka `MPI_PROC_NUM` (in `runme_intel64_dynamic`) = 8
- `ppn` aka `perhost` aka `MPI_PER_NODE` = 2
- `NUMA_PER_MPI=1` = 1
- `p` = 2
- `q` = 4

```bash

# For OpenMPI
if [ -n "${OMPI_COMM_WORLD_RANK}" ]
then PMI_RANK=${OMPI_COMM_WORLD_RANK}
fi
```

By default, neither of these are used and this can be ignored.
-----> MPI_RANKS
