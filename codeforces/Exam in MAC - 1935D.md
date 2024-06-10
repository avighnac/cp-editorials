https://codeforces.com/contest/1935/problem/D

To begin with, let me state an obvious boolean logic fact: $\neg (a \land b) = \neg a \lor \neg b$. One way to solve this problem is to find the number of pairs that **don't satisfy** the given condition, and subtract this from the total number of pairs.

The negated problem statement is this: given an array of unique integers $s$, find the number of pairs of integers $(x, y)$ such that $0 \le x \le y \le c$, and either $x+y \in s$ or $x-y \in s$, or both.

The **total** number of these $(x,y)$ pairs is $\frac{(c+1)(c+2)}{2}$. This example with $c=3$ should clear things up:
	$(0, 0)$, $(0, 1)$, $(0, 2)$, $(0, 3)$,
	$(1, 1)$, $(1, 2)$, $(1, 3)$,
	$(2, 2)$, $(2, 3)$,
	$(3, 3)$

Now, for a pair $(a,b)$, let $e$ being 'present on the left' mean that $a+b=e$, and let $e$ being 'present on the right' mean that $b-a=e$. Let $\operatorname{lp}(x)$ be a function that returns the number of times $x$ is present on the left across all $\mathcal{O}(n^2)$ pairs, and let $\operatorname{rp}(x)$ be a similar function that returns the number of times $x$ is present on the right across all $\mathcal{O}(n^2)$ pairs. Along with these two functions, we need to define another additional function: Let $\operatorname{pe}(x,y)$ be $1$ if there exists a pair $(a,b)$ with $a, b \leq c$ such that both $a+b=x$ and $b-a=y$, and $0$ otherwise (all of this may seem a little bit arbitrary, but stick with me for a second).

We'll now try to find a way to count the number of pairs satisfying our negated problem by iterating over $s$ (that's really the only thing we can iterate over). With some [PIE](https://brilliant.org/wiki/principle-of-inclusion-and-exclusion-pie/), we get that the number of pairs ($x$) is:
$$\displaystyle x=\sum_{e\in s} \operatorname{lp}(e)+\sum_{e \in s}\operatorname{rp}(e) - \sum_{u \in s} \sum_{v \in s} \operatorname{pe}(u,v)$$
Let us first try to find $v_3:=\displaystyle \sum_{u \in s} \sum_{v \in s} \operatorname{pe}(u,v)$.
$$
\begin{cases}
a + b = x \\
b - a = y
\end{cases}
$$
is a system of two equations with two unknowns. Solving for $a$ and $b$, we get $\displaystyle a=\frac{x-y}{2}$ and $\displaystyle b=\frac{x+y}{2}$. For $(a,b)$ to be a valid pair, both $a$ and $b$ need to be integers between $[0,c]$. In other words:
	$\displaystyle 0 \le a \le c$ and $\displaystyle 0 \le b \le c$
	$\displaystyle \implies 0 \le \frac{x-y}{2} \le c$ and $\displaystyle 0 \le \frac{x+y}{2} \le c$
	$\displaystyle \implies 0 \le x-y \le 2c$ and $\displaystyle 0 \le x+y \le 2c$

From this we infer that if $\operatorname{pe}(x,y)=1 \implies y \le x$. If we iterate over each element $s_i$ and find the first index $j$ such that $s_i + s_j > 2c$ (if there's no such element, $j=n$), $j-i$ pairs of the form $(s_u,s_i)$ with $u \in [i, j)$ will be valid, existing pairs: we simply add $j-i$ to $v_3$ (initialized to $0$) on every iteration.

But wait, we're not done yet! We also need to make sure that $s_u+s_i$ is even, so that $a$ and $b$ are integers. This means that we want the parities of $s_u$ and $s_i$ to match. Here's an easy way to do this: if $s_i$ is even, subtract from $v_3$ a number equal to how many odd numbers $s_k$ there are with $k \in [i,j)$, and if $s_i$ is odd, do the same thing for even numbers. Two prefix-sum arrays can be used to quickly count how many odd/even numbers there are within a range.

This will take $\mathcal{O}(n \log n)$ time with binary search.

Now let us determine an expression for $\operatorname{lp}(e)$. How many pairs have $e$ present on the left is equivalent to asking how many ways there are to write $e$ as a sum of two integers $x,y \le c$, without double counting (that is, if $(x,y)$ has been counted as a solution, $(y,x)$ should not). Let a pair $(x,y)$ summing up to $e$ mean that $x+y=e$.

$e=0$ is a special case. If $e=0$, there is only $1$ pair that sums up to $e$: $(0,0)$. Similarly, since $x,y \le c$, if $e > 2c$, it's impossible to have any pairs that sum up to $e$. Now let us consider two cases

If $e \le c$, then we can write all of the pairs that sum up to $e$ as:
	$(0,e)$
	$(1,e-1)$
	$(2,e-2$)
	$...$
	$(e,0)$

We will now find the first overcounted pair in this list. Let the $n^{\text{th}}$ pair be $(n-1,e-n+1)$. Then we get the inequality:
	$\displaystyle n-1 > e-n+1$
	$\displaystyle \implies 2n > e+2$
	$\displaystyle \implies n > \frac{e+2}{2}$

Let us define another function:
$$\operatorname{gtf}(e) =
\begin{cases}
\displaystyle \frac{e}{2}+2 & \text{if } e \text{ is even} \\
\displaystyle \lceil\frac{e+2}{2}\rceil & \text{otherwise}
\end{cases}$$
('gtf' is short for 'greater function')

Since the first overcounted pair is $\operatorname{gtf}(e)$, the number of pairs in this case is $\operatorname{lp}(e)=\operatorname{gtf}(e)-1$.

When $e>c$, our pairs still remain the same, but some at the beginning (for example, the pair $(0,e)$ in all cases) and some at the end will be invalid. The valid pairs will lie somewhere in the middle. We have three conditions to abide by:
	1) $n-1\le c$, the first term of the pair
	2) $e-n+1 \le c$, the second term of the pair
	3) $n \le \operatorname{gtf}(e)-1$, to prevent overcounting

