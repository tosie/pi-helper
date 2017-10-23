# Setup a new computer

### Become root

The default password is "root".

```bash
su root
```

### Add color to pacman

```bash
sed -i 's/#Color/Color/' /etc/pacman.conf
```

### Update system
```bash
pacman -Syu
```

### Install vim, sudo, mosh, tmux, wget
```bash
pacman -S vim sudo mosh tmux wget
```

### Install rmate
```bash
wget -O /usr/local/bin/rmate https://raw.github.com/aurora/rmate/master/rmate
chmod a+x /usr/local/bin/rmate
```

### Edit sudoers to include the "wheel" group without password
```bash
visudo
```

### Create new user and add him to "wheel"
```bash
useradd -m -G wheel -s /bin/bash username
passwd username
```

### Update locale settings
```bash
sed -i "s/#en_US.UTF-8/en_US.UTF-8/" /etc/locale.gen
sed -i "s/#de_DE.UTF-8/de_DE.UTF-8/" /etc/locale.gen
locale-gen
localectl set-locale LANG=en_US.UTF-8
```

### Set the time zone

Use `timedatectl list-timezones` to list available time zones.

```bash
# timedatectl set-timezone Europe/Berlin
timedatectl set-timezone Europe/Paris
```

### Setup a swap file

```bash
fallocate -l 1024M /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo 'vm.swappiness=1' > /etc/sysctl.d/99-sysctl.conf
```

### Configure journald

```bash
sed -i "s/#Storage=auto/Storage=volatile/" /etc/systemd/journald.conf
sed -i "s/#Compress=yes/Compress=yes/" /etc/systemd/journald.conf
sed -i "s/#RuntimeMaxUse=/RuntimeMaxUse=40M/" /etc/systemd/journald.conf
```

### Update the hostname

```bash
wget -O /usr/local/bin/update-hostname https://raw.github.com/tosie/pi-helper/master/update-hostname
chmod a+x /usr/local/bin/update-hostname
update-hostname
```

### Keep the hostname up-to-date with every reboot

```bash
wget -O /etc/systemd/system/set-hostname.service https://raw.github.com/tosie/pi-helper/master/set-hostname.service
systemctl enable set-hostname.service
```

### Reboot

```bash
reboot now
```

### Reconnect via SSH using the new account

### Become root

```bash
sudo -s
```

### Lock the root user

```bash
passwd -l root
```

### Remove the alarm user

```bash
userdel -r alarm
```

### Check for config differences after updating the system

```bash
pacdiff
```
