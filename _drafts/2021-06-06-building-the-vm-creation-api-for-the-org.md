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

At a larger scale, people end up using automation to create these Virtual machines in the way they want them to be, given the manual nature of work would just start becoming a bottleneck in scaling quickly when required.  

So how did we go about it?

## Existing solution

I have spoken a bit about our current automation using [proctor](https://github.com/gojek/proctor), 

<iframe width="560" height="315" src="https://www.youtube.com/embed/mE1JZKMhnNs" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

I will not delve more into that, but in gist, we had a way already present in which developers could simply demand a VM getting created via a command line cli interface.

As time went by, the automations to create different variations of virtual machines based on language stack kept getting added, helping developers create Virtual machines on demand. 

Which is the original and broader goal of automation too I feel, to move out more and more things out of the human hands so that people can move on to do other things. And as always, automation is a moving target for everyone, you build abstractions on top of abstractions sometimes to ease things out for everyone, without compromising on the usual things like quality of what is being delivered, ease of using. 

And this post is about how we ended up making the building block for a lot of automation around Virtual machine creation orchestration inside the org, as part of the platform team.

## What is the need for a VM creation API in the first place?

Yes, true. Everything is working fine and dandy. People are able to create VM's on demand whenever they want and however they would like to, but the process is still via the CLI which is being given out to the developers. 

## What we ended up doing



## Why didn't we add it in proctor itself?

A couple of reasons. We already had a service which was used to create k8s clusters on demand. We wanted to add another resource to it, which would be used to create virtual machines, making it the central tool to orchestrate resource creation, the resource here could be anything, from a virtual machine to a k8s cluster. 

We also felt it would be just cleaner as in interface, where proctor would be fronted by this orchestration service for resource creation and be the entrypoint for developers and the like for having any automation around resource creation needs. 

Could we have built everything back in proctor? Defintely. Would it work the same way as it does now? Mostly yes. 

## What has it helped solve

## Moving forward

