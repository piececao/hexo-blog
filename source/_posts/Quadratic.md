---
title: 二次型
date: 2025-10-05 17:46:51
tags: ['线性代数']
categories: ['笔记']
mathjax: true
---

基本曲线形状：

- 椭圆：$\frac{x^2}{a}+\frac{y^2}{b}=1$
- 双曲线：$\frac{x^2}{a}-\frac{y^2}{b}=1$
- 抛物线：$ax^2-y=1$

一般形式：
$$
\begin{align}
ax^2+bxy+cy^2+dx+ey+f &= 0 \\
\xRightarrow{(略去一次项)} \frac{a}{f}x^2 + \frac{b}{f}xy+\frac{c}{f}+1 &= 0 \\
\Rightarrow ax^2 + bxy^2 + cy^2 + 1 &= 0
\end{align}
$$
二次型矩阵
$$
\begin{align}
f(x,y) &= \begin{bmatrix}x & y\end{bmatrix}\begin{bmatrix}
a & b\\c & d
\end{bmatrix}\begin{bmatrix}
x \\ y
\end{bmatrix} \\
&= \begin{bmatrix}x & y\end{bmatrix} \begin{bmatrix}
ax+by \\ cx+dy
\end{bmatrix} \\
&= (ax^2+bxy) + (cxy + dy^2) \\
&= ax^2+(b+c)xy + dy^2 \\
&= ax^2+b'xy + dy^2
\end{align}
$$
也可以写做

$$
\begin{align}
f(x,y) &=  \vec{\mathbf{x}}^T\mathbf A\vec{\mathbf x},(\vec{\mathbf{x}}=\begin{bmatrix}x \\ y \end{bmatrix}) \\
&\mathbf A为实对称矩阵
\end{align}
$$

Q：为什么$\mathbf A$是实对称矩阵？

A：实对称矩阵有良好的性质：具有实特征值、特征向量俩俩正交
