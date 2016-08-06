---
layout: post
title: "My intern"
description: "My "
tags: [internships, wingify]
comments: true
share: true
cover_image: '/content/images/2016/7/decorators.png'
---

## What's the fuss about?

As I am siting here at the Delhi Airport waiting for my flight back to Chennai, I could just not stop myself from thinking about my time as an intern back at [Wingify](http://wingify.com/) which ended last week and here is what I wrote down after getting carried way with several cups of coffee (thanks for luring me costa coffee)

So here it is then!

## Day 1

It was 5 o'clock in the morning and I was quite drowsy. Reason being the all nighter I pulled the other night for the last exam of our end sems. **phew**

Here I was at the Delhi airport just a day after my semester exams, ready to start with my internship. Talk about eagerness here!
Well for me, I was happy that I was moving out of Chennai for some time at least!

Joined them the next day at their main office at the heart of NSP, Delhi.

I was excited to work in a company which had grown and become one of the best startups in India in such a short span of time. On top
of that, this was my first internship in a well-established product based start-up and I was hoping that I could learn all that I could and perform in accordance to their standards. I was introduced to Ankit Jain (Lead software Engineer at Wingify) and Ajay Sharma (Senior Software Engineer) by my HR. We had a brief chat where I was told I would be working with the Backend Development team for [vwo](https://vwo.com/), their flagship product.

Talking about vwo, it's the world's easiest A/B testing tool. And we are quite good(read "The Best") at it! The month before I joined, we had sales crossing a little over 1 million dollars.

## Integration of Statsd and graphite

StatsD collects and aggregates my metrics. And I knew that StatsD ships them off to Graphite. Which I knew stores the time-series data and enables us to render graphs based on these data. After playing around with some flush values in StatsD. I finally got it right and the metrics for our internal service was being graphed correctly by Graphite.

The service on which we integrated StatsD and graphite runs on several servers. And while plotting the graphs we wanted to know the server from where the stats are being pushed on to the buckets of statsd.

## Bumblebee - An experimental slack bot vwo

So wingify has this culture of organizing hackathons at the end of every month, where people from the engineering team come together to hack on something which they want to see at vwo.

To be honest, I was quite clueless on what to build for the first half an hour and so and after a little nudge from Ankit I decided upon bumblebee

Bumblebee makes use of the vwo API to provide functionalities (if not all) to the vwo account holder right at the comfort of his slack
channel. Like you can get details of all the campaings of your account, check their statuses  (whether they are running, paused et el). Start/Stop/Pause a particular campaign. Share your campaign with someone else and some more things.

It was written in python and Ankit was too kind to let me open source it. Here is the link for the curious.

https://github.com/wingify/bumblebee

## Optimization problem

My 3rd project revolved around optimzation of an internal service. I had to increase the efficiency(read performance). I implemented some rough 3 approaches and the last one bumped the performance by up to 23.6%. I could have tried for dropping it down further to a lower one but sadly the end to my internship was looming around the corner so I dropped it. And that was the 3rd and the last project I did as an intern at Wingify.

## How was my experience?

My experience? I loved it here!

- Solving hard engineering problems - Check
- Extremely talented engineering team - Check
- Approachable mentors - Check
- Delhi :P - check

Jokes apart. I made some really good friends back there and learned a ton from everyone. I am proud that I was part of a team which is building something that has an impact on thousands of customers and helping them build their brand. Because at the end, I think what matters for me is building something which makes the lives of others easier than before.

## So what now?

*Insert an emotional quote here*

Looking back at the time when I recieved a call from [Nupur](https://in.linkedin.com/in/nupur-jain-39a62558) about my acceptance as an intern at [wingify](http://wingify.com/). I was thinking about whether to join it over the other 7-8 odd companies which accepted my application as a summer intern.

And I now believe that I did just the right thing on choosing Wingify over others!

Until next time Delhi!