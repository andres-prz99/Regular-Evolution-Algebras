# Regular Evolution Algebras

This repository aims to compute one-dimensional and codimension-one subalgebras of a regular evolution algebras over algebraically closed fields, mainly the complex numbers &#8450;. Recall that a (finite-dimensional) evolution algebra is said to be *regular* if its structure matrix is non-singular. In particular, we implemented two different functions which correspond with the algebraic criteria described in my article:
> M. Ladra, A. Pérez-Rodríguez. **"Regular evolution algebras are closed under subalgebras"**, *Comptes Rendus. Mathématique*, Vol. 363 (2025), pp. 1461–1465. [https://doi.org/10.5802/crmath.804](https://doi.org/10.5802/crmath.804)

Next, we describe how both functions work, along with the inputs and outputs obtained in each case. Note that each function is implemented in a separate script file within this repository.

## 1. Computing one-dimensional subalgebras

The function `find_one_dimensional_solutions(M)` computes all one-dimensional subalgebras of a regular evolution algebra of arbitrary dimension $n$. According to **Lemma 1** in our paper, finding one-dimensional subalgebras is equivalent to finding the non-zero algebraic solutions of a non-linear polynomial system derived from the structure matrix.

### Mathematical Foundation
Given a regular evolution algebra with an $n \times n$ structure matrix $M$, an element $v = \sum_{i=1}^n x_i e_i$ generates a one-dimensional subalgebra if and only if $v^2 =kv$ for some scalar $k\neq0$. As justified in **Lemma 1** of our article, to compute all one-dimensional subalgebras, it is enough to obtain all such $v$ such that $v^2=v$; and these elements correspond exactly to computing all $n$-tuples $(x_1, \dots, x_n)$ which satisfy the quadratic system:

$$
\begin{pmatrix} 
x_1^2 \\\\ 
x_2^2 \\\\ 
\vdots \\\\ 
x_n^2 
\end{pmatrix} 
= (M^t)^{-1} 
\begin{pmatrix} 
x_1 \\\\ 
x_2 \\\\ 
\vdots \\\\ 
x_n 
\end{pmatrix}
$$

#### How it works:
1. **Matrix Inversion:** It transposes the structure matrix $M$ and computes its inverse $T = (M^t)^{-1}$.
2. **System Generation:** It dynamically creates $n$ symbolic variables $(x_1,\dots, x_n)$ and builds the system of equations $x_i^2 = \sum_{j=1}^n T_{ij} x_j$ for each $i \in \{1, \dots, n\}$.
3. **Algebraic Solving:** It uses SageMath's internal symbolic engine (`solve`) to find all exact solutions of the non-linear system.
4. **Filtering and Formatting:** It discards the trivial zero solution $(0, \dots, 0)$. For each valid non-zero solution, it outputs a clean dual representation: the raw coordinate values followed by its corresponding mathematical generator vector $v$ in the natural basis $\\{e_1, \dots, e_n\\}$.

#### Inputs and Outputs:
* **Input:** `M` (a square, non-singular SageMath matrix representing the structure constants).
* **Output:** It prints to the console a structured list of all found one-dimensional subalgebras. For instance:

```text
  [Subalgebra 1]
  -> Raw solution: [x_1 == 1, x_2 == 0, x_3 == 2/3]
  -> Structure: span{ e_1 + (2/3)*e_3 }
```

## Prerequisites

To run this code, you need to have **SageMath** (version 9.0 or higher recommended) installed or use it via an online environment like [CoCalc](https://cocalc.com/).

## Usage

1. Load the function into your SageMath session or notebook.
2. Define the structure matrix $M$ of your 3D evolution algebra.
3. Call the function `non_zero_solutions_dim3(M)`.

### Quick Example

```python
# Define a 3x3 structure matrix for a regular evolution algebra
M = matrix(QQ, [[1, 0, 2], 
                [0, 1, -1], 
                [1, 1, 1]])

# Run the calculator
non_zero_solutions_dim3(M)
```
