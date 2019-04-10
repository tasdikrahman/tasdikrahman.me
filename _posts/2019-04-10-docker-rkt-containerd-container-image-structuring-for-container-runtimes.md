---
layout: post
title: "Container Image Structuring for container runtimes"
description: "Container Image Structuring for container runtimes"
tags: [docker, containers]
comments: true
share: true
cover_image: '/content/images/2019/04/docker.png'
---

While you might have read posts about [docker](https://news.ycombinator.com/item?id=16036268) being dead, but given it's adoption. That's not [really the case](https://sysdig.com/blog/2018-docker-usage-report/).

While we have other container runtimes like [runc](https://github.com/opencontainers/runc), 
[containerd](https://blog.docker.com/2017/08/what-is-containerd-runtime/), 
[rkt](https://coreos.com/rkt/) and some others. Docker is still something which a lot of 
folks running containers use as their container runtime. 

What this post will describe is one of the many approaches of structuring your container images, keeping in mind reusability, security and best practices in mind and keeping them as lightweight as possible. At the time of writing this, this is something which is still used to run production container workloads in my last company.

### Prelude

Before going ahead, just so that we are on the same page.

> A Container image is a filesystem tree that includes all of the requirements for running a container, 
as well as metadata describing the content. You can think of it as a packaging technology.

> A container is composed of two things: a writable filesystem layer on top of a container image, 
and a traditional linux process. Multiple containers can run on the same machine and share the OS 
kernel with other containers, each running as an isolated processes in the user space. Containers
take up less space than VMs (application container images are typically tens of MBs in size), and
start almost instantly.

[Source: project atomic, container best practices](https://web.archive.org/web/20180128191607/http://docs.projectatomic.io/container-best-practices/)

### Introduction

[Immutable Server](http://martinfowler.com/bliki/ImmutableServer.html) pattern is something which 
we used to follow in my last company. [Netflix has written at length](https://medium.com/netflix-techblog/how-we-build-code-at-netflix-c5d9bd727f15)
on how they do it. More on how we used to do it in another post.

I will not go into the how and why of immutable infra in this blog post, as that is something which 
deserves it's own post.

Docker presents fit's right in, if you follow the above pattern for your infrastructure.

Which is, if you are baking the whole application using [packer](https://www.packer.io/) or something similar, including config inside the [AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html),and then adding that AMI in the launch config for the [ASG](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html) so that the newer instance which comes up when the ASG scales up, is an exact copy of the instances already present in the ASG. 

What you have is repeatable infra in short, with the above pattern. And you start treating servers as 
[cattles and not pets](https://news.ycombinator.com/item?id=7311704).

### Layering of container images

<center><img src="/content/images/2019/04/container-image-layering.png"></center>

We used to follow a layered approach of immutable infrastructure, where we would have a base layer.

- **Base Layer**: 

contains a fresh copy of an operating system ([alpine linux](https://alpinelinux.org/) in this case) 
and would include core system tools, (eg: such as bash or coreutils, curl, [dumb-init](https://github.com/Yelp/dumb-init) et al) 
and tools necessary to install packages and make updates to the image over time. 

As for the Intermediate container images, each would use the base layer, hence inheriting from the 
base image.

- **Intermediate Layers**:

  - Language runtime
    - python-27
    - php-7.1
    - go-1.8.3
  - Web server
    - apache2
    - nginx
  - Combination of (specific web server + specific language runtime)
    - python-27-{ nginx/apache2 } 
    - php7-nginx-{ nginx/apache2 }
    - golang-nginx-{ nginx/apache2 }

*Note: The above intermediate layers are just to show you an example, you can 
replace it with your use case.*

Dependency managers like pip/composer/golang-dep would go in this layer for the next layer
to make use of it and after their use we can clear their cache.

For example in the case of
- pip : `rm -rf ~/.cache/pip/*`
- composer : `composer clear-cache`
- apk: `rm -rf /var/cache/apk/*` that is if `--no-cache` is not being passed whilst `apk add <package>`
- go-dep: the cache might be useful for debugging if something went wrong with that 
old cache. But this can be debated of whether or not to remove `$GOPATH/pkg/dep`

- **Application Image layer**

This is where the container image would contain dependencies specific to the application and other 
required tooling, inheriting other things from the previous layers.

### Security

- [Dumb-init](https://engineeringblog.yelp.com/2016/01/dumb-init-an-init-for-docker.html) 
should be specified as the entrypoint for the application container image which is yet
to be followed in some remaining container images so that /entrypoint.sh is executed as 
CMD as argument to dumb-init. [More on why have something like dumb-init as PID 1](https://news.ycombinator.com/item?id=11802993)

- If the service does not need `root` privileges, create a new user and switch the user with `USER` 
directive in the application container image.

```
RUN groupadd -r myapp && useradd -r -g myapp myapp
USER myapp
```

- Adding better security vulnerability testing
    - [Drone Clair Plugin](https://github.com/jmccann/drone-clair)

### Keeping the size of the docker image small

At each layer

- the necessary package managers should be cleared of their cache
- Unnecessary layers file system layers should not be created
- Unwanted packages/libs should not be added.

The above division ideally, should always be maintained and any new requirement should 
always go into the layer most appropriate for it

- Use `.dockerignore` wherever necessary as when building the image, docker has to prepare 
context first, gather all files which would be used in a process. Default context contains 
all files in the directory, which would include things like .git directory for example. Which 
can get pretty big citing the .git/objects subdir.

- Optimizing `COPY` and `RUN` directives
    - Putting least frequently changed things on the top of the Dockerfile, which would 
    help us enable caching better. 
- Whenever possible, chaining commands together (if possible) and sorting multi-line arguments alphanumerically, which will help avoid duplication of packages and make the list much easier to update. This also makes it a lot easier to read and review. Adding a space before a backslash (\) helps as well.

Example: 

```
RUN apt-get update && apt-get install -y \
  bzr \
  cvs \
  git \
  mercurial \
  subversion
```

### Good to have 

- Linting, which can enforce standardization across the container images, a possible solution will be https://github.com/projectatomic/dockerfile_lint

### References

- http://docs.projectatomic.io/container-best-practices/
- https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/
- https://opensource.googleblog.com/2018/01/container-structure-tests-unit-tests.html
- https://github.com/Yelp/dumb-init