## From dice rolling to the Multinomial theorem

### Introduction

At first glance, calculating dice sums and expanding polynomials seem like entirely different mathematical endeavors. One belongs to the world of probability and games, the other to algebra and analysis. Yet beneath the surface, these problems share a common combinatorial structure that reveals the elegant unity of mathematics.

In this exploration, we'll discover how both dice sum problems and polynomial coefficient problems reduce to the same fundamental question about counting solutions to constrained equations, and how the powerful stars and bars method—enhanced by inclusion-exclusion—provides the key to solving both.

### Two Problems, One Solution

#### The Dice Problem

Consider rolling n m-sided dice (numbered 1 through m). We want to know: how many ways can we obtain a sum of exactly a?

For example, with 3 six-sided dice, how many ways to get sum 10? This is a classic probability problem that appears in games, statistics, and even quality control processes.

#### The Polynomial Problem

Now consider the polynomial expansion of $(1 + x + x^2 + \cdots + x^m)^n$. We want to find the coefficient of $x^a$ in this expansion.

At first, this might seem like a purely algebraic exercise with little connection to dice. But let's look deeper.

### The Revealing Transformation

The key insight comes from representing the dice problem algebraically. Each die can show values from 1 to m, so we can represent a single die's outcomes as:

$$x^1 + x^2 + \cdots + x^m$$

When we roll n independent dice, we multiply these expressions:

$$(x^1 + x^2 + \cdots + x^m)^n$$

When we expand this product, each term corresponds to one possible outcome of the dice rolls. Specifically, the term $x^a$ will appear as many times as there are ways to get sum a.

We can factor this as:

$$(x^1 + x^2 + \cdots + x^m)^n = x^n(1 + x + \cdots + x^{m-1})^n$$

This brings us to the standard form $(1 + x + x^2 + \cdots + x^m)^n$ (with a shift in the exponent), showing that the dice problem is essentially equivalent to finding coefficients in this polynomial expansion.

### The Core Combinatorial Problem

Both problems reduce to finding the number of non-negative integer solutions to:

$$k_1 + k_2 + \cdots + k_n = a$$

with the constraint that $0 \leq k_i \leq m$ for each i.

In the dice context, $k_i$ represents the value showing on die i (shifted by 1). In the polynomial context, $k_i$ represents the exponent chosen from the i-th factor.

#### The Unbounded Case: Stars and Bars

If we had no upper bound ($k_i \leq m$), the number of solutions would be given by the classic stars and bars formula:

$$\binom{a + n - 1}{n - 1}$$

This formula counts the ways to distribute a identical items (stars) among n distinct boxes, using n-1 separators (bars).

#### The Bounded Case: Inclusion-Exclusion

With the upper bound constraint, we employ the inclusion-exclusion principle. Let $C(a)$ be the number of valid solutions, then:

$$C(a) = \sum_{j=0}^{\lfloor \frac{a}{m+1} \rfloor} (-1)^j \binom{n}{j} \binom{a - j(m+1) + n - 1}{n - 1}$$

Here's what each term represents:
- $\binom{n}{j}$ counts how many variables exceed the bound
- $j(m+1)$ accounts for the minimum total "overflow" when j variables exceed m
- $\binom{a - j(m+1) + n - 1}{n - 1}$ counts solutions to the reduced problem after accounting for overflow
- $(-1)^j$ implements the inclusion-exclusion alternation

### Worked Examples

#### Dice Example: 3 Six-Sided Dice, Sum 10

We want the coefficient of $x^{10}$ in $(x + x^2 + \cdots + x^6)^3$, or equivalently, the coefficient of $x^7$ in $(1 + x + \cdots + x^5)^3$.

Using our formula with m=5, n=3, a=7:

$$C(7) = \sum_{j=0}^{\lfloor \frac{7}{6} \rfloor} (-1)^j \binom{3}{j} \binom{7 - 6j + 2}{2}$$

