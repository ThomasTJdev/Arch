# Arch install

# Install Arch and setup system

Boot Arch and follow instructions.

## Download and burn arch

Get arch: [[https://www.archlinux.org/download/|https://www.archlinux.org/download/]]

Find USB: ''lsblk''

Burn iso: ''dd bs=4M if=/path/to/archlinux.iso of=/dev/sdx status=progress && sync''

## Boot arch

Plugin the USB and boot from it. If it doesn't boot, push F8, F12 (specific per PC) and choose the USB.

## Install

```
loadkeys dk

wifi-menu -o wlp2s0

lsblk

parted /dev/sdX

(parted) mkpart primary ext4 1MiB 100MiB
(parted) set 1 boot on
(parted) mkpart primary ext4 100MiB 100%
(parted) q

cryptsetup --verbose --cipher aes-xts-plain64 --key-size 512 --hash sha512 --iter-time 5000 --use-random luksFormat /dev/sdXY
cryptsetup open --type luks /dev/sdXY lvm

pvcreate /dev/mapper/lvm
vgcreate main /dev/mapper/lvm
lvcreate -L 1GB -n swap main
lvcreate -l 100%FREE -n root main

mkswap /dev/mapper/main-swap
mkfs.ext4 /dev/mapper/main-root

mount /dev/mapper/main-root /mnt
mkdir /mnt/boot
mount /dev/sdXY /mnt/boot
swapon </dev/mapper/main-swap>

pacstrap -i /mnt base base-devel

genfstab -U /mnt>> /mnt/etc/fstab

arch-chroot /mnt /bin/bash

locale-gen

nano /etc/locale.conf #Uncomment
LANG=en_DK.UTF-8

nano /etc/vconsole.conf
KEYMAP=dk

tzselect
ln -s /usr/share/zoneinfo/Zone/SubZone /etc/localtime #Zone/SubZone --> Europe/Copenhagen

hwclock --systoch --utc

nano /etc/default/grub
GRUB_CMDLINE_LINUX="cryptdevice=/dev/sdXY:main" #sdb2 on USB
## OOOBBBSSS
# /boot/grub/grub.cfg - this worked:
# linux   /vmlinuz-linux cryptdevice=UUID=sdXY:main root=/dev/mapper/main-root resume=/dev/mapper/main-swap quiet

nano /etc/mkinitcpio.conf
HOOKS="... encrypt lvm2 ... filesystems ..."

mkinitcpio -p linux

pacman -S grub os-prober

grub-install --recheck --target=i386-pc /dev/sdX

grub-mkconfig -o /boot/grub/grub.cfg

nano /etc/hostname
myhostname

pacman -S iw wpa_supplicant dialog

passwd

umount <mountpoint> # /mnt/boot & /mnt
swapoff </dev/mapper/main-swap>
sudo vgchange -a n  <volume_group_name> # main
sudo cryptsetup luksClose <label> #

reboot

</file>

## Post install

```pacman -Syu

nano /etc/pacman.conf
#Uncomment multilib (2 lines, both [description] and URL

pacman -Syy #update mirrors

pacman -S xfce4 thunar-archive-plugin xarchiver python3 python-pip
geany chromium keepass network-manager-applet xfce4-notifyd sudo git
wget unzip networkmanager networkmanager-openvpn gnome-keyring nmap
alsa-utils openssh ntfs-3g fail2ban archlinux-keyring

startxfce4

#Navigate to mousepad and enable

localectl set-keymap --no-convert dk

useradd -m -G wheel -s /bin/bash username

#####
#SUDO
EDITOR=nano visudo
# Reset environment by default
Defaults   env_reset
# Set default EDITOR to nano, and do not allow visudo to use EDITOR/VISUAL.
Defaults      editor=/usr/bin/nano, !env_editor
#Uncomment group wheel

####
#Yaourt
git clone https://aur.archlinux.org/package-query.git
cd package-query
makepkg -si
cd ..
git clone https://aur.archlinux.org/yaourt.git
cd yaourt
makepkg -si
cd ..

yaourt -S private-internet-access-vpn tigervnc-viewer numix-frost-themes hash-identifier sqlmap
# If numix frost key not accepted, try: gpg --keyserver http://pgp.mit.edu:11371 --recv-keys CDBD406AA1AA7A1D

####
#BlackArch
curl -O https://blackarch.org/strap.sh
sha1sum strap.sh #86eb4efb68918dbfdd1e22862a48fda20a8145ff
chmod +x strap.sh
sudo ./strap.sh

sudo pacman -S bettercap ettercap-gtk create_ap polkit macchanger set net-tools wireshark-gtk hoover mlocate
sudo gem update

####
#PAM
# Logout after 5 login attempt
sudo nano /etc/pam.d/system-login
    auth required pam_tally.so deny=2 unlock_time=600 onerr=succeed file=/var/log/faillog
    #auth required pam_tally.so onerr=succeed file=/var/log/faillog

####
#Setup network manager
# Can cause trouble!! Remember wifi-menu -o wlp2s0
systemctl start NetworkManager.service
systemctl enable NetworkManager.service

####
#Setup sounds - alsa-utils is installed before
alsamixer
#find <Master> - arrow up

####
#SSH
sudo nano /etc/ssh/sshd_config
#Add/change "#LogLevel INFO" -->
LogLevel VERBOSE
sudo systemctl start sshd
sudo systemctl enable sshd #for persistence

###
#Fail2ban
#Add roules for iptables:
sudo nano /etc/fail2ban/jail.d/ssh-iptables.conf
#Add:
[ssh-iptables]
enabled  = true
filter   = sshd
action   = iptables[name=SSH, port=ssh, protocol=tcp]
           sendmail-whois[name=SSH, dest=your@mail.org, sender=fail2ban@mail.com]
logpath  = /var/log/fail2ban.log
maxretry = 5
#Create empty logfile
nano /var/log/fail2ban.log
#Activate
sudo systemctl start fail2ban
sudo systemctl enable fail2ban #for persistence

#Install screensaver
sudo pacman -S xscreensaver
xscreensaver-demo
</file>

## Bonus or Problems

```####
#Xfce panel
#enable numix theme
#window-manager numix theme

####
#Macchanger on boot
#Remember to fit the device to your actual devices (ifconfig)
#For inspiration: https://perot.me/mac-spoofing-what-why-how-and-something-about-coffee // https://github.com/EtiennePerot/macchiato
yaourt -S macchiato-git
sudo cp /usr/share/macchiato/conf/sample.sh.example /etc/macchiato.d/wlp2s0.sh
sudo cp /usr/share/macchiato/conf/sample.sh.example /etc/macchiato.d/wlp0s20u2.sh
macchanger wlp2s0
macchanger wlp0s20u2
sudo /usr/share/macchiato/install-udev-rules.sh /etc/macchiato.d

####
#Mount windows
mkdir /mnt/win
mount -t ntfs-3g /dev/sda2 /mnt/win

#####
Internet
#No ethernet?
ifconfig
ip link set dev eno1 up
dhcpcd eno1

#####
Gain access

sudo cryptsetup luksOpen /dev/sdXY <label>
sudo vgchange -a y <volume_group_name>
mount /dev/mapper/<vg_name-lv_name>  <mountpoint>

umount <mountpoint> #Think about using "umount -R"
swapoff </dev/mapper/main-swap>
sudo vgchange -a n  <volume_group_name>
sudo cryptsetup luksClose <label>

#####
Grub problem

nano /etc/default/grub #Check device
nano /etc/mkinitcpio.conf #Check hooks
sudo mkinitcpio -p linux
sudo grub-mkconfig -o /boot/grub/grub.cfg
sudo grub-install --recheck --target=i386-pc /dev/sda

####
# Login problem?

Remember keyboard layout!
```

## Cron

```
sudo pacman -S cronie

sudo systemctl enable cronie

sudo systemctl start cronie
```

## Autostart X

```
#
# ~/.bash_profile
#

[[ -f ~/.bashrc ]] && . ~/.bashrc

if [ "$(tty)" = "/dev/tty1" -o "$(tty)" = "/dev/vc/1" ] ; then

    startxfce4
fi
```

## Links

```
####
Links

http://blog.philippbeck.net/linux/archlinux-install-encryption-lvm-luks-grub2-69
https://www.howtoforge.com/tutorial/how-to-install-arch-linux-with-full-disk-encryption/
http://teawithtux.blogspot.dk/2012/03/body-width-800px-padding-left-240px.html
http://unix.stackexchange.com/questions/214167/arch-linux-root-device-problem-in-dev-mapper-mystorage-rootvol
http://unix.stackexchange.com/questions/160504/lvm-ontop-of-luks-using-grub

https://wiki.archlinux.org/index.php/beginners'_guide
https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#LVM_on_LUKS
https://wiki.archlinux.org/index.php/Dm-crypt/Device_encryption#Encryption_options_for_LUKS_mode
https://wiki.archlinux.org/index.php/Dm-crypt/System_configuration#mkinitcpio
```

