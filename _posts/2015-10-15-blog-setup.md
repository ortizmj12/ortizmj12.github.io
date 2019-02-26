---
layout: post
title: Blog Setup
---

So here's the setup I have right now:
* [GitHub Pages](https://help.github.com/articles/using-jekyll-with-pages/) - Obviously.
* [Jekyll](http://jekyllrb.com/docs/home/) - The tech powering this whole bloggy thing.
* Jekyll theme: [Hyde](https://github.com/poole/hyde) - I like the simple display of the posts + the simple side menu.

One thing I wanted to do was migrate my Wordpress.com posts over to Jekyll (I hate making switches and having to start completely fresh). Jekyll's site had a handy page on various ways of importing stuff, [this one](http://import.jekyllrb.com/docs/wordpressdotcom/) is specifically for importing wordpress.com stuff. I ended up using the [exitwp](https://github.com/thomasf/exitwp) tool mentioned on that page. That took all the text and converted them to individual files under the *\_posts* directory, but all the images were linked to the files over at wordpress.com. I wanted this all hosted on GitHub Pages, so I had to go and manually download each image and change the code on each post. Luckily there weren't that many posts...

Another issue I ran into was getting ruby setup properly on this Ubuntu box so I could run Jekyll locally to checkout posts before publishing them or testing themes. The default ruby package is only v1.9 and Jekyll needs v2.0+. I don't do a lot of coding so having multiple versions on a single machine was a learning curve. I ended up using rvm to handle this. The rvm.io site has a [pretty straight forward page](https://rvm.io/rvm/install) on installing rvm. Then I found [this page](https://rvm.io/rubies/default) for steps on how to make v2.2.3 the default.
