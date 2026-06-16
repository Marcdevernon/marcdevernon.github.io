---
title: 'On using the psuedo-inverse in code'
date: 2024-02-28
permalink: /posts/2024/02/Psuedo_inverse
tags:
  - Scientific Computing
  - Psuedo-inverse
  - Practical Computing
---

Assuming you have a semi-definite matrix, ie: one that only has one zero eigenvalue, it is possible to still solve $Ax=b$ with a psuedo-inverse in a number of different methods. Typically we use the normal equations to find a least-sqaures aproximation to the solution vector $x$ such that the distance between the solution and the space spanned by the matrix A is minimized. This can be done with the SVD plugged into the normal equations, or in this case the eigen-decomposition.

That said, since there is a zero eigen-value, we can have an arbitary eigenvector in our equations, which is problematic since we do not want to introducce any more information than is necessary. Further, when running the backslash operaror or the function "pinv" in matlab it can be opaque as to what exactly the algorithm is doing with the zero eigen value and eigen vector.

A simple solution is to do an eigen decomposition, writing the matrix $A = S \Lambda S^{T}$ where $\Lambda$ has the eigenvalues and $S\ \text{is unitary such that}\ S^{T} S = I$

Then, simply the algorithm to compute the psuedo-inverse, least squares solution without adding any extra information to the mean of the operator is:

$$
\begin{gathered}
B = S^{T} b \\
\text{Set the row of B that corresponds to the zero eigen value to zero. This is first row of } \Lambda \\
\text{Loop over all the other rows and divide them by the corresponding eigenvalue.} \\
C = \Lambda^{-1} B \\
D = S C \\
\end{gathered}
$$

Then $D$ is the solution vector.

This makes use of the fact that S is unitary, i.e. rows and columns are norm one, and orthogonal, the inverse of $\Lambda$ is just the $\frac{1}{\lambda_{j}}$ where $j = 1,...,n$ if A is $(nxn)$

The algorithm above impliments the following math in psuedocode.

$$
\begin{gathered}
\text{The normal equations solution:} \\
Ax=b \\
A^{*}Ax = A^{*}b \\
x = (A^{*}A)^{-1}A^{*}b \\
\text{Insert the eigen decomposition, using the identities below: } \\
x = (S\Lambda S^{T} S\Lambda S^{T})^{-1}S\Lambda S^{T}b \\
x = S(\Lambda^{2})^{-1}S^{T}S\Lambda S^{T}b \\
x = S(\Lambda^{-1})S^{T}b \\
\end{gathered}
$$

Since:

$$
\begin{gathered}
S^{T}S=I \\
(S^{T})^{-1} S = (S^{T})^{-1} I \\
S =(S^{T})^{-1} \\ 
\text{And if the matrix is real, } \\
S^{*} = S^{T} = S^{-1} \\
\text{Eigen decomposition of A} \\
A = S \Lambda S^{T} \\
\text{Conjugate transpose of A} \\
A^{*} = (S \Lambda S^{T})^{*} \\
= (S^{T})^{*} \Lambda^{*} S^{*} \\
= S \Lambda S^{T} \\ 
\text{Inverse of A} \\
A^{-1} = (S \Lambda S^{T})^{-1} \\
= (S^{T})^{-1} \Lambda^{-1} S^{-1} \\
= S \Lambda S^{T} \\ 
\end{gathered}
$$

$$
\begin{gathered}
\text{Note that the calculation of }\ (\Lambda^{2})^{-1} \Lambda \\
\text{is achieved through simple division. i.e: for the }\ j^{th} \text{eigenvector on the diagonal, } \\
\frac{1}{\lambda_{j}} = \frac{\lambda_{j}}{\lambda_{j}^{2}}
\end{gathered}
$$

Thus we have calculated a psudeo-inverse for a positive semi-definite matrix $A$ without relying on the unknown proceses of matlab's pinv or backslash operator.

$\text{Reference: Dr Thomas Hagstrom at Southern Methodist University}$