Solving these three inequalities gives us $\displaystyle e-c+1 \le n \le \min(c+1, \operatorname{gtf}(e)-1)$, which means $n \in [e-c+1, \min(c+1, \operatorname{gtf}(e)-1)]$ (it can be checked that the start of this range is only bigger than its end when $e>2c$). Thus the number of valid pairs in this case will be $\operatorname{lp}(e)=\min(c+1, \operatorname{gtf}(e)-1)-e+c$.

We now have:
$$\mathrm{lp}(e) =
\begin{cases}
\displaystyle 1 & \text{if } e=0 \\
\displaystyle 0 & \text{if } e>2c \\
\displaystyle \operatorname{gtf}(e)-1 & \text{if } e\le c \\
\displaystyle \min(c+1, \operatorname{gtf}(e)-1)-e+c & \text{otherwise }
\end{cases}$$

Let us finally determine an expression for $\operatorname{rp}(e)$. How many pairs have $e$ present on the right is equivalent to asking how many ways there are to write $e$ as a difference of two integers $x,y \le c$, without double counting. Let a pair $(x,y)$ differing to $e$ mean that $y-x=e$.

If $e > c$, the answer is $0$ since a difference of two non-negative integers $(y-x)$ cannot equal $e$ for any $y<e$. If $e\le c$, then the pairs differing to $e$ will be:
	$(0,e)$
	$(1,e+1)$
	$(2,e+2)$
	$...$
	$(c-e,c)$

There are $c-e+1$ such pairs. So:
$$\mathrm{rp}(e) =
\begin{cases}
\displaystyle 0 & \text{if } e>c \\
\displaystyle c-e+1 & \text{otherwise }
\end{cases}$$

We now have all the tools necessary to find the answer, which is $\displaystyle \frac{(c+1)(c+2)}{2}-x$.