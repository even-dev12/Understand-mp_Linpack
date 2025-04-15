# Understand and Run LINPACK

- [Understand and Run LINPACK](#understand-and-run-linpack)
  - [Understanding Intel's MKL LINPACK Process Flow](#understanding-intels-mkl-linpack-process-flow)
    - [runme\_intel\_dynamic](#runme_intel_dynamic)
      - [I\_MPI\_HYDRA\_HOST\_FILE](#i_mpi_hydra_host_file)
    - [mpirun](#mpirun)
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

### Example: Running Linpack on an Interactive Intel Node (Current Issue)

While testing the Linpack benchmark on an interactive Intel node using mpiexec, the following command was executed:

```bash
mpiexec -hosts dvnode069.cm.cluster ./runme_intel64_dynamic -np ${MPI_PROC_NUM} -n 49536 -b 192 -p 1 -q 3

Note: ${MPI_PROC_NUM} is set according to the number of MPI processes intended for the test.

```

However, the current execution returns the following errors:
```
Illegal input in file HPL.dat. Exiting ...
Need at least 3 processes for these tests
```

This indicates that the configuration used by runme_intel64_dynamic is not valid for the current MPI setup (most likely due to the number of processes or matrix size). The test is not working as expected at this stage.

Work is in progress to correctly configure the HPL.dat file and MPI environment so that the Linpack MPI benchmark runs successfully on this node.




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

## Useful Links

Reference documentation for the usage of the new Leonardo supercomputer davinci-1 in : https://gitlab.leonardocompany.com/HPCC/hpc_usage/-/blob/master/Overview/General%20information/README.md

Also if you are using Intel processors you can find useful information for the HPL.dat configuration in : https://www.intel.com/content/www/us/en/developer/articles/technical/fourth-generation-xeon-scalable-family-overview.html
