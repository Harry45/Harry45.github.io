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

<p align="justify"></p>


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

{% include image.html url="/images/scipy_continuous.jpg" caption="Samples generated using scipy" width=500 align="right" %}

{% include image.html url="/images/own_cdf_pdf_samples.jpg" caption="Generating our own samples" width=500 align="left" %}

<!--
<a href="https://en.wikipedia.org/wiki/Gibbs_sampling">Wikipedia</a>
-->