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
# traffic-control-linux

  

traffic control

  

tool name: tc

  

## componsents

  

* qdisc

* classes

* filters

* handle

### <u>**QDISC:**</u>

  

Qdisc are like queues for packets.

  

* Every class and classful qdisc requires a unique identifier within the traffic control

structure called handle

  

### <u>**HANDLE:**</u>

  

handle is represented by a `major:minor` no.

  

**major** : This parameter is completely free of meaning to the kernel. The user may use an arbitrary numbering

scheme, however all objects in the traffic control structure with the same parent must share a major

handle number. Conventional numbering schemes start at 1 for objects attached directly to the root

qdisc.

  

**minor**: This parameter unambiguously identifies the object as a qdisc if minor is 0. Any other value

identifies the object as a class. All classes sharing a parent must have unique minor numbers.

  

All qdiscs and classes have individual id which has the format m:n, where m is the major number and n is the minor number. Both m and n are limited to 16 bits. The id is used as the handle in the tc command. For a qdisc, the minor number is 0. For a class, the major number is that of the qdisc that the class belongs to. So if a handle's minor number is 0, it is the id of a qdisc. Otherwise, it is an id of a class whose qdisc is identified by its major number. The root qdisc has the handle 1:0. The handle `ffff:0` is reserved for the ingress qdisc.

  

## setup htb qdisc to limit bandwidth on Egress

  

```sh

dev=eno2

rate=5

SRC_IP=192.168.1.55/32

  

tc qdisc del dev $dev root

tc qdisc add dev $dev root handle 1:0 htb default 30

tc class add dev $dev parent 1: classid 1:30 htb rate ${MAX_IFACE_RATE}

tc class add dev $dev parent 1: classid 1:1 htb rate ${rate}mbit

tc filter add dev eth0 parent 1:0 handle ${htid}::${hash} protocol ip prio 1 u32 match ip src $SRC_IP flowid 1:1

  

```

  

## Intermediate Functional Block

  

The Intermediate Functional Block (ifb) pseudo network interface acts as a QoS concentrator for multiple different sources of traffic. Packets from or to other interfaces have to be redirected to it using the mirred action in order to be handled, regularly routed traffic will be dropped. This way, a single stack of qdiscs, classes and filters can be shared between multiple interfaces.

  

Here's a simple example to feed incoming traffic from multiple interfaces through a Stochastic Fairness Queue (sfq):

  

```sh

modprobe ifb numifbs=1

ip link set ifb0 up

```

  

The first step is to load the ifb kernel module (1). By default, this will create two ifb devices: ifb0 and ifb1. After setting ifb0 up in (2), the root qdisc is replaced by sfq in (3). Finally, one can start redirecting ingress traffic to ifb0, e.g. from eth0:

  

```sg

tc qdisc add dev eno2 handle ffff: ingress

tc filter add dev eth0 parent ffff: u32 protocol ip match u32 0 0 action mirred egress redirect dev ifb0

```

  

The same can be done for other interfaces, just replacing eth0 in the two commands above. One thing to keep in mind here is the asymmetrical routing this creates within the host doing the QoS: Incoming packets enter the system via ifb0, while corresponding replies leave directly via eth0. This can be observed using tcpdump on ifb0, which shows the input part of the traffic only. What's more confusing is that tcpdump on eth0 shows both incoming and outgoing traffic, but the redirection is still effective - a simple prove is setting ifb0 down, which will interrupt the communication. Obviously tcpdump catches the packets to dump before they enter the ingress qdisc, which is why it sees them while the kernel itself doesn't.

  

## setup htb (args)

  

`tc qdisc ... dev dev ( parent classid | root) [ handle major: ] htb [ default minor-id ]`

  

## Adding fq_codel as subque to multiques Nic

  

> We do not have a fixed/unified queue selection.

>

> It can be tweaked by many different things, depending on exact needs.

>

> MQ is not a qdisc per se, it's only a fake one, a demux if you want, so

> that each tx queue has a separate qdisc lock.

>

> If you stick one fq_codel at the top of the hierarchy (instead of MQ),

> then you loose all the pros of having multiple locks : sending packets

> from fq_codel to different queues on hardware makes no sense, since the

> single qdisc lock is the bottleneck.

>

> So if you want fq_codel and MQ, to be able to drive 40G links from many

> cpus, just use :

>

> ETH=eth0

> NQUEUES=16 # or more, check how many tx queues your NIC supports

> tc qd del dev $ETH root 2>/dev/null

> tc qd add dev $ETH root handle 1: mq

> for i in `seq 1 $NQUEUES`

> do

> tc qd add dev $ETH parent 1:$i fq_codel

> done

>

> Thats only replaces the default pfifo_fast on each slave qdisc by

> fq_codel.

  

## deleting htb classes

  

> htb classes can only be deleted after all its children and filters are removed

