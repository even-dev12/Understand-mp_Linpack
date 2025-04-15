 ```
--> cd CHECK/checktest/linpack/x86_mpi/in/
--> vim HPL.dat
 ```
 ```
HPLinpack benchmark input file
Innovative Computing Laboratory, University of Tennessee
../out/HPL.out          output file name (if any)
file                    device out (6=stdout,7=stderr,file)
1                       # of problems sizes (N)
1000                    Ns
1                       # of NBs
192 256                 NBs
1                       PMAP process mapping (0=Row-,1=Column-major)
1                       # of process grids (P x Q)
1 2                     Ps
1 2                     Qs
16.0                    threshold
1                       # of panel fact
2 1 0                   PFACTs (0=left, 1=Crout, 2=Right)
1                       # of recursive stopping criterium
2                       NBMINs (>= 1)
1                       # of panels in recursion
2                       NDIVs
1                       # of recursive panel fact.
1 0 2                   RFACTs (0=left, 1=Crout, 2=Right)
1                       # of broadcast
0                       BCASTs (0=1rg,1=1rM,2=2rg,3=2rM,4=Lng,5=LnM)
1                       # of lookahead depth
0                       DEPTHs (>=0)
0                       SWAP (0=bin-exch,1=long,2=mix)
1                       swapping threshold
1                       L1 in (0=transposed,1=no-transposed) form
1                       U  in (0=transposed,1=no-transposed) form
0                       Equilibration (0=no,1=yes)
8                       memory alignment in double (> 0)

```


## cosa sono questi parametri?

**PFACTs (Panel Factorization Type)**

This parameter specifies the method used for the panel factorization during the LU decomposition. The options are:

0 = Left-looking factorization

1 = Crout factorization

2 = Right-looking factorization

It determines the approach used to compute the LU factorization of the matrix.

**NBMINs (Minimum Block Size)**

This defines the minimum block size for the matrix block. It’s the threshold size below which the blocks will be treated as scalar (i.e., no further blocking). Typically, this value is set to 1 or 2, and it impacts the granularity of the factorization process.

**BCASTs (Broadcasting Algorithm)**

This controls the method used to broadcast data between processes during the computation. The possible values are:

0 = 1-rank broadcast (used for certain one-dimensional process grids)

1 = 1-row broadcast

2 = 2-rank broadcast

3 = 2-row broadcast

4 = Long broadcast (used in specific scenarios with large data)

5 = Long multi-row broadcast

This parameter determines how data is shared across different processes in the parallel execution.

**RFACTs (Recursive Factorization Type)**

This controls the type of recursive factorization to be used. The options are:

0 = Left-looking recursive factorization

1 = Crout recursive factorization

2 = Right-looking recursive factorization

This parameter affects the strategy for handling recursive subproblems during the factorization process.

**SWAP (Swapping Method)**

This controls the type of data swapping method used for pivoting during the factorization:

0 = Binary exchange (used in a simpler, less costly method)

1 = Long exchange (a more complex method, potentially more expensive)

2 = Mixed (combines different swapping methods)

The SWAP parameter determines how the matrix rows and columns are exchanged during the LU factorization to ensure numerical stability.

## command & new config

Quindi io ho usato questa configurazione in base al seguente comando

 ```
[eciampa_ext@dvlogin04 ~]$ vim my_linpack.sh
source CHECK/check2/bin/load_check.sh
module load OneAPI_2025/mpi/latest
module load OneAPI/mkl/latest
cd CHECK/checktest/linpack/x86_mpi/in/

export NUMMIC=0
export I_MPI_PIN_DOMAIN=auto
export OMP_NUM_THREADS=1
export MPI_PER_NODE=1
export SLURM_JOB_NUM_NODES=1
export KMP_AFFINITY=compact
export MPI_PROC_NUM=16


mpiexec -hosts dvnode080.cm.cluster ./runme_intel64_dynamic -np 16 -n 10000 -b 256 -p 4 -q 4

 ```
HPL.dat :
```
HPLinpack benchmark input file
Innovative Computing Laboratory, University of Tennessee
../out/HPL.out          output file name (if any)
file                    device out (6=stdout,7=stderr,file)
1                       # of problems sizes (N)
10000                   Ns
1                       # of NBs
256                     NBs
1                       PMAP process mapping (0=Row-,1=Column-major)
1                       # of process grids (P x Q)
4                       Ps
4                       Qs
16.0                    threshold
1                       # of panel fact
1                       PFACTs (0=left, 1=Crout, 2=Right)
1                       # of recursive stopping criterium
2                       NBMINs (>= 1)
1                       # of panels in recursion
2                       NDIVs
1                       # of recursive panel fact.
1                       RFACTs (0=left, 1=Crout, 2=Right)
1                       # of broadcast
1                       BCASTs (0=1rg,1=1rM,2=2rg,3=2rM,4=Lng,5=LnM)
1                       # of lookahead depth
0                       DEPTHs (>=0)
0                       SWAP (0=bin-exch,1=long,2=mix)
1                       swapping threshold
1                       L1 in (0=transposed,1=no-transposed) form
1                       U  in (0=transposed,1=no-transposed) form
0                       Equilibration (0=no,1=yes)
4                       memory alignment in double (> 0)

 ```

# output : Err

HPL ERROR from process # 0, on line 806 of function HPL_pdinfo:
>> Illegal input in file HPL.dat. Exiting ... <<

HPL ERROR from process # 0, on line 496 of function HPL_pdinfo:
>> Need at least 16 processes for these tests <<
