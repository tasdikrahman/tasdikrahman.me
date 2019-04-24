---
layout: post
title: "Handling signals in kubernetes"
description: "Handling signals in kubernetes"
tags: [kubernetes, docker, infrastructure]
comments: true
share: true
cover_image: '/content/images/2019/'
---

When the power goes off in a device where linux is running, one can think of ways in which this event can be handles in the applications running. One thing to note is that, when you plug the power cable off, the power doesn't really go off immediately. 

But this needs to be notified to processes so that they can handle such an event and save the state of the application (if any).

A few possible ways of doing so would be to
- Save everything in RAM to disk and then restore the contents stored on disk back to RAM when the startup happens next, but the problem with this approach is that, storing on disk is a slow process.
- Use a file to state of power, where 0 would denote that the power is on and 1 would mean that the power has gone off, but this approach would mean that the processes running in the system should keep track of this bit stored in the file.
- The kernel sends a signal like `SIGTERM` to the process and leaves it to the process on how it handles this signal.

How is you stop a container is something which is very important, depending on your application of course.

## References

- https://lasr.cs.ucla.edu/vahab/resources/signals.html