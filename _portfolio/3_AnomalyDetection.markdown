---
layout: post
title: Anomaly Detection for the Elderly
description: Detecting anomalous living patterns
img: /img/Elderly/GMMOnSineWave.png
---

Project Duration: July 2015 - Aug 2015

At HKC Solutions in Hong Kong I designed and implemented an Anomaly Detection system for homes for the elderly. There was a housing project in Singapore to make a new set of apartments for the elderly "Smart". Singaporean housing officials wanted a system that protects elderly inhabitants against emergency situations. The company was proposing such a system for the project which included anomaly detection.

We wanted to use simple, cheap sensors in an inhabitant's home to detect anomalies in his behaviour. These are simple motion sensors and door contact sensors. My task was to see how we can learn an inhabitant's normal behaviour using these sensors, and be able to detect anomalous behaviour by looking for deviations from the usual behaviour. 

I don't think I can go into too much detail of how it works, but here's the gist. The sensors give us a pattern of how the inhabitant spends his time in each room on each day. We can find structure and fit parametric distributions to the pattern to summarize the inhabitant's usual behaviour. Given enough data, say data from the past 6-8 weeks, we can assume the distributions are a good approximation to how the inhabitant will behave this week. An anomaly will then be a datapoint of low probability based on the current model.

To find structure in the data I experimented with using k-means and Gaussian Mixture Models (GMMs) for clustering. Here is some Matlab code[link] I wrote to fit a mixture of Gaussians to data using Expectation Maximization (EM) where the M step does MAP estimation instead of maximum likelihood (ML) estimation. I first started using ML estimation but too many numerical errors came up due to singular matrices. Switching to MAP  made those errors go away entirely. More details of MAP estimation are in Kevin Murphy's book on machine learning. A key question I worked on in clustering is how many clusters to pick in the first place. The final algorithm solves this problem in a reasonable way and uses a combination of k-means and multi-variate Gaussians.
<div>
<img class="img_row_full" src="{{ site.baseurl }}/img/Elderly/GMMOnSineWave.png" alt="fitting a GMM with 3 components to a sine wave" title="fitting a GMM with 3 components to a sine wave"/>
</div>
<div class="col three caption">
	GMMs can be very flexible! Here is a GMM with 3 components (contours in red) fit to a scaled-up sine wave.
</div>
<br>
I wrote the program in Octave, which is an open source version of Matlab. The final anomaly detection system was incorporated into the company's system for making an apartment "smart", which I was very excited about. It was actually tested on an elderly inhabitant in Singapore. Hopefully the system will one day save someone's life by quickly detecting and reporting anomalous behaviour.