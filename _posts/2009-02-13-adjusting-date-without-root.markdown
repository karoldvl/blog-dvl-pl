---
layout: post
title:  "Adjusting date without root access"
date:   2009-02-13 17:43:00
tags: fake-timezone Linux no-root-access ntpd perl rdate system-time time-shift workaround
disqus: true
---

Lately I've experienced some issues with the gameserver I was looking after. The problem with administering something without full privileges and some absurd chain of command is if something goes wrong, you have to be really flexible to fix it. Once again problems - this time the **ntpd went wild**. Actual **time was shifted by more than 2 hours** and there seemed to be **no way to rectify the issue without root intervention** - which was not possible at all in this case.

#### Solution

To be honest this is **not a real solution to the problem**. The proper way to do it is just to fix the underlying cause (faulty ntpd). But I had to **implement some tricky workaround** for the time being.

In fact, it's possible to **"cheat" the userland applications into using the correct local time** by establishing a **fake timezone for the user shell**. Very ugly, but **does not need any administrative privileges**.

To automate the whole process, I wrote a quick **perl script**:

```
#!/usr/bin/perl

use strict;
use POSIX;

# NTP server hostname
our $NTP_SERVER="ntp.icm.edu.pl";

# Local timezone shift in minutes (to UTC)
our $TZ_SHIFT=3600;

# Path where the fake timezone script is saved
our $TZ_FILE="~/.tz_shift";

# Check your rdate path here
my @date = `/usr/sbin/rdate -p -v $NTP_SERVER`;

$date[1] =~ s/.* by ([0-9]*) seconds/\1/g;

my $offset = $date[1]+$TZ_SHIFT;

print "Time offset: $offset seconds\n";

my $hours = floor($offset / 3600);
$offset -= $hours * 3600;

my $minutes = floor($offset / 60);
$offset -= $minutes * 60;

my $seconds = $offset;

print "Shifting time zone: $hours hour(s), $minutes minute(s), $seconds second(s)\n";

open(TZ, ">", $TZ_FILE);

printf TZ "#!/bin/bash\n";
printf TZ "export TZ=COR-$hours:$minutes:$seconds\n";

# This should not be needed, but for some unknown reason
# this fixed some issues when not executing the script on logon
system("export TZ=COR-$hours:$minutes:$seconds\n");

system("source $TZ_FILE");
```

You may ask **why use rdate** - when everything goes wrong, it means everything - the **ntp client interface was crippled too**, so I had not much choice left. But from there on it works like a charm, at least for a while. Though it does the trick, it's not a thing you would like to try at home, so I won't recommend it. Better get the ntpd fixed properly.