# Regular Evolution Algebras

This repository aims to compute one-dimensional and codimension-one subalgebras of a regular evolution algebra over algebraically closed fields, mainly the complex numbers &#8450;. Recall that a (finite-dimensional) evolution algebra is said to be *regular* if its structure matrix is non-singular. In particular, we implemented two different functions which correspond with the algebraic criteria described in my article:
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

### 2. Computing codimension-one subalgebras

The function `find_codimension_one_subalgebras(M)` calculates all codimension-one subalgebras of a regular evolution algebra of arbitrary dimension $n$. Following **Proposition 5** and its corollaries from our paper, the task is reduced to systematically inspecting pairs of indices and evaluating specific matrix ranks and polynomial restrictions.

#### Mathematical Foundation
Let $A$ be a regular evolution algebra with natural basis $\\{e_1, \dots, e_n\\}$ and structure matrix $M$. A codimension-one subalgebra is spanned by a subset of $n-2$ basis elements, namely $\\{e_i\colon i\neq p,q\\}$, combined with an additional generator $v = \alpha e_p + \beta e_q$. 

According to **Proposition 5**, for each pair of indices $(p, q)$ with $1 \le p < q \le n$, we define the submatrix $M_{p,q}$ by removing rows $p$ and $q$ and keeping columns $p$ and $q$. A codimension-one subalgebra associated with $(p, q)$ exists if and only if $\text{rank}(M_{p,q}) \le 1$ and the coefficients satisfy one of the following conditions:

* **Case 1: $\text{rank}(M_{p,q}) = 1$**. The elements of any non-zero row of $M_{p,q}$ yield the coordinates $(\alpha, \beta)$. The subalgebra exists if and only if these constants satisfy the polynomial relation (P):
  $$\alpha^2 \beta a_{pp} + \beta^3 a_{qp} = \alpha^3 a_{pq} + \alpha \beta^2 a_{qq}$$
* **Case 2: $\text{rank}(M_{p,q}) = 0$**. A continuous family of subalgebras exists, where the generator is of the form $v = e_p + \lambda e_q$, and $\lambda \neq 0$ is a root of the characteristic cubic equation:
  $$a_{qp} \lambda^3 - a_{qq} \lambda^2 + a_{pp} \lambda - a_{pq} = 0$$.
  
  (Note: Boundary cases where $\lambda = 0$ or $v = e_q$ are also checked if $a_{p,q}=0$ or $a_{q,p}=0$).

#### How it works:
1. **Regularity Check:** It computes the determinant of $M$ to issue a warning if the algebra is not regular, ensuring the prerequisites of Proposition 5 are met.
2. **Combinatorial Loop:** It automatically loops through all $\binom{n}{2}$ possible index pairs $(p, q)$ using 0-indexed variables mapped dynamically to 1-indexed notation for clarity.
3. **Submatrix Analysis:** For each pair, it extracts $M_{p,q}$ using SageMath slicing and evaluates its rank. If $\text{rank}(M_{p,q}) > 1$, the pair is immediately discarded and the reason is printed.
4. **Equation Verification:** Depending on whether the rank is 1 or 0, it dynamically extracts the row constants or invokes SageMath's `solve` engine to compute the exact roots of the cubic constraint equation.

#### Inputs and Outputs:
* **Input:** `M` (a square, non-singular SageMath matrix representing the structure constants).
* **Output:** It prints a comprehensive step-by-step diagnostic trace of every index pair check, showing ranks, discarded cases, and full algebraic definitions for successful subalgebras. For instance:

```text
[Indices p=1, q=2] -> rank(M_1,2) = 1
  -> No subalgebras for this pair (Condition (3) does not hold).

[Indices p=2, q=3] -> rank(M_2,3) = 0
  -> SUCCESS: Subalgebras exist of the form span{e_i : i != 2,3} U {v}
     - Continuous family v = e_2 + lambda*e_3, where lambda satisfies:
       lambda_val^3 - lambda_val^2 + lambda_val - 1 == 0
       Solutions found for lambda:
       [lambda_val == 1, lambda_val == -I, lambda_val == I]
```
### 3. Comprehensive Example

To illustrate the usage of both scripts, we consider the $3$-dimensional regular evolution algebra defined by the following structure matrix over the rational field $\mathbb{Q}$:

$$
N = \begin{pmatrix} 
1 & 1/3 & 4/9 \\\\ 
1/3 & 1 & 0 \\\\ 
0 & 0 & 1 
\end{pmatrix}
$$

#### SageMath Script Execution

You can define this matrix and run both algorithms sequentially in your SageMath session:

```text
# Define the structure matrix N
N = matrix([[1, 1/3, 4/9],[1/3, 1, 0],[0, 0, 1]])

# 1. Compute 1-dimensional subalgebras (Lemma 1)
find_one_dimensional_solutions(N)

# 2. Compute codimension-one subalgebras (Proposition 5)
find_codimension_one_subalgebras(N)
```
#### Expected Console Output
```text
============================================================
COMPUTING ONE-DIMENSIONAL SUBALGEBRAS FOR DIMENSION N = 3
============================================================

THE ONE-DIMENSIONAL SUBALGEBRAS ARE:

  [Subalgebra 1]
  -> Raw solution: [x_1 == (3/4), x_2 == (3/4), x_3 == (1/2)]
  -> Structure: span{ (3/4)*e_1 + (3/4)*e_2 + (1/2)*e_3 }

  [Subalgebra 2]
  -> Raw solution: [x_1 == 0, x_2 == 0, x_3 == 1]
  -> Structure: span{ e_3 }

============================================================
============================================================
ANALYZING CODIMENSION-ONE SUBALGEBRAS FOR DIMENSION N = 3
============================================================

[Indices p=1, q=2] -> rank(M_1,2) = 0
  -> SUCCESS: Subalgebras exist of the form span{e_i : i != 1,2} U {v}
     - Continuous family v = e_1 + lambda*e_2, where lambda satisfies:
       1/3*lambda_val^3 - lambda_val^2 + lambda_val - 1/3 == 0
       Solutions found for lambda: [lambda_val == 1]

[Indices p=1, q=3] -> rank(M_1,3) = 1
  -> No subalgebras for this pair (Condition (P) does not hold).

[Indices p=2, q=3] -> rank(M_2,3) = 1
  -> No subalgebras for this pair (Condition (P) does not hold).

============================================================
```

