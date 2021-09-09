---
layout: post
title: "Creating the language stack inventory for applications deployed on containers in the org"
description: "Creating the language stack inventory for applications deployed on containers in the org"
tags: [softwaremaintenance]
comments: true
share: true
cover_image: '/content/images/2021/05/'
---

Given the number of language stacks which different product teams end up using inside the org, variations come, in the form of different versions being used, or different versions of dependent libraries coming in. This combination will quickly lead up to a whole set of container image variations for a particular language, crossed with operating system versions.

## What is the problem then?

Tracking what is being used by different product teams and their services arises when we would want to know what infrastructure combination is being used by different services. Tracking this piece of information is paramount for a couple of reasons for the central infrastructure team.

If you don't know what people run, you wouldn't know what to deprecate. Or in other words. You wouldn't know what you support for the product teams.

### Upgrades/Deprecations

For example, [Ubuntu 16.04 LTS](https://ubuntu.com/about/release-cycle) recently got EOL'd, back in April 30th, 2021 where-in it stopped receiving security updates, creating an invetory of which services, in case they are using deprecated OS versions for example, would help in prioritizing and notifying the product teams about the impending upgrade.

This applies very well to the language stack deprecations needing to be done.

### Library additions

Catering to different requirements of library versions and variations of libraries required by each language+os stack variation also crops up. Deprecating/upgrading existing versions would need tracking as a pre-requisite.

## What did we end up doing

Given this would need to be solved for all applications inside the org, we started to tackle the problem starting with tracking the container images being used by the services onboarded to our internal service registry, to begin with and then handle the services which were not onboarded to the service registry.

<center><img src="/content/images/2021/06/vesemir-deployment-flow.jpg"></center>

Given our deployment flow, the cli which we give out to the developers, is the first touchpoint in the CI/CD pipeline. The container images to be used for packaging and deploying the application are specified in the CI/CD pipeline configurations. Now we would want to parse this information present already by one of the scripts being used to either deploy/build/run test stage.

Given the simplicity of the solution, it would be simpler to just parse that specific string line for the container images for packaging the application and the image which is being used as the base image.

The deploy scripts are just a wrapper which make use of the cli interacting with the central service registry to deploy the application, along with added logic to handle the response

Next was to just add a simple route on the service registry which would recieve an HTTP request to receive this information, everytime a deployment would happen via the CI/CD UI. Leading to updation

## Current state

## Takeaways

Tracking what is the container image, language stack/version being used in the service's test/build/deploy pipeline is what we would need to solve fowhere the container image get's built is simply impractical. The problem is compounded when the number of services which are running are more than
