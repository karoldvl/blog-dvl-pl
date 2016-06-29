---
layout: post
title:  "Increasing swap space in Xen VPS using swap files"
date:   2009-07-20 19:10:00
tags: CheapVPS fstab Linux mkswap swap-file swap-partition VPS Xen
disqus: true
excerpt: Getting back after some major VPS problems I had to rebuild my system from scratch...
---

#### Problem:

Getting back after some major VPS problems (repercussions of the turmoil CheapVPS was in after [the attack](http://www.securitywatch.co.uk/2009/06/11/vaserv-crippled-by-hackers/)) I had to rebuild my system from scratch. Fortunately, my VPS wasn't directly affected by the attack, so no data loss occurred, but the host node I resided on got crippled so badly it had to be recreated before it could reboot all the domUs. But as always, something must have gone wrong, and my VPS was network dead for almost a week. After some unsuccessful bug squashing, the only way to go was to relocate to a new VPS instance.

[As previously](http://blog.dvl.pl/article/2009/02/14/cheapvps-issues/), the **swap partition was not installed properly**. However, this time **no swap disk was available** and the [/etc/fstab trick](http://blog.dvl.pl/article/2009/02/14/cheapvps-issues/) didn't work. So I thought it would be faster to sort it out on my own, than file another support ticket after a week of struggling to get my VPS up and running.

#### Solution:

When there's **no dedicated swap partition**, a workaround is possible by the means of **swap files**. The bad thing about them is they consume the space within allotted disk limits. But the good side is they are always available and their size can be tweaked to one's liking (or probably needs).

The procedure is quite straightforward, but should be done with caution (as the commands employed are of destructible nature):

1\. First, **create an empty file which will serve as a swap file** by issuing the following command:

```dd if=/dev/zero of=/swap bs=1024 count=1048576```

where `/swap` is the desired name of the swap file, and `count=1048576` sets the size to 1024 MB swap (for 512 MB use 524288 respectively).

2\. **Set up a Linux swap** area with:

```mkswap /swap```

Old versions of mkswap needed the size of the swap file/partition, but with newer ones it's better not to specify it at all since a small typo can erase the whole disk.

3\. It's wise to set the permissions as follows:

```chmod 0600 /swap```

4\. The last thing - add the new swap file to **/etc/fstab**:

```/swap            swap          swap     defaults,noatime           0 0```

This way it will be loaded automatically on boot.

5\. To **enable the new swap** space immediately, issue:

```swapon -a```

And done. Check with `free -m` if everything went right. You should be seeing **additional swap space** available. Though it's an easy way to **increase available swap on Xen VPS systems**, swap files won't do for deficient memory. But they provide a nice cushion for temporary spikes - it's better to serve content slowly, than die completely in the process, locking out the entire system.