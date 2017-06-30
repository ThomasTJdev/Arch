# Arch encryption

## Crypt your home partition

### Create partition for home and then:

```
cryptsetup -v luksFormat /dev/sdX4
cryptsetup open /dev/sdX4 crypthome
mkfs -t ext4 /dev/mapper/crypthome
cryptsetup close crypthome
```

### Create cryptsetup and add line

```
sudo nano /etc/cryptsetup
home /dev/sdX4 none noauto
# Youll might want to use UUID instead
# sudo blkid
home UUID=XXXX none noauto
```

### Add it to fstab

```
sudo nano /etc/fstab
/dev/mapper/crypthome /home ext4 defaults,noauto 0 2
```

If you reboot now, you'll might have some trouble with X, since user home folder does not exist. Either mount and create folder in crypthome or press "Ctrl + Alt + F2" and create folder. Remember to change permissions: ''chown user:users folder''


## Crypt using EncFS

```
sudo pacman -S encfs
```

### Setup and decrypt:

''.name'' will hold the encrypted data while ''name'' will show the unencrypted data.

```
encfs ~/.name ~/name
```

### Encrypt:

```
fusermount -u ~/name
```

``` More info ```
Link: [[https://wiki.archlinux.org/index.php/EncFS|https://wiki.archlinux.org/index.php/EncFS]]


