
  - [Understanding the Benchmark's Math - LU Decomposition (Factorization)](#understanding-the-benchmarks-math---lu-decomposition-factorization)
    - [Why LU Decomposition](#why-lu-decomposition)
      - [Solving $Ax=b$ Through Matrix Inversion](#solving-axb-through-matrix-inversion)
      - [Example of Using Matrix Inversion](#example-of-using-matrix-inversion)
      - [Issues with Direct Inversion:](#issues-with-direct-inversion)
      - [Using LU Decomposition for Matrix Inversion](#using-lu-decomposition-for-matrix-inversion)
      - [Example of Using LU Decomposition for Matrix Inversion](#example-of-using-lu-decomposition-for-matrix-inversion)
      - [Advantages of LU Decomposition:](#advantages-of-lu-decomposition)

## Understanding the Benchmark's Math - LU Decomposition (Factorization)

- The benchmark performs an LU decomposition of matrix $A$, which is the process of factoring $A$ into the product of a lower triangular matrix $L$ and an upper triangular matrix $U$, such that $A = LU$. This decomposition includes partial pivoting, where rows of the matrix are swapped to enhance numerical stability.

### Why LU Decomposition

We know we are benchmarking, but why LU decomposition specifically? To understand this, you need to understand how we normally solve the equation $Ax=B$.

#### Solving $Ax=b$ Through Matrix Inversion

The direct inversion of a matrix $A$ to find $A^{-1}$ typically involves the use of the adjugate matrix and the determinant:

$$
A^{-1} = \frac{1}{\text{det}(A)} \text{adj}(A)
$$

where $\text{adj}(A)$ is the adjugate (or adjoint) of $A$, and det$(A)$ is the determinant of $A$.

1. **Calculate the determinant** of $A$. If det$(A) = 0$, $A$ is not invertible.
   1. If this value is zero, it means the machine’s design doesn’t allow for reversal, indicating the matrix can't be inverted.
2. **Find the matrix of cofactors** for $A$.
   1. Each cofactor represents the effect of removing the element's row and column from the matrix. In essence, the cofactor reflects how the exclusion of a particular element (and its corresponding row and column) impacts the overall determinant of the matrix.
3. **Transpose the matrix of cofactors** to get the adjugate matrix.
   1. Once you have the cofactors, you transpose them, meaning you flip the grid along its diagonal. This doesn’t change the individual contributions but reorganizes the layout, ensuring the machine's operation can be reversed correctly. This rearrangement produces what’s called the adjugate matrix.
4. **Divide each element** of the adjugate matrix by det$(A)$ to get $A^{-1}$.
   1. The final step involves adjusting the adjugate matrix by the machine's overall scaling factor (the determinant). You scale each component's contribution down or up to ensure that when you apply this reversed operation, it perfectly undoes whatever the original machine did. The result is your inverse matrix, \(A^{-1}\), which can reverse the effects of the original matrix \(A\).

#### Example of Using Matrix Inversion

Below is an example of this in action.

$$
A = \begin{bmatrix} 4 & 7 \\
2 & 6 \end{bmatrix}
$$

**Step 1: Calculate the determinant of $A$**

The determinant of a 2x2 matrix:

$\begin{bmatrix} a & b \\ 
c & d 
\end{bmatrix}$ 

is calculated as $ad - bc$. For our matrix $A$:

$$
\text{det}(A) = (4)(6) - (7)(2) = 24 - 14 = 10
$$

Since $\text{det}(A) \neq 0$, the matrix $A$ is invertible.

**Step 2: Find the matrix of cofactors for $A$**

The cofactor matrix is found by calculating the cofactor for each element of $A$. For a 2x2 matrix, this involves swapping the elements on the main diagonal, changing the signs of the off-diagonal elements, and then taking the determinant of each 1x1 matrix (which is the element itself in this case):

- Cofactor of $4$ (top-left) is $6$ (bottom-right element).
- Cofactor of $7$ (top-right) is $-2$ (bottom-left element, sign changed).
- Cofactor of $2$ (bottom-left) is $-7$ (top-right element, sign changed).
- Cofactor of $6$ (bottom-right) is $4$ (top-left element).

So, the cofactor matrix is:

$$
\text{Cof}(A) = \begin{bmatrix} 6 & -2 \\ 
-7 & 4 \end{bmatrix}
$$

**Step 3: Transpose the matrix of cofactors to get the adjugate matrix**

The adjugate of $A$ is the transpose of the cofactor matrix. For a 2x2 matrix, this simply means swapping the non-diagonal elements:

$$
\text{adj}(A) = \text{Cof}(A)^T = \begin{bmatrix} 6 & -7 \
 -2 & 4 \end{bmatrix}
$$

**Step 4: Divide each element of the adjugate matrix by $\text{det}(A)$ to get $A^{-1}$**

Finally, we divide each element of the adjugate matrix by the determinant of $A$ to find the inverse:

$$
A^{-1} = \frac{1}{\text{det}(A)} \text{adj}(A) = \frac{1}{10} \begin{bmatrix} 6 & -7 \\
 -2 & 4 \end{bmatrix} = \begin{bmatrix} 0.6 & -0.7 \\
 -0.2 & 0.4 \end{bmatrix}
$$

So, the inverse of matrix $A$ is:

$$
A^{-1} = \begin{bmatrix} 0.6 & -0.7 \\
 -0.2 & 0.4 \end{bmatrix}
$$

#### Issues with Direct Inversion:

- **Computational Complexity:** This method involves calculating determinants and cofactors for each element of the matrix, leading to a computational complexity of $O(n^3)$ to $O(n^4)$ for an $n \times n$ matrix, which is highly inefficient for large matrices.
- **Numerical Stability:** The process can be numerically unstable, especially if det$(A)$ is very close to zero, leading to large errors in $A^{-1}$.

#### Using LU Decomposition for Matrix Inversion

LU decomposition expresses matrix $A$ as the product of a lower triangular matrix $L$ and an upper triangular matrix $U$. This decomposition can be leveraged to compute $A^{-1}$ more efficiently and stably.

1. **Decompose $A$ into $L$ and $U$:** $A = LU$.
2. **Solve $LY = I$ for $Y$:** Use forward substitution, where $I$ is the identity matrix.
3. **Solve $UX = Y$ for $X$:** Use backward substitution, where $X$ eventually represents $A^{-1}$.

#### Example of Using LU Decomposition for Matrix Inversion

$$
A = \begin{bmatrix} 4 & 7 \\
 2 & 6 \end{bmatrix}
$$

**Decompose $A$ into $L$ and $U$**

For LU decomposition, we want to break down $A$ into a lower triangular matrix $L$ and an upper triangular matrix $U$.

1. Start with $U$ as $A$ and $L$ as an identity matrix, which will be modified to represent the elimination steps.
2. To eliminate the $2$ in the second row of $U$, subtract $\frac{1}{2}$ times the first row from the second row. The multiplier $\frac{1}{2}$ is stored in $L$.

Now we have:

$$
L = \begin{bmatrix} 1 & 0 \\
 \frac{1}{2} & 1 \end{bmatrix}, \quad U = \begin{bmatrix} 4 & 7 \\
 0 & \frac{5}{2} \end{bmatrix}
$$

**Step 2: Solve $LY = I$ for $Y$**

The goal here is to find a matrix $Y$ such that when $L$ is multiplied by $Y$, the result is the identity matrix $I$. The matrix $L$ is a lower triangular matrix, and $I$ is the $2 \times 2$ identity matrix.

Since $L$ is lower triangular with 1s on the diagonal, we can solve for $Y$ using forward substitution. However, in this context, since we are eventually seeking $A^{-1}$, we don't need to explicitly solve for $Y$ because it will be the identity matrix.

**Step 3: Solve $UX = Y$ for $X$**

Finally, we need to solve $UX = I$ (since $Y = I$ in this context) for $X$, where $X$ represents $A^{-1}$. This is done using backward substitution.

We compute $X$ by directly using backward substitution on the equation $UX = I$, where $I$ is the identity matrix. This process will yield $X$, which is the inverse of $A$.

After solving $UX = I$, we get:

$$
A^{-1} = \begin{bmatrix} 0.6 & -0.7 \\
 -0.2 & 0.4 \end{bmatrix}
$$

You can see the intermediate steps in this program:

```python
import numpy as np
import scipy.linalg

# Define the matrix A
A = np.array([[4, 7], [2, 6]])

# Perform LU decomposition with pivoting
P, L, U = scipy.linalg.lu(A)

# Display the matrices P, L, and U
print("P (Permutation Matrix):")
print(P)
print("\nL (Lower Triangular Matrix):")
print(L)
print("\nU (Upper Triangular Matrix):")
print(U)

# Step 1: Solve LY = P for Y using forward substitution
Y = scipy.linalg.solve_triangular(L, P, lower=True)

# Display the intermediate matrix Y
print("\nY (Intermediate Matrix):")
print(Y)

# Step 2: Solve UX = Y for X using backward substitution, where X is A^(-1)
X = scipy.linalg.solve_triangular(U, Y, lower=False)

# Display the computed inverse of A
print("\nX (Computed Inverse of A):")
print(X)

# Verify by direct inversion
A_inv = np.linalg.inv(A)
print("\nDirectly Calculated Inverse of A (for verification):")
print(A_inv)
```

#### Advantages of LU Decomposition:

- **Computational Efficiency:** LU decomposition reduces the problem to solving triangular matrices, which is computationally cheaper than the direct method. The overall complexity remains $O(n^3)$ but with a significantly lower constant factor.
- **Reusability:** Once $A$ has been decomposed into $L$ and $U$, these matrices can be reused to solve multiple systems or invert other matrices derived from $A$, without the need to perform decomposition again.
- **Numerical Stability:** Partial pivoting (adjusting $L$ and $U$ during decomposition to maintain stability) ensures that LU decomposition is more numerically stable than direct inversion, especially for matrices that are close to singular.

