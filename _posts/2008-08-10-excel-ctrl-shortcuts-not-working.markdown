---
layout: post
title:  "Excel Ctrl+C/Ctrl+V shortcuts not working"
date:   2008-08-10 22:02:00
tags: bug conflict copy Ctrl+C Ctrl+V dual-display DualView Excel NVIDIA nView Office paste shortcut
disqus: true
---

First of all, I have to admit, this one was a bit tough. I've spent a couple of painful hours trying to solve it, but let's get to the matter. My Excel installation behaved strangely lately (a lot of crashes - even when applying bold formatting to cell (sic!)). I've just bought a new LCD (yeah, first-in-a-lifetime DualView experience - great!), so it was a good time for a system reinstallation. However, though no more crashes occurred, a new problem arose - **I couldn't use the good old Ctrl+... shortcuts for copying/pasting** and other things.

#### Follow-up:

As **no "Ctrl" shortcuts worked as they should** (instead I got alternating cell selection and other weird functions) it was a no-go. A really frustrating experience having to work like that. So it had to get priority handling.

After some hours of clicking here and there (no Google answers available at the time) I found out (more or less accidentally) that there's one way to avoid this problem - when **starting MS Office by "Start Menu->Run->Excel" everything worked as it should.** Weird, but 100% reproducible. Starting directly - works OK. Shortcuts, menu items, opening spreadsheets in Explorer or even starting from command line - every time the same bug.

Second "accident" - when starting it "the wrong way" (f.e. opening Excel files) it occurred to me that **using Ctrl+C combination when Excel is loading (spreadsheet visible) can fix this behaviour** too. But you have to do it at the very beginning - when the application has not yet been fully loaded (initializing add-ons and stuff).

#### Solution:

I thought it may be the case of some malformed add-on (even though it was a distil clean install), so I disabled all of them - to no avail. Then I thought about things which have changed since that reinstallation I made some days ago. The only one new was the **dual display configuration** - you bet it couldn't have any influence on it. Gotcha - see how insanely absurd can the computers be at times.

In fact **NVIDIA MultiView drivers and Microsoft Office 2007** do not tend to coexist peacefully. A lot of problems can be seen (seems that Office 2007 has some non-standard windows procedures - this way it can get that unique look'n'feel which does not correspond to any built-in Windows XP style), but here it was the imprecise problem of some MultiView software keyboard conflicts. Not sure if it's a plain keyboard-hook bug (disabling all MultiView shortcuts does not resolve the issue), but it's certainly somewhere deep inside as **turning MultiView features completely off works like a charm**.

Hey, you could've asked, what's the point of disabling all additional DualView functionality in a multi-display configuration? A big price to pay for Excel compatibility. Fortunately, **you can disable individual applications in nView Desktop Manager** (check nView properties or the Control Panel). Just go to the Applications tab and add "disable-all" rules for "excel". It seems there's no way not to lose Excel multi-display functionality and maintain sane keyboard shortcuts. But I'd rather stay with my Ctrl+C/Ctrl+V working even at the price of manual resizing, repositioning and moving Excel windows between the displays.

Huh, hope you don't have to go through it and by the time you're reading this Microsoft and NVIDIA geeks will have it up and running correctly.