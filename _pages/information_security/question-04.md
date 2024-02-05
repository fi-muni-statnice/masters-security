---
title: Probability, Theory of Information
author: John Doe
category: Information Security
layout: post
---

$\def\F{\mathscr{F}}
\def\R{\mathbb{R}}
\def\Im{\text{Im}}
\def\Var{\text{Var}}
\def\abs#1{|#1|}
\def\bigabs#1{\left|#1\right|}
\def\st{~|~}
\def\set#1{\\{#1\\}}
\def\condset#1#2{\\{#1~|~#2\\}}$

Probability. Discrete and continuous probability space. Random variables and their use. Mean value, variance. Stochastic processes, Markov chains. Theory of information (entropy, mutual information), coding theory (Huffman codes, noisy channel capacity). (IV111)

> **Random experiment** is a model of physical or thought experiment, where we are uncertain about **outcomes** of the experiment. It is specified by the **set of possible outcomes** and **probabilities** that each particular outcome occurs.
{: .block-tip}

# Probability
> **Sample space** is the non-empty set of possible outcomes of a **random experiment**, denoted by $S$. The outcomes of a random experiment are denoted by **sample points**.
{: .block-tip}
> **Event** of a random experiment with a sample space $S$ is any **subset of** $S$.
{: .block-tip}

> A **probability** function $P$ on a discrete sample space $S$ with a set of all events $\F = 2^S$ is a function $P: \F \to [0, 1]$ such that:
> - $P(S) = 1$
> - for any countable sequence of pairwise disjoint events $A_1, A_2, \dots$
>
>   $$P\left(\bigcup_{i=1}^\infty A_i\right) = \sum_{i=1}^\infty{P(A_i)}$$
>
>   (**countable additivity axiom**)
{: .block-tip}

# Probability Spaces

> Events $A_1, A_2, \dots, A_n$ are **mutually exclusive** iff
>
> $$\forall i \not= j . A_i \cap A_j = \emptyset$$
{: .block-tip}

> Events $A_1, A_2, \dots, A_n$ are **collectively exhaustive** iff
>
> $$A_1 \cup A_2 \cdots \cup A_n = S$$
{: .block-tip}

Mutually exclusive and collectively exhaustive subset of $S$ is called a **partition** of $S$.

> A **discrete probability space** is a triple $(S, \F, P)$ where
> - $S$ is a **sample space**,
> - $\F = 2^S$ is a collection of all events,
> - $P: \F \to [0, 1]$ is a **probability function**.
{: .block-tip}

> Let $S$ be a sample space. Then $\F \subseteq 2^S$ is a $\sigma$**-field** if
> - $\emptyset \in \F$,
> - if $A_1, A_2, \cdots \in \F$ then $\cup_{i=1}^\infty{A_i} \in \F$, and
> - if $A \in \F$ then $\bar{A} \in \F$.
{: .block-tip}

> A **probability space** is a triple $(S, \F, P)$ where
> - $S$ is a **sample space**,
> - $\F \subseteq 2^S$ is a $\sigma$**-field** - a collection of sets representing the allowable events
> - $P: \F \to [0, 1]$ is a **probability function**.
{: .block-tip}

# Random Variables
*[RV]: random variable
We define random variables to develop methods for studying random experiments with outcomes that can be described numerically. Almost all probabilistic computation is done using random variables.

## Discrete Random Variables
> A **discrete random variable** $X$ on a sample space $S$ is a function $X: S \to \R$ that assigns a real number $X(s)$ to each sample point $s \in S$.
{: .block-tip}

The **image** of a random variable $X$ is $\Im(X) = \condset{X(s)}{s \in S}$.

> For a random variable $X$ and $x \in \R$ we define the **inverse image** of $x$ to be the event
>
> $$[X = x] = \condset{s \in S}{X(s) = x}$$
>
> i.e. the set of all sample points from $S$ to which $X$ assigns the value $x$.
{: .block-tip}

$$P([X = x]) = P(\condset{s \in S}{X(s) = x}) = \sum_{s:X(s)=x}{P(s)}$$

> **Probability mass function** (probability distribution) of a discrete RV $X$ is a function $p_X: \R \to [0, 1]$ given by
>
> $$p_X(x) = P(X = x) = \sum_{s:X(s)=x}{P(s)}$$
{: .block-tip}

