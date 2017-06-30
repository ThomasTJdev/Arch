# Arch


## Package is corrupted (invalid or corrupted package (PGP signature))
```
sudo pacman -Scc
sudo pacman-key --refresh-keys  
sudo pacman -Syu
```

## No WiFi
```
One of the reasons is, that your are a secure person, who wants the WIFI-PWD to be secret for other logged in users. If you're using NetworkManager, you have to edit the connection and allow "All users to use this network". This will remove the permission problem in ''/etc/NetworkManager/system-connections/FILENAME''
```

## Things not loading properly
```
# Do a dmesg and look through it
dmesg
```

## error: you need to load the kernel first
```
# mount /dev/sdxY /mnt   #Your root partition.
# mount /dev/sdxZ /mnt/boot   #Your boot partiton (if you have one).
# arch-chroot /mnt
# pacman -S linux
# grub-mkconfig -o /boot/grub/grub.cfg
```
