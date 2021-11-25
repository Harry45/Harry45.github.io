---
layout: post
mathjax: true
title:  "Parameter Inference with MOPED and GP"
date:   2021-11-16 07:11:00
author: A.Mootoovaloo
permalink:
categories:
  - Machine Learning and Statistics
tags:
  - 
  -
excerpt:
---


<p align="justify">In this post, I will cover briefly my first paper which was published in <a href="https://academic.oup.com/mnras/article/497/2/2213/5873022">MNRAS</a> during my PhD. An important step in this paper is the compression and emulation of the MOPED coefficients. MOPED is an algorithm developed by <a href="https://academic.oup.com/mnras/article/317/4/965/1039456">Heavens et al. 2000</a>, which essentially compresses a data vector of size $N$ to just $p$ numbers, where $p$ is the number of parameters in the model. The first and subsequent MOPED vectors are given respectively by</p>

\begin{align}
\mathbf{b}\_{1}=\frac{\mathbf{C}^{-1}\mathbf{\mu}\_{,1}}{\sqrt{\mathbf{\mu}\_{,1}^{\textrm{T}}\mathbf{C}^{-1}\mathbf{\mu}\_{,1}}}
\end{align}

<p align="justify">and</p>

\begin{align}
\mathbf{b}\_{\alpha}=\frac{\mathbf{C}^{-1}\mathbf{\mu}\_{,\alpha}-\sum_{\beta=1}^{\alpha-1}(\mathbf{\mu}\_{,\alpha}^{\textrm{T}}\mathbf{b}\_{\beta})\mathbf{b}\_{\beta}}{\sqrt{\mathbf{\mu}\_{,\alpha}^{\textrm{T}}\mathbf{C}^{-1}\mathbf{\mu}\_{,\alpha}-\sum_{\beta=1}^{\alpha-1}(\mathbf{\mu}\_{,\alpha}^{\textrm{T}}\mathbf{b}\_{\beta})^{2}}}\;\;(\alpha>1).
\end{align}


<p align="justify"> The weighing vector, $\mathbf{b}$ encapsulates as much information as possible for a specific model parameter $\mathbf{\theta}_{\alpha}$. This vector is then used to find linear combination of the data, $\mathbf{d}$ such that the compressed data is</p>

\begin{align}
y\_{\alpha}\equiv\mathbf{b}^{\textrm{T}}\_{\alpha}\mathbf{d}
\end{align}


<p align="justify">and the expected theoretical prediction is simply $y_{\alpha}\equiv\mathbf{b}^{\textrm{T}}_{\alpha}\mathbf{\mu}$. One would then use an MCMC algorithm to sample the posterior distribution of the model parameters using the MOPED compression algorithm. In particular, each MOPED vector is orthogonal to each other and is also normalised, hence the log-likelihood is simply</p>

\begin{align}
\textrm{log}\,\mathcal{L} = -\frac{1}{2}\sum\_{\alpha=1}^{p}(y\_{\alpha}-\mathbf{b}\_{\alpha}^{\textrm{T}}\mathbf{\mu})^{2}  + \textrm{constant}
\end{align}

<p align="justify">However, calculating the MOPED coefficient at each step in an MCMC can be expensive if the forward model itself is expensive. Hence, a training set is first generated at $N$ Latin Hypercube samples (LHS) and the MOPED coefficients are computed at these points, followed by modelling $p$ separate Gaussian Processes. These are then used as surrogates to sample the posterior distribution of the model parameters and the result is shown in the figure below. The figure shows the posterior with the full accurate solver CLASS (in tan) and the posterior due to the emulator is shown in blue. The contours are plotted at 68% and 95% credible intervals.</p>

{% include image.html url="/images/triangle_plot_derived_sigma_8_semi_gp_maximin_1000_7D.jpg" caption="The full posterior distribution of all parameters using the MOPED compression scheme."  width=800 align="center" %}

