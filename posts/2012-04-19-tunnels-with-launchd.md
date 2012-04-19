---
title: pow + tunnels + launchd
date: 2012-04-19 18:47
---

[tunnels](https://github.com/jugyo/tunnels) is awesome which enables us runnning pow over SSL.

    $ gem install tunnels
    $ sudo tunnels (just to use)

And it's better to work in the background by launchd.

    $ sudo vi /Library/LaunchDaemons/org.rubygems.tunnels.plist

<script src="https://gist.github.com/2420053.js"> </script>
