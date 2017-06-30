# SSH Commands

## SSH
### Connect
```
ssh username@ip.ad.dre.ss
```

### Connect with SSH key
Find you folder with the ssh-key (*.pem):

```
ssh -i "Key-Name.pem" username@ip.ad.dre.ss
```

### Connect with SSH key to Amazon

```
ssh -i "AmazonKey.pem" username@url-to-aws.com
```


## SCP
### Send:
scp source_file_name username@destination_host:destination_folder

### Get information on sending by using verbose:

scp -v source_file_name username@destination_host:destination_folder

### Get estimated time and speed:

scp -p source_file_name username@destination_host:destination_folder

### Compress file (only on transfer) to increase speed:

scp -C source_file_name username@destination_host:destination_folder
