# **Algorithms and Parallel Computing - C++: Notes**

# **Lesson 3 - Algorithms Complexity**

## Analysis of algorithms

The *theoretical* study of computer-program **performance** and **resource-usage**

## The problem of **Sorting**

*Input:* sequence $<a_1, a_2 , ..., a_n>$ of numbers

*Output:* **permutation** $<a'_1, a'_2, ..., a'_n>$ such that $a'_1 \leq a'_2 \leq ... \leq a'_n$

## **Insertion Sort** - C++

    void InsertionSort (std::vector<int> &A)
    {
        int i;
        int j;
        int key;
        
        for (j = 1; j < A.size(); j++)
        {
            key = A[j];
            i = j - 1;
            while (i >= 0 && A[i] > key)
            {
                A[i+1] = A[i];
                i = i - 1;
            }
            A[i+1] = key;
        }
    }

## Running time

Running time depends on the input: an already sorted sequence is easier to sort

Parametrize the running time by the size of the input, since short sequences are easier to sort

**Seek *upper bounds***

## Kind of analyses

1. **Worst case** (usually)
`T(n)`: *maximum* time of algorithm on any input of size `n`

2. **Average case** (sometimes)
`T(n)`: *expected* time of algorithm over all inputs of sice `n`

3. **Best case** (bogus)
cheat with a slow algorithm that works fast on *some* input

## Machine independent time

*What is insertion sort's worst case time?*

Idea: ignore machine-dependent constants and look at *growth* of `T(n)` as `n` tends to infinity

## **$\Theta$**-notation 

Math:

$\Theta(g(n))=\left\{f(n):\exist~ c_1,c_2,n_0>0 ~ s.t. ~ \forall n \geq n_0 ~ 0 \leq c_1g(n) \leq f(n) \leq c_2g(n)\right\}$

Engineering:
Drop low-order tems, ignore leading constants

Asymptotic performance

## Insertion Sort analysis

- **Worst case**:

$T(n) = \displaystyle \sum_{j=2}^{n} \Theta(j) = \Theta(n^2)$ [arithmetic series]

- **Average case**

$T(n) = \displaystyle \sum_{j=2}^{n} \Theta(\frac{j}{2}) = \Theta(n^2)$

## **Merge Sort**
$a_{1}, ..., a_{n}$
1. If $n = 1$, done
2. Recursively sort ${a_{1}, ..., a_{\lceil \frac{n}{2} \rceil }}$ and ${a_{\lceil \frac{n}{2} \rceil + 1}, ..., a_{n}}$
3. "Merge" the two sorted list

## **Merge Sort** - C++

    void merge_sort(std::vector<int> &A)
    {
        std::vector<int> A1;
        std::vector<int> A2;

        if (A.size() == 1) return;

        for (int i = A.size()/2; i < A.size(); i++)
            A1.push_back(A[i]);

        for (int i = A.size()/2; i < A.size(); i++)
            A2.push_back(A[i]);
        
        merge_sort(A1);
        merge_sort(A2);

        A = merge(A1,A2);
    }

$T(n) = \Theta(n)$ (linear time)

## Merge Sort analysis

$T(n) = 
\begin{cases}
    \Theta (1) & \quad \text{if } n = 1 \\
    2T(\frac{n}{2}) + \Theta(n) & \quad \text{if } n > 1
\end{cases}
$

where:
- $T(\frac{n}{2})$ is the time/number of operations for **sub-sorting**
- $\Theta(n)$ is for **merging**

But how much is $T(\frac{n}{2}$? The relation above is recursive, so a method to solve is needed. One possible approach is the **Recursion Tree**

Solution: $T(n) = \Theta(n\log n)$

*Conclusion:* **merge sort asymptotically better than insertion sort in the worst case**

## **Asymptotic notations:**

## **O**-notation (upper bounds)

$O(g(n)) = \left\{f(n) : \exist c > 0, n_{0} > 0 s.t. \forall 0 \leq f(n) \leq cg(n)\right\}$

*EXAMPLE:* $2n^2 \in O(n^3)$

## **$\Omega$**-notation (lower bounds)

$\Omega(g(n)) = \left\{ f(n) : \exist c > 0, n_0 > 0 s.t. \forall 0 \leq cg(n) \leq f(n) \right\}$

*EXAMPLE:* $\sqrt{n} in \Omega(\log n )$

## **$\Theta$**-notation (tight bounds)

$\Theta(g(n)) = O(g(n)) \cap \Omega(g(n))$

## **O** vs **$\Theta$**

Keep in mind that sometimes in the literature **O**-notation informally describe asymptotically tight bounds, that is, what we have defined using **$\Theta$**-notation

[img] http://bigocheatsheet.com