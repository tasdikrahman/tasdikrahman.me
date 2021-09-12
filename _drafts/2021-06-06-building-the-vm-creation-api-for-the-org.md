---
layout: post
title: "Building the VM creation API for the org"
description: "Building the VM creation API for the org"
tags: ["softwaredevelopment"]
comments: true
share: true
cover_image: '/content/images/2021/05/'
---

Over the years, there have been a lot of changes in the way, people create their virtual machines in their cloud environment. 

At a very primitive level, one would simply go about doing it via the cloud provider console. A couple of clicks and lo and behold. 

But what happens when you are scaling quickly in terms of new services getting added. Then this approach would quickly start becoming a bottleneck where the operations team would start getting involved more and more about the different viriations people would end up asking for. Which is just natural. 

So how do you go about it?

## Existing solution

I have spoken a bit about our current automation using [proctor](https://github.com/gojek/proctor), 

<iframe width="560" height="315" src="https://www.youtube.com/embed/mE1JZKMhnNs" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

I will not delve more into that, but in gist, we had a way already present in which developers could simply demand a VM getting created to an automation tool which they would then 

There has been a lot of progress in terms of the automation which we have built over the years inside the organization, which would help us along with the developers to achieve the things which we would want to do without much hand holding. Which is the original and broader goal of automation too I feel, to move out more and more things out of the human hands so that people can move on to do other things. And as always, automation is a moving target for everyone, you build abstractions on top of abstractions sometimes to ease things out for everyone, without compromising on the usual things like quality of what is being delivered, ease of using. 

And this post is about how we ended up making the building block for a lot of automation around Virtual machine creation orchestration inside the org, as part of the platform team.

## What is it right now

## What has it helped solve

