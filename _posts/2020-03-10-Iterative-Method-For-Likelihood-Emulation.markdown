---
layout: post
mathjax: true
title:  "Iterative Method For Likelihood Emulation"
date:   2020-03-10 15:30:00
author: A.Mootoovaloo
permalink:
categories:
  - Machine Learning and Statistics
tags:
  - 
  -
excerpt:
---

<p align="justify">The paper, entitled '<b><font size="2.5">Cosmological parameter estimation via iterative emulation of likelihoods</font></b>' was published on <a href="https://arxiv.org/abs/1912.08806">arXiv</a> recently. The idea behind is to use Gaussian Process to emulate the log-likelihood and to progressively augment the training set via Bayesian Optimisation. In this post, I shall illustrate the technique via simple straight line fitting example, which I believe will be trivial to follow.</p>

<img src="/images/bo.png" align="right" width = "400" style = "margin-left: 10px; margin-bottom: 10px"/>

<p><b><font size="3">Analytical Posterior</font></b></p>

<p align="justify">To start with, we randomly draw 50 points uniformly from $x\in[0, 1]$ and compute $\mathbf{y}$, which is given by</p>

$$
\mathbf{y} = \theta\mathbf{x} + \boldsymbol{\epsilon}
$$

<p align="justify">We fix $\theta = 1$ and $\boldsymbol{\epsilon}\sim\mathcal{N}(0, 0.04^{2})$. We also assume a Gaussian prior for $\theta$, where $p(\theta)=\mathcal{N}(1,1)$. The posterior distribution of $\theta$ can be derived analytically and is in fact another Gaussian distribution, given by:
$$
p(\theta|\mathbf{y}) = \mathcal{N}(\mu, \sigma^{2})
$$

where 

<ul>
  <li>$\mu = 625\sigma^{2}\mathbf{D}^{\textrm{T}}\mathbf{y}$</li>
  <li>$\sigma^{2}=(1 + 625\mathbf{D}^{\textrm{T}}\mathbf{D})^{-1}$</li>
  <li>$\mathbf{D} = [x_{0},\,x_{1},\ldots x_{N-1}]^{\textrm{T}}$</li>
</ul>
</p>

with the factor 625 arising due to 1/0.04<sup>2</sup>.

<p><b><font size="3">Gaussian Process and Bayesian Optimization</font></b></p>

<p align="justify">Gaussian Process is a joint multivariate Gaussian distribution over functions with a continuous domain. We will not cover it in this post (for further details, see <a href="https://harry45.github.io/blog/2016/10/Gaussian-Process">here</a>). The predictive distribution is a Normal distribution with mean and variance given respectively by:

$$
f_{*} = \mathbf{k}_{*}^{\textrm{T}}\mathbf{K}^{-1}\mathbf{y}_{\textrm{train}}
$$

$$
\sigma_{*}^{2} = k_{**} - \mathbf{k}_{*}^{\textrm{T}}\mathbf{K}^{-1}\mathbf{k}_{*}
$$

On the other hand, Bayesian Optimization is a proxy for finding the optima of expensive objective functions via the acquisition function, which we will discuss briefly below. A typical example of expensive computation is in cosmology where we have a series of multiple integrations and other expensive computations (for example, when we also have to account for the systematics) prior to computing the log-likelihood in a Bayesian analysis.

<p><b><font size="2">Acquisition Functions</font></b></p>
One class of acquisition function (also referred to infill function) is improvement-based function. <i>Probability of improvement</i> (also called maximum probability of improvement or the P-algorithm) is one such example and is given by:

$$
\textrm{PI}(\theta) = \Phi(z)
$$

In the same spirit, the <i>expected improvement</i> does not only consider the probability of improvement but also takes into account the magnitude of improvement that the additional point will provide. It reads:

