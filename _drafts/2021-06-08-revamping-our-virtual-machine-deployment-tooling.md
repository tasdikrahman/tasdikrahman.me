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

## Background

Our deployment platform comprises, of a central service registry, two separate services to deploy to virtual machines and kubernetes respectively, a cli to interact with the service registry, which is given out to the developers of the org using which they can do numerous things for their application in a self serve manner. One of those things, being the ability to deploy their applications via the CLI.

The idea is to abstract out the deployment process from the developers as much as possible, but also giving them the means to debug with the logs being shown to them, which are meaningful to them to figure out what went wrong if it doesn't work as expected.

For this post, I will specifically talk about the virtual machine deployment service, called vesemir, which we have in place and how we went about revamping it's infrastructure along with refactoring it to keep it healthy and maintainable for others to work on it's codebase.

### What does it really do?

Vesemir is a inhouse python service, which in a gist is a wrapper on top of chef API's, where-in it receives a request for deploying a service in a particular cluster (a GCP project), filters out the VM's where it has to deploy the changeset and then goes about deploying the changeset, either one at a time or at the level of concurrency insisted upon by the request.

### How does it do it?

The initial request payload, bits which are of most relevance are
- application name
- environment
- team
- chef tags to be used for filtering the application VM's
- haproxy metadata (degradation time specified, cookbook/recipe/tags to filter the haproxy boxes, haproxy backend)
- concurrency (number of VM's to deploy at a time)

Once vesemir, receives this piece of information, it queries the chef server via [pychef](https://github.com/coderanger/pychef), with the information passed for the search tags, the query for which gets constructed and send to the central chef server. The hostnames and their IP's then get parsed.

This takes care of the first bit of the problem where we know which VM's are to be touched for deploying the changeset.

Along with this bit, the [playbook.PlaybookExecutor](https://github.com/ansible/ansible/blob/v2.5.0/lib/ansible/executor/playbook_executor.py#L41) gets initialised with the ansible playbook(more on this later) which we would be executing on the hosts found, the inventory file, and the variable manager named argument taking in extra variables which are going to be used by the templatised playbook.

We write the ansible inventory file in a temporary file, using [NamedTemporaryFile](https://docs.python.org/3/library/tempfile.html#tempfile.NamedTemporaryFile) which gets deleted after the whole request/response lifecyle for a deployment, which gets fed into the [InventoryManager](https://github.com/ansible/ansible/blob/v2.5.0/lib/ansible/inventory/manager.py#L118), where we pass the hostnamefile which we created above as [`sources`](https://github.com/ansible/ansible/blob/v2.5.0/lib/ansible/inventory/manager.py#L137)

The ansible playbook which gets fed into `playbook.PlaybookExecutor`, is a static playbook with jinja templating, where we feed the options via the [VariableManager](https://github.com/ansible/ansible/blob/v2.5.0/lib/ansible/vars/manager.py#L76) into the [`extra_vars`](https://github.com/ansible/ansible/blob/v2.5.0/lib/ansible/vars/manager.py#L85)

The options named var, will take in details like, which user and other authz/authn details to be used by ansible to ssh into the hosts, along with options like `forks` where the concurrency is set for the number of boxes where we would want to run the playbook.

The next step is to execute the [`run`](https://github.com/ansible/ansible/blob/v2.5.0/lib/ansible/executor/task_executor.py#L84) method on the initialiazed object of `playbook.PlaybookExecutor`. The important bit here after the run is the collection of stats which we pick up, from the [`TaskQueueManager`](https://github.com/ansible/ansible/blob/v2.5.0/lib/ansible/executor/task_queue_manager.py#L52), which is what we then use to check for nodes where the playbook ran successfully or not, if the hosts were unreachable and so on.

This piece of information is then used to form a response to be given back to the client which has called vesemir.

As for the playbook and what does it do, in gist, it first, disables the server where it is first going to deploy, in the haproxy backend for the application servers. Introduces the changeset, restarts the service, enables this VM back in the HAproxy backend with weights if provided during the request, an option to sleep for a bit is also introduced which is again controlled by the request, before which the VM is inserted back with 100% weight.

The playbook is then looped over the hosts, returned by the chef query while searching.

## Previous state of affairs

Trite as it is, everything in software I feel is improved over iterations and the same is true with vesemir here too. We have a functional piece of software which does things as expected and has been running for quite some time now. After the initial set of features, this project remained in a bug fix state for a bit.

When our team took ownership for the deployment ecosystem, we started looking for ways in which we could better handle the support requests for deployment issues and to reduce the firefighting, given we were slowly starting to gain context bit by bit.

For starters, there were around 2-3 boxes(VM's) in which Vesemir was deployed, which would receive requests for deploying applications. We also noticed some teams independently cloning the initial vesemir VM's, where they had added their own set of changes and then using this Vesemir VM to deploy their applications.

Centralised logging was something which we needed to add. This would cause devs to look inside the specific VM boxes of vesemir to see what was going on.

Monitoring was missing on the boxes, if and when CPU would spike, we would not know about it and the deployments being processed would get slower and take more time. This would cause deployment failures, a bit of that is explained here in this [post](https://tasdikrahman.me/2021/06/06/bug-which-would-cause-some-deployments-to-get-triggered-again-and-again/)

Integration testing for the changes before deploying them to production set of vesemir VM's was very manual and haphazard, prone to manual errors. It involved, removing one of the vesemir boxes, from behing the HAproxy and then deploying the changes to this vesemir box, testing it all along.

The way we deployed vesemir to the vesemir VM was very manual, someone would have to remove the box from the HAproxy manually, pull the changes to the boxes, and then restart the service, being supervised by systemd. The whole process of deploying would take around ~10mins for the whole set of vesemir boxes.

Before starting with anything, we would want to solve these issues for basic sanity of vesemir.

## What did we fix first

As with everything, prioritisation is crucial. Breaking down the tasks into tactical and strategic fixes was what we ended doing.

Fixes which would immediately give us value and would help us reduce the firefighting were prioritised first. Which would help us get the breathing space.

Fixes/features which would help us long term value over a larger segment of users, re-architecting the whole setup, providing better reliability, refactoring which would help us maintain the codebase better.

## Baseline fixes

To solve the onboarding issue of other devs in the team and to reduce the backlog of support requests received by our team for deployment failures, we ended up creating a playbook which would consist of the different failure modes for vesemir and their solutions. Which we gave to our platform support team (more on this model later),  who would then look at the tickets and then solve the customer deployment failure issues. If there would be something which they wouldn't be able to help with, we would come in as the second line of defense. This helped us immensely in freeing up time to actually dedicate to solve the initial burning issues which we were seeing with vesemir.

The first thing which our team ended up solving was adding logging support to Vesemir, which would be then pushed to a centralised logging platform. This would allow us, as well as the developers of the product teams to look into what really caused the issue and then give us that initial bit of information about what went wrong.

Along with it, we also consolidated the vesemir VM's and put them behind an HAproxy box, to front the service and loadbalance the incoming requests.

We also picked up adding monitoring and alerting for the vesemir VM's which would allow us visibility into what was happening. This also allowed us to see the CPU going all the way upto 100%, without any signifant increase in deployment requests being received.

[Vidit](https://twitter.com/viditganpi/) has written a [great article](https://www.gojek.io/blog/how-we-reduced-skyrocketing-cpu-usage) on solving the same via going from python2 to python3 helped fix the issue altogether of the CPU spiking and staying that way, helping us reduce the number of VM's which we had

We ended up replicating the production setup for an integration setup, which allowed us to have an exact replica of production setup. The integration environment of the deployment orchestrator, was updated to start pointing to the integration of vesemir which we just created. This helped us test end to end, by sending deploy requests from the cli which we give to the developers. Allowing us to catch the errors before them landing up on production.

Deleting dead code, adding missing tests, a linter, along with refactoring the codebase helping us maintain the codebase better and helping new devs getting onboarded to it much faster then before.

Adding support for storing deployment metadata like which application is getting deployed, the number of VM's which we are deploying, the time it's taking for the deployment, the chef tags being used to search for the application etc were also a few things which we started persisting in the vesemir database.

## Deploying vesemir by the service which is used for deploying to kubernetes

The next big chunk of work which we wanted to tackle was to automate the manual deployment process of vesemir, which would not only be time consuming for a developer in our team but would also be error prone whenever someone would be doing it.

This is where normandy came into picture. More on normandy in another post, but it's a service written on top of go-client which would effectively be used for creating creating/updating/deleting kubernetes resources required by an application when it is getting deployed to a kubernetes cluster.

The immediate benefit coming out of deploying vesemir via normandy, was that now what used to take more than 10-15mins of manually deploying vesemir to the VM's. We now were having a system in place where we would be able to deploy vesemir with the help of our deployment tooling to the developers, with the same deployment UX, along with rollback being available via the same interface.

We ended up adding support for python stack based deployments via our deployment platform as part of this effort, which came as a strategic win for us.

## Outcome

The biggest outcome, reducing the defect rate by ~18% from what it was earlier, was the most tangible thing which came out of our revamp.

Monitoring, logging and reliability changes, along with better testing mechanisms introduced, helped reduce the hesistation by devs diving deeper into vesemir's codebase, which helped in taking it from a codebase which people feared to introduce changes into to something where the changeset could now be introduced in a couple of minutes, with immutability baked in.

## Takeaways

I learned a bunch during the course of the whole revamp, but the biggest for me was prioritisation on what is the more important problem to solve which would create more impact and helping the end users along with project management.

This was also the time when fresh grads had joined our team, helping them ramping up on the codebase and the operational side of things was something which helped me deepen my own understanding around our domain.

Thanks for reading, more on around normandy, our kubernetes deployment service, the other part of our deployment platform.

### Links

- [playbook.PlaybookExecutor class - https://github.com/ansible/ansible/blob/v2.5.1/lib/ansible/executor/playbook_executor.py](https://github.com/ansible/ansible/blob/v2.5.1/lib/ansible/executor/playbook_executor.py)
- [VariableManager class - https://github.com/ansible/ansible/blob/v2.5.0/lib/ansible/vars/manager.py#L76](https://github.com/ansible/ansible/blob/v2.5.0/lib/ansible/vars/manager.py#L76)
- [TaskExecutor class - https://github.com/ansible/ansible/blob/v2.5.0/lib/ansible/executor/task_executor.py#L84](https://github.com/ansible/ansible/blob/v2.5.0/lib/ansible/executor/task_executor.py#L84)
- [TaskQueueManager class - https://github.com/ansible/ansible/blob/v2.5.0/lib/ansible/executor/task_queue_manager.py#L52](https://github.com/ansible/ansible/blob/v2.5.0/lib/ansible/executor/task_queue_manager.py#L52)
- [https://github.com/coderanger/pychef](https://github.com/coderanger/pychef)
- [https://www.gojek.io/blog/how-we-reduced-skyrocketing-cpu-usage](https://www.gojek.io/blog/how-we-reduced-skyrocketing-cpu-usage)
