# Arch install Raspberry Pi

## Install Arch on RPI and setup system=


### Archinstall raspberry pi - As sudo...

#### Use fdisk or gparted
fdisk /dev/sdX

Type o. This will clear out any partitions on the drive.
Type p to list partitions. There should be no partitions left.

##### For boot
Type n, then p for primary, 1 for the first partition on the drive, press ENTER to accept the default first sector, then type +100M for the last sector.
Type t, then c to set the first partition to type W95 FAT32 (LBA).

##### For swap
n, p, 2, [choose size. First sector OK, second +400M], t, [choose partition: 2], 82

##### For root / - remember to define size
Type n, then p for primary, 3 for the second partition on the drive, and then press ENTER twice to accept the default first and last sector (get separate /home, then define last sector).

##### For /home partion
n, p, 4, [Accept first and last sector]

Write the partition table and exit by typing w

mkfs.vfat /dev/sdX1
mkdir boot
mount /dev/sdX1 boot

mkfs.ext4 /dev/sdX3
mkdir root
mount /dev/sdX3 root

mkswap /dev/sdX2
cryptsetup -v luksFormat /dev/sdX4
cryptsetup open /dev/sdX4 crypthome
mkfs -t ext4 /dev/mapper/crypthome
cryptsetup close crypthome

wget http://archlinuxarm.org/os/ArchLinuxARM-rpi-2-latest.tar.gz
bsdtar -xpf ArchLinuxARM-rpi-2-latest.tar.gz -C root
sync

mv root/boot/* boot
nano boot/cmdline.txt
(Change correct reference from /dev/sdX2 to /dev/sdX3)

#### Umount
umount /dev/sdx1
umount /dev/sdx3

#### Connect the RPi to ethernet and power - connect through ssh
ssh alarm@xxxxxx
pwd: alarm

#### Set local keys - example for danish
localectl set-keymap --no-convert dk

#### Stuff
su root
root
passwd

#### Hostname
nano /etc/hostname

#### Get on wifi
wifi-menu -o

#### Update
pacman -Syu

#### Specific download
pacman -S fail2ban python3 sudo git wget unzip base-devel openvpn

#### Enable sudo
EDITOR=nano visudo
```
# Add following:
Defaults env_reset
Defaults editor=/usr/bin/nano, !env_editor
#Uncomment group wheel
```

#### Get Yaourt
git clone https://aur.archlinux.org/package-query.git
cd package-query
makepkg -si
cd ..
git clone https://aur.archlinux.org/yaourt.git
cd yaourt
makepkg -si
cd ..

#### Setup encryptio 
sudo nano /etc/cryptsetup
```
    home           /dev/mmcblk0p4             none                  noauto
```

sudo nano /etc/fstab
```
#Should be like this:
/dev/mmcblk0p1  /boot   vfat    defaults        0       0
/dev/mmcblk0p2  none    swap    defaults    0   0
/dev/mapper/home /home  ext4    defaults,noauto 0   0
```

sudo nano /etc/profile.d/mountCrypthome.sh
```
    if grep -qs '/home' /proc/mounts; then
        echo "/home is mounted."
    else
        echo "/home is not mounted"
        sudo cryptsetup luksOpen /dev/mmcblk0p4 home
        sudo mount /dev/mapper/home /home
        cd /home
        echo "/home is now mounted."
    fi
```
```
sudo chmod +x /etc/profile.d/mountCrypthome.sh
```

#### Pacman conf
sudo nano /etc/pacman.conf
```
# Remove # from color
```

### Add user
sudo su
useradd -G wheel -s /bin/bash user
passwd user

### Bettercap
sudo pacman -S libpcap
(Pacman is systemwide while gem is user-specific. if RPi has some trouble with pacman and bettercap then use gem. Ensure to do it as sudo cause bettercap need priviliges rights.)
sudo gem install bettercap
(Maybe needing ruby-bundler? and bundle update)

#### Language
sudo nano /etc/locale.gen
```
Uncomment relevant
```
sudo locale-gen

#### Software
sudo pacman -S tigervnc xfce4
sudo pacman -S xorg-server xorg-xkb-utils xorg-xauth xorg-server-utils xorg-xinit xf86-video-vesa xf86-input-mouse xf86-input-keyboard networkmanager networkmanager-openvpn

#### GUI
sudo nano /etc/X11/Xwrapper.config
allowed_users=anybody

#### SETUP VNC
nano startvnc.sh
```
#!/bin/bash
echo "Starting vnc"
nohup x11vnc -display :0 -configure 1200x700 -rfbauth ~/.x11vnc/passwd>& /dev/null &
```

chmod +x startvnc.sh

### Install screensaver
sudo pacman -S xscreensaver
xscreensaver-demo

### PAM (Logout after 5 login attempt)
sudo nano /etc/pam.d/system-login
```
    auth required pam_tally.so deny=2 unlock_time=600 onerr=succeed file=/var/log/faillog
    #auth required pam_tally.so onerr=succeed file=/var/log/faillog
```

### PIA
yaourt -S private-internet-access-vpn
sudo nano /etc/private-internet-access/login.conf
```
    USERNAME
    PASSWD
```
chmod 0600 /etc/private-internet-access/login.conf
chown root:root /etc/private-internet-access/login.conf
sudo pia -a

### GUI connect:
```
sudo systemctl enable NetworkManager
#sudo systemctl start NetworkManager
#sudo systemctl restart NetworkManager #if startet
#Settings, add auto connect VPN on boot
```

### OPENvpn without NetworkManager:
sudo nano /etc/openvpn/Denmark.conf
auth-user-pass /etc/private-internet-access/login.conf
sudo systemctl enable openvpn@Denmark.conf

### Macchanger
yaourt -S macchanger
nano changemac.sh
```
    #!/bin/bash

    if [[ $EUID -ne 0 ]]; then
       echo "This script must be run as root"
       exit 1
    fi

    echo "Changing mac"
    echo "Taking wlan0 down"

    ip link set wlan0 down
    macchanger -a wlan0
    ip link set wlan0 up

    echo "Setting wlan0 up"
    echo " "
    echo "Taking eth0 down"

    ip link set eth0 down
    macchanger -a eth0
    ip link set eth0 up

    echo "Setting eth0 up"
    echo " "
    echo "Run ifconfig to check"
```
chmod +x changemac.sh

### Searchengine
sudo pacman -S mlocate
sudo updatedb

### Clean up disk
sudo pacman -S baobab localepurge
localepurge #follow instructions

### SSH - secure (think about keys)
sudo nano /etc/ssh/sshd_config
PermitRootLogin no

### Cron
sudo pacman -S cronie
sudo systemctl enable cronie
sudo systemctl start cronie

### BlackArch
curl -O https://blackarch.org/strap.sh
sha1sum strap.sh #86eb4efb68918dbfdd1e22862a48fda20a8145ff - sha1 changes per version, check https://blackarch.org/downloads.html#install-repo
chmod +x strap.sh
sudo ./strap.sh

### Wifi problems
Yes you do have wifi trouble.. Add the network to networkmanager - and then make it ignore IPV6!!

''cd /etc/NetworkManager/system-connections/''

''sudo nano NETWORK-NAME''
```
#[ipv6]
#addr-gen-mode=stable-privacy
#dns-search=
#ip6-privacy=0
#method=auto
```
Reconnet via network-manager

