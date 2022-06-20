# A proof on the separating words problem

We argue that, given any two unique words in $\\{0,1\\}^n$, there exists a finite state machine with $m = O(\log n)$ states that separates them. 

If accurate, this proof would close this open problem.

_For a good primer of the separating words problem, see [Remarks on Separating Words](http://web.mit.edu/dwilson/www/papers/desw.pdf)_

### Prior work
A [recent proof](https://link.springer.com/chapter/10.1007/978-3-031-07469-1_13) suggests a logarithmic lower bound (suggesting this upper bound is optimal).

The [state of the art](http://people.maths.ox.ac.uk/~chase/swtr.pdf) is that there exists a separating DFA with ${Õ}(n^{1/3})$ states.

### Our approach
We show that, as $n\rightarrow\infty$, the probability approaches 1 that, for any pair words $(a, b)$ such that $a \neq b$, they can be separated by a machine of size $m = \log_{2} n$.

Moreover, we show that, for large m, the converse probability is more than halved each time the number of states increases by 1.

### The probability

Let's define the probability $P(n, m)$ that, for all pairs of strings $(a, b)\text{, }a \neq b$ of length $n$, there exists a machine with $m$ states that separates them.

This is equivalent to:

$$
\begin{align}
P(n,m) &= p(\text{a random pair CAN be separated})^{(\text{number of pairs of strings})} \\
       &= [1 - p(\text{a random pair CAN'T be separated})]^{(\text{number of pairs of strings})} \\
       &= [1 - p(\text{a randomly selected machine does not separate a random pair})^{(\text{number of machines})}]^{(\text{number of pairs of strings})} \\
\end{align}       
$$

Which, if we introduce the variables:

$$
\begin{align}
  S :&= p(\text{a randomly selected machine does not separate a random pair}) \\
  M :&= \text{The number of machines of size m} \\
  N :&= \text{The number of pairs of unique strings of length n} \\
\end{align}  
$$

becomes:

$$
P(n,m) = (1 - S^M)^N
$$

Now let's calculate (and bound) these variables...

### M := The number of machines of size m
How many DFAs are there with m states?

Consider an m-state DFA with states $s_1, s_2, ... s_m$. Each state has $2$ transitions. That's $2m$ transitions. And each transition has $m$ places it can go. So, that makes $m^{2m}$ unique transition functions. The start state can be any one of $m$ states, so that gives $m^{2m+1}$.

If we want to work with unique DFAs, then we should account for those that are isomorphic to each other. Since there are $m!$ ways to order the states, we can divide by $m!$ to get the number of unique DFAs: $\frac{m^{2m+1}}{m!}$.

For the sake of simplicity, since $m! < m^m$, we will use the lower bound:

$$
M > m^m
$$

### N := The number of pairs of unique strings of length n
There are $2^n$ strings of length n, so there are $(2^n)^2 = 2^{2n}$ pairs of strings. But, $2^n$ of those combos are $(a, b) \text{ such that } a = b$. 

For the sake of simplicity, we will use the upper bound:

$$
N < 2^{2n}
$$

### S := p(a randomly selected machine doesn't separate a random pair)

Given a random pair of strings of length $n$, the probability that their final difference occurs at position $n$ is $\frac{1}{2}$, at $n-1$ is $\frac{1}{4}$, at $n-2$ is $\frac{1}{8}$, etc.

The probability that they differ in the $n^{th}$ position but are not separated is the probability that both transitions go to the same state: $\frac{1}{m}$.

Given that their final difference is in the $(n-1)^{th}$ position, the probability they are not separated is $1 - p(\text{they stay separate at each transition}) = 1 - (\frac{m-1}{m})^2$ (because if they were to transition to the same state, they'd stick together until the end since there are no more differences).

Similarly, given that their final difference is in the $(n-d)^{th}$ position, the probability they are not separated is $1 - (\frac{m-1}{m})^d$

##### An inlined footnote:
> (Actually, $\frac{m-1}{m}$ is a lower bound on the probability that the strings stay separate for a given transition. Because, knowing that the two strings have stayed separate when transitioning on digit $d_i$, we later sample transitions with replacement, i.e. there's a small chance that we see that exact transition again for later digits $d_{j > i}$, thus slightly increasing the probability of separation beyond the naive $\frac{m-1}{m}$. However, underestimating the actual probability by instead using $\frac{m-1}{m}$ is fine since it yields us an upper bound on the probability that the strings are not separated.)

Putting this together, $S$ is:

$$
\begin{align}
\frac{1}{2} *& \frac{1}{m} +& \\
\frac{1}{4} *& (1 - (\frac{m-1}{m})^2) +& \\
\frac{1}{8} *& (1 - (\frac{m-1}{m})^3) +& \\
 &... +& \\
(\frac{1}{2})^n *& (1 - (\frac{m-1}{m})^n) \\
\end{align}
$$

Now, we can work with a simpler upper bound on this probability.

Since $(\frac{m-1}{m})^x > \frac{m-x}{m}$, then $1 - (\frac{m-1}{m})^x < 1 - \frac{m-x}{m} = \frac{x}{m}$, so we can bound by:

$$
\begin{align}
\frac{1}{2} *& \frac{1}{m} +& \\
\frac{1}{4} *& \frac{2}{m} +& \\
\frac{1}{8} *& \frac{3}{m} +& \\
... \\
(\frac{1}{2})^n *& \frac{n}{m} +& \\
\end{align}
$$

Factoring out the $\frac{1}{m}$, we get $\frac{1}{m} * \displaystyle\sum_{i=1} ^{n} \frac{i}{2^i}$, and the summation can be bounded above by 2 (see [here](https://www.quora.com/How-do-you-evaluate-the-sum-of-n-2-n-from-n-1-to-infinity)). So, our final upper bound here is:

$$
S < \frac{2}{m}
$$

### A bound on the probability of separation
Plugging in these bounds to our probability function, we get a lower bound on our probability function:

$$
P(n,m) > (1 - (\frac{2}{m})^{m^m})^{2^{2n}} 
$$

Now, using this lower bound on $P(n,m)$, we demonstrate that the expected DFA solution size, in terms of states, is $m = O(\log n)$

### A logarithmic DFA solution size
We're going to work with a bound on the binomial expansion. But first, a little lemma:

#### A lemma
If $KX<1$, then $(1 - X)^K < 1 - KX$

#### Proof of lemma
Consider adjacent terms in the binomial expansion $t_0 - t_1 + t_2 + t_3 - ...$:

$$
1 - K * X + {K \choose 2} * X^2 - {K \choose 3} * X^3 ...
$$

For $i > 1$, 

$$
\begin{align}
{K \choose i} &= {K \choose {i-1}} * \frac{K-i}{i} \\
              &< {K \choose {i-1}} * K \\
\end{align}
$$

which implies that: 

$$
\begin{align}
t_{i} &= t_{i-1} * \frac{K-i}{i} * X \\
      &< t_{i-1} * K * X \\
      &< t_{i-1} * 1 \\
\end{align}      
$$

That is, each subsequent term (ignoring the sign) is less than the previous term.

Then, since the signs of terms alternate, $1 - KX$ is a lower bound (because the rest of the expansion can be thought of as adding pairs $(t_i,t_{i+1})$ such that $t_i-t_{i+1} > 0$, and if there are an odd number of terms left, the final "pair" consists only of an addition).

#### Using the lemma

Now, let $n$ and $m$ be exponentially related, such that $m = 2^n$, i.e. $m = \log_2 n$

Then, the probability function becomes:

$$
P(n = 2^m, m) > (1 - (\frac{2}{m})^{m^m})^{2^{2*{2^m}}} 
$$

Now, by the above lemma, since for large $m$, $2^{4^m} * (\frac{2}{m})^{m^m} < 1$, we get the following lower bound on the probability:

$$
P(n = 2^m, m) > 1 - 2^{4^m} * (\frac{2}{m})^{m^m}
$$

By similar logic, since $m^m$ grows much faster than $4^m$, as $n\rightarrow\infty$, so too does $m$, and the probability approaches $1$.

#### A note on the probability's rate of increase

Moreover, holding $n = 2^m$ constant but otherwise increasing the state machine size by 1 (to $m+1$), we see that the probability of NOT separation more than halves for sufficiently large $m$. This strengthens the argument that the expected solution size is $O(\log n)$:

$$
\begin{align}
1 - P(n = 2^m, m + 1) &= 2^{4^m} * (\frac{2}{m+1})^{({m+1})^{m+1}} \\
                  &< 2^{4^m} * (\frac{2}{m})^{m^{m+1}} \\
                  &= 2^{4^m} * (\frac{2}{m})^{m^{m} * m} \\
                  &= 2^{4^m} * [(\frac{2}{m})^{m^{m}}]^m \\
                  &= 2^{4^m} * (\frac{2}{m})^{m^{m}} * [(\frac{2}{m})^{m^{m}}]^{m-1} \\
                  &= [1 - P(n = 2^m, m)] * [(\frac{2}{m})^{m^{m}}]^{m-1} \\
                  &< [1 - P(n = 2^m, m)] * \frac{1}{2} \\      
\end{align}
$$




