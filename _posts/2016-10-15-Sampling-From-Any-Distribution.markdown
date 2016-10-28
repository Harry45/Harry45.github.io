---
layout: post
mathjax: true
title:  "Sampling From Any Distribution"
date:   2016-10-15 08:26:00
author: A.Mootoovaloo
permalink:
categories:
  - Machine Learning and Statistics
tags:
  - 
  -
excerpt:
---

<p align="justify">One of the recent topics which I had to study was how to sample from any distribution. While this seems to be a trivial question, Google did not help me much, although I did also try to post the problem on <a href="http://stackoverflow.com/questions/40263486/drawing-random-samples-from-any-distribution">stackoverflow</a>! Here, we will show three methods which we can use to generate random numbers from a distribution. In particular, we will look at some in-built functions in <code>scipy</code>, acceptance-rejection sampling and use our own method as well. The distribution which we will use is given by </p>

\begin{align}
\mathcal{P}\left(x\right) = \dfrac{k\,x^3}{e^{2x} - 1}
\end{align}

<p align="justify">Note that we will use the following notations: $\mathcal{P}\left(\centerdot\right)$ is the probability distribution function (PDF) while $\Phi\left(\centerdot\right)$ is the cumulative distribution function (CDF). This generic shape of this distribution follows that of a black body spectrum. This distribution is used only to illustrate the idea of sampling and has no underlying explanation to the actual physics of black body radiation.</p>

<h2>Using Scipy</h2>

<p align="justify">The class <code>rv_continuous</code> in <code>scipy.stats</code> is straightforward to use. We simply need to define either the PDF or CDF using <code>_pdf</code> and <code>_cdf</code> respectively as shown below. We first define the PDF, followed by a function to normalise it. Note also that the PDF that we define in the class should be normalised.</p>

{% highlight python %}
x = np.linspace(1E-5, 10.0, 1E4)

def p(x):
	return (x**3)/(np.exp(2.0 * x) -1)

# Define function to normalise the PDF
def normalisation(x):
	return simps(p(x), x)

# Define the distribution using rv_continuous
class blackbody(ss.rv_continuous): 
    def _pdf(self, x, const):
        return (1.0/const) * p(x)
{% endhighlight %}

<p align="justify">We are now ready to use our above written functions to find $\mathcal{P}\left(x\right)$, $\Phi\left(x\right)$ and generate samples from the underlying distribution. We first need to instantiate it with the lower and upper limits given by <code>a</code> and <code>b</code> respectively. In principle, if not defined, it will be considered to be from $-\infty$ to $+\infty$.</p>

{% highlight python %}

blackbody_distribution = blackbody(name="blackbody_distribution", a=0)

# Find the normalisation constant first
norm_constant = normalisation(x)

# create pdf, cdf, random samples
pdf = blackbody_distribution.pdf(x = x, const = norm_constant)
cdf = blackbody_distribution.cdf(x = x, const = norm_constant)
samples = blackbody_distribution.rvs(const = norm_constant, size = 1E4)

{% endhighlight %}

{% include image.html url="/images/scipy_continuous.jpg" caption="Samples generated using scipy" width=500 align="center" %}

<p align="justify">The above plot shows the PDF, CDF and the samples generated from the distribution. In particular, we choose to draw 10 000 random samples. <code>rv_continuous</code> becomes useful when one needs more than just the samples. Once we have defined it, we can simply find other properties such as its mean, standard deviation and several more (see the <a href="https://docs.scipy.org/doc/scipy-0.16.0/reference/generated/scipy.stats.rv_continuous.html">documentation</a> for further details).</p>

<h2>Acceptance-Rejection Sampling</h2>

{% include image.html url="/images/circle_accept_reject.jpg" caption="Estimating value of $\pi$ using Monte Carlo Method" width=250 align="right" %}

<p align="justify">Imagine we have a square board of size $l$, on which there is a circle of radius $0.5l$ at the centre of the board, that is, at $\left(0.5l,0.5l\right)$ in a Cartesian coordinate system. We are throwing darts randomly on the board $N$ times. If, $n$ is the number of times that the darts lie within the circle, the probability of throwing the darts successfully into the circle is simply $\frac{n}{N}$. This is also roughly equal to the ratio of the area of the circle to the area of the square board. One could use this idea to have a rough estimate of the value of $\pi$, that is,</p>
\begin{align}
\pi \approx 4 \times \frac{n}{N}
\end{align} 

<p align="justify">As $N$ becomes larger, one would have a more accurate estimate of the number $\pi$. This is the idea behind Monte Carlo sampling. Through this lens, an alternative way to sample from a normalised distribution is to use acceptance-rejection sampling scheme. It is also sometimes referred to as Lahiri's Sampling method.</p>

<p align="justify"> Suppose $X$ is a scalar random variable taking values in the interval $\left[a, b\right]$ according to the continuous probability density function $f\left(x\right)$. Let $M$ be an upper bound for $f$ on $\left[a, b\right]$, $M$ assumed finite. Choose $x$ uniformly in $\left[a, b\right]$ (for example, $x = a + t(b–a)$ where $t$ is uniform in $\left[0, 1\right]$). Then choose $u$ uniformly in $\left[0, M\right]$. If $u\leq f\left(x\right)$, we select $x$. Otherwise we reject $x$ and start over.</p>


<h2>Our Own Method</h2>
{% include image.html url="/images/own_cdf_pdf_samples.jpg" caption="Generating our own samples" width=500 align="center" %}

