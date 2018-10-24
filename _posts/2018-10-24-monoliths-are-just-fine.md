---
layout: post
title: "Monoliths are just fine"
description: "Monoliths are just fine"
tags: [architecture]
comments: true
share: true
cover_image: '/content/images/2018/10/monolithic-vs-microservices.png'
---

A lot of great material has already been [written](https://martinfowler.com/articles/microservices.html) out there around what microservices are and what they are not. 

What I would try putting down here is what I saw as we grew from a monolith to a microservices architecture over the period of time back here at [Razorpay](https://www.razorpay.com). Please take it with a grain of salt when you read this as this is going to be opinionated.

> Microservices are something which you grow into, not something you start with

Having a microservice(s) based architecture just for the sake of having one is kind of like using [kubernetes](https://kubernetes.io) to deploy a service or two on it. In the end, you will only overcomplicate things by having multiple things to manage. And trust me, [kubernetes is hard](https://codeengineered.com/blog/2017/kubernetes-is-hard/). I say that with at least a year and a half of running production workloads at Razorpay on self-hosted kubernetes and having scaled them to what it is now. It's complicated and can be hard to get right at first. But makes life much easier when you have multiple services to manage at the very least.

If you have a look at Shopify, they are one huge Ruby on Rails shop. And the last time I checked their scale is pretty huge. So the argument that microservices are the only way to scale is not entirely true.

There are reasons why I feel monoliths are the way one should start with, because

>  Microservices are complicated to manage

If you're just starting to write your app, you need to get market validation fast and get the features out before your competition does, instead of having to worry about 10 other different things. Your customers/investors probably won't care about how your service works or what is their underlying tooling here unless they are techies and would be curious about how is it working internally but at the end of the day, they just want things to work!

This is the part, where you are working on your prototype and you don't need the extra overhead of worrying about how the 10 different microservices you wrote are interacting with each other. As at the end of the day, the tooling and the architecture matters, but not if you have not even attained that critical mass. This is a little similar to fight over what language to choose over the other, of course, there would be obvious choices for some specific jobs but you can always go first with the one which you and the rest of the team are most comfortable with to [beat the averages](http://www.paulgraham.com/avg.html)

Plus the overhead of managing the health checks, monitoring and graceful shutdown for all the microservices is something which you need to put on your head around when starting with microservices apart from writing your main business logic.

> Architect your monolith right

When you start off with your monolith, there would be places where you feel you see things repeating. And that's where you separate that out into functions or modules. But you should not be afraid to repeat yourself at the start and try to modularise/abstract out everything, as you would not like to end up with [leaky abstractions](https://www.joelonsoftware.com/2002/11/11/the-law-of-leaky-abstractions/)

When you understand the boundaries and functions well enough, that's when you would start modularizing your codebase. So as to have clear distinctions of which part of the codebase does what. This helps in a few things, specific teams being able to work on specific codebases and clear distinction between core functionality and helper functions.

When you are done dividing your codebase into modules, using a message queue for asynchronous communication between the modules would make more sense. This will enable you to debug faster, distribute the work of different components to different teams for things to start with. 

With all the above in check, you already have a lot of things sorted which would not become a technical debt when trying to move to microservices.

From what I have noticed is, over-optimising from the start is just gonna bog you down. The priority at the start should be to 
- Get it working.
- Get feedback about your product.
- Fix the bugs when they are reported or when you find any.
- If things go down, look why they went wrong and fix them. This is where you will learn from your experience. 
- Repeat the whole process.

I think of it this way, your product is not a program like [ls](http://man7.org/linux/man-pages/man1/ls.1.html) command which is feature complete, you need to constantly iterate upon it, but even before that. You have to give out a working model for people out there to use it. 

> Moving to a microservice?

The argument of moving to a microservice can be made when 
- you have divided certain work among certain teams, by virtue of which there would be times when there would be friction, miscommunication happening over when contributing to certain parts of the codebase which come common when different teams are working. Even if you have managed to write something, it's quite possible that something which you added might have had a regression over something, and at this point, your automated tests in the CI should ideally catch them. But having a clear separation of work when you have multiple teams working on things, microservices can make sense.
- you would want to rewrite a piece of codebase into something more performant.

## References 
 
- https://martinfowler.com/bliki/MonolithFirst.html
- https://www.joelonsoftware.com/2002/11/11/the-law-of-leaky-abstractions/
- http://www.paulgraham.com/avg.html

