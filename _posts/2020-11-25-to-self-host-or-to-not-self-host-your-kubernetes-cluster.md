---
layout: post
title: "To self host or to not self host kubernetes cluster(s)"
description: "To self host or to not self host kubernetes cluster(s)"
tags: [kubernetes]
comments: true
share: true
cover_image: '/content/images/2019/04/k8s-image.png'
---

A friend of mine asked this to me recently, about how was it to [self host](https://en.wikipedia.org/wiki/Self-hosting) [kubernetes](https://kubernetes.io) clusters. And I was cursing myself about why I did not write this post earlier (I mean, technically I have written about how we used to do self hosting [before](https://tasdikrahman.me/2019/04/04/self-hosting-highly-available-kubernetes-cluster-aws-google-cloud-azure/), but not the pros and cons of it), as this was not the first time I had been asked this question. So this post is dedicated to my friend and to others when they chance upon this question.

Just for context, self host here is does not only mean, [kubernetes inside kubernetes](https://tasdikrahman.me/2019/04/04/self-hosting-highly-available-kubernetes-cluster-aws-google-cloud-azure/), but in a broader sense mean, managing the control plane of the kubernetes cluster too, along with the worker nodes (which is the usual case these days with the cloud vendor k8s options)

I had to pry out this tweet thread which I wrote about sometime back and it's a good summary more or less, most of which still holds true for me.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">If you can, don&#39;t try managing your own <a href="https://twitter.com/kubernetesio?ref_src=twsrc%5Etfw">@kubernetesio</a> clusters. It can become a huge engineering effort in itself very quickly. If the core business product is not around providing infrastructure to others, using <a href="https://twitter.com/hashtag/kubernetes?src=hash&amp;ref_src=twsrc%5Etfw">#kubernetes</a> solutions provided by cloud providers is not a bad idea</p>&mdash; Tasdik Rahman (@tasdikrahman) <a href="https://twitter.com/tasdikrahman/status/1110927284926447617?ref_src=twsrc%5Etfw">March 27, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

### Don't self host unless you have a very good reason to

The very obvious answer if you ask why, is because with all honesty, it's a huge engineering effort to even run and manage vendor managed kubernetes clusters. I have already written quite a lot, on a bunch of reasons, one particular issue which no-one can't run away from, which is kubernetes upgrades, which I have covered in detail [here](https://tasdikrahman.me/2020/07/22/a-few-notes-on-gke-kubernetes-upgrades/) on what entails in one such upgrade. And there a bunch of things mentioned in this post [here](https://tasdikrahman.me/2020/11/21/choosing-between-one-big-cluster-or-multiple-smaller-kubernetes-clusters/) on the complexities which entail with a kubernetes cluster, vendor managed or self-hosted alike and how effort multiplies as one goes into the direction of multiple clusters. If you have read the above two posts, you might just feel this post getting a bit repeated, hence I will not delve into the points covered in the other two posts.

As the tweet mentions, unless providing kubernetes as a PaaS is your bread and butter and your main business? In most cases you are better off just using a cloud vendor managed offering.

### But Tasdik, I need to customize my kubernetes installation, I will lose that power if I go ahead with a vendor

It will be a very rare event, when a vendor managed kubernetes cluster is not giving you the required flexibility to choose a particular option these days. But even then, if you are really trying to use something esoteric which is really important to you and is required for your workloads, do keep in mind the maintenance of the cluster and that since now you are running it, you would need to know how to operate it.

An event like switching out our [CNI](https://chrislovecnm.com/kubernetes/cni/choosing-a-cni-provider/), moving from kube-dns to [core-dns](https://kubernetes.io/docs/tasks/administer-cluster/coredns/), for example, is best abstracted out in most cases.

I get that people do run other things like postgresql or mysql on VM's, but postgresql doesn't need an upgrade every few months, and would just about run for years without an upgrade without too much hiccups for most of the times.

There's just a mouthful of things to take care off too, in case you decide to manage your own etcd clusters, along with the control pane components. There's great learning for sure in all this, but the idea is to provide a reliable peice of infrastructure over to your customers here (assuming you work in the operations/platform team, your customers are your developers), unless the team is really rock solid in terms of their k8s knowledge and is able to keep up with self hosting, this sort of path can very quickly go down the wrong path for your team.

Plus it would not make too much sense, if you're whole team is just about in the initial days of building your product, this will only (I would also say k8s, if the team is not very familiar with it) end up being a distraction and you are better off with plain simple ASG's and instance groups for that matter. Less moving part is good to start with.

### Ending notes

Having managed and built platforms on top of multiple k8s clusters, for the better part of 3 and a half years now, one thing for sure I love about k8s is the power it gives. The ease of deployments(rolling deployments out of the box), the immutable nature of deployments(containerized workloads), the programmtic interface for starters([kubernetes/client-go](https://github.com/kubernetes/client-go), need I even say more), but at the same time, the right tradeoffs do need to made, if you're just in the phase of building your product, would highly recommend thinking twice in case of going the self hosting route, if you must.

If you are in a very specialised environment where you absolutely know what you are doing and you must, this post is obviously not for you, in which case I would love to hear more about your learnings :).

### Links

- [https://tasdikrahman.me/2020/07/22/a-few-notes-on-gke-kubernetes-upgrades/](https://tasdikrahman.me/2020/07/22/a-few-notes-on-gke-kubernetes-upgrades/)
- [https://tasdikrahman.me/2019/04/04/self-hosting-highly-available-kubernetes-cluster-aws-google-cloud-azure/](https://tasdikrahman.me/2019/04/04/self-hosting-highly-available-kubernetes-cluster-aws-google-cloud-azure/)
- [https://tasdikrahman.me/2020/11/21/choosing-between-one-big-cluster-or-multiple-smaller-kubernetes-clusters/](https://tasdikrahman.me/2020/11/21/choosing-between-one-big-cluster-or-multiple-smaller-kubernetes-clusters/)
