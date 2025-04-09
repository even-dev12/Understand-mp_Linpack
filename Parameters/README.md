 - [Understanding Benchmark Parameters](#understanding-benchmark-parameters)
      - [1. Problem Size (N)](#1-problem-size-n)
    - [2. Block Size (NB)](#2-block-size-nb)
      - [Understanding Block Size Math](#understanding-block-size-math)
    - [3. Process Grid (P x Q)](#3-process-grid-p-x-q)
  
   ## Understanding Benchmark Parameters

#### 1. Problem Size (N)

This is the size of the problem that LINPACK will solve. If you set this to 10,000, then LINPACK will solve for a 10,000x10,000 matrix. This subsequently controls the amount of memory the benchmark will use. For the benchmark you want the memory to be ~80%.

$$
N = \sqrt{\frac{{.8\times\text{Memory in Gigabytes}}}{8 \times \text{sizeof(double)}}}
$$

Since each double-precision number uses 8 bytes, we divide the allocated memory in bytes by 8.

### 2. Block Size (NB)

I explain exactly how blocks work [in this section](#understanding-block-size-math). The high level is that instead of trying to do the matrix multiplication outright, we chunk it up into blocks and the size of these blocks is determined by the block size. Ex: If you were solving a 4x4 matrix and you selected a block size of 2 it would chunk that multiplication into multiplications with 2x2 matrices.

Common values range from 128 to 256. The optimal value often depends on the specifics of your system's memory hierarchy. Many people simply start with 256. Some reasons why this matters:

- **Cache Efficiency:** Smaller matrices fit to CPU cache speeding things up
- **Data Locality:** By working on small contiguous blocks of the matrices at a time, the algorithm improves data locality, leading to better performance.
- **Parallelization:** Each block multiplication can potentially be performed in parallel, offering opportunities for performance optimization in multicore or multiprocessor environments.

#### Understanding Block Size Math

Suppose we have a 4x4 matrix $A$ and we want to multiply it by another 4x4 matrix $B$.

$$
A = \begin{bmatrix}
a_{11} & a_{12} & a_{13} & a_{14} \\
a_{21} & a_{22} & a_{23} & a_{24} \\
a_{31} & a_{32} & a_{33} & a_{34} \\
a_{41} & a_{42} & a_{43} & a_{44}
\end{bmatrix}
B = \begin{bmatrix}
b_{11} & b_{12} & b_{13} & b_{14} \\
b_{21} & b_{22} & b_{23} & b_{24} \\
b_{31} & b_{32} & b_{33} & b_{34} \\
b_{41} & b_{42} & b_{43} & b_{44}
\end{bmatrix}
$$

We will choose a **block size of 2**, meaning we'll partition this matrix into four 2x2 blocks.

$$
A = \left[
\begin{array}{cc}
A_{11} & A_{12} \\
A_{21} & A_{22}
\end{array}
\right] = \left[
\begin{array}{cc}
\begin{bmatrix} a_{11} & a_{12} \\
 a_{21} & a_{22} \end{bmatrix} & \begin{bmatrix} a_{13} & a_{14} \\
 a_{23} & a_{24} \end{bmatrix} \\

\begin{bmatrix} a_{31} & a_{32} \\
 a_{41} & a_{42} \end{bmatrix} & \begin{bmatrix} a_{33} & a_{34} \\
 a_{43} & a_{44} \end{bmatrix}
\end{array}
\right]
$$
$$
B = \left[
\begin{array}{cc}
B_{11} & B_{12} \\
B_{21} & B_{22}
\end{array}
\right] = \left[
\begin{array}{cc}
\begin{bmatrix} b_{11} & b_{12} \\
 b_{21} & b_{22} \end{bmatrix} & \begin{bmatrix} b_{13} & b_{14} \\
 b_{23} & b_{24} \end{bmatrix} \\

\begin{bmatrix} b_{31} & b_{32} \\
 b_{41} & b_{42} \end{bmatrix} & \begin{bmatrix} b_{33} & b_{34} \\
 b_{43} & b_{44} \end{bmatrix}
\end{array}
\right]
$$

Where each block $A_{ij}$ is a 2x2 matrix:

$$
A_{11} = \begin{bmatrix} a_{11} & a_{12} \\
 a_{21} & a_{22} \end{bmatrix}, \quad B_{11} = \begin{bmatrix} b_{11} & b_{12} \\
 b_{21} & b_{22} \end{bmatrix}
$$

$$
A_{12} = \begin{bmatrix} a_{13} & a_{14} \\
 a_{23} & a_{24} \end{bmatrix}, \quad B_{12} = \begin{bmatrix} b_{13} & b_{14} \\
 b_{23} & b_{24} \end{bmatrix}
$$

$$
A_{21} = \begin{bmatrix} a_{31} & a_{32} \\
 a_{41} & a_{42} \end{bmatrix}, \quad B_{21} = \begin{bmatrix} b_{31} & b_{32} \\
 b_{41} & b_{42} \end{bmatrix}
$$

$$
A_{22} = \begin{bmatrix} a_{33} & a_{34} \\
 a_{43} & a_{44} \end{bmatrix}, \quad B_{22} = \begin{bmatrix} b_{33} & b_{34} \\
 b_{43} & b_{44} \end{bmatrix}
$$

Now we multiply them as follows to get $C = AB$:

1. Multiply the corresponding 2x2 blocks of matrices $A$ and $B$ together.
2. Add the resulting 2x2 matrices to form the blocks of the resulting matrix $C$.

**For $C_{11}$**:
   
$$
C_{11} = A_{11}B_{11} + A_{12}B_{21}
$$

Breaking it down, the multiplication and addition are:

$$
C_{11} = \begin{bmatrix} a_{11} & a_{12} \\
 a_{21} & a_{22} \end{bmatrix} \begin{bmatrix} b_{11} & b_{12} \\
 b_{21} & b_{22} \end{bmatrix} + \begin{bmatrix} a_{13} & a_{14} \\
 a_{23} & a_{24} \end{bmatrix} \begin{bmatrix} b_{31} & b_{32} \\
 b_{41} & b_{42} \end{bmatrix}
$$

This results in a new 2x2 matrix for $C_{11}$.

**For $C_{12}$**:

$$
C_{12} = A_{11}B_{12} + A_{12}B_{22}
$$

$$
C_{12} = \begin{bmatrix} a_{11} & a_{12} \\
 a_{21} & a_{22} \end{bmatrix} \begin{bmatrix} b_{13} & b_{14} \\
 b_{23} & b_{24} \end{bmatrix} + \begin{bmatrix} a_{13} & a_{14} \\
 a_{23} & a_{24} \end{bmatrix} \begin{bmatrix} b_{33} & b_{34} \\
 b_{43} & b_{44} \end{bmatrix}
$$

**For $C_{21}$**:

$$
C_{21} = A_{21}B_{11} + A_{22}B_{21}
$$
$$
C_{21} = \begin{bmatrix} a_{31} & a_{32} \\
 a_{41} & a_{42} \end{bmatrix} \begin{bmatrix} b_{11} & b_{12} \\
 b_{21} & b_{22} \end{bmatrix} + \begin{bmatrix} a_{33} & a_{34} \\
 a_{43} & a_{44} \end{bmatrix} \begin{bmatrix} b_{31} & b_{32} \\
 b_{41} & b_{42} \end{bmatrix}
$$

**For $C_{22}$**:

$$
C_{22} = A_{21}B_{12} + A_{22}B_{22}
$$
$$
C_{22} = \begin{bmatrix} a_{31} & a_{32} \\
 a_{41} & a_{42} \end{bmatrix} \begin{bmatrix} b_{13} & b_{14} \\
 b_{23} & b_{24} \end{bmatrix} + \begin{bmatrix} a_{33} & a_{34} \\
 a_{43} & a_{44} \end{bmatrix} \begin{bmatrix} b_{33} & b_{34} \\
 b_{43} & b_{44} \end{bmatrix}
$$

Each $C_{ij}$ block is computed by adding the products of the 2x2 blocks from matrices $A$ and $B$. The summation of these products gives us the respective blocks of the resulting matrix $C$.

### 3. Process Grid (P x Q)

At the highest level, this controls the distribution of processes over a two-dimensional grid. P and Q are the dimensions of the grid.

Generally, what you want is:

$$
P \times Q = \text{Total number of cores or threads}
$$

where we choose $P$ and $Q$ such that their product is equal to the number of physical cores or logical threads (if hyper-threading is enabled). The ideal grid minimizes the difference between $P$ and $Q$. For a system with 12 cores, you might use 3x4 or 2x6. You will probably want to explore different values to see which is best.

Extending [the example for block size](#understanding-block-size-math), this is effectively assigning a core to each subblock we create. Ideally they match perfectly, but you really just want to get it as close as you can.