> [https://bugzilla.kernel.org/show_bug.cgi?id=10166]

  

## Network Profiles

  

| Profile Name | Bandwidth in/out (Kbps) | Bandwidth (Megabits/s)(this is wrong ignore) | Latency (ms) | Packet Loss (%) |

| ---------------------------- | ------------------------------------------------------------ | -------------------------------------------- | ------------ | --------------- |

| 2g-gprs-good | 50/30 | 0.4/0.24 | 500 | 1 |

| 2g-gprs-lossy | 30/20 | 0.24/0.16 | 650 | 2 |

| edge-good | 250/150 | 2/1.2 | 300 | 0 |

| 2gedge-lossy | 150/100 | 1.2 /0.8 | 500 | 1 |

| 3g-umts-good | 400/100 | 3.2/0.8 | 100 | 0 |

| 3g-umts-lossy | 200/50 | 1.6/0.4 | 200 | 1 |

| 3.5g-hspa-good | 1800/400 | 14.4/3.2 | 100 | 0 |

| 3.5g-hspa-lossy | 900/200 | 7.2/1.6 | 190 | 1 |

| 3.5g-hspa-plus-good | 7000/1500 | 56/12 | 100 | 0 |

| 3.5g-hspa-plus-lossy | 2000/600 | 16/4.8 | 130 | 1 |

| 4g-lte-good | 18000/9000 | | 100 | 0 |

| 4g-lte-high-latency | 18000/9000 | | 3000 | 0 |

| 4g-lte-lossy | 7000/3000 | | 120 | 1 |

| 4g-lte-advanced-good | 25000/18000 | | 80 | 0 |

| 4g-lte-advanced-lossy | 15000/10000 | | 70 | 1 |

| no-network (Android only) | 0 | | 0 | 0 |

| airplane-mode (Android only) | 0 | | 0 | 0 |

| Reset | Set network settings to Browserstack’s default configuration |

  

EXERCISE

  

```sh

dev=eno2

rate=5

SRC_IP=192.168.1.55/32
tc qdisc del dev eno2 root

tc qdisc add dev eno2 root handle 1:0 htb default 30

# tc qdisc add dev eno2 parent 1:30 handle 31 fq_codel

tc class add dev eno2 parent 1: classid 1:1 htb rate 5mbit

tc class add dev eno2 parent 1: classid 1:2 htb rate 5kbit

tc class add dev eno2 parent 1: classid 1:30 htb rate 100mbit

tc filter add dev eno2 parent 1:0 protocol ip prio 1 u32 match ip dst 192.168.1.55/32 flowid 1:1

tc filter add dev eno2 parent 1:0 protocol ip prio 1 u32 match ip dst 192.168.1.55/32 flowid 1:2

  

tc filter replace dev eno2 parent 1:0 protocol ip prio 1 u32 match ip dst 192.168.1.55/32 flowid 1:1

  

tc filter add dev eno2 parent 1:0 protocol handle ::${ipaddr last 3 bits} ip prio 1 u32 match ip dst 192.168.1.55/32 flowid 1:2

tc filter add dev ifb0 parent 1:0 protocol ip prio 1 u32 match ip src 192.168.1.55/32 flowid 1:2

  
  

modprobe ifb

  

ip link add name ino2 type ifb

ip link set ino2 up

  
  

tc qdisc add dev ino2 root handle 1: htb default 30

tc class add dev ino2 parent 1: classid 1:2 htb rate 1mbit

tc class add dev ino2 parent 1: classid 1:30 htb rate 100mbit

  

tc filter add dev ino2 parent 1: protocol ip prio 1 u32 match ip src 192.168.1.55/32 flowid 1:2

  
  

tc filter add dev eno2 parent ffff: u32 match u32 0 0 action mirred egress redirect dev ifb0

```

  

## setup hfsc


```sh

#!/bin/bash

tc qdisc add dev eno2 parent root handle 1: hfsc default 10

tc class add dev eno2 parent 1: classid 1:10 hfsc sc rate 100mbit ul rate 100mbit

tc class add dev eno2 parent 1:1 classid 1:12 hfsc sc umax 1500 dmax 50ms rate 10mbit ul rate 10mbit

tc qdisc add dev eno2 parent 1:12 handle 12 netem delay 150ms

tc filter add dev eno2 parent 1: protocol ip u32 match ip sport 22 0xffff flowid 1:12

```


hfsc is a much newer queueing algorithm that implements fair quewing with st curves
## Filter's handle

  

Handle in filter is used to identify the filter.

They consist of three components of which we will only be using two.

These components are sperated by colons like so `htid:hash:nodeid`.

**Manpage definition**

> HANDLE

The handle is used to reference a filter and therefore must be unique. It consists

of a hash table identifier htid and optional hash (which identifies the hash

table's bucket) and nodeid. All these values are parsed as unsigned, hexadecimal

numbers with length 12bits ( htid and nodeid) or 8bits ( hash). Alternatively one

may specify a single, 32bit long hex number which contains the three fields bits in

concatenated form. Other than the fields themselves, it has to be prefixed by 0x.

  

As far as weve seen htid is mostly `800` all the time so we dont have to change that.

We can use the last 3 bit of ip address as the nodeid component of handle to uniquely identify each filter.

  

But ofcourse this prevents a signle ip having more than one filter ,but fortunetely we dont need anymore than one.

  

example :

  

1. Add filters with handles

  

```sh

tc filter add dev eno2 parent 1:0 handle ::100 protocol ip prio 1 u32 match ip dst 192.168.1.100/32 flowid 1:3

tc filter add dev eno2 parent 1:0 handle ::200 protocol ip prio 1 u32 match ip dst 192.168.1.200/32 flowid 1:3

```

  

2. Delete filters with handles

  

```sh

tc filter add dev eno2 parent 1:0 handle ::100 protocol ip prio 1 u32

tc filter add dev eno2 parent 1:0 handle ::200 protocol ip prio 1 u32

```

  

> we dont even need to specify the match condition as well as the flowid

  

```mermaid

flowchart LR

Start --> Stop

  

```

  

## References

  

* <https://www.youtube.com/watch?v=kykJx8hP0lw&list=PL2Zki8-8tLnt3lzhCT44hPnP_mpPEcPMl&index=3>

* <https://tldp.org/HOWTO/Adv-Routing-HOWTO/lartc.adv-filter.hashing.html>

* <https://manpages.ubuntu.com/manpages/kinetic/en/man8/tc-u32.8.html>

* <http://linux-ip.net/articles/Traffic-Control-HOWTO/elements.html#e-shaping>