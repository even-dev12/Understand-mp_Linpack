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

**N**
Hai 500 GB di memoria a disposizione, ma devi usare l'80% di questa memoria:

500 GB × 0.8 = 400 GB

Poiché 8×8=64, il calcolo diventa:

𝑁 = RADICE (400 GB / 64)

Calcolo finale: La radice quadrata di 6.25 è 2.5. Poiché stiamo calcolando la dimensione di una matrice, l'unità di N sarà il numero di righe (o colonne) della matrice, quindi:

N=2500
Per utilizzare 500 GB di memoria (l'80% dei 500 GB) e applicare la formula correttamente, N sarà 2500.

Questo è il valore di N che devi utilizzare se desideri sfruttare 500 GB di memoria per il benchmark LINPACK, considerando una matrice di tipo double (8 byte).

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

**Swapping threshold**

Il "swapping threshold" definisce la quantità di memoria utilizzata prima che il sistema inizi a spostare i dati dalla RAM al disco. Più precisamente, può essere visto come una percentuale di utilizzo della memoria o un valore assoluto che indica quando il sistema deve iniziare a fare swapping.

In un sistema con grandi quantità di memoria (come nel caso di server o macchine ad alte prestazioni), lo swapping è generalmente qualcosa che si cerca di evitare, poiché l'accesso al disco è molto più lento rispetto all'accesso alla memoria RAM. L'idea è di mantenere il più possibile i dati in memoria RAM per evitare il rallentamento dovuto alla lettura e scrittura dei dati sul disco.
Quindi è una soglia che determina quando il sistema inizia a usare la memoria di swap per compensare la mancanza di RAM.

La scelta del valore dipende dal tipo di applicazione, dalla memoria disponibile, dalla velocità del disco e dalle esigenze di prestazioni.

Un valore basso di swappiness (ad esempio, 10 o 20) è solitamente preferibile per evitare lo swapping, mentre un valore alto (ad esempio, 80 o 100) è adatto per sistemi che hanno bisogno di gestire molta memoria virtuale e che non soffrono eccessivamente delle prestazioni di I/O.

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


