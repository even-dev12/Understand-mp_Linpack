- [How MPI Processes Work](#how-mpi-processes-work)
    - [What are MPI Processes?](#what-are-mpi-processes)
    - [How MPI Processes Communicate](#how-mpi-processes-communicate)
    - [Creating MPI Processes](#creating-mpi-processes)
    - [Distribution of Work](#distribution-of-work)
    - [Running MPI Programs](#running-mpi-programs)
    - [MPI Ranks](#mpi-ranks)
      - [MPI Ranks as Related to LU Decomposition](#mpi-ranks-as-related-to-lu-decomposition)
 
## How MPI Processes Work

Helpful Presentation: https://www.uio.no/studier/emner/matnat/ifi/INF3380/v11/undervisningsmateriale/inf3380-week06.pdf

MPI (Message Passing Interface) processes are at the core of parallel computing in systems that use the MPI standard for communication.

### What are MPI Processes?

- **Independent Processes**: Each MPI process is an independent unit of computation that executes concurrently with other MPI processes. They can be thought of as separate instances of the program, often running on different cores or different nodes in a cluster.
- **Separate Memory Spaces**: Each process has its own private memory space. This means variables and data in one process are not directly accessible to another process. Any data sharing or communication must be done explicitly using MPI's communication mechanisms.

### How MPI Processes Communicate

- **Message Passing**: The fundamental concept of MPI is that processes communicate by sending and receiving messages. These messages can contain any type of data, and the communication can occur between any two processes.
- **Collective Communications**: Besides point-to-point communication, MPI provides collective communication operations (like broadcast, reduce, scatter, gather) that involve a group of processes. These operations are optimized for efficient communication patterns commonly found in parallel applications.

### Creating MPI Processes

- **MPI_Init and MPI_Finalize**: An MPI program starts with MPI_Init and ends with MPI_Finalize. Between these two calls, the MPI environment is active, and the program can utilize MPI functions.
- **Parallel Region**: The code between MPI_Init and MPI_Finalize is executed by all processes in parallel, but each process can follow different execution paths based on their [rank](#mpi-ranks) or other conditional logic.
- **Rank and Size**: Each process is assigned a unique identifier called its rank. The total number of processes is called the size. These are often used to divide work among processes or to determine the role of each process in communication patterns.

### Distribution of Work

- **Dividing the Problem**: In typical MPI applications, the large problem is divided into smaller parts, and each MPI process works on a portion of the problem. This division can be based on data (data parallelism) or tasks (task parallelism).
- **Synchronization**: Processes can synchronize their execution, for example, by using barriers (MPI_Barrier), ensuring that all processes reach a certain point of execution before continuing.

### Running MPI Programs

- **MPI Executors**: MPI programs are usually executed with a command like `mpirun` or `mpiexec`, followed by options that specify the number of processes to launch and other execution parameters.
- **Scalability**: The scalability of an MPI program depends on its ability to efficiently partition the workload among the available processes and the effectiveness of the communication between the processes.

In the context of the LINPACK benchmark, the MPI processes would each handle a portion of the LINPACK workload, collaborating by exchanging messages to solve the large linear algebra problem in parallel.

### MPI Ranks

An MPI rank is a unique identifier assigned to each process in a parallel program that uses the Message Passing Interface (MPI) for communication. MPI is a standardized and portable message-passing system designed to function on a wide variety of parallel computing architectures. In an MPI program, multiple processes can run simultaneously, potentially on different physical machines or cores within a machine.

Ranks are used for the following:

- **Identification:** The rank allows each process to be uniquely identified within an MPI communicator. A communicator is a group of MPI processes that can communicate with each other. The most common communicator used is `MPI_COMM_WORLD`, which includes all the MPI processes in a program.
- **Coordination and Communication:** By knowing its rank, a process can determine how to coordinate its actions with other processes, such as dividing data among processes for parallel computation or determining the order of operations in a parallel algorithm.
- **Scalability:** Using ranks, MPI programs can be designed to scale efficiently with the number of processes, allowing for parallel execution on systems ranging from a single computer to a large-scale supercomputer.

Ranks are typically represented as integers starting from 0. So, in a program with `N` processes, the ranks are numbered from 0 to `N-1`. This numbering is used in various MPI operations, such as sending and receiving messages, where you specify the rank of the target process.

Here's a simple example to illustrate the use of ranks in MPI:

```c
#include <mpi.h>
#include <stdio.h>

int main(int argc, char** argv) {
    MPI_Init(&argc, &argv);

    int world_size;
    MPI_Comm_size(MPI_COMM_WORLD, &world_size);

    int world_rank;
    MPI_Comm_rank(MPI_COMM_WORLD, &world_rank);

    printf("Hello world from rank %d out of %d processes\n", world_rank, world_size);

    MPI_Finalize();
    return 0;
}
```

In this example, each process finds out its rank within `MPI_COMM_WORLD` (with `MPI_Comm_rank`) and the total number of processes (with `MPI_Comm_size`) and prints a message including its rank and the total number of processes. When this program is run with MPI across multiple processes, each process will print its unique rank.

#### MPI Ranks as Related to LU Decomposition

LU decomposition involves breaking down a matrix $A$ into the product of a lower triangular matrix $L$ and an upper triangular matrix $U$. See [LU Decomposition](#lu-decomposition-factorization) for an explanation of LU decomposition.

Recall that given a matrix $A$, the goal is to decompose it into:

$$ A = LU $$

where $L$ is a lower triangular matrix and $U$ is an upper triangular matrix. This decomposition is used to solve $Ax = b$ for $x$ by solving two simpler systems:

1. $Ly = b$
2. $Ux = y$

When MPI is applied to matrices there are two main strategies used:

- **2D Block Decomposition:** The matrix is divided into [blocks](#understanding-block-size-math) and distributed among the ranks. Each rank works on its block(s) performing necessary operations for the decomposition. This method requires communication between ranks to exchange bordering row and column information during the elimination steps.
- **1D Block Decomposition:** The matrix is divided into horizontal or vertical strips. Each rank works on its strip, and operations are coordinated to ensure the matrix is correctly modified across all ranks. This can be simpler but might not balance load and communication as effectively as 2D blocking.

The benchmark will initialize a dense $n\times n$ matrix $A$ and a vector $b$ with specific values that ensure the solution will converge and is known in advance for verification purposes.
