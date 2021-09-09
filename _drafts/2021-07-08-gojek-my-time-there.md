---
layout: post
title: "Creating the language stack inventory for applications deployed on containers in the org"
description: "Creating the language stack inventory for applications deployed on containers in the org"
tags: [softwaremaintenance]
comments: true
share: true
cover_image: '/content/images/2021/05/'
---

Given the number of language stacks which different product teams end up using inside the org, variations come, in the form of different versions being used, or different versions of dependent libraries coming in. This combination will quickly lead up to whole set of container image variations for a particular language, crossed with operating system versions.

## What is the problem then?

Tracking what is being used by different product teams and their services arises when we would want to know what infrastructure is being used by a different services. Being the central infrastructure team, a couple of other angles arise in our case.

### Upgrades

For example, [Ubuntu 16.04 LTS](https://ubuntu.com/about/release-cycle) recently got EOL'd, back in April 30th, 2021 where-in it stopped receiving security updates, creating an invetory of which services, in case they are using deprecated OS versions, they would need to be prioritized with the product team.

### Library additions

Furthermore, since each container image would be a mix of the OS which it was based on, the language stack it installed, and the versions of the dependencies it was giving to the end, service container image to build on top,

## What did we end up doing

## Current state

## Takeaways





Tracking what is the container image, language stack/version being used in the service's test/build/deploy pipeline is what we would need to solve fowhere the container image get's built is simply impractical. The problem is compounded when the number of services which are running are more than

