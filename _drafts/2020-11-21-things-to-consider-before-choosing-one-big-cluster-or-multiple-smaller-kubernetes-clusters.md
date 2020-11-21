---
layout: post
title: "Things to consider before choosing a big cluster or multiple smaller kubernetes clusters"
description: "Things to consider before choosing a big cluster or multiple smaller kubernetes clusters"
tags: [kubernetes]
comments: true
share: true
cover_image: '/content/images/2020/04/'
---

This post is a continuation on the discussion which I was having with [@vineeth](https://twitter.com/VineethReddy02)

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">But why would someone choose one large cluster over multiple small clusters? Aren&#39;t multiple clusters already a pattern in enterprises?</p>&mdash; Vineeth Pothulapati (@VineethReddy02) <a href="https://twitter.com/VineethReddy02/status/1329656769900003329?ref_src=twsrc%5Etfw">November 20, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

Context is when I came across a tweet which demonstrated the ability of kubernetes to scale uptill 15k nodes due to recent improvements.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">15k nodes cluster 🤯 <a href="https://t.co/VMWI7HeYHH">https://t.co/VMWI7HeYHH</a></p>&mdash; Tasdik Rahman (@tasdikrahman) <a href="https://twitter.com/tasdikrahman/status/1329615066405093379?ref_src=twsrc%5Etfw">November 20, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

The discussion was originally was around costs and how much would it take to run one such large kubernetes cluster, but it went into a different direction altogether. While this post is not a recommendation on what one should do, but the idea is to guide you to take a more informed decision with the data points and constraints that you have.

So what should the decision be? Rather, we can take the part where we discuss a few things about both sides of the coin

Before we start, big, here will be relative, but for the sake of this conversation, let's say you plan to run a cluster, with worker nodes greater than ~50 (this is a big cluster for me right now, more on the why part of it in a bit)

#### Multi tenancy

Hard multi-tenancy is something, which might not work the way as expected as of now on k8s, even with [netpols](https://kubernetes.io/docs/concepts/services-networking/network-policies/)/[namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) and upcoming mechanisms like [Hierararchical namespaces (aka HNC)](https://github.com/kubernetes-sigs/multi-tenancy/blob/master/incubator/hnc), which is still in incubation at the time of writing this. That's what I have last checked, but please correct me here if I am wrong if have something which tells otherwise.

All of this might make people concentrate towards having multiple k8s clusters to achieve it that way.

#### Upgrade charter

One operational aspect which might be important to note here is the burden of upgrades. Keeping up with upgrades, with the release cycle of kubernetes is not a trivial task. I have written about our experiences in this thread sometime back, to give you an idea of what really goes into one such upgrade.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">A few notes on <a href="https://twitter.com/kubernetes?ref_src=twsrc%5Etfw">@kubernetes</a> cluster upgrades on GKE (1/n)</p>&mdash; Tasdik Rahman (@tasdikrahman) <a href="https://twitter.com/tasdikrahman/status/1285619368353726465?ref_src=twsrc%5Etfw">July 21, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

Even on a managed platform platform provided by the cloud vendors (where they maintain the control plane for you), it's still an operational heavy task, and we are not even talking about self managed clusters via for eg: [kops](https://github.com/kubernetes/kops/).

Multiply this effort with multiple such clusters, and you will end up needing to have people dedicated to just do this. There is no such thing as an [LTS](https://en.wikipedia.org/wiki/Long-term_support) release as of now, unless you are fine with running super old kubernetes installations which would have CVE's reported and fixed in the upcoming releases/you are ok with doing big bang upgrades. Both of which might not be a great idea to begin with. Even if someone decides to run an archaic installation for long, if you feel running a super old installation will fly with your cloud provider, you will be in for a rude shock, where they can literally force upgrade your cluster(yes, they do it).

All of the toil which goes into cluster upgrades might have been a factor, which would have led to the initiation of this discussion [here](https://github.com/kubernetes/sig-release/issues/1290) where folks discuss about modifying the kubernetes release cadence. I for one [did vote](https://github.com/kubernetes/sig-release/issues/1290#issuecomment-709774428), on moving to 3 releases than 4 every year. But there are also arguments against doing slower releases, which might make the next release bloated with features and going against the philosophy of small incremental changes, but that discussion is for another post.

Given someone ends up with such a large cluster, the upgrade is equally gonna be as operationally heavy if not more than when someone was upgrading a bunch of clusters, as the operations to be done would remain more or less the same.

What might increase a side effect of the cluster being huge, is that someone would have to baby sit the whole upgrade operation longer, than what they would spend on a smaller cluster(give upgrade automation is not present/not mature enough to remove the human involved here.)

And if there are non-standardized workloads in the cluster(folks hand applying yamls/no track of objects in cluster), there are various ways on how an upgrade can either get stuck due to pdb budgets of pods not being met when the node gets cycled/end up causing an outage. Which makes this whole operation for a focused cluster running x teams applications much more saner.

Hence, upgrade strategy is something to consider seriously while choosing the sizing.

#### API deprecations

API's getting deprecated, for example [1.16](https://kubernetes.io/blog/2019/07/18/api-deprecations-in-1-16/) was a release which had a bunch of changes, which would have involved the cluster operators to upgrade their automation/helm charts to be compatible with those changes.

There's no one to blame here in case of API deprecations, an object getting stabler and getting promoted to a more stable API, is a natural progression. To enjoy the benefits of a stable kubernetes object, it only makes sense to move to the stable api rather than being stuck on one which is less stable/getting deprecated in the next release.

If you're a small org, with a small group of folks managing the k8s clusters, the manual toil will be quite high. The reasons are also obvious, the lack of bandwidht will attribute to them not being able to automate the redundant tasks required for the upgrade. Even if they manage to write some automation, the automation will become stale over time if not given prioritisation, as the domain changes. Plus, an average operations team will also have developer requests coming in their way, prioritising all this with a small task is just a hard task to begin with.

For a larger org having bandwidth, managing multiple clusters for teams, they will eventually have automation over time to reduce the toil.

Folks have started working on building something in the open, although I have not personally used it, but I have heard good things about [kube-no-trouble](https://github.com/doitintl/kube-no-trouble), which takes a stab at telling the deprecated API's in a cluster.

#### Access management

Giving the right kind of access to the developers/operational folks/xyz designation person in the team is a problem to solve. Solving the same problem over for multiple clusters requires automation and a proper mechanism. If your workloads are sensitive (payments data etc.), you would require for such environments to be tighly audited and managed.

There are a few tools out there which help you in doing so, like [krane](https://github.com/appvia/krane), [audit2rbac](https://github.com/appvia/krane), which help you in this process (thanks to [rahul](https://rmenn.in) for pointing them out to me).


#### Deployment automation

With all honesty, hand applying yaml files will only go so far. It works, but is a recipe for disaster over the long run, creating more spaghetti in the cluster and creating more problems than solving them. Problems like, who applied x resource object, who changed x resource, who is using this x resource. And the list so goes on.

No one need not do Continous Delivery, but having some form CI to safely modify the respective resources via a tool/process will prevent a lot of surprises in production.

#### Concentration risk

If we look at the flip-side, having one big cluster, concentrates the risk of failure.

If you are not on a vendor specific kubernetes installation, what happens when the control plane goes for a toss? 1 person having all the context is not scalable, what happens when the person who knows the operational know how is not present to handle the pager?

What about zonal failures affecting the cluster? Do we have checks and balances to handle such an event?

Having one cluster, per product group is also a model which people follow, helping de-risk the affects of failure to not affect other products/product groups. But then the operation problem/complexity of managing multiple clusters arise.

#### Ending notes

Either of the two options, one large cluster vs multiple large clusters, both are an opinionated way to run clusters, or for that matter any compute infrastructure. What works best, might not work out in another context. Someone else's best practice might turn out into a nightmare for another org/team to manage/run.

Given, most/all of these problems can be solved, the right trade-offs can be made when deciding for a solution and I hope this discussion helps you making the right decision in your context.

### References

- [https://kubernetes.io/docs/concepts/services-networking/network-policies/](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
- [https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)
- [https://blog.gojekengineering.com/how-we-upgrade-kubernetes-on-gke-91812978a055](https://blog.gojekengineering.com/how-we-upgrade-kubernetes-on-gke-91812978a055)
- [https://kubernetes.io/blog/2019/07/18/api-deprecations-in-1-16/](https://kubernetes.io/blog/2019/07/18/api-deprecations-in-1-16/)
- [https://github.com/kubernetes/sig-release/issues/1290](https://github.com/kubernetes/sig-release/issues/1290)
- [https://github.com/kubernetes-sigs/multi-tenancy]*(https://github.com/kubernetes-sigs/multi-tenancy)
- [https://github.com/kubernetes-sigs/multi-tenancy/blob/master/incubator/hnc](https://github.com/kubernetes-sigs/multi-tenancy/blob/master/incubator/hnc)