> The **cumulative distribution function** (distribution function) of a discrete RV $X$ is a function $F_X: \R \to [0, 1]$ given by
>
> $$F_X(x) = P(X \leq x) = \sum_{t \leq x}{p_X(t)}$$
{: .block-tip}

## Continuous Random Variables
> Given a probability space $(S, \F, P)$, a **continuous random variable** $X$ on a sample space $S$ is a function $X: S \to \R$ such that$
>
> $$\condset{s}{X(s) \leq r} \in \F \text{ for each } r \in \R$$
{: .block-tip}

> The **cumulative distribution function** of a random variable $X$ is a function $F_X: \R \to [0, 1]$ given by
>
> $$F_X(x) = P(X \leq x)$$
{: .block-tip}

Mean value, variance. Stochastic processes, Markov chains. Theory of information (entropy, mutual information), coding theory (Huffman codes, noisy channel capacity

## Mean Value
> **Expectation** (mean value) of a random variable $X$ is defined as
>
> $$E(X) = \sum_{x \in \Im(X)}{x \cdot P(X = x)}$$
>
> provided the sum is absolutely convergent (is finite). In case the sum is convergent but not absolutely convergent, we say that no finite expectation exists.
{: .block-tip}

For continuous variables:

$$E(X) = \int{x \cdot f_X(x) dx}$$

## Moments
> The $k^{\text{th}}$ **moment of a random variable** $X$ is defined as $E(X^k)$.
>
> The $k^{\text{th}}$ **central moment** of $X$ is
>
> $$\mu_k = E([X - E(X)]^k)$$
{: .block-tip}

## Variance
> The second central moment is known as the **variance** of $X$ and defined as
>
> $$\Var(X) = \sigma_X^2 = E([X - E(X)])^2$$
>
> The square root of $\Var(X)$ is the **standard deviation** $\sigma_X$.
{: .block-tip}

If variance is small, then $X$ takes values close to $E(X)$ with high probability. If the variance is large, then the distribution is more 'diffused'.

Also, $\Var(X) = E(X^2) - [E(X)]^2$

## Important Theorems
> **Markov inequality**: Let $X$ be a nonnegative random variable with finite mean value $E(X)$. Then for all $t > 0$ it holds that
>
> $$P(X \geq t) \leq \frac{E(X)}{t}$$
>
> Alternatively, for $k > 0$,
>
> $$P(X \geq k \cdot E(X)) \leq \frac{1}{k}$$
{: .block-tip}

> **Chebyshev inequality**: Is Markov inequality applied to the variable $[X - E(X)]^2$. Let $X$ be a random variable with finite variance. Then
>
> $$P[\abs{X - E(X)} \geq t] \leq \frac{\Var(X)}{t^2}, t > 0$$
{: .block-tip}

> **Weak Law of Large Numbers**: If $X_1, X_2, \dots$ is a sequence of i.i.d. random variables with expectation $\mu = E(X_k)$, then for every $\varepsilon > 0$
>
> $$\lim_{n \to \infty}{P\left(\bigabs{\frac{X_1 + \cdots + X_n}{n} - \mu} > \varepsilon\right)} = 0$$
{: .block-tip}

*[i.i.d.]: independent and identically distributed

The Weak Law of Large Numbers states that the probability the average of $X_i$ differs from the expectation by any positive value goes to 0 with the increasing number of samples.

> **Strong Law of Large Numbers**: If $X_1, X_2, \dots$ is a sequence of i.i.d. random variables with expectation $\mu = E(X_k)$, then
>
> $$P\left(\lim_{n \to \infty}{\frac{X_1 + \cdots + X_n}{n} = \mu}\right) = 1$$
{: .block-tip}

The Strong Law of Large Numbers states that for every infinite sequence the average of it has zero probability to differ from the expectation.

# Stochastic Processes
> A **stochastic process** is a collection of random variables $X = \condset{X_t}{t \in T}$. The index $t$ often represents **time**; $X_t$ is called the **state** of $X$ at time $t$.
{: .block-tip}

- The time domain $T$ can be either countable (**discrete-time process**), or uncountable (**continuous-time process**).
- The state space of $X_t$ can be finite (**finite-state process**), countable (**discrete-state process**), or uncountable (**continuous-state process**).

# Markov Chains
> A discrete-time stochastic process $\set{X_0, X_1, X_2, \dots}$ is a time-homogeneous **Markov chain** if
>
> - $P(X_t = x_t \st X_{t - 1} = x_{t - 1}, X_{t - 2} = x_{t - 2}, \cdots, X_0 = x_0) = P(X_t = x_t \st X_{t - 1} = x_{t - 1})$ (*Markov/memoryless property*)
> - $P(X_t = x \st X_{t - 1} = y) = P(X_1 = x \st X_0 = y)$ for all $n$ (*time-homogeneous property*)
{: .block-tip}

> A **discrete-time Markov chain** is a tuple $(S, P, \pi_0)$ where
> - $S$ is the **set of states**,
> - $P: S \times S \to [0, 1]$ with $\sum_{s' \in S}{P(s, s') = 1}$ is the **transition matrix**, and
> - $\pi_0 \in [0, 1]^{\abs{S}}$ with $\sum_{s \in S}{\pi_0(s) = 1}$ is the **initial distribution**.
{: .block-tip}

