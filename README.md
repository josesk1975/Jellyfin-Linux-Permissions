# Jellyfin-Linux-Permissions
LINUX - Ultimate and Definitive Guide for Jellyfin Permissions --- hopefully....

Hopefully this is a definitive guide for Linux and Jellyfin Server permissions.

Don't forget to check out [Jellyfin-Inhibit-Sleep](https://github.com/Kwakers01/Jellyfin-Inhibit-Sleep).

## Configure Jellyfin permissions : local and mounted drives and partitions

> [!IMPORTANT]  
> Jellyfin runs as a "SERVICE" on Linux.  
> It does not run as a normal user.  
> It has its own userID=jellyfin and groupID=jellyfin.  
> File permissions need to be set up for the jellyfin server to be able to see your media files and directories.

> [!NOTE]  
> I have a Jellyfin Server running as a user under Windows 10.  
> I need to have the media files available to my Linux Jellyfin Server.  
> I am dual booting and need the media files to be available in Windows and Linux in case I need to boot back into Windows 10.  
> Jellyfin runs as a service on Linux, the userID=jellyfin and groupID=jellyfin.  
> I am using the username "boss" as my primary username for managing the Linux Server and media files e.g copying media to directories.

Please read this link to understand the permission issues with Linux and Jellyfin.  
[https://forum.jellyfin.org/t-mounting-local-storage-in-linux-linux-permissions-primer](https://forum.jellyfin.org/t-mounting-local-storage-in-linux-linux-permissions-primer)


## Configuring Local Media

DO NOT use your home directory ~/ (/home/boss/) for Jellyfin media files.

If you have local media (e.g. single pc with single hard drive) then the below configuration is the best way to configure your jellyfin media files.

Let's configure a new directory just for Jellyfin media.

> [!IMPORTANT]
> You should change 'boss' in the examples below to your username.  

```
cd /
sudo mkdir -p /jellyfin-media/local/video
sudo mkdir -p /jellyfin-media/local/music
sudo chown -R boss:jellyfin /jellyfin-media
sudo chmod -R 750 /jellyfin-media
```
put your video files in /jellyfin-media/local/video  
put your music files in /jellyfin-media/local/music  

That's it....  
You should now be able to log into your jellyfin server http://127.0.0.1:8096 and use /jellyfin-media/local as the library directory.  

As user 'boss' you can add media to /jellyfin-media/local/video and /jellyfin-media/local/music.

**If you cannot see your media in the jellyfin library or the media will not play, you need to check the file permissions of the directories and media files. [Example of file permission issues](#example-of-file-permission-issues)**

## Media on Mounted Drives and Partition's

> [!NOTE]  
> The below example is a test system (one hard disk drive with 7 partitions).  
> One Windows 10 partition, one Linux Mint 22 Cinnamon partition -sda6 and one Linux Mint 22.1 xfce partition -sda7.  
> I am dual booting as I am testing moving from Windows 10 to Linux Mint.  
> I have a Jellyfin server running as a user under Windows. I need to have the media files available to Linux Mint and also Windows in case I need to boot back into Windows.  
> Jellyfin runs as a service on Linux and the userID and groupID are called jellyfin.  
> I am using the username "boss" as my primary username for managing the Jellyfin/Linux server.  


Using the information provided from the primer link:  
[https://forum.jellyfin.org/t-mounting-local-storage-in-linux-linux-permissions-primer](https://forum.jellyfin.org/t-mounting-local-storage-in-linux-linux-permissions-primer)

```
boss@boss-Lenovo-B570e-2:/$ sudo fdisk -l

Disk /dev/sda: 1.82 TiB, 2000398934016 bytes, 3907029168 sectors
Disk model: ST2000LM003 HN-M
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: C120F67B-26CD-11E5-B44B-A897BECAAE35

Device          Start        End    Sectors   Size Type
/dev/sda1        2048     534527     532480   260M EFI System
/dev/sda2      534528     567295      32768    16M Microsoft reserved
/dev/sda3      567296 2087932831 2087365536 995.3G Microsoft basic data
/dev/sda4  3886356480 3888130047    1773568   866M Windows recovery environment
/dev/sda5  3888130048 3907028991   18898944     9G Windows recovery environment
/dev/sda6  2087933952 2987145215  899211264 428.8G Linux filesystem
/dev/sda7  2987145216 3886356153  899210938 428.8G Linux filesystem

Partition table entries are not in disk order.
```
**I want to mount the Windows 10 partion on /dev/sda3**
```
boss@boss-Lenovo-B570e-2:/$ blkid /dev/sda3
/dev/sda3: LABEL="TIH0046700A" BLOCK_SIZE="512" UUID="42DED129DED115CF" TYPE="ntfs" PARTLABEL="Basic data partition" PARTUUID="e2c4442f-b77f-4a15-98ae-5742d2d926d8"
```
<u>Note the below:-</u>

**PARTUUID="e2c4442f-b77f-4a15-98ae-5742d2d926d8"**

**UUID="42DED129DED115CF"**
```
boss@boss-Lenovo-B570e-2:/$ lsblk -o PATH,FSTYPE,MOUNTPOINT /dev/sda3
PATH      FSTYPE MOUNTPOINT
/dev/sda3 ntfs   /media/boss/TIH0046700A
```
------------------------------------------------------------------------------------------------------------------------------------------------

### Configuring the /etc/fstab for the drive/partition

> [!IMPORTANT]
> In this example the user "boss" is my username (e.g. the person who will have access to add media to the server) change this for your username.

> [!NOTE]
> A note about mounting Windows drives and partitions.
> Linux will by default, mount a windows drive/partition with full rwxrwxrwx for all users.  
> To control the Linux access to the drive/partition you need to mount it to a directory that you have created and set your required permissions on.  

Create a directory for the partition to be mounted e.g. here I have used, hdd (hard disk drive) and m (In Windows 10, drive letter M: = my media server partition)
```
cd /
sudo mkdir /hdd
sudo mkdir /hdd/m
```
Change the permissions to only allow "boss" to have ownership of /hdd and /hdd/m. Otherwise all users will have read write access to /hdd and /hdd/m and thus full access to the mapped hard drive.
```
sudo chown boss:boss /hdd
sudo chown boss:boss /hdd/m
```
You could do (`sudo chown -R boss:boss /hdd`)

Example of /etc/fstab from (https://forum.jellyfin.org/t-mounting-local-storage-in-linux-linux-permissions-primer)  
e.g. UUID=63c1787f-e1a3-b34a-ae05-eb66f240e17d /media/storage2 ext4 defaults 0 0  
e.g. example for me is (which causes an error) UUID=e2c4442f-b77f-4a15-98ae-5742d2d926d8 /hdd/m ntfs-3g defaults 0 0 ... however see Note below:  

> [!NOTE]
> Because my system is a partition on the drive that is in on my laptop and not a separate drive we have to use the UUID and not the PARTUUID.
> If you use the PARTUUID you get an error when you try to mount it as a partition.

So I am using for my /etc/fstab file
```
sudo nano /etc/fstab
```
add (in my example) to the end of the file
```
UUID=42DED129DED115CF /hdd/m ntfs-3g defaults 0 0
```
Example of my /etc/fstab file with the added mount point.
```
 /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda7 during installation
UUID=5396630c-6144-4cfd-a7af-2af44496efa8 /               ext4    errors=remount-ro 0       1
# /boot/efi was on /dev/sda1 during installation
UUID=5ECF-1A09  /boot/efi       vfat    umask=0077      0       1
#boss added :  mount "windows M: drive" partition in /hdd with boss permission as per chown/chmod in notes
UUID=42DED129DED115CF /hdd/m ntfs-3g defaults 0 0
```

If you wish to test the mount without rebooting, you will need to unmount the above partition if it has auto mounted in a filemanger e.g. nemo or thunar.  
You can do this by right clicking the drive and clicking unmount.

You can then mount the drive to the new mount point in /etc/fstab to check it works
```
sudo systemctl daemon-reload
sudo mount -a
```
and see if you get any errors and correct them if you do.

you can unmount it again with
```
sudo umount /hdd/m
```

## Creating a Mount Point for Jellyfin

We are now going to create a mount just for jellyfin and give access to just the 'media' directory on the /hdd/m/ mounted drive.

> [!NOTE]
> Setting boss as the owner allows you to easily change directory etc. rather that having to add sudo to do a ls -al
> You could do root:jellyfin or jellyfin:jellyfin instead of boss:jellyfin but you would have to do sudo every time you want to look at the directories with ls.

If you have not made it already for local media files:-
```
cd /
sudo mkdir /jellyfin-media
sudo chown boss:jellyfin /jellyfin-media
sudo chmod 750 /jellyfin-media

```
Create directory for my Windows M: drive
```
cd /jellyfin-media
sudo mkdir ./m
sudo chown boss:jellyfin ./m
sudo chmod 750 ./m
```
>[!NOTE]  
> You could use the recursive option `sudo chown -R boss:jellyfin /jellyfin-media` and `sudo chmod -R 750 /jellyfin-media`, but to ensure that you do not set permissions on lots of files by mistake I would always do each of these separately.


check permissions
```
boss@boss-Lenovo-B570e-2:/jellyfin-media$ ls -al
total 12
drwxr-x---  3 boss jellyfin 4096 May  9 10:22 .
drwxr-xr-x 24 boss root     4096 May  9 10:21 ..
drwxr-x---  2 boss jellyfin 4096 May  9 10:22 m
boss@boss-Lenovo-B570e-2:/jellyfin-media$ 
```
To test you have the correct permissions for you jellyfin server....
```
sudo mount --bind /hdd/m/media /jellyfin-media/m 
```
You can then logon to your server http://127.0.0.1:8096 and try to add the library and play media.

If it is not working you can unmount it and correct the permissions 
```
sudo umount /jellyfin-media/m
```


When you are happy, you can make the change permanent e.g. after reboot by:

### Updating fstab
```
sudo nano /etc/fstab
```
add to the end of the file
```
/hdd/m/media    /jellyfin-media/m    none    bind
```
This is my /etc/fstab
```
 /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda7 during installation
UUID=5396630c-6144-4cfd-a7af-2af44496efa8 /               ext4    errors=remount-ro 0       1
# /boot/efi was on /dev/sda1 during installation
UUID=5ECF-1A09  /boot/efi       vfat    umask=0077      0       1
#boss added : mount "windows M: drive" partition in /hdd with boss permission as per chown/chmod in notes
UUID=42DED129DED115CF /hdd/m ntfs-3g defaults 0 0
#boss added : mount "window M:/media" to "linux /jellyfin-media/m" with jellyfin permissions as per chown/chmod in notes
/hdd/m/media    /jellyfin-media/m    none    bind
```
now you need to
```
systemctl reboot
```

You should now be able to login to jellyfin http://127.0.0.1:8096
and use /jellyfin-media/m as the library directory


**If you cannot see your media in the jellyfin library or the media will not play, you need to check the file permissions of the directories and media files.**

>[!NOTE]  
>You should not need to do this on a Windows NTFS drive as it will have been mounted with full rwxrwxrwx so once jellyfin is past /jellyfin-media/m/ it should have full access to the directory that has been mounted using -bind.

# Example of file permission issues

An example of an issue with the file permissions:-
```
boss@boss-Lenovo-B570e-2:/jellyfin-media/local/video/another test$ ls -al
total 9136
drwxrwxr-x 2 boss boss        4096 May  9 11:11  .
drwxr-x--- 3 boss jellyfin    4096 May  9 10:57  ..
-rw------- 1 boss boss       37929 May  7 20:08  record-open-C.mp4
-rw-r--r-- 1 boss boss     9303046 May  9 11:11 'THX Trailer.mp4'
```
Here you will find that the "THX Trailer.mp4" will play on jellyfin, but the "record-open-C.mp4" will not play and comes up with an error as the permissions only allow 'boss' to read the file and not userID=jellyfin.

You would need to change the permissions to:
```
chmod +r ./record-open-C.mp4
```
This allows any user (including jellyfin) to read the file.
```
boss@boss-Lenovo-B570e-2:/jellyfin-media/local/video/another test$ ls -al
total 9136
drwxrwxr-x 2 boss boss        4096 May  9 11:11  .
drwxr-x--- 3 boss jellyfin    4096 May  9 10:57  ..
-rw-r--r-- 1 boss boss       37929 May  7 20:08  record-open-C.mp4
-rw-r--r-- 1 boss boss     9303046 May  9 11:11 'THX Deep Note Trailer 2019 4K  Genesis720p.mp4'
```
"record-open-C.mp4" will now play on jellyfin.



------------------------------------------------------------------------------------------------------------------------------------------------





## Useful Links

[https://forum.jellyfin.org/t-mounting-local-storage-in-linux-linux-permissions-primer](https://forum.jellyfin.org/t-mounting-local-storage-in-linux-linux-permissions-primer)

[https://help.ubuntu.com/community/Fstab](https://help.ubuntu.com/community/Fstab)

[https://askubuntu.com/questions/205841/how-do-i-mount-a-folder-from-another-partition](https://askubuntu.com/questions/205841/how-do-i-mount-a-folder-from-another-partition)