Calculating:
- j=0: $\binom{3}{0} \binom{9}{2} = 1 \times 36 = 36$
- j=1: $-\binom{3}{1} \binom{3}{2} = -3 \times 3 = -9$

Total: $36 - 9 = 27$ ways.

We can verify this makes sense: the possible triples that sum to 10 with three six-sided dice include (4,3,3), (4,4,2), (5,3,2), etc., and there are indeed 27 such combinations.

#### Polynomial Example: $(1 + x + x^2 + x^3)^4$, Coefficient of $x^5$

Here m=3, n=4, a=5:

$$C(5) = \sum_{j=0}^{\lfloor \frac{5}{4} \rfloor} (-1)^j \binom{4}{j} \binom{5 - 4j + 3}{3}$$

Calculating:
- j=0: $\binom{4}{0} \binom{8}{3} = 1 \times 56 = 56$
- j=1: $-\binom{4}{1} \binom{4}{3} = -4 \times 4 = -16$

Total: $56 - 16 = 40$.

### Practical Implementation

Here's a Python implementation that solves both problems:

```python
import math

def coefficient(m, n, a):
    """
    Calculate the coefficient of x^a in (1 + x + x^2 + ... + x^m)^n
    Equivalent to number of ways to get sum a with n m-sided dice
    """
    if n == 0:
        return 1 if a == 0 else 0
    if a < 0 or a > n * m:
        return 0
    
    total = 0
    j_max = a // (m + 1)
    
    for j in range(0, j_max + 1):
        term1 = math.comb(n, j)
        remaining = a - j * (m + 1)
        term2 = math.comb(remaining + n - 1, n - 1) if remaining >= 0 else 0
        
        if j % 2 == 0:
            total += term1 * term2
        else:
            total -= term1 * term2
    
    return total

# Examples
print(f"Dice example: {coefficient(5, 3, 7)}")  # 3 dice with faces 0-5, sum 7
print(f"Polynomial example: {coefficient(3, 4, 5)}")  # (1+x+x^2+x^3)^4, x^5
```

### Extensions and Generalizations

#### Different Die Types

If each die has a different number of sides, we're finding coefficients in:
$$(x + x^2 + \cdots + x^{m_1})(x + x^2 + \cdots + x^{m_2})\cdots(x + x^2 + \cdots + x^{m_n})$$

This can be handled with generating functions and dynamic programming, though the closed-form solution becomes more complex.

#### Generating Functions Approach

Both problems are naturally expressed using generating functions. For dice problems:
$$G(x) = \prod_{i=1}^n (x + x^2 + \cdots + x^{m_i})$$

The coefficient of $x^a$ gives our answer. This perspective connects to many other areas of mathematics, from number theory to complex analysis.

#### Real-World Applications

This unified approach has practical applications in:
- **Probability theory**: Calculating exact probabilities for dice games
- **Statistics**: Multinomial distribution calculations
- **Computer science**: Analyzing algorithms with bounded loops
- **Operations research**: Resource allocation with constraints
- **Physics**: State counting in statistical mechanics

### Conclusion: The Beauty of Mathematical Unity

The connection between dice sums and polynomial coefficients reveals a profound truth about mathematics: seemingly different problems often share the same underlying structure. By recognizing this unity, we can:

1. **Transfer insights** between domains
2. **Develop general methods** that solve multiple problems
3. **Appreciate the elegance** of mathematical patterns
4. **Build intuition** for recognizing similar structures in new contexts

The stars and bars method, enhanced by inclusion-exclusion, provides a powerful tool that transcends its original context. It shows us that whether we're rolling dice in a game or expanding polynomials in algebra, we're engaging with the same fundamental combinatorial reality.

This unity is not just aesthetically pleasing—it's practically useful. It means that advances in understanding one problem can illuminate others, and techniques developed in one domain can find unexpected applications elsewhere. In mathematics, as in nature, deep connections often hide beneath surface differences, waiting to be discovered by the curious mind.