---
layout: post
title: "Things I would try avoiding while doing a PR review"
description: "Things I would try avoiding while doing a PR review"
tags: [softwaredevelopment]
comments: true
share: true
cover_image: '/content/images/2020/12/review-image.png'
---

As with time this doc will change, but jotting my thoughts down here on things which I feel I would like to avoid while I review PR's.

## Code formatting/style suggestions

I believe it's best left to the machine to do this instead of a human trying to fixate their attention to this, given it takes away the precious time of the reviewer which could be diverted to review the crux of the changes which the submission tries introducing. A code formatter should ideally pick this step up from the human reviewers's plate. An opinionated code-formatter/linter/style checker is the best option to have. An example for this will be [gofmt](https://golang.org/cmd/gofmt/)/linter which weeds out code formatting issues right in the build/test step. [rubocop](https://github.com/rubocop-hq/rubocop) is another great example.

What a tool is not able to enforce should be added as a team guide when reviewing PR's, opining about certain styles and blocking the merging of the PR is not the best way to deal about changes being introduced, worse yet. If the person is new/not someone from the team, would only add up to the friction of contributing towards the codebase, which is not a good sign.

If there is something which the reviewer is very concerned as a style in the PR, they should ideally leave it be for this changeset if it was not caught by the automated tooling/not mentioned in the team style guide and raise it with the team to be voted upon from the next PR's.

Obvious thing to avoid here is going against the language guidelines themselves, I would preferabley rather stick to the language guidelines([pep8](https://www.python.org/dev/peps/pep-0008/) for example) unless absolutely required. What this will enable is, someone new joining the team would have less familiar things to get onboarded to making them productive faster in terms of moving around the codebase, finding things or making sense of why for certain choices.

Wrote a small thread around the same here.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Was talking to a friend of mine who was pining about that their PR&#39;s would sometimes get a lot of nits in terms of style guides. (1/n)</p>&mdash; Tasdik Rahman (@tasdikrahman) <a href="https://twitter.com/tasdikrahman/status/1338507609758859264?ref_src=twsrc%5Etfw">December 14, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## Going over the whole design again

A higher level design change is not something which I will suggest on a PR, this needs to be caught right before someone starts implementing foo feature. It's a massive waste of time for the whole team, while you go over something which neither of the team members had consensus upon. Which is why I really like the approach of [RFC](https://en.wikipedia.org/wiki/Request_for_Comments) like process where the changeset if big enough would involve a discussion and a consensus be formed upon, so that things don't come as surprises.

What this will also immediately do is also make others familiar with what you are trying to propose and point out any mistakes which might not get caught by you in the early desing phase, which they might have seen/experienced.

## Idealistic changeset

This is something which I would like to prevent in the codebase, if the feature adoption is hard for foo feature, it would probably not make sense to add it if no one is going to use it. There should be some strategy on how to get users for the problem you are solving and reducing the friction for adoption. If no-one is uses that feature/it provides to added value in the codebase, it's as good as dead code to me.

If the feature is not gonna get used, why bother adding it at that moment? If instead the attention can be diverted to more burning issues/solving customer problems which are gonna give more leverage.

Again, going via an RFC approach might be one way where this would get caught as others would be able to point out the shortcomings in planning on how this changeset is going to get adopted.

For example, there was this one change made in the codebase in the client of an API which would get distributed to developers internally for a toolset which our team would provide. This change would effectively introduce parsing of the API response and being quite tied to the exact semantics of the payload which it would recieve from the server. Trickling down such logic to the client meant, that any changes to the response would have to be backward compatible. And when we did end up introducing a change to the response payload, we ended up having to keep this versioned API backward compatible. While I agree that versioning is for this specific purpose, but I feel it could have been avoided if the client would have been decoupled from the exact response semantics in this case and would just about be very naive, instead the server being smart enough to send the appropriate data over to the client.

## Accepting a large enough changeset which introduces a whole bunch of things

With all honesty, I really don't feel that someone would be able to effectively, actually go through a PR which changes 50 different files and has a huge LOC changeset, without having to spend copious amounts of time dedicated to just reviewing the PR. I personally have felt that such changes are super hard to review and would considerably increase the PR review time. In worst cases, to just unblock the team member, you have to either trust their changeset while you have gone through it on a very high level and going ahead and merging their PR, this introduces the problem of the PR not having gone through the usual PR process which would be a bit more rigorous.

There is no right size for a PR, but ideally smaller changes, which have one purpose are easier to review. Say for example, refactoring a flow to remove redundancy and re-using another module from the same codebase. Adding a small feature which would not require a lot of work.

If the feature/changeset requires a lot of changes, it would either mean that the scope was not broken down properly, which ended up creeping inside the PR as a reflection.

Over time people will notice what is a big enough changeset for their specific services and they will start breaking the PR's down, if that is not happening it's definitely something which needs to be brought to notice by other members in the team.

## Ending notes

These are the things are usually try following which would allow the changeset getting accepted in a timely manner without compromising on the quality on the quality too much, but would love to hear what you think about it.
