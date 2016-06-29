---
layout: post
title:  "Changing Last.fm username"
date:   2010-03-04 16:47:00
tags: .scrobbler-timeless.log Essential-Profile-Transfer Last.fm listening-history login-change Python recent-tracks scrobble
disqus: true
---

A while ago I conducted a wide-scale **login change** for most of my Internet services. Some of them, however, were reluctant to allow **effortless username change**. This applies in particular to **Last.fm**.

According to the [Last.fm's FAQ](http://www.last.fm/help/faq?category=96#161) it's **not possible to change one's initial username**. Neither is it possible to **move the collected stats/scrobbles** to a new account.

### Solution:

Well, till (and if) **Last.fm** introduces this kind of functionality, there's no real solution to this problem. But there's some (a bit ugly) **workaround** for this.

Thanks to [Ionut Bizau's lastfm_backup.py](http://www.lastfm.pl/group/Last.fm+Web+Services/forum/21604/_/555597) script it's possible to **fetch all the scrobbles to a text file**. For the time being, I see the **original script is no longer available** on GitHub, so here's the source:

```
#!/usr/bin/env python

# Copyright (c) 2009 Ionut Bizau
# All rights reserved.

# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions
# are met:
# 1\. Redistributions of source code must retain the above copyright
#    notice, this list of conditions and the following disclaimer.
# 2\. Redistributions in binary form must reproduce the above copyright
#    notice, this list of conditions and the following disclaimer in the
#    documentation and/or other materials provided with the distribution.
# 3\. The name of the author may not be used to endorse or promote products
#    derived from this software without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE AUTHOR ``AS IS'' AND ANY EXPRESS OR
# IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES
# OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED.
# IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY DIRECT, INDIRECT,
# INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT
# NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
# DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
# THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
# (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF
# THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

__doc__ = """Usage: %s username""" % __file__

import gzip
import sys
from StringIO import StringIO
import time
import urllib2

from lxml.html import parse

try:
    from urlgrabber.keepalive import HTTPHandler
    opener = urllib2.build_opener(HTTPHandler())
except ImportError:
    sys.stderr.write("WARNING: urlgrabber not available. Will not use keepalive.\n")
    opener = urllib2.build_opener()

def parse_xml(url):
    request = urllib2.Request(url, None, {"Accept-encoding": "gzip"})
    response = gzip.GzipFile(fileobj=StringIO(opener.open(request).read()))
    return parse(response).getroot()

def scrape_page(url):
    doc = parse_xml(url)
    for row in reversed(doc.cssselect("table.tracklist tr")):
        try:
            artist, title = [a.text.strip() for a in row.cssselect("td.subjectCell a")]
            timestamp = row.cssselect("td.dateCell abbr")[0].attrib['title']
            yield artist, title, timestamp
        except Exception:
            pass

def scrape_user(url):
    doc = parse_xml(url)
    try:
        page_count = int(doc.cssselect("a.lastpage")[0].text)
    except:
        page_count = 1
    for i in reversed(range(1, page_count + 1)):
        for artist, title, timestamp in scrape_page("%s?page=%s" % (url, i)):
            yield artist, title, timestamp

def main(username):
    url = "http://last.fm/user/%s/tracks" % username
    for artist, title, timestamp in scrape_user(url):
        sys.stdout.write((u"%s\t%s\t%s\n" % (artist, title, timestamp)).encode("utf-8"))

if __name__ == '__main__':
    if len(sys.argv) == 2:
        username = sys.argv[1]
        main(username)
    else:
        print __doc__
```

Before you use it, make sure your **recent tracks are set to public** (for the transition at least). Theoretically another script by the same author (lastfm2itunes.py) should work better - it's possible to bulk upload your listening history from iTunes/Winamp on the first account login in Last.fm (via the default client). But of course this went wrong somehow, and I had to rescrobble my tracks "manually" instead of bulk uploading them (you get only one try with this).

There's a plethora of tools to use the Last.fm API to **submit your listening logs** - check [Rockbox](http://www.rockbox.org/wiki/LastFMLog) for this. I used [dap-scrob](http://scrob.paulstead.com/) as the fastest solution, but due to some unknown problem some of my scrobbles got submitted more than once (though this concerned only a minuscule fraction of tracks).

One problem you can encounter on the way is the need to **sanitize the log** to conform to **.scrobbler-timeless.log** format. The general [.scrobbler.log info](http://www.audioscrobbler.net/wiki/Portable_Player_Logging) can be handy.

There are **two major drawbacks** to this approach. First, **transferring all other settings** (loved tracks, groups etc.) has to be done manually. Second - this method disrupts the **listening statistics** (thousands scrobbles in a few days time - this gives really weird per day average). As to the former issue - there's some **recent development** ([Essential Profile Transfer](http://www.easyclasspage.de/lastfm/seite-14.html)) that should alleviate this particular problem.

This way it's possible to **imitate a Last.fm's username change**. For sure this would be much more clean and easy if Last.fm provided such an option by itself, but it probably involves too much work in the core to be adopted in the near future. So, for the time being, it's the only way I know to "change" the login without beginning completely from scratch.