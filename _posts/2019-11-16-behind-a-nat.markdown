---
layout: post
title:  "Self-hosting behind a NAT"
date:   2019-11-16
category: cloud self-hosting linux
backgrounds:
    - https://blog.jdonado.com/assets/images/behind-a-nat-bg.jpg
thumb: https://blog.jdonado.com/assets/images/behind-a-nat.jpg
tags: cloud self-hosting linux
---

> No one knows what it's like to be a bad man, to be a sad man... behind a NAT

Said [a famous song](https://www.youtube.com/watch?v=dMrImMedYRo) (more or less...)

# You might be behind a NAT too

It's been years now since the times where you would get a static IPv4 address assigned to your home together with your internet access. An address that you could use to ping your home router from anywhere in the world. Now you're lucky if you're even offered the option of paying an extra fee to get one. Sometimes not even a business contract will give you that.

Now, most of the time, your home or small business router will only be reachable on an [IPv6](https://en.wikipedia.org/wiki/IPv6) address. In some cases your [ISP](https://en.wikipedia.org/wiki/Internet_service_provider) might be using something like [DS-Lite](https://www.citrix.com/blogs/2012/03/22/ds-lite-%E2%80%93-ipv4-over-ipv6-and-nat/), which puts you behind a [NAT (Network Address Translation)](https://en.wikipedia.org/wiki/Network_address_translation) protocol, making it very difficult for you to [self-host](https://en.wikipedia.org/wiki/Self-hosting_(web_services)) anything at home.

## But... why??

There's just [not enough IPv4 addresses](https://en.wikipedia.org/wiki/IPv4_address_exhaustion) for all of us, and although **[IPv6](https://en.wikipedia.org/wiki/IPv6) is the solution** (and although it's been around since 1998...), [adoption is still slow](https://www.google.com/intl/en/ipv6/statistics.html#tab=ipv6-adoption). Chances are if your server is only available under an IPv6 address, most people out there won't be able to reach you. You still need an IPv4 address for people to be able to find you on the internet in 2019. **Sadly**.

So if you want to be able to **self-host** anything at home you need some kind of workaround. A [dynamic DNS](https://en.wikipedia.org/wiki/Dynamic_DNS) is probably the most popular option (one that supports IPv6), but depending on your ISP's setup, that might not work for you either. So, what other options do we have?

## Wait a minute, we have the cloud now! Why should I bother about self-hosting at all?

True. I wouldn't recommend self-hosting at home for anything other than experimenting with software for your personal use. I wouldn't even self-host at home my personal website.

**BUT** there are just [so many awesome things that you can self-host](https://github.com/awesome-selfhosted/awesome-selfhosted/blob/master/README.md) for your personal use... here are some examples:

- You can have your own [alternative to Dropbox or Google Drive](https://filebrowser.xyz/). Just buy a cheap 8TB hard drive and forget about paying monthly fees for getting as much online storage as you want.
- You can use [mStream](https://www.mstream.io/) or [Koel](https://koel.phanan.net/) as an alternative to a music streaming service (Spotify, Deezer, Apple Music...). If you happen to have an mp3 collection from the days where an iPod was a cool gadget you might as well have been missing it! It's time to give it a second chance.
- The same idea applied to Netflix: [Streama](https://github.com/streamaserver/streama)
- Who wants to upload photos to Flickr when you have [Lychee](https://lycheeorg.github.io/) 😄
- You can even set up your own [Kubernetes cluster](https://medium.com/nycdev/k8s-on-pi-9cc14843d43) using Raspberry PIs. Hours of entertainment await you with that.

## Okay, you've convinced me, I'm behind a NAT and I want your magic solution!

Now we're talking! If you want to self-host stuff at home and you happen to be behind a NAT; if for whatever reason a dynamic DNS doesn't work for you, **you can always SETUP A REVERSE PROXY** ⚡️⚡️⚡️⛈.

The catch here is that you need access to a machine that is already reachable under an IPv4 address. If you're reading this, you probably have one. If you don't have it yet, you can get one for [3€ a month](https://www.vpsag.com/), and use it not just as a portal for your self-hosted stuff, but for hosting anything that doesn't require a lot of processing power (that's where I host this blog and all my side project's pages). Some other popular options are [Digital Ocean](https://www.digitalocean.com/) or [AWS EC2](https://aws.amazon.com/ec2/).

You also need a machine (or more!) that is always running at home, where you'll run the stuff that you want to self-host. An old laptop will do. You just install there whatever Linux distro you like the best and you're good to go.

Take a look at mine, isn't it beautiful?

![laptop for self-hosting](https://blog.jdonado.com/assets/images/laptop.jpeg "My good'ol Thinkpad")

A Raspberry PI will do the trick too.

## Setup a Reverse Proxy

I'm taking for granted that you already have something on that laptop that you want to see online from an IPv4 address. Something that you can visit locally as `http://localhost:8080` or similar.

You also have an online server, which is reachable on an IPv4 address, like this `216.58.213.196`.

First, [install NGINX](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04) on your server. Once you're ready, you should be able to visit the default NGINX initial page on `http://[YOURONLINEIP:80]`.

Now you can establish an `SSH` tunnel between your laptop at home and your server. A tunnel with a reverse proxy where your local port (`8080` in the example) is mapped to some other port of your server (e.g.: port `17899`). To achieve this, use this command:

`ssh -N -g -R 17899:localhost:8080 yourusername@[YOURONLINEIP]`

While this tunnel is active, edit the NGINX configuration of your server with the following lines:

```nginxconfig

http {

  upstream backend-home {
    server 0.0.0.0:17899;
  }

  server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name _;
    return 301 https://$host$request_uri;

    location / {
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_pass_request_headers on;
      proxy_pass http://backend-home;
    }
  }

}
```

Restart NGINX on your server:

`sudo systemctl restart nginx`

Now you should be able to visit `http://[YOURONLINEIP:80]` and see whatever you have on the `localhost:8080` of your laptop at home. ISN'T IT GREAT!!???

## Some Considerations

If you want that connection to always be active, you'll need to follow a couple of steps more. Otherwise, the tunnel will be closed after a while and the magic will be gone.

To do that, you need to first configure an SSH Key-Based Authentication to establish an SSH connection with the remote machine **without a password**. [Here's a very easy tutorial](https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server) that explains how to do it.

Once you can connect from your laptop to the remote machine via SSH without entering a password, you need a service that establishes that connection and keeps it alive.

A rather simple and reliable way is by defining a `systemd` service on your laptop. To do that, edit a new file called `sshtunnel.service` on `/etc/systemd/system/`:

`sudo vim /etc/systemd/system/sshtunnel.service`

And add the following contents:

```systemd
[Unit]
Description=SSH Tunnel
After=network.target

[Service]
Restart=always
RestartSec=20
User=[YOURLAPTOPUSER]
ExecStart=/usr/bin/ssh -N -g -R 17899:localhost:8080 yourusername@[YOURONLINEIP]

[Install]
WantedBy=multi-user.target
```

Then run the following commands:

```shscript
sudo systemctl enable sshtunnel
sudo systemctl start sshtunnel
```

The first command will tell your system to run the service on startup. The second one (`start`) will start the service right away.

Note: there are alternatives to creating your custom ssh service (e.g.: [autossh](https://linux.die.net/man/1/autossh)), but a custom service proved to be more stable on my machine. Feel free to use whatever configuration works best for you to keep the connection active.
