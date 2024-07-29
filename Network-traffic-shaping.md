# Traffic shaping
***
## Introduction

Traffic shaping is a bandwidth management technique that delays the flow of network   packets  to manipulate network traffic and apply rate-limits, introduce latency and even simulate packet loss.
There are various tools that help us do traffic shaping, some of the most notable ones 
are [tc (traffic-control)](https://manpages.ubuntu.com/manpages/focal/man8/tc.8.html) for linux and [pfctl (packet-filter-control)](https://www.unix.com/man-page/osx/8/pfctl/) for osx.
we will use [tc](https://manpages.ubuntu.com/manpages/focal/man8/tc.8.html) to accomplish traffic shaping.

## tc(traffic-control)

tc command is used to configure Traffic Control in linux kernel.
Processing of traffic is controlled by three kinds of objects.
They are

* **Qdiscs**
* **classes**
* **filters**
***
### Qdisc

**qdisc** is short for 'queuing discipline' and it is elementary to understanding traffic
control. Whenever the kernel needs to send a packet to an interface, it is **enqueued** to the qdisc  configured  for  that interface. Immediately afterwards, the kernel tries to get as many packets as possible from the qdisc, for giving them to the network adaptor driver.
Qdiscs can be mainly divided into two groups 
Classfull qdiscs - qdiscs which can contain classes
Classless qdiscs - qdiscs which cannot contain classes 

### class

Some qdiscs can contain classes, which contain further qdiscs - traffic may then be
enqueued in any of the inner qdiscs, which are within the classes. When the kernel tries
to dequeue a packet from such a classful qdisc it can come from any of the classes. A
qdisc may for example prioritize certain kinds of traffic by trying to dequeue from
certain classes before others.

### filter

A filter is used by a classful qdisc to determine in which class a packet will be
enqueued. Whenever traffic arrives at a class with subclasses, it needs to be classified.Various methods may be employed to do so, one of these are the filters. All filters attached to the class are called, until one of them returns with a verdict. If no verdict was made, other criteria may be available. This differs per qdisc.
 
***
