# Introduction

This repository contains helper files for setting up some computers.



# Quick Install

```bash
sudo wget -O /usr/local/bin/update-hostname https://raw.github.com/tosie/pi-helper/master/update-hostname
sudo chmod a+x /usr/local/bin/update-hostname

sudo wget -O /etc/systemd/system/set-hostname.service https://raw.github.com/tosie/pi-helper/master/set-hostname.service
sudo systemctl enable set-hostname.service
```
