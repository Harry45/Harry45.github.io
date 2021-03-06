---
layout: post
mathjax: true
title:  "Bayesian Model Selection"
date:   2017-05-20 06:00:00
author: A.Mootoovaloo
permalink:
categories:
  - Machine Learning and Statistics
tags:
  - 
  -
excerpt:
---
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({ TeX: { extensions: ["color.js"] }});
</script>

<p align="justify">Given a data set, one could presumably use various models to explain the data. One of the key questions underlying science is that of model selection: how do we select between competing theories which purport to explain observed data? The great paradigm shifts in science fall squarely into this domain. With so many models available to us, overfitting remains a real issue. Hence, choosing the most suitable model is essential in the Statistics world.</p>

<p align="justify">In the context of astronomy - as with most areas of science - the next two decades will see a massive increase in data volume through large surveys such as the Square Kilometre Array (SKA) and the Large Synoptic Survey Telescope (LSST). Robust statistical analysis to perform model selection at scale will be a critical factor in the success of such future surveys.</p>

<p align="justify">Although, we might have very good data, we may not know when to stop fitting. Two competing models may equally fit the data properly but how do we choose the most appropriate model? The solution in fact lies in the simpler model being preferred. This is known as the <i>Occam’s razor</i>. The complex model which explains the data somewhat better compared to the simpler model should be penalised for the additional parameters that it introduces. Extra parameters bring about lack of predictability. In this spectrum, Bayesian model selection is becoming an increasingly important tool to determine whether or not the introduction of a new parameter is justified by the data.</p>

<p align="justify">In this post, we will focus only on the calculation of the <b>Bayesian Evidence</b>, which is often regarded as the average likelihood under the prior. The evidence is the normalisation integral of the product of the likelihood and the prior and is often referred to as the <i>model likelihood</i>, or the <i>marginal likelihood</i> and is denoted by $\mathbb{Z}$. It is interpreted as the probability of the data $\left(\mathcal{D}\right)$ given the model $\left(\mathcal{M}\right)$. The Bayesian Evidence is given as </p>

\begin{equation}
\mathbb{Z}\equiv\mathcal{P}\left(\mathcal{D}\left|\mathcal{M}\right.\right)=\int\mathcal{P}\left(\mathcal{D}\left|\boldsymbol{\theta},\,\mathcal{M}\right.\right)\,\mathcal{P}\left(\boldsymbol{\theta}\left|\mathcal{M}\right.\right)\,d\boldsymbol{\theta}
\end{equation}

<p align="justify">where $\mathcal{P}\left(\mathcal{D}\left|\boldsymbol{\theta},\,\mathcal{M}\right.\right)$ is the likelihood function which should typically reflect the way the data is obtained and $\mathcal{P}\left(\boldsymbol{\theta}\left|\mathcal{M}\right.\right)$ is the prior distribution for the parameters. Using Bayes' theorem, we can write

$$
\mathcal{P}\left(\mathcal{M}\left|\mathcal{D}\right.\right)=\dfrac{\mathcal{P}\left(\mathcal{D}\left|\mathcal{M}\right.\right)\mathcal{P}\left(\mathcal{M}\right)}{\mathcal{P}\left(\mathcal{D}\right)}
$$

</p>

<p align="justify">The left-hand side of equation is the posterior probability of the model given the data. Consider two competing models $\mathcal{M}_{1}$ and $\mathcal{M}_{2}$. The ratio of the respective posterior probabilities of the models, given the data is

$$
\dfrac{\mathcal{P}\left(\mathcal{M}_{1}\left|\mathcal{D}\right.\right)}{\mathcal{P}\left(\mathcal{M}_{2}\left|\mathcal{D}\right.\right)}=\dfrac{\mathcal{P}\left(\mathcal{D}\left|\mathcal{M}_{1}\right.\right)}{\mathcal{P}\left(\mathcal{D}\left|\mathcal{M}_{2}\right.\right)}\,\dfrac{\mathcal{P}\left(\mathcal{M}_{1}\right)}{\mathcal{P}\left(\mathcal{M}_{2}\right)}
$$
</p>

<p align="justify">Note that $\mathcal{P}\left(\mathcal{D}\right)$ is only a constant and can be dropped when calculating the ratio of posterior probability of the models. The ratio $$B_{12}=\dfrac{\mathcal{P}\left(\mathcal{D}\left|\mathcal{M}_{1}\right.\right)}{\mathcal{P}\left(\mathcal{D}\left|\mathcal{M}_{2}\right.\right)}$$ is the <b>Bayes factor</b> and is, in fact, the ratio of the models' evidences. The ratio $$\dfrac{\mathcal{P}\left(\mathcal{M}_{1}\left|\mathcal{D}\right.\right)}{\mathcal{P}\left(\mathcal{M}_{2}\left|\mathcal{D}\right.\right)}$$ is the posterior odds while $$\dfrac{\mathcal{P}\left(\mathcal{M}_{1}\right)}{\mathcal{P}\left(\mathcal{M}_{2}\right)}$$ is the prior odds. Therefore, in words, one can describe the above as 

$$
\textrm{posterior odds}=\textrm{Bayes Factor}\times\textrm{prior odds}
$$
</p>

<p align="justify">In the case of non-committal priors on the models, that is, $\mathcal{P}\left(\mathcal{M}_{1}\right)=\mathcal{P}\left(\mathcal{M}_{2}\right)$, the posterior odds are just equal to the Bayes factor. As $B_{12}$ increases, our belief that model $\mathcal{M}_{1}$ is better than model $\mathcal{M}_{2}$ increases. Otherwise, model $\mathcal{M}_{2}$ will be the preferred one. Therefore, the Bayes factor indicates how the relative odds vary in the light of new data, irrespective of the prior odds of the models. The Bayes factor is typically interpreted by referring to the Jeffreys' scale (see <a href="http://www.tandfonline.com/doi/abs/10.1080/01621459.1995.10476572">Kass et al., 1995</a>). It is an empirically determined scale as shown in the table below </p>


<style>
table {width: 53%;}
</style>

<center> 
<table class="tableizer-table">
<thead><tr class="tableizer-firstrow"><th>$\textrm{ln }\left(B_{12}\right)$</th><th>$B_{12}$</th><th>Evidence against $\mathcal{M}_{2}$</th></tr></thead><tbody>
 <tr><td align="center">0 to 1</td><td align="center">1 to 3</td><td align="center">Inconclusive</td></tr>
 <tr><td align="center">1 to 3</td><td align="center">3 to 20</td><td align="center">Weak Evidence</td></tr>
 <tr><td align="center">3 to 5</td><td align="center">20 to 150</td><td align="center">Moderate Evidence</td></tr>
 <tr><td align="center">>5</td><td align="center">>150</td><td align="center">Strong Evidence</td></tr>
</tbody></table>
</center>

