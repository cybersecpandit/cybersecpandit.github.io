---
title: Over The Wire Natas - Level 0 - 9 Solutions
tags: [WebExploitation, OverTheWire, Walkthrough, Natas]
---

OverTheWire Bandit Wargames is designed like a Capture the Flag or (CTF) game where we have to capture each flag or password to proceed to the next level.
This makes it very enjoyable and learning a lot of tools and tricks of various basic Linux Concepts.

>This writeup will be heavily **hint** focused, but we will also be disclosing each command to get the job done for each level.

<!--more-->

If you are able to understand Nepali Language, then perhaps you could also enjoy the YouTube Playlist that I have made for this topic going in Depth in each of the Levels.

# Level 0

The password for the next level can be seen in the source code.

Right Click -> View Source.

The password will be shown inside an HTML comment `<!-- This is an comment -->`

# Level 1

 Right Clicking is disabled. 

 But we can use some Keyboard shortcuts

F12 - usually opens up the Developer Tools and Browsing to the Source or NetWork Tab we should be able to see the source code.

If this does not work then there must be another shortcut to View Source may be `Q`


# Level 2

Viewing the source gives us a file. 

Whats in the Directory that contains that image. Are there any other files there.

` /files/` directory and the `users.txt` file are interesting.



# Level 3

Bots are not allowed. That means it must be something to do with robots or the `/robots.txt` 