*[DTMC]: discrete-time Markov chain

The $m$-step transition matrix $P^{(m)} = P^m$ gives us the $m$-step reachability for all pairs of states in $S$.

> The **hitting time** of a set of states $A \subseteq S$ is a random variable $H^A: 2^S \to \set{0, 1, 2, \cdots} \cup \set{\infty}$ given by
>
> $$H^A = \inf\condset{n \geq 0}{X_n \in A}$$
{: .block-tip}

We write $i \Rightarrow^* j$ as an abbreviation for $H^\set{j} < \infty$ given $X_0 = i$ (starting from state $i$, we *eventually visit* $j$).

We write $i \Rightarrow^+ j$ as an abbreviation for $0 < H^\set{j} < \infty$ given $X_0 = i$ (starting from state $i$, we *eventually visit* $j$ in a *positive number of steps*).

> Starting from a state $s$, the **probability of hitting** $A \subseteq S$ is
>
> $$h_s^A = P(H^A < \infty \st X_0 = s)$$
>
> and the **mean time taken to reach** a set of states $A$ is
>
> $$\begin{align*}
> k_s^A &= E(H^A \st X_0 = s)\\
> &= \sum_{n < \infty}{n \cdot P(H^A = n \st X_0 = s)} + \infty \cdot P(H^A = \infty \st X_0 = s)
> \end{align*}$$
>
> where $0 \cdot \infty = 0$.
{: .block-tip}

## State Classification
> A state $i$ of a Markov chain is said to be **absorbing** iff it cannot be left once it is entered ($p_{i,i} = 1$).
{: .block-tip}

> A state $i$ of a Markov chain is said to be **recurrent** iff, starting from state $i$, the process eventually returns to state $i$ with probability 1, i.e.
>
> $$P(i \Rightarrow^+ i) = 1$$
{: .block-tip}

> A state of a Markov chain is said to be **transient** (non-recurrent) iff there is a positive probability that the process will not return to this state, i. e.
>
> $$P(i \Rightarrow^+ i) < 1$$
{: .block-tip}

Every transient state is visited **finitely many times** *almost surely* (with probability 1).

In a **finite-state Markov chain**, each recurrent state is almost surely either **not visited** or **visited infinitely many times**.

## Markov Chain Analysis
**Transient analysis**:
- distribution after $k$ steps
  - the $k$-th power of the transition matrix
- reaching/hitting probability
- (mean) hitting time

**Long-run analysis**:
- probability of infinite hitting
- mean inter-visit time
- long-run limit distribution
- stationary (invariant) distribution

# Theory of Information

## Entropy
> Let $X$ be a random variable with a probability distribution $p(x) = P(X = x)$. Then the **(Shannon) entropy** of the random variable $X$ is defined as
>
> $$H(X) = -\sum_{x \in \Im(X)} \log p(x)$$
{: .block-tip}

> Let $X$ and $Y$ be random variables and $y \in \Im(Y)$. The **conditional entropy** of $X$ given $Y = y$ is
>
> $$H(X \st Y = y) = - \sum_{x \in \Im(X)} P(X = x \st Y = y) \log P(X = x \st Y = y)$$
{: .block-tip}

