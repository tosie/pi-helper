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

### Install pacman-contrib, vim, sudo, mosh, tmux, wget, bash-completion
```bash
pacman -S pacman-contrib vim sudo mosh tmux wget bash-completion
```

### Install rmate
```bash
curl -L --output /usr/local/bin/rmate https://raw.github.com/aurora/rmate/master/rmate
chmod a+x /usr/local/bin/rmate
```

### Edit sudoers to include the "wheel" group without password
```bash
sed -i "s/# %wheel ALL=(ALL) NOPASSWD: ALL/%wheel ALL=(ALL) NOPASSWD: ALL/" /etc/sudoers
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
# timedatectl list-timezones
# timedatectl set-timezone Europe/Berlin
timedatectl set-timezone Europe/Paris
```

### Setup NTP time synchronization

```bash
timedatectl set-ntp false
sed -i "s/#NTP=/NTP=0.arch.pool.ntp.org 1.arch.pool.ntp.org 2.arch.pool.ntp.org 3.arch.pool.ntp.org/" /etc/systemd/timesyncd.conf
sed -i "s/#FallbackNTP/FallbackNTP/" /etc/systemd/timesyncd.conf
systemctl enable systemd-timesyncd.service
systemctl start systemd-timesyncd.service
timedatectl set-ntp true
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

### Enable systemd to make use of the hardware watchdog

```bash
sed -i "s/#RuntimeWatchdogSec=0/RuntimeWatchdogSec=10/" /etc/systemd/system.conf
```

### Update the hostname

```bash
curl -L --output /usr/local/bin/update-hostname https://raw.github.com/tosie/pi-helper/master/update-hostname
chmod a+x /usr/local/bin/update-hostname
update-hostname
```

### Keep the hostname up-to-date with every reboot

```bash
curl -L --output /etc/systemd/system/set-hostname.service https://raw.github.com/tosie/pi-helper/master/set-hostname.service
systemctl enable set-hostname.service
```

### Install Avahi and Advertise Certain Services

The service will be started after a reboot. The reboot seems to be necessary to initialize the d-bus daemon completely.

```bash
pacman -S avahi
cp /usr/share/doc/avahi/ssh.service /etc/avahi/services/
systemctl enable avahi-daemon
```

### Enable and Start Regular Paccache Runs

```bash
systemctl enable paccache.timer
systemctl start paccache.timer
```

### Disable IPv6

```bash
echo "# Do not assign IPv6 addresses to any network interface" >> /etc/sysctl.d/40-ipv6.conf
echo "net.ipv6.conf.all.disable_ipv6 = 1" >> /etc/sysctl.d/40-ipv6.conf
echo "net.ipv6.conf.eth0.disable_ipv6 = 1" >> /etc/sysctl.d/40-ipv6.conf
sed -i "s/::1	localhost.localdomain	localhost/#::1	localhost.localdomain	localhost/" /etc/hosts
```

### Reboot

```bash
reboot now
```

### Copy the public key file to the system

```bash
ssh-copy-id <hostname>
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

### SSHD config

```bash
sed -i "s/PermitRootLogin yes/PermitRootLogin no/" /etc/ssh/sshd_config
sed -i "s/#PasswordAuthentication yes/PasswordAuthentication no/" /etc/ssh/sshd_config
```

### Check for config differences after updating the system

```bash
pacdiff
```
