---
layout: post
title: "The making of bhola - your cert expiration overseer - Part 1"
description: "The making of bhola - your cert expiration overseer - Part 1"
tags: [ruby, rubyonrails]
comments: true
share: true
cover_image: '/content/images/2020/10/bhola.png'
---

You might have already seen me writing a bit about bhola already on [twitter](https://twitter.com/tasdikrahman), I wrote a little bit about why I have been building [bhola](https://github.com/tasdikrahman/bhola). This post is more of a continuation to this tweet and what I envision it to be moving forward.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Do you sometimes wake up, with a call by someone from your team, telling you some SSL cert has expired? Do you keep track of SSL cert expirations on your to do notes or excel sheets? Would you like to be on top of such x509 cert renewals? <a href="https://t.co/MVFRZCUlZN">https://t.co/MVFRZCUlZN</a> is for you (1/n) <a href="https://t.co/pj8JHJEkje">pic.twitter.com/pj8JHJEkje</a></p>&mdash; Tasdik Rahman (@tasdikrahman) <a href="https://twitter.com/tasdikrahman/status/1306945863369936896?ref_src=twsrc%5Etfw">September 18, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

Do you sometimes wake up, with a call by someone from your team, telling you some SSL cert has expired? Do you keep track of SSL cert expirations on your to do notes or excel sheets? Would you like to be on top of such x509 cert renewals? Then [bhola](https://github.com/tasdikrahman/bhola) is for you!

## What does bhola do?

[v0.1](https://github.com/tasdikrahman/bhola/releases/tag/v0.1.0) of Bhola, gives you a dead simple API, which you can use to ask Bhola, to track domains which have certs attached to it. It automatically checks for the cert expiration in the background keeping note of when is it expiring.

The operator can set a buffer period, which would bhola, then use to see if it meets the threshold number of days, before the cert is going to expire, before marking the cert, that it needs renewal asap.

Want to check, what domains, is it already tracking? Bhola comes with a very simple bare minimum UI, which one can use to check, what domains are being tracked, when are they expiring and other metadata details.

What is required by Bhola to run? It just needs a good old postgres to function to keep track of the domains, and that's it. Nothing shiny. Plain and simple.

Further on [v0.2](https://github.com/tasdikrahman/bhola/releases/tag/v0.2.0) of bhola adds support for sending notifications to slack as part of evolving, from just being a dashboard to something which can be used to preemptively alert the operator on is the certificate expiring.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">It will alert for all the domains, which have already expired/are about to expire within the buffer period which you have set &amp; send notification to your slack channel via webhook endpoint, periodically checking in the interval set by the operator, for expiration. (2/n) <a href="https://t.co/IdpDGxJQtr">pic.twitter.com/IdpDGxJQtr</a></p>&mdash; Tasdik Rahman (@tasdikrahman) <a href="https://twitter.com/tasdikrahman/status/1312414866133512192?ref_src=twsrc%5Etfw">October 3, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

Further more, smaller improvements like 1 step dev setup, docker-compose setup and container images available for docker would make reproducing the setup for bhola easier than before, than the status in milestone 0.1.

Not that it matters much, but [rails](https://rubyonrails.org/) has been a fun framework to work on, especially with [rspec](https://rspec.info/), practicing [BDD](https://en.wikipedia.org/wiki/Behavior-driven_development) and [TDD](https://en.wikipedia.org/wiki/Test-driven_development) has been a great experience.

### What's next?

I do plan to extend bhola further, there are quite a few thing which I want to see in it, in future. Some of them being

- Ability for it to associate domains and alerts with users, this will allow bhola to be multi-tenant.
  - The idea is to have a system in place if someone wants to enable this feature, this can be turned on with just enabling a feature flag when they start the
    the webserver.
  - while accessing the api to do insertion for tracking domains, add authz/authn.
- Ability to delete domains associated with a user.
- Ability to sign up using email id.
- Ability to send alert notifications of all the domains associated to the user in their specified email id.
- Not an immediate goal, but I want to host this on my own infrastructure, as a public facing endpoint.


While I have not spread the above in specific milestones, but I would mostly pick up the first one for milestone 0.3.

I envision bhola to be a 1 stop service for your needs of tracking your domain expirations for starters.

As bhola is completely open source, would love to hear what you feel can be added to make [bhola](https://github.com/tasdikrahman/bhola) better than before.
