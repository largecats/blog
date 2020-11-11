---
layout: post
title:  "Two's Complement"
date:   2019-11-18
categories: computer-systems
tags: computer-systems
---
<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>
* content
{:toc}

**Definition.** The two's complement of an $n$-bit binary number $x$ is given by $\bar{x}+1$, where $\bar{x}$ is constructed from $x$ by inverting the $0$s and $1$s.

**Definition.** The two's complement representation of a signed $n$-bit number $a_{n-1}\cdots a_1a_0$ (or simply "two's complement number") is given by

$$-2^{n-1} a_{n-1} + 2^{n-2}a_{n-2}+ \cdots 2^1 a_1 + 2^0a_0$$

where the first bit is the sign bit.

**Theorem.** Let $x_c$ denote the two's complement of an $n$-bit binary number $x$. Then $x + x_c = 2^n$ in decimal.



**proof.** The binary representating of $2^n$ is 

$$1\underbrace{00\cdots 0}_{\text{n}}.$$ 

Write this as 

$$1 + \underbrace{11\cdots 1}_{\text{n}}.$$ 

Note that by definition of $\bar{x}$,

$$\underbrace{11\cdots 1}_{\text{n}} - x = \bar{x}.$$ 

So

$$x+x_c = x + (\bar{x}+1) = x + (\underbrace{11\cdots 1}_{\text{n}} - x) + 1 = \underbrace{11\cdots 1}_{\text{n}} + 1 = 1\underbrace{00\cdots 0}_{\text{n}},$$

which is $2^n$ in decimal.

**Corollary.** The two's complement representation of the negative of a binary number $x$ is given by $-x = \bar{x} + 1$. That is, the two's complement representation of the negative of a binary number can be constructed by inverting the $0$s and $1$s and adding $1$.

**proof.** By the definition of $\bar{x}$, $x + \bar{x} = \underbrace{11\cdots 1}_{\text{n}}$, which is $-1$ in two's complement representation because

$$-2^{n-1} + 2^{n-2} + \cdots + 2 + 1 = -2^{n-1} + \sum_{i=0}^{n-2} 2^i = -2^{n-1} + 1\cdot (2^{n-1}-1) = -1.$$

So in two's complement representation, $-x = \bar{x}+1$.
