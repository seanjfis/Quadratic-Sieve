# Math 404 - Computing Assignment

## Enhanced Quadratic Sieve Algorithm Implementation for Large Integer Factorization

### Authors:
Harssh Golechha, Karim Ghaddar, Sean Fiscus

---

## Procedure Outline

Here is a basic outline of the procedure for using the quadratic sieve to factor a large integer \( n \), which we implemented as a foundation before adding additional methods for optimization:

1. **Choosing a Factor Base \( B \)**: A factor base \( B \) (a positive integer) is chosen with respect to \( n \), from which a set \( S \) of all primes less than or equal to \( B \) is generated.
2. **Generating B-smooth Integers**: A set of \( |S| + 1 \) integers are generated whose squares modulo \( n \) are \( B \)-smooth, where \( |S| \) is the size of the set \( S \). A \( B \)-smooth integer is one whose factors are all in \( S \). These integers are located by taking \( x \)-values only slightly larger than \( \sqrt{n} \), \( \sqrt{2n} \), or \( \sqrt{3n} \), etc.
3. **Matrix Formation and Row Reduction**: For all such \( B \)-smooth integers \( y \) found, express their value mod \( n \) as a product of primes in \( S \). Create a \( |S|+1 \) by \( |S| \) matrix with the \( |S|+1 \) strings as rows, and row reduce to find a linear dependence.
4. **Checking for Non-trivial Factors**: Taking the square roots of both sides of the equation and ensuring one side is not equivalent to plus/minus the other side mod \( n \), check that \( \text{gcd}(x-y, n) \) is a nontrivial factor of \( n \), where \( x \) and \( y \) are the product of integers and product of primes from \( S \), respectively.

---

## Step-by-Step Breakdown

### Setting Value of \( B \)

For our quadratic sieve, built in Python, we began by implementing a function to determine the proper bound \( B \) as a function of the integer to factor \( n \). The value of \( B \) also dictates which integer values are considered \( B \)-smooth. Our function is based on the recommendation of an efficient \( B \)-value by Carl Pomerance in his paper on “Smooth Numbers and the Quadratic Sieve.” 

**Recommended Bound:** (image taken from the article)

### Primality Testing

We implemented a primality function `sieve_of_atkin`, based on the well-defined prime sieve of Atkin and Bernstein. This function generates a list of all primes less than the factor base, \( B \).

**Algorithm Used:**
1. **Initialization**: The sieve array is initialized to False for all indices (numbers).
2. **Quadratic Conditions**: Uses three quadratic expressions to test numbers potentially prime based on their remainder modulo 12.
3. **Elimination of Non-Primes**: Removes all composite numbers, ensuring only candidates for primality are left marked as True.
4. **Compilation of Prime List**: Constructs the list of primes.

### Finding B-smooth Values

We define two functions `gen_x` and `find_b_smooths` which locate values modulo \( n \) that are composed of only primes less than \( B \).

**Outline of `find_b_smooths` Function:**
1. **Initialization**: Computes the natural logarithms of \( n \) and all prime numbers up to \( B \). Establishes a logarithmic threshold.
2. **Smooth Number Generation and Factorization**: Generates candidate numbers and checks for B-smoothness.
3. **Termination and Results**: Continues until the set of unique identified smooth numbers meets a predetermined count.

### Finding Linear Dependencies and Checking for Factors

Defines two functions to input the list of B-smooth numbers and output a list of linear dependencies among their prime factors.

**Outline of `gaussian_elimination` Function:**
1. **Initialization**: Determines the number of rows and columns in the matrix.
2. **Column Iteration**: Locates potential pivots and swaps rows.
3. **Row Reduction**: Clears all other ones in the same column using XOR operations.
4. **Termination**: Returns the reduced matrix and pivot positions.

The `find_null_space` function then outputs a basis for the null space, and vectors are checked to see if \( \text{gcd}(x-y, n) \) is a non-trivial factor of \( n \).

