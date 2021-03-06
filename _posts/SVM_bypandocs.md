---
layout: post
title: SVMs
---

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>



Linear SVM
==========

The basic motivating example for a linear support vector machine goes something like: suppose we have a dataset with two features, each datapoint is of either class $A$ or $B$, and we need to predict the class of each datapoint.

Geometrically this corresponds to having a set of points on the plane, each being either red or blue, and we need to predict the color of a point based on its location. That is we have a set of points $\mathbf{x}\_{i}$ where $i$ indexes the datapoints. For each $i$ we also have a target $y_{i}$ which is either -1 or 1. We can think of -1 as being red and 1 as being blue, but the reason we choose these two numbers (as opposed to say, 0 and 1) will become clear later.

The goal of a linear SVM is to find a line such that points on the left of the line are red, and points on the right are blue, and such that the distance between this line and the closest point of each class is minimized.

Hard Margin
-----------

-   Of course, to minimize such a distance we need to consider how this distance is calculated. To engange in some high school algebra, a line through the origin with unit normal $\mathbf{n}$ and a point $\mathbf{x}\_{0}$. As is known, the distance from $\mathbf{x}\_{0}$ to the line is $\mathbf{n}\cdot\mathbf{x}\_{0}$. Hence the line itself is given by the set of points such that $\mathbf{n}\cdot\mathbf{x}=0$.

-   If a point $\mathbf{x}\_{0}$ satisfies $\left(\mathbf{x}\_{0}-\mathbf{v}\right)\cdot\mathbf{n}=0$ for some vector $\mathbf{v}$, then $\mathbf{x}\_{0}-\mathbf{v}$ lies on the line. This basically adds $\mathbf{v}$ to all points on the previous line, resulting in a line with the same gradient $m$, but with a different intercept. Setting $\mathbf{v}\cdot\mathbf{n}=\tilde{b}$ we see that the equation for the $\mathbf{v}$-shifted line is $\mathbf{n}\cdot\mathbf{p}-\tilde{b}=0$.

### The Optimization Problem

-   Now a little twist that we can do is set $$\mathbf{n}=\frac{\mathbf{w}}{\left\Vert \mathbf{w}\right\Vert }$$ and $$\frac{\tilde{b}}{\left\Vert \mathbf{w}\right\Vert }=b$$

-   The previous equation now reduces to $$\mathbf{w}\cdot\mathbf{x}-b=0$$

-   For points lying on this line we have $\mathbf{n}\cdot\mathbf{x}=\frac{b}{\left\Vert \mathbf{w}\right\Vert }$.

-   For points satisfying $\mathbf{w}\cdot\mathbf{x}-b=1$ we have $\mathbf{n}\cdot\mathbf{x}=\frac{1}{\left\Vert \mathbf{w}\right\Vert }+\frac{b}{\left\Vert \mathbf{w}\right\Vert }$.

-   Since we have some freedom in choosing $\left\Vert \mathbf{w}\right\Vert $, we can set it so that $\mathbf{w}\cdot\mathbf{x}\_{0}-b=1$ where $\mathbf{x}\_{0}$ is the closest point to the line, whence the margin is $\frac{1}{\left\Vert w\right\Vert }$.

-   The optimization problem is to find $\mathbf{w}$ and $b$ such that $\mathbf{w}\cdot\mathbf{x}\_{i}-b\geq1$ if $y_{i}=1$, and $\mathbf{w}\cdot\mathbf{x}\_{i}-b\leq-1$ if $y_{i}=-1$, and such that $\left\Vert \mathbf{w}\right\Vert $ is minimized. Ie, to minimize $\left\Vert \mathbf{w}\right\Vert $ subject to $$\mbox{sgn}\left(\mathbf{w}\cdot\mathbf{p}\_{i}-b\right)=y_{i}$$ for all $i$.

-   The point is that getting $\mathbf{n}$ and $b$ is equivalent to specifying a hyperplane.

