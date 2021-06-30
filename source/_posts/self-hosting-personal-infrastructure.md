---
title: Self hosting your own services
date: 2021-06-30 19:07:28
tags:
 - linux
 - web
 - security
---

_TL;DR: reboot often, don't self host email, everything behind a vpn_

Self hosting your own web(?) services is a lot of fun and it can be educational
and rewarding at the same time. I've been hosting various things for over 10
years (I started with random things like IRC bots) and here's what I think about
it:

- Treat servers as cattle, not pets.

This is really important - chasing uptime was cool at some point, but it's not
anymore. Rebooting often catches configuration you may have updated by hand that
is not persistent, lets you apply updates (I don't think this should be
automated, however) etc;

- Do **not** self host critical infra like email.

If you cannot afford downtime, don't do it. Even the cheapest mail provider will
be a better bet than your self hosted solution, for the same amount of
effort/cost. Even this blog, which is a static web site, is hosted on Github
pages even though I've more than enough servers.

- You don't need kubernetes. Keep it simple.
- Use Ansible or another something like that to automate provisioning your
server. It does not matter if you only have a single VPS, a bash script is
better than editing your configuration by hand and forgetting about it in a few
days.
- You should ideally also be using ansible to automate cloudflare/registrar
setup for your domains.
- Monitoring - set up something basic. If n>1 then consider having a
minimalistic prometheus setup (maybe even without grafana it's heavier) that you
can use to debug stuff.
- If you use Docker, be aware that it is **not** secure by default. Use your
hosting providers software firewall solution if possible or triple check that it
hasn't punched any holes/done anything else that's weird. I use podman, which is
better than Docker in this regard.
- Don't trust containers you find on the docker public registry. If its
critical/hosts your secrets, build it yourself. It's just writing a Dockerfile,
  its easy.
- If you use AWS/another cloud provider, make sure you have billing alerts set
up even if you don't expect to exceed quotas/are on the free tier.
- Backups are important. RAID isn't a backup. If you're on dedicated
server providers like Hetzner you should plan for your disks to fail. It will
happen to you.

Security
--------

Software is buggy/insecure by default. Your (/my) configuration is most probably
insecure. Self hosting has a huge surface area that can be exploited by anyone
bored enough to do so.

- Set up sane defaults for SSH and firewalls.
- Preferably host your infrastructure behind a VPN. None of my services are
accessible over the internet. I use Wireguard to connect all my personal devices
and all my servers and treat it as a "LAN" of sorts.
- Pin dependencies if possible.

Effort v Reward
---------------

Keep it simple. Unless you're getting paid to do it, this is a hobby. Don't
over-complicate things and end up with a second day job. Offload stuff to paid
services once it becomes critical enough.
