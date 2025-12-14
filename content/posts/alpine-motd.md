---
title: "Alpine MOTD/Login Banner"
date: 2025-12-14T10:00:00-07:00
tags: ["alpine", "motd"]
draft: false
---

# MOTD/Login Banner
Example:

<img src="/MOTDLogin Banner_image.png" width="669" height="196">

```
# Start blue
echo -en "\033[1;34m" > /etc/motd
# Blue text
echo "Welcome to Alpine!" >> /etc/motd
echo "" >> /etc/motd
# Cyan text
echo -en "\033[1;36m" >> /etc/motd
echo "The Alpine Wiki contains a large amount of how-to guides and general" >> /etc/motd
echo "information about administrating Alpine systems." >> /etc/motd
echo "See <https://wiki.alpinelinux.org/>." >> /etc/motd
echo "" >> /etc/motd
# Yellow text
echo -en "\033[1;33m" >> /etc/motd
echo "You can setup the system with the command: setup-alpine" >> /etc/motd
echo "" >> /etc/motd
# Red text for your folder warning
echo -en "\033[1;31m" >> /etc/motd
echo "All files are stored here: cd /root/" >> /etc/motd
# Reset to default
echo -en "\033[0m" >> /etc/motd

```