Soft Margin
-----------

-   If the classification is wrong, we need a measure of how wrong it is. This can be given by the hinge loss $$L\left(\mathbf{x},y\right)=\max\left(0,1-\left(\mathbf{w}\cdot\mathbf{x}+b\right)y\right)$$ Now minimize the average hinge loss.

-   In words, the hinge loss is zero for a correct classification (since $\mathbf{w}\cdot\mathbf{x}+b$ and $y$ have the same sign), and is proportional to the distance to the separating in the case on an incorrect classification (in which case they have opposite signs).

-   Remark: This is conceptually similar not entirely equivalent to maximizing $$\left(\mathbf{w}\cdot\mathbf{x}+b\right)y$$ which is what was done at <span>\[</span>1<span>\]</span>.

### Regularization

-   Have a parameter $\lambda$ that regulates the tradeoff between the margin and the hinge loss. Ie between getting more points right and getting wrong points less wrong. Now minimize $$\frac{1}{n}\sum_{i}^{n}L\left(\mathbf{x}\_{i},y_{i}\right)+\lambda\left\Vert w\right\Vert$$

General Kernels
===============

What’s actually happening here?

-   We’re basically picking a function $f$ so that the predictions are given by $$\begin{aligned}
    \mbox{pred}\left(\mathbf{x}\right) & = & \mbox{sgn}\left(f\left(\mathbf{x}\right)+b\right)\\
    f\left(\mathbf{x}\right) & = & \mathbf{w}\cdot\mathbf{x}\\
     & = & \sum_{j}w_{j}x_{j}\end{aligned}$$

-   Of course, we pick $f$ to minimize the hinge loss $$\begin{aligned}
    L\left(\mathbf{x}\_{i},y_{i}\right) & = & \max\left(1-y_{i}\left(f\left(\mathbf{x}\_{i}\right)+b\right),0\right)\\
    \bar{L} & = & \frac{1}{N}\sum_{i=1}^{N}L\left(\mathbf{x}\_{i},y_{i}\right)\end{aligned}$$

RBF Kernel
----------

This is where it gets more interesting: we can pick something else for $f$ $$f\left(\mathbf{x}\right)=\sum_{i}^{n}w_{i}y_{i}\exp\left(-4\left\Vert \mathbf{x}\_{i}-\mathbf{x}\right\Vert ^{2}\right)$$

-   Think of this as just superimposing a Gaussian hump on top of every point in the training dataset, pointing up or down depending on $y_{i}$, and with height given by $w_{i}$.

-   We need to find a set of heights $w_{i}$ that minimizes the hinge loss.

-   Then to classify a point, just look at whether the humps sum to something positive at that location.

General
-------

In fact we actually pick anything for $f$, as long as it’s of the form $$f\left(\mathbf{x}\right)=\sum_{i}w_{i}y_{i}K\left(\mathbf{x}\_{i},\mathbf{x}\right)$$

-   We require that $K$ be positive-semidefinite. Without this property all of these optimization problems would be able to run to negative infinity or use negative terms <span>\[</span>1<span>\]</span>.

-   Remark: the $w_{i}$ are positive, otherwise having $y_{i}$ would be redundant. It would also imply we could flip the sign of $K$ with impunity while having it remain positive semi-definite, which is false.

Recasting the Linear Kernel
---------------------------

To compare the above to the linear kernel, write $$\begin{aligned}
K\left(\mathbf{x}\_{i},\mathbf{x}\right) & = & \mathbf{x}\_{i}\cdot\mathbf{x}\\
f\left(\mathbf{x}\right) & = & \sum_{i}\alpha_{i}y_{i}\left(\mathbf{x}\_{i}\cdot\mathbf{x}\right)\\
 & = & \sum_{j}^{n}\left(\sum_{i}\alpha_{i}y_{i}\mathbf{x}\_{i}^{\left(j\right)}\right)\mathbf{x}^{\left(j\right)}\\
 & = & \sum_{j}^{n}w^{\left(j\right)}\mathbf{x}^{\left(j\right)}\\
 & = & \mathbf{w}\cdot\mathbf{x}\end{aligned}$$ So that $$\begin{aligned}
