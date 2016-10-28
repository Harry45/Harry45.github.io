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

blockquote {
    font-family: Georgia, serif;
    font-size: 12px;
    font-style: normal;
    width: 450px;
    margin: 0.25em 0;
    padding: 0.25em 40px;
    line-height: 1.45;
    position: relative;
    color: #383838;
    background:#ececec;
}


<p align="justify">One of the recent topics which I had to study was how to sample from any distribution. While this seems to be a trivial question, Google did not help me much, although I did also try to post the problem on <a href="http://stackoverflow.com/questions/40263486/drawing-random-samples-from-any-distribution">stackoverflow</a>! Here, we will show three methods which we can use to generate random numbers from a distribution. In particular, we will look at some in-built functions in <code>scipy</code>, acceptance-rejection sampling and use our own method as well. The distribution which we will use is given by </p>

\begin{align}
\mathcal{P}\left(x\right) = \dfrac{k\,x^3}{e^{2x} - 1}
\end{align}

<p align="justify">Note that we will use the following notations: $\mathcal{P}\left(\centerdot\right)$ is the probability distribution function (PDF) while $\Phi\left(\centerdot\right)$ is the cumulative distribution function (CDF). This generic shape of this distribution follows that of a black body spectrum. This distribution is used only for illustrate the idea of sampling and has no underlying explanation to the actual physics of black body radiation.</p>

{% highlight latex %}
a = b + c
\mathcal{P}
{% endhighlight %}



<h2>Using Scipy</h2>

<p align="justify">The class <code>rv_continuous</code> in <code>scipy.stats</code> is straightforward to use. We simply need to define either the PDF or CDF using <code>_pdf</code> and <code>_cdf</code> respectively.</p>

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

<p align="justify">We are now ready to use our above written functions to find $\mathcal{P}\left(x\right)$, $\Phi\left(x\right)$ and generate samples from the underlying distribution.</p>

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

<p align="justify">I am happy with the way we can generate the PDF, CDF and even draw samples easily using <code>scipy</code>.</p>

<h2>Acceptance-Rejection Sampling</h2>

<blockquote><h4>Proposition</h4>
<p align="justify">Suppose $X$ is a scalar random variable taking values in the interval $\left[a, b\right]$ according to the continuous probability density function $f\left(x\right)$. Let $M$ be an upper bound for $f$ on $\left[a, b\right]$, $M$ assumed finite. Choose $x$ uniformly in $\left[a, b\right]$ (for example, $x = a + t(b–a)$ where $t$ is uniform in $\left[0, 1\right]$). Then choose $u$ uniformly in $\left[0, M\right]$. If $u\leq f\left(x\right)$, we select $x$. Otherwise we reject $x$ and start over.</p>
</blockquote>

<h2>Our Own Method</h2>
{% include image.html url="/images/own_cdf_pdf_samples.jpg" caption="Generating our own samples" width=500 align="center" %}

<!--
<a href="https://en.wikipedia.org/wiki/Gibbs_sampling">Wikipedia</a>
-->