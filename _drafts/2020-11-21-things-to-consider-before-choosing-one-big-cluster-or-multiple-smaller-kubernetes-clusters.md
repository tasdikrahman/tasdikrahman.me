---
layout: post
title: "A few things to consider before choosing a big cluster or multiple smaller kubernetes clusters"
description: "A few things to consider before choosing a big cluster or multiple smaller kubernetes clusters"
tags: [kubernetes]
comments: true
share: true
cover_image: '/content/images/2020/04/'
---

This post is a continuation on the discussion which I was having with [@vineeth](https://twitter.com/VineethReddy02)

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">But why would someone choose one large cluster over multiple small clusters? Aren&#39;t multiple clusters already a pattern in enterprises?</p>&mdash; Vineeth Pothulapati (@VineethReddy02) <a href="https://twitter.com/VineethReddy02/status/1329656769900003329?ref_src=twsrc%5Etfw">November 20, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

Context is when I came across a tweet which demonstrated the ability of kubernetes to scale uptill 15k nodes due to recent improvements.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">15k nodes cluster 🤯 <a href="https://t.co/VMWI7HeYHH">https://t.co/VMWI7HeYHH</a></p>&mdash; Tasdik Rahman (@tasdikrahman) <a href="https://twitter.com/tasdikrahman/status/1329615066405093379?ref_src=twsrc%5Etfw">November 20, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

The discussion was originally was around costs and how much would it take to run one such large kubernetes cluster, but it went into a different direction altogether. While this post is not a recommendation on what one should do, but the idea is to guide you to take a more informed decision with the data points and constraints that you have.

### What is the right size of a cluster?

I have personally only seen clusters which have node pools,
