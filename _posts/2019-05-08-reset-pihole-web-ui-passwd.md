---
layout: post
title: Resetting the Pi-hole Web UI Password The Hard(er) Way
---

I've setup pi-hole recently, twice. The first time was on a first gen Raspberry Pi that stopped booting. I think the SD card got corrupt somehow. The second time was on a new Raspberry Pi 3 B+ that I just bought. If you haven't setup a pi-hole before, at the end of the setup it prints out the password for the web UI. I noticed during the first install that it was just 8 characters and a mix of uppercase and lowercase letters. I thought maybe it was just RNG luck that I didn't get any numbers, but the same thing happened during the second install! But then again, the risk is pretty low as it's only reachable inside my network and the insider threat level (my wife and young kids) just isn't that high.

The end of the install also shows that there's an install log at `/etc/pihole/install.log`. While checking that out I noticed another file in `/etc/pihole/` called `setupVars.conf`. Peaking in there I noticed a parameter called `WEBPASSWORD` with a value that looks like a hash. OK, _now_ I'm interested.

First hash I checked was base64 - super common these days it semes to do all kinds of things. Decoded it, but it was nothing but non-ASCII characters. I did a bit of Googling and found [this thread](https://discourse.pi-hole.net/t/what-is-setupvars-conf-and-how-do-i-use-it/3533) on the pi-hole discourse site. One of the developers commented and said it was double SHA256. There's a couple examples on the page on how to re-create the hash, but figured I'd break one of them down.

```bash
echo -n <YOURPASSWORDHERE> | sha256sum | awk '{ printf "%s",$1 }' | sha256
```

There's one example using `cut` instead of `awk`, but I'm very familiar with `cut` and I like finding new ways to use `awk`. One thing that stood out to me here was the use of `printf`. I've always used `print`. Doing some more Googling I found that `printf` allows a bit more control over the output. Where `print` will add a newline, `printf` will not, which is important when it comes to hashing this password. A newline is a character and will effect the hash value. One thing I don't understand is why they add `"%s"` to the command. Doing some testing, `printf "%s",$1` and `printf $1` will achieve the same thing.

Also of note is the use of the `-n` option for `echo`. This _also_ will print the string without adding a newline. In the `echo` command you can also put the password in quotes and they will not be included in the hash value. That can be handy if the character you want to use contains special characters. Bash could interrupt them differently if they're not in quotes.

Sooo yeah, you can use the above command to get a double SHA256'd hash of the password you'd like to use that you can then assign as the value for `WEBPASSWORD` in `/etc/pihole/setupVars.conf`, then use that password when logging into the pi-hole web UI. Or you can just run the command:

```bash
pihole -a -p <YOURPASSWORDHERE>
```

while you're already logged into the pi-hole RPi/VM and have it do all the hashing for you lol
