---
layout: post
title: "Things I learned using terraform for more than a year in production"
description: "Things I learned using terraform for more than a year in production"
tags: [IIAC, terraform, infrastructure]
comments: true
share: true
cover_image: '/content/images/2018/10/observability-cover.jpg'
---

### Why should I even use terraform?

It's a valid question, to be honest. I mean why should you invest on learning something which still has 
- a lot of [open issues](https://github.com/hashicorp/terraform/issues) (1,387 at the time of writing this)
- is still at a `0.x.x` [release](https://github.com/hashicorp/terraform/graphs/contributors) cycle going on, which would mean `1.x.x` would mean major breaking changes with the way it currently works, if you are following [semver](https://semver.org/). The first release(v0.1.0) was at [28 Jul 2014](https://github.com/hashicorp/terraform/releases/tag/v0.1.0). 


out of the many more reasons, but why should you anyway?

Well for starters, versioned infrastructure. Having someone being able to review what is gonna be provisioned is a huge plus if you ask me. Declarative syntax of terraform is something which is very similar to the way of what kubernetes follows (very loosely I would say). Which is, that you have declared a particular state in your config and then you would try to reconcile with that state(in case of a drift.)

Which gives a huge benefit over doing things manually over from the cloud console of your cloud provider. Hence reducing the chance for human error. And it makes it 
repeatable, not that you could not script it. But I will come to that. 

If the argument was to use versioned infra or [Infrastructure as code](https://en.wikipedia.org/wiki/Infrastructure_as_Code
) you get to choose a plethora of tools for different cloud providers
- [AWS cloudformation](https://aws.amazon.com/cloudformation/) if you're on AWS.
- 



