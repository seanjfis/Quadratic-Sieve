# Quadratic Sieve Implementation

This repository contains an implementation of the Quadratic Sieve algorithm, which is used for integer factorization. The implementation is organized into several key steps.

## Steps

### Step 1: Select an Integer and Bound

In this step, we select an integer `n` and compute the bound `B`. This involves calculating a small term that approaches zero as `n` increases and using it to determine `B`.

### Step 2: Get All Primes Up to B

This step involves generating all primes up to the bound `B`. The Sieve of Atkin algorithm is used to find these primes efficiently.

### Step 3: Find B-Smooth Squares

We then find B-smooth numbers and construct a power matrix for these numbers. This involves generating candidate numbers, checking their smoothness, and recording their factorization in terms of the primes found in Step 2.

### Step 4: Row-Reduce Matrix and Find Linear Dependence

In this step, Gaussian elimination is performed on the power matrix to find linear dependencies among the B-smooth numbers. This helps in identifying combinations of smooth numbers that can lead to factorization.

## Key Concepts

- **Quadratic Sieve Algorithm:** A factorization algorithm used to find factors of large composite numbers.
- **B-Smooth Numbers:** Numbers that have all their prime factors less than or equal to a bound `B`.
- **Sieve of Atkin:** A method for finding all primes up to a specified limit.
- **Gaussian Elimination:** A technique used to solve systems of linear equations and find dependencies in matrices.

This implementation is structured to handle large integers and efficiently find factors by reducing the problem to manageable steps.