w^{\left(j\right)} & = & \sum_{i}\alpha_{i}y_{i}x_{i}^{\left(j\right)}\\
\mathbf{w} & = & \sum_{i}\alpha_{i}y_{i}x_{i}\end{aligned}$$

-   Ie we have a linear combination of dot prducts which will reduce to just a dot product in the end.

-   Graphically, think of it like this: the 3d plot for $\mathbf{x}\cdot\mathbf{y}$ is just a tilted plane that passes through the origin. Superimposing such planes results in just another such plane. Eventually you just get a plane, and this is what you’re testing for whether it’s greater than or less than zero.

### Support Vectors

Are those for which $\mathbf{w}\_{i}\neq0$. In the linear case there are two points $\mathbf{x}\_{1}$ and $\mathbf{x}\_{2}$ such that $\mathbf{x}\_{1}-\mathbf{x}\_{2}$ is parallel to the dividing line. But this isn’t necessarily the only way to do it.

Higher Dimensional Projection
-----------------------------

Abstractly, the positive-definiteness can be captured by setting $K\left(\mathbf{x}\_{1},\mathbf{x}\_{2}\right)=\left\langle \phi\left(\mathbf{x}\_{1}\right),\phi\left(\mathbf{x}\_{2}\right)\right\rangle $ where $\phi:V\to W$ is some map between inner product spaces.

For example $$\phi:\left(x,y\right)\mapsto\left(x,y,x^{2}+y^{2}\right)$$ So that $$\begin{aligned}
K\left(\mathbf{x},\mathbf{y}\right) & = & \phi\left(\mathbf{x}\right)\cdot\phi\left(\mathbf{y}\right)\\
 & = & \mathbf{x}\cdot\mathbf{y}+\left(\mathbf{x}\_{1}^{2}+\mathbf{x}\_{2}^{2}\right)\left(\mathbf{y}\_{1}^{2}+\mathbf{y}\_{2}^{2}\right)\end{aligned}$$

-   Note that this doesn’t imply conjugate symmetry or sesquilinearity at all, since $\phi$ can be anything, perhaps even discontinuous.

-   The whole projection to a higher dimensional space metaphor/interpretation really isn’t all that useful. It’s really trying to apply the intuition of the linear kernel to general kernels, when the other way around is much more natural.

Relationship to kNN
-------------------

-   The SVM is actually the same as weighted kNN with $k$ infinite, except that in this case $\alpha_{i}=1$ for all $i$.

-   So an SVM is like a weighted kNN on steroids.

-   Note that the rbf kernel is not separable, ie cannot be written in the form $$\exp\left(-4\left\Vert \mathbf{x}\_{i}-\mathbf{x}\right\Vert ^{2}\right)=f\left(\mathbf{x}\_{i}\right)g\left(\mathbf{x}\right)$$

Relationship to Logistic Regression
-----------------------------------

-   Remember that $$\begin{aligned}
    \log\left(\frac{p}{1-p}\right) & = & \sum_{i}w_{i}x_{i}\\
     & = & \mathbf{w}\cdot\mathbf{x}\end{aligned}$$ and $$\mbox{pred}\left(\mathbf{x}\right)=\mbox{sgn}\left(\mathbf{w}\cdot\mathbf{x}+\beta\right)$$ where $\beta$ is some function of the probability threshold.

-   So you’re optimizing $\mathbf{w}$ again, which is the vector of coefficients.

-   What we have is a linear SVM where you’re maximizing the log likelihood instead of the margin (or minimizing the hinge loss).

<span>1</span> http://www.win-vector.com/blog/2011/10/kernel-methods-and-support-vector-machines-de-mystified/
