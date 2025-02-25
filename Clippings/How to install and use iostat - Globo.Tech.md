---
title: "How to install and use iostat - Globo.Tech"
source: "https://www.globo.tech/learning-center/install-use-iostat/"
author:
  - "[[GloboTech Communications]]"
published: 2016-09-08
created: 2025-02-14
description: "This tutorial will show you how to install and use iostat on your server. iostat commands provide valuable information for system administrator."
tags:
  - "clippings"
---
	Posted By: Sep. 07, 2016

## How to install and use iostat

iostat, short for input/output statistics, is a popular Linux system monitoring tool that allows users to identify a variety of performance issues as well as obtain CPU and system information from the collection of statistics it collects. These statistics, like the iostat name suggests, involve operating system storage input and output. As more than a statistics collection tool, the comprehensive overview that the iostat command provides is valuable for any system administrator.

This tutorial will cover installing and using iostat for yourself on a Linux server.

## Getting Started

The completion of this tutorial requires the following:  
• 1 Linux Server ([Cloud Server](https://www.globo.tech/cloud-server-pricing) or [Dedicated Server](http://www.globo.tech/dedicated-server-hosting))

## Tutorial

The process of installing iostat is very simple. iostat is part of the sysstat package, which you can install on your respective system with one of the below commands. Linux iostat command to report CPU statistics and I/O statistics:

**CentOS**

`yum install sysstat`

**Ubuntu / Debian**

`sudo apt-get install sysstat`

## Running iostat

The iostat tool is accessed via its command, iostat. Enter this command to see how it works:

`iostat`

Here is a sample output of the sort you will when you call the iostat command:

`Linux 2.6.32x86_64 (dev-db) 01/01/2016   avg-cpu:  %user  %nice  %system  %iowait  %steal  %idle              5.00    0.00    0.50    2.00    0.00    92.50   Device:  tps  Blk_read/s  Blk_wrtn/s  Blk_read   Blk_wrtn   sda     200.53   1022.33  1394.72    2214048305  3845623328   sda1    150.20    473.24  1422.29    1844686896  3295752889   sda2    20.00     320.12  145.21     1000012186  1668872653`

While this report may, at first glance, look complicated, understanding what the report shows is actually fairly straightforward. We will explain, section by section, what each aspect of the iostat report entails so that you can easily use the reporting for yourself:

**Section 1: the CPU report**

Within the first part of the report (the area with percentage values), you will find the CPU statistics that iostat has collected. Here we break down for you the statistics that you will find, and what they mean. For related statistics, we have added a helpful summary in bold so that you can see what is involves at an overview:

Concerning CPU usage at the user/application level with and without nice priority, and at the system/kernel level:

**%user:** This column displays the percentage of CPU utilization that has occured during execution at the user/application level.  
**%nice:** This column also displays the percentage of CPU utilization that occurs while executing at the user level, however this time with a nice priority. This means that only commands called with the Linux command nice, which lowers priority below the standard level, are shown in this statistic.  
**%system:** In this column, you can see the percentage of CPU utilization that occured while executing at the system (also known as kernel) level, as opposed to the above statistics.

Concerning CPU idle and wait times:

**%iowait:** This statistic is the percentage of the time that the CPU or CPUs were idle during which the system had an outstanding disk I/O request.  
**%steal:** The second to last column goes on to show the percentage of time spent in involuntary wait by the virtual CPU or CPUs while the hypervisor was servicing another virtual processor.  
**%idle:** The last column in this section shows the percentage of time that the CPU or CPUs were idle and the system did not have an outstanding disk I/O (input/output) request.

**Section 2: the device utilization report**

The second portion of the iostat report, which is oftentimes larger than the first section, lists all devices and displays statistics that concern their usage. This section is read horizontally, with each row displaying the statistics for its respective device:

**Device:** The very first column is the one that shows the device or partition name as listed in the /dev directory is shown.  
**tps:** This next column begins the statistics for the device, and displays the number of transfers per second (tps) that were issued to the device. A busier processor will have a higher number of these transfers.  
**Blk\_read/s:** This statistic, blocks read per second, shows the amount of data read from the device, as expressed in a number of blocks (e.g. kilobytes, megabytes) per second.  
**Blk\_wrtn/s:** Similarly, blocks written per second is shown with the amount of data written to the device also expressed in a number of blocks (e.g. kilobytes, megabytes) per second.  
**Blk\_read:** The total number of blocks read for each device are displayed in the second-to-last column.  
**Blk\_wrtn:** Likewise, the total number of blocks written is displayed, for each device, is in the last column.

## Enhancing the iostat report

**1\. Display megabytes instead of bytes**

By default, iostat measures system input and output with the unit bytes. As bytes are so small, for easier comprehensibility by humans we can convert the report to instead display reports with megabytes, which are significantly larger and much easier to understand at first sight. Call iostat using the -m flag to display megabytes inside the reports:

`iostat -m`

Notice how the output now changes to show megabytes as desired, as seen in this sample output:

`Linux 2.6.32x86_64 (dev-db) 01/01/2016   avg-cpu:  %user  %nice  %system  %iowait  %steal  %idle              5.00    0.00    0.50    2.00    0.00    92.50   Device:  tps   MB_read/s MB_wrtn/s  MB_read  MB_wrtn   sda     200.53    0.62      0.74    1032680  1742461   sda1    150.20    0.29      0.64    1002131  1204562   sda2    20.00     0.17      0.14     393451   322456`

**2\. Display extended status**

While the original report still contains a lot of useful information, we can obtain even more information by using the flag -x with the iostat command in order to display extended reports:

`iostat -x`

The output of iostat will now also include extended disk I/O statistics information as shown in this sample below. Note that we have shown the output wrapped as the header information line for the device columns is very long in comparison to before:

`Linux 2.6.32x86_64 (dev-db) 01/01/2016   avg-cpu:  %user  %nice  %system  %iowait  %steal  %idle              5.00    0.00    0.50    2.00    0.00    92.50   Device:  rrqm/s  wrqm/s   r/s     w/s     rsec/s   wsec/s  \   sda     7684.00  19.00   2420.00  523.00  81848.00 5287.00  \            avgrq-sz  avgqu-sz  await  svctm  %util            29.86     32.99     11.17  0.34   100.00`

To understand what the new extended columns mean, you can look in the man page for iostat either online or with the command:

`man iostat`

**3\. Run iostat with a delay**

It is possible to tell iostat to take multiple reports with a delay known as the interval. This is done with a number parameter given to the iostat command. However, if only the interval parameter is given, then iostat will run continuously generating reports on that interval until you stop it! So we will also provide a second parameter known as the count parameter, which is also a number.

These are given to iostat in the form:

`iostat [OPTIONS] [INTERVAL] [COUNT]`

For an example situation where we want to generate three reports with a three-second interval between reports, and showing megabytes, run the command:

`iostat -m 3 3`

The last two numbers in the above command represent the interval parameter and the count parameter. The interval parameter, the first 3 in the example, tell iostat to wait three seconds between each report, with each subsequent report containing statistics for the time since the previous report. The count parameter, the second 3 in the example, tells iostat how many reports should be generated in total. Returning to our example, the output should now look similar to the following:

`Linux 2.6.32x86_64 (dev-db) 01/01/2016   avg-cpu:  %user  %nice  %system  %iowait  %steal  %idle              5.00    0.00    0.50    2.00    0.00    92.50   Device:  tps   MB_read/s MB_wrtn/s  MB_read  MB_wrtn   sda     200.53    0.62      0.74    1032680  1742461   sda1    150.20    0.29      0.64    1002131  1204562   sda2    20.00     0.17      0.14     393451   322456`

Three seconds pass.

`Linux 2.6.32x86_64 (dev-db) 01/01/2016   avg-cpu:  %user  %nice  %system  %iowait  %steal  %idle              5.00    0.00    0.50    2.00    0.00    92.50   Device:  tps   MB_read/s MB_wrtn/s  MB_read  MB_wrtn   sda     200.53    0.62      0.74    1032680  1742461   sda1    150.20    0.29      0.64    1002131  1204562   sda2    20.00     0.17      0.14     393451   322456`

Three seconds pass.

`Linux 2.6.32x86_64 (dev-db) 01/01/2016   avg-cpu:  %user  %nice  %system  %iowait  %steal  %idle              5.00    0.00    0.50    2.00    0.00    92.50   Device:  tps   MB_read/s MB_wrtn/s  MB_read  MB_wrtn   sda     200.53    0.62      0.74    1032680  1742461   sda1    150.20    0.29      0.64    1002131  1204562   sda2    20.00     0.17      0.14     393451   322456`

**4\. Display only one report section**

Iostat also lets you modify the report to show only the first CPU section or only the second disk section. To show only the CPU statistics, we can use the -c flag with iostat with the command:

`iostat -c`

We will use the example situation where we want to show the CPU section only, using a two-second interval, and display the information five times (that is, make five reports total with one report every two seconds for only the CPU section). For this, execute the following command:

`iostat -c 2 5`

The output will now look something like the following:

`Linux 2.6.32x86_64 (dev-db) 01/01/2016   avg-cpu:  %user  %nice  %system  %iowait  %steal  %idle              5.00    0.00    0.50    2.00    0.00    92.50`

Two seconds pass.

`Linux 2.6.32x86_64 (dev-db) 01/01/2016   avg-cpu:  %user  %nice  %system  %iowait  %steal  %idle              5.00    0.00    0.50    2.00    0.00    92.50`

Two seconds pass.

`Linux 2.6.32x86_64 (dev-db) 01/01/2016   avg-cpu:  %user  %nice  %system  %iowait  %steal  %idle              5.00    0.00    0.50    2.00    0.00    92.50`

Two seconds pass.

`Linux 2.6.32x86_64 (dev-db) 01/01/2016   avg-cpu:  %user  %nice  %system  %iowait  %steal  %idle              5.00    0.00    0.50    2.00    0.00    92.50`

Two seconds pass.

`Linux 2.6.32x86_64 (dev-db) 01/01/2016   avg-cpu:  %user  %nice  %system  %iowait  %steal  %idle              5.00    0.00    0.50    2.00    0.00    92.50`

## Conclusion

This guide has shown the basics of using the iostat system monitoring and statistics tool for your Linux system. More information about possible modifications to the reports can be found in the online man page for iostat in order to get the most you can out of them. If you found this introduction to iostat useful, share it with your friends!