---
layout: post
title:  "Atomic directory symlink replacement with mv 'no-target-directory' option"
date:   2009-04-18 13:32:00
tags: --no-target-directory atomic-rename Coreutils directory filesystem-replication Fileutils GNU Linux mv POSIX replace shell symbolic-link symlink
disqus: true
---

This time in one of my scripts I had to create a **replicated filesystem-like structure** (whole directory tree with selected files) using **symbolic links**. One of the problems with managing this kind of structure was maintaining an appropriate resolution for all the symlinks throughout the whole update process of the replicated tree, which occurred occasionally. Preferably **atomic rename** would do the trick. And as Linux **'mv'** is nothing more than a nice wrapper around **rename call**, doing this right should not have been hard at all.

In fact, a **simple problem** occurred not where I would suspect. I couldn't force the **'mv'** command to **rename a symlink if the destination had already existed as a symbolic link to another directory**.

For instance, let's assume the following structure:

```
./foo -> ./baz (foo being a symbolic link to directory baz)  
./bar -> ./qux (bar being a symbolic link to directory qux)
```

Then executing:  

```
mv foo bar
```

would in fact **move the foo symlink under** **qux directory** (symlinked as bar).

Though this seemed a bit absurd, I could not find a way to make it work as desired (**forcing an overwrite of bar with foo**).

#### Solution:

As always, the most **trivial solution** was the one I hadn't thought about. Condemned once again for **working on archaic systems**, I found out that quite a long time ago (but later than the installation of that particular system) an **option** precisely dedicated for this problem **was added to the 'mv' command**.

All the **recent versions of `mv` support the `-T` or `--no-target-directory` switch**. It disables **treating the last operand in a special way** when it's a **directory**/**symlink to a directory**.

So doing:  

```
mv -f -T foo bar
```
 
would work as expected. Though I'm not 100% sure about its **atomicity** (according to **POSIX** it should be), it's the best I could get. And I haven't noted any problems/**race conditions** so far.

More about the idea behind **'Target directory'** behaviour can be found in the **[GNU Coreutils manuals](http://www.gnu.org/software/coreutils/manual/coreutils.html#Target-directory)**.

Last but not least, always check if you're working on the **latest version of your tools**. Or at least if updating won't fix your issue right away.