> Let $X$ and $Y$ be random variables with a joint probability distribution $p(x, y) = P(X = x, Y = y)$. Let us denote $p(x \st y) = P(X = x \st Y = y)$. The **conditional entropy** of $X$ given $Y$ is
>
> $$\begin{align*}H(X \st Y) &= \sum_{y \in \Im(Y)} p(y) H(X \st Y = y) \\
> &= -\sum_{y \in \Im(Y)} p(y) \sum_{x \in \Im(X)} p(x \st y) \log p(x \st y) \\
> &= -\sum_{x \in \Im(X)} \sum_{y \in \Im(Y)} p(x, y) \log p(x \st y) \\
> &= -E_p [\log p(X \st Y)] \\
> \end{align*}$$
{: .block-tip}

#### Chain Rule of Conditional Entropy
Let $X$ and $Y$ be random variables. Then, $H(X, Y) = H(Y) + H(X \st Y)$.

## Mutual Information
> The **cross-entropy** of the distribution $q$ relative to a distribution $p$ on a common set of sample points $\Im(X)$ is defined as
>
> $$H(p \Vert q) = -\sum_{x \in \Im(X)}{p(x) \log{q(x)}} = -E_p[\log{q(X)}]$$
{: .block-tip}

The relative entropy measures the **inefficiency of assuming that a given distribution is** $q$ when the true distribution is $p$.

> The **relative entropy** (**Kullback-Leibler divergence**) between two probability distributions $p(x)$ and $q(x)$ on a common set of sample points $\Im(X)$ is defined as
>
> $$D(p \Vert q) = -\sum_{x \in \Im(X)}{p(x) \log{\frac{p(x)}{q(x)}}} = -E_p\left[\log{\frac{p(X)}{q(X)}}\right]$$
>
> If $p$ and $q$ images differ, we use $0 \log \frac{0}{q} = 0$ and $p \log \frac{p}{0} = \infty$.
{: .block-tip}

Note that $D(p \Vert q) = H(p \Vert q) - H(p)$.

> Let $X$ and $Y$ be random variables with a joint distribution $p$. The **mutual information** $I(X; Y)$ is the relative entropy between the joint distribution and the product of marginal distributions $p_X$ and $p_Y$
>
> $$I(X; Y) = D(p \Vert (p_X \cdot p_Y)) = E_p \left[ \log\frac{p(X, Y)}{p_X(X)p_Y(Y)} \right]$$
{: .block-tip}

Also, $I(X; Y) = H(X) - H(X \st Y)$ and from the symmetry, $I(X; Y) = H(Y) - H(Y \st X)$.

As $H(X, Y) = H(X) + H(Y \st X)$, we get

$$\begin{align*}
I(X; Y) &= - H(X, Y) + H(X) + H(Y \st X) + H(Y) - H(Y \st X) \\
&= H(X) + H(Y) - H(X, Y)
\end{align*}$$

![mutual-information](/masters-security/assets/mutual_information_incexc.png 'Mutual information'){:height="250"}

# Coding Theory
We model the **source** of information as a random variable $X$ with all possible messages equal to $\Im(X)$. The source emits the message $x$ with probability $P(X = x)$. A sequence of messages is created by a sequence of independent trials described by $X$ and thus by a random process $X_1, X_2, \dots$ where $X_i$ are i.i.d. - a **memoryless source**.

> A **code** $C$ for a random variable (memoryless source) $X$ is a mapping $C: \Im(X) \to D^*$, where $D^*$ is the set of all finite-length strings over the alphabet $D$. With $\abs{D} = d$, we say the code is $d$-ary, $C(x)$ is the codeword assigned to $x$ and $l_C(x)$ denotes the length of $C(x)$.
{: .block-tip}

> The expected length $L_C(X)$ of a code $C$ for a random variable $X$ is given by
>
> $$L_C(X) = \sum_{x \in \Im(X)}{P(X = x) l_C(x) = E[l_C(X)]}$$
> {: .block-tip}

We will assume (WLOG) that the alphabet is $D = \set{0, 1, \dots, d - 1}$.

*[WLOG]: without loss of generality

> A code $C$ is said to be **non-singular** if it maps every element in the range of $X$ to different string in $D^*$, i.e.
>
> $$\forall x, y \in \Im(X) . x \not= y \Rightarrow C(x) \not= C(y)$$
{: .block-tip}



## Huffman Codes

## Noisy Channel Capacity