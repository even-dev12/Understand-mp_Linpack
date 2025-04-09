- [Interpreting Benchmark Results for Single Node Test](#interpreting-benchmark-results-for-single-node-test)
    - [What is Numerical Stability](#what-is-numerical-stability)
    - [Right Looking LU Factorization](#right-looking-lu-factorization)
    - [Row Partial Pivoting](#row-partial-pivoting)

Interpreting Benchmark Results for Single Node Test


## Interpreting Benchmark Results for Single Node Test

```bash
This is a SAMPLE run script for running a shared-memory version of
Intel(R) Distribution for LINPACK* Benchmark. Change it to reflect
the correct number of CPUs/threads, problem input files, etc..
*Other names and brands may be claimed as the property of others.
runme_xeon64: line 28: [: too many arguments
Wed Mar  6 01:17:25 PM EST 2024
Sample data file lininput_xeon64.

Current date/time: Wed Mar  6 13:17:25 2024

CPU frequency:    2.692 GHz
Number of CPUs: 2
Number of cores: 24
Number of threads: 24

Parameters are set to:

Number of tests: 15
Number of equations to solve (problem size) : 1000  2000  5000  10000 15000 18000 20000 22000 25000 26000 27000 30000 35000 40000 45000
Leading dimension of array                  : 1000  2000  5008  10000 15000 18008 20016 22008 25000 26000 27000 30000 35000 40000 45000
Number of trials to run                     : 8     6     4     4     3     3     3     3     3     3     2     1     1     1     1
Data alignment value (in Kbytes)            : 4     4     4     4     4     4     4     4     4     4     4     1     1     1     1

Maximum memory requested that can be used=16200901024, at the size=45000

=================== Timing linear equation system solver ===================

Size   LDA    Align. Time(s)    GFlops   Residual     Residual(norm) Check
1000   1000   4      0.013      51.4481  1.319833e-12 4.019661e-02   pass
1000   1000   4      0.006      115.4058 1.319833e-12 4.019661e-02   pass
1000   1000   4      0.006      114.9894 1.319833e-12 4.019661e-02   pass
1000   1000   4      0.006      113.9290 1.319833e-12 4.019661e-02   pass
1000   1000   4      0.006      117.8895 1.319833e-12 4.019661e-02   pass
1000   1000   4      0.006      119.3595 1.319833e-12 4.019661e-02   pass
1000   1000   4      0.006      116.9847 1.319833e-12 4.019661e-02   pass
1000   1000   4      0.006      118.7078 1.319833e-12 4.019661e-02   pass
2000   2000   4      0.023      231.5382 5.176637e-12 4.065060e-02   pass
2000   2000   4      0.024      225.9432 5.176637e-12 4.065060e-02   pass
2000   2000   4      0.022      239.2326 5.176637e-12 4.065060e-02   pass
2000   2000   4      0.025      214.1079 5.176637e-12 4.065060e-02   pass
2000   2000   4      0.022      239.7369 5.176637e-12 4.065060e-02   pass
2000   2000   4      0.023      228.6342 5.176637e-12 4.065060e-02   pass
5000   5008   4      0.230      362.3217 2.344738e-11 3.096182e-02   pass
5000   5008   4      0.225      371.2111 2.344738e-11 3.096182e-02   pass
5000   5008   4      0.226      369.3271 2.344738e-11 3.096182e-02   pass
5000   5008   4      0.226      369.2534 2.344738e-11 3.096182e-02   pass
10000  10000  4      1.421      469.3290 8.459977e-11 2.865107e-02   pass
10000  10000  4      1.398      476.9397 8.459977e-11 2.865107e-02   pass
10000  10000  4      1.385      481.4629 8.459977e-11 2.865107e-02   pass
10000  10000  4      1.383      482.1042 8.459977e-11 2.865107e-02   pass
15000  15000  4      4.469      503.5350 2.237140e-10 3.392439e-02   pass
15000  15000  4      4.462      504.3754 2.237140e-10 3.392439e-02   pass
15000  15000  4      4.459      504.7193 2.237140e-10 3.392439e-02   pass
18000  18008  4      7.425      523.7450 3.461987e-10 3.673234e-02   pass
18000  18008  4      7.262      535.4732 3.461987e-10 3.673234e-02   pass
18000  18008  4      7.238      537.2687 3.461987e-10 3.673234e-02   pass
20000  20016  4      10.235     521.1456 3.549664e-10 3.036127e-02   pass
20000  20016  4      10.230     521.4355 3.549664e-10 3.036127e-02   pass
20000  20016  4      10.089     528.7277 3.549664e-10 3.036127e-02   pass
22000  22008  4      13.360     531.4139 4.328670e-10 3.065988e-02   pass
22000  22008  4      13.327     532.7448 4.328670e-10 3.065988e-02   pass
22000  22008  4      13.300     533.8255 4.328670e-10 3.065988e-02   pass
25000  25000  4      19.541     533.1316 5.450296e-10 3.015733e-02   pass
25000  25000  4      19.242     541.4267 5.450296e-10 3.015733e-02   pass
25000  25000  4      19.187     542.9651 5.450296e-10 3.015733e-02   pass
26000  26000  4      21.612     542.2365 6.520824e-10 3.323271e-02   pass
26000  26000  4      21.666     540.8737 6.520824e-10 3.323271e-02   pass
26000  26000  4      21.641     541.5003 6.520824e-10 3.323271e-02   pass
27000  27000  4      24.343     539.1040 6.041370e-10 2.857495e-02   pass
27000  27000  4      24.304     539.9609 6.041370e-10 2.857495e-02   pass
30000  30000  1      33.634     535.2317 9.424864e-10 3.610605e-02   pass
35000  35000  1      53.551     533.8045 1.064996e-09 3.020948e-02   pass
40000  40000  1      79.507     536.6813 1.523135e-09 3.289340e-02   pass
45000  45000  1      110.173    551.4434 2.085267e-09 3.591039e-02   pass

Performance Summary (GFlops)

Size   LDA    Align.  Average  Maximal
1000   1000   4       108.5892 119.3595
2000   2000   4       229.8655 239.7369
5000   5008   4       368.0283 371.2111
10000  10000  4       477.4589 482.1042
15000  15000  4       504.2099 504.7193
18000  18008  4       532.1623 537.2687
20000  20016  4       523.7696 528.7277
22000  22008  4       532.6614 533.8255
25000  25000  4       539.1745 542.9651
26000  26000  4       541.5368 542.2365
27000  27000  4       539.5325 539.9609
30000  30000  1       535.2317 535.2317
35000  35000  1       533.8045 533.8045
40000  40000  1       536.6813 536.6813
45000  45000  1       551.4434 551.4434

Residual checks PASSED

End of tests

Done: Wed Mar  6 01:27:43 PM EST 2024
```

1. **Size and LDA (Leading Dimension Array)**:
   - **Size**: This refers to the problem size, specifically the number of linear equations to solve, which directly corresponds to the size of the matrix involved in the computations. Larger sizes indicate more complex problems that require more computational resources.
   - **LDA**: The Leading Dimension of the Array is a parameter that specifies the physical storage dimensions of the matrix in memory. It's usually equal to or greater than the actual matrix size (problem size) to accommodate efficient memory access and alignment. The choice of LDA can affect the efficiency of the algorithm, especially in terms of memory usage and speed of accessing array elements during computation.
   - The key here is that LDA must be *at least* as large as the size of the matrix being solved.
2. **Align**: This represents the data alignment in kilobytes (Kbytes) and is crucial for optimizing memory access. Proper alignment ensures that data is stored in memory in a way that aligns with the cache lines, thereby minimizing cache misses and improving the efficiency of memory access. When data is well-aligned, the processor can fetch and store data more efficiently, leading to faster computation times and reduced risk of bottlenecks due to memory access delays.
3. **Time(s)**: The time, measured in seconds, indicates how long the system took to solve a set of linear equations of a given size.
4. **GFlops (Giga Floating-Point Operations Per Second)**: GFlops measure the number of billions of floating-point operations the system can perform per second.
5. **Residual and Residual(norm)**:
   - **Residual**: This is a measure of the error in the solutions of the linear equations. It's calculated as the difference between the left-hand side and right-hand side of the equations when using the computed solution. Ideally, the residual should be close to zero, indicating that the computed solution closely approximates the true solution.
   - **Residual(norm)**: The normalized residual provides a scale-independent measure of accuracy, which is particularly useful for comparing the accuracy across different problem sizes or different systems. A small normalized residual suggests that the solution is accurate relative to the size of the input matrix.
6. **Check**: This column indicates whether each individual test passed or failed based on the computed residuals. A "pass" means that the solution meets the predefined accuracy criteria, usually a small residual. This is crucial for verifying the reliability of the computational results, ensuring that they are within acceptable error margins.

### What is Numerical Stability

Numerical stability refers to the ability to produce accurate results even when subjected to small perturbations, such as rounding errors or slight changes in input values. A numerically stable algorithm minimizes the error amplification over its computation, ensuring that the outcome remains close to the true mathematical solution, even in the presence of inherent numerical inaccuracies due to the finite precision of floating-point representation.

Imagine the linear system $Ax = b$, where $A$ is a matrix, $x$ is the vector of unknowns, and $b$ is the result vector. What happens when, due to the nature of base 2, we have a slight inaccuracy in the calculation of $b$?

- **Original System**: $Ax = b$
- **Perturbed System**: $Ax' = b + \delta b$ (where $\delta b$ is a tiny change in $b$)

In the ideal outcome the solution $x'$ should only slightly differ from $x$, reflecting the small change $\delta b$.

A numerically stable algorithm ensures that the small change $\delta b$ leads to a proportionally small change in the solution $x'$.

In a numerically unstable algorithm a tiny change in $\delta b$ could result in a dramatically different solution $x'$, amplifying errors and leading to unreliable results.

### Right Looking LU Factorization

Right-looking LU factorization is a variant of the standard LU factorization. In regular LU factorization, the matrix is decomposed into a lower triangular matrix (L) and an upper triangular matrix (U) generally without specifying the order of operations. In contrast, the right-looking approach specifically refers to the order in which the matrix is processed: it begins with the leftmost column and moves rightward, updating the remainder of the matrix as it progresses. 

### Row Partial Pivoting

Row-partial pivoting in LU factorization is a strategy to enhance numerical stability. It interchanges rows of the matrix as it's decomposed into lower (L) and upper (U) triangular matrices. Before zeroing out below-diagonal elements in a column, the algorithm swaps the current row with a below row that has the largest absolute value in the current column. This step helps prevent small pivot elements, which can lead to significant numerical errors, ensuring the algorithm remains stable even for matrices prone to inducing calculation inaccuracies.