---

## Performance

During our final class period’s competition, we received the following results:

| Number | Number of Digits | Factor 1 | Factor 2 | Runtime |
|--------|------------------|----------|----------|---------|
| 29223973 | 8 | 3313 | 8821 | 0.033s |
| 7067947793 | 10 | 95747 | 73819 | 0.426s |
| 736055622283 | 12 | 920729 | 799427 | 0.063s |
| 5479839591439397 | 16 | 61168739 | 89585623 | 0.538s |
| 92905709270744788219 | 20 | 9709089349 | 9568941631 | 6.069s |
| 60381558672724747724459 | 23 | 343250062451 | 175911282409 | 16.187s |

Plots of runtime against the number of digits show a linear relationship between \( \log(\text{Runtime}) \) and the number of digits in the integer.

### Future Improvements

1. **Lower-Level Implementation**: Implement in a lower-level language like C for parallelization and reduced memory overhead.
2. **Alternative Methods**: Implement Block Lanczos over \( \text{GF}(2) \) for finding the nullspace of the parity matrix.
3. **Parallelization**: Consider parallelizing the search for smooth numbers to speed up the sieve.
4. **General Number Field Sieve**: Implement ideas from the General Number Field Sieve for integers with more than 100 digits.

---

## References

1. Carl Pomerance, "Smooth Numbers and the Quadratic Sieve," 2008.
2. A.O.L. Atkin and D.J. Bernstein, "Prime Sieves Using Binary Quadratic Forms," Mathematics of Computation, vol. 73, no. 246, 2004, pp. 1023-1030.

---

## Appendix A (Code)

The complete Quadratic Sieve code is below. It is recommended to run the code in the Jupyter Notebook for interpretability. The notebook can be accessed [here](https://drive.google.com/file/d/1fQr1UaM464-0TDUtHIOQgXBrjZ-zGWH-/view?usp=sharing). It is fastest when downloaded and run locally on your machine.

```python
import math
import numpy as np
import time

# Test values
n = 10000 # NUMBER TO FACTOR
start_time = time.time()

# Set the bound according to n
def little_o_of_1(n):  # not needed but for authenticity to paper
    return 1 / math.log(n)

temp = math.log(n) * math.log(math.log(n))
B = int(math.exp(float(0.5 + little_o_of_1(n)) * math.sqrt(temp)))
print(B)

def sieve_of_atkin(limit):
    """
    This function implements the Sieve of Atkin to find all prime numbers up to the specified limit.
    """
    if limit < 2:
        return []

    sieve = [False] * (limit + 1)
    sqrt_limit = int(limit ** 0.5)

    for x in range(1, sqrt_limit + 1):
        for y in range(1, sqrt_limit + 1):
            n = 4*x*x + y*y
            if n <= limit and (n % 12 == 1 or n % 12 == 5):
                sieve[n] = not sieve[n]

            n = 3*x*x + y*y
            if n <= limit and n % 12 == 7:
                sieve[n] = not sieve[n]

            n = 3*x*x - y*y
            if x > y and n <= limit and n % 12 == 11:
                sieve[n] = not sieve[n]

    for a in range(5, sqrt_limit + 1):
        if sieve[a]:
            for b in range(a*a, limit + 1, a*a):
                sieve[b] = False

    primes = [2, 3] 
    primes += [i for i in range(3, limit + 1) if sieve[i]]

    return primes

S = sieve_of_atkin(B)
print(S)
print(len(S))

def gen_x(n, p, r):
    """
    Generate a list of x values that are likely candidates for being smooth numbers.
    """
    sqrt_n = math.sqrt((1 + 2 * r) * n)
    sqrt_2n = math.sqrt((2 + 2 * r) * n)
    return [math.ceil(sqrt_n + i) for i in range(p + 1)] + \
           [math.ceil(sqrt_2n + i) for i in range(p + 1)]

def find_b_smooths(n,
