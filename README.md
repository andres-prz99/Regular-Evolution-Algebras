# Regular Evolution Algebras

This repository aims to compute one-dimensional and codimension-one subalgebras of a regular evolution algebras over algebraically closed fields, mainly the complex numbers &#8450;. Recall that a (finite-dimensional) evolution algebra is said to be *regular* if its structure matrix is non-singular. In particular, we implemented two different functions which correspond with the algebraic criteria described in the article:

> Ladra, A. Pérez-Rodríguez. **"Regular evolution algebras are closed under subalgebras"**, *Comptes Rendus. Mathématique*, Vol. 363 (2025), pp. 1461–1465. [https://doi.org/10.5802/crmath.804](https://doi.org/10.5802/crmath.804)

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