$$
\textrm{EI}(\theta)=\begin{cases}
\begin{array}{c}
\left(\mu-f_{\textrm{max}}-\alpha\right)\Phi(z)+\sigma\phi(z)\\
0
\end{array} & \begin{array}{c}
\textrm{if }\sigma>0\\
\textrm{if }\sigma=0
\end{array}\end{cases}
$$

where $\Phi(\centerdot)$ and $\phi(\centerdot)$ correspond to the Cumulative Distribution Function (CDF) and Probability Distribution Function (PDF) of a normal distribution respectively. $f_\textrm{max}$ refers to the maximum of the expensive function, given a set of inputs, $\theta$ and 

$$
z(\theta) = \frac{\mu(\theta) - f_{\textrm{max}} - \alpha}{\sigma(\theta)}.
$$

On the other hand, another acquisition function based on upper confidence bound is given by:

$$
\textrm{UCB}(\theta) = \mu + \alpha \sigma
$$

Note the additional parameter $\alpha$ in the acquisition functions. It is usually user-defined and it essentially controls the trade-off between exploration (region of high uncertainty) and exploitation (region with high mean).
</p>

<p><b><font size="3">Our Implementation</font></b></p>

<p align="justify">
We initially start with just 4 training points (generated using Latin Hypercube samples, <a href="https://en.wikipedia.org/wiki/Latin_hypercube_sampling">LHS</a>) given by the first 4 rows in the table at the bottom. We then use the Upper Confidence Bound (UCB) acquisition function (with $\alpha=15$) to iteratively add two points (the last two rows in red in the table) to the Gaussian Process model. See algorithm below for further details. 
</p>


<style>
table {width: 30%;}
</style>

<table class="tableizer-table" align = "left">
<thead><tr class="tableizer-firstrow"><th>$\theta$ </th><th>$\textrm{log } L$</th></tr></thead><tbody>
 <tr><td align="center">0.9662</td><td align="center">-26.1204</td></tr>
 <tr><td align="center">1.0064</td><td align="center">-17.2318</td></tr>
 <tr><td align="center">1.0223</td><td align="center">-18.1605</td></tr>
 <tr><td align="center">1.0511</td><td align="center">-26.2248</td></tr>
 <tr><td align="center"><font color="red">0.9860</font></td><td align="center"><font color="red">-19.7343</font></td></tr>
 <tr><td align="center"><font color="red">1.0369</font></td><td align="center"><font color="red">-21.2069</font></td></tr>
</tbody></table>

<img src="/images/BO-Algorithm.png" align="right" width = "600" style = "margin-right: 10px; margin-bottom: 10px"/>

<p><b><font size="3">Results and Conclusions</font></b></p>

<p align="justify">
In this setup, we are able to reconstruct the log-likelihood almost perfectly after augmenting the data set in two iterations. As seen in the plot below in the right panel, the posterior distribution of $\theta$ is identical to the exact one (which we calculated analytically previously). The vertical broken line shows the value of $\theta=1$ which we initially used to generate the data. 
</p>

<img src="/images/finalPosterior.png" align="center" width = "800" style = "margin-bottom: 0.1px"/>

<p align="justify">
However, in high dimensions, the volume of the parameter space increases and it is not easy to reconstruct a function perfectly (if this is the main objective). Moreover, the acquisition functions themselves contain multiple local optima (as seen in the Figure on the top) and the choice of the acquisition function is an interesting topic. They can often be greedy and favour exploitation over exploration, hence the choice of $\alpha$ also matters. 
</p>

<p><b><font size="3">References</font></b></p>
<ol type="1">
<li>A Tutorial on Bayesian Optimization (arXiv, <a href="https://arxiv.org/abs/1012.2599"><i style="font-size:12px" class="fa">&#xf08e;</i></a>) </li>
<li>Cosmological parameter estimation via iterative emulation of likelihoods (arXiv, <a href="https://arxiv.org/abs/1912.08806"><i style="font-size:12px" class="fa">&#xf08e;</i></a>) </li>
</ol>
