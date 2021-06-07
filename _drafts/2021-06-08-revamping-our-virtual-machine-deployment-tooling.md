---
layout: post
title: "Revamping our virtual machine deployment service"
description: "Revamping our virtual machine deployment service"
tags: ["softwaredevelopment"]
comments: true
share: true
cover_image: '/content/images/2020/04/'
---

The build and deployment pipeline for each org will be different in some way or the other, given each co will have it's own requirements. Even in my last org, the way our team enabled other teams to ship code/config changes, was pretty different from the way we do it in my current org.

Our deployment platform comprises, of a central service registry, two separate services to deploy to virtual machines and kubernetes respectively, a cli to interact with the service registry, which is given out the developers of the org using which they can do numerous things for their application, to keep things self serve. One of those things, being the ability to deploy their application via the CLI.

## Background

The idea is to abstract out the deployment process from the developers as much as possible, but also giving them the means to debug with the logs being shown to them which is meaningful to them.

For this post, I will specifically talk about the virtual machine deployment service, called vesemir, which we have in place and how we went about revamping it's infrastructure along with refactoring it to keep it healthy and maintainable for others to work on it's codebase.

### What does it really do?

Vesemir, in a gist is a wrapper on top of chef API's, where-in it receives a request for deploying a service in a particular cluster (a GCP project), filters out the VM's where it has to deploy the changeset and then running,

