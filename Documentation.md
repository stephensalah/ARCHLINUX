## Welcome to ARCH LINUX Install Documentation Page

## All of my commands that I entered into the terminal will be in commented with the ## and in parenthesis for clarification.

I started by first downloading the ARCH LINUX ISO from the download page via HTTP Direct Downloads. 
I found my country, United States and chose the website, archlinux-br.org. 
I then verified that the checksums matched up which they did, verifying the signature, but at the time did not know for sure if this was the verification.

Next, I was using the slides to make sure I did not miss a step with the VMWARE setup, but I could not figure out how to edit the VMWARE to make the VM have 2GB of RAM and 20 GB HDD space, so I hope having VM Fusion that it automatically does this, but I do not know…

I looked up how to do both or how to check if my VM has this. I opened the VM settings and it is already defaulted at 20 GB of Hard Disk and 4GB of RAM which is fine.

Now, I tried to verify the signature by typing in the verify signature command

## ( gpg --keyserver-options auto-key-retrieve --verify archlinux-version-x86_64.iso.sig )

but it said it was not found, so I have to try to resolve this problem before continuing.
Since, I already validated the signature by checking the checksums, I believe it is fine, so I will continue, but if a problem arises, I will come back to this.

I then, booted up the live environment and chose to enter into UEFI mode.
The default keyboard type is US, so I did not have to change anything there. 
I verified that I was in UEFI mode by typing in 
## ( ls /sys/firmware/efi/efivars )
and ensured no errors were met.

Next, I have to connect to the internet by using commands in iwctl. I inputted the command
## ( Iwctl )
This puts you in an interactive command mode called iwd.
I looked up my devices using the command
## ( device list )
 but it appears that there are no devices.
When I used the command
## ( ip link )
 it says that I have a network called ens33 which means it is a wired/ethernet connection, which is weird, but I will continue with that. I believe it might be because it is a VMWARE that it thinks it is wired. I have been going through a bunch of pages of wiki’s trying to solve this problem for a couple hours now to no avail. I eventually used the command 
## ( lspci -k )
 to show that it is an ethernet connection using the VM. I now have internet connection. I tested this by using 
## ( ping archlinux.org )
 with 0 packet loss. 
Next, I tried to set the time using 
## ( timedatectl status )
which first gave me the time which was wrong. So, I used 
## ( timedatectl set-timezone US/Central )
 which gave me the correct time.
I partitioned the disks in the manner that was described in the slides by first using 
## ( fdisk /dev/sda )
 to open up the commands. I created the first partition by using the command 
## ( n )
Then, it prompted me to select a partition [1-4], which I chose 1 by typing
## ( 1 )
Next, I created the EFI System by writing 
## ( EFI System )
 into the [type] portion of the partition. 
The first sector was default, so I just pressed ENTER and the last sector I wrote 
## ( +500M )
 to make it 500 Megabytes long. 
The second partition I created using
## ( n )
was the rest of the drive, chose partition 2 using 
## ( 2 )
and inputting both default inputs after selecting
## ( p ) 
as primary by pressing ENTER twice.
 I then saved it with 
 ## ( w )
and then moved on to formatting the partitions by using 
## ( mkfs.ext4 /dev/sda2 )
 because the second partition is what we use for ARCH OS. 
Next, I mounted the file systems by using 
## ( mount /dev/sda2 /mnt )

Then, I began to try to select my mirrors from the mirror list which took a lot of time to do. 
I first made an edit of the mirrorlist because I found a guide online on how to do this. I used the mirrorlist generator to get the mirror list and edited the # out of the server lines using a text editor to get the correct text file and then saved it. 
I found out that this is unneccesary by reading further. (Stupid Mistake)
I started by using 
## ( curl -o ~/mirrorlist )
to download a mirrorlist file with all the mirrors. I then used, 
## ( sed -I ‘s/#S/S/g’ ~/mirrorlist )
 After doing all of this and trying out a bunch of ways to organize the mirrorlist for a couple of hours, I realized you can do all of this using 
## ( reflector )
automatically to overwite the mirrorlist file. I ran the 
## ( pacstrap /mnt base linux linux-firmware )
 command to download the essential packages which took a while. 
I initially did not realize I had to download other packages at this point, but I will come back to this.
I ran 
## ( genfstab -U /mnt >> /mnt/etc/fstab )
 as well as 
## ( arch-chroot /mnt )
I was rushing through these parts because I was tired, so I backed up and researched what exactly was going on in these parts to ensure I did them correctly.
At this point, I was prettu confused on what was going on, so I spoke to Codi after class on Thursday where he explained a bunch to me about the ARCH Install.
I learned that I have to download the other packages before continuing on this step to configure the system correctly, so I used the pacman package manager to install a text editor, man pages and info. I used the command
## ( pacman -S nano )
to install the nano text editor and the command 
## ( y )
to confirm the install. Next, I installed the man pages using
## ( pacman -S man-db )
and used the command
## ( y )
to confirm the installation.
After this, I continued through the installation wiki by changing the localtime to the current time by using the correct region and city which was America and Chicago respectively.
## ( ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime )
Then, I used
## ( hwclock --systohc )
to generate /etc/adjtime.
I have to edit /etc/local.gen by uncommenting en_US.UTF-8 UTF-8 by using the command
## ( nano /etc/locale.gen )
Then, I went into the text editor found #en_US.UTF-8 UTF-8 and deleted the #, saved and exitted the text editor. Next, I added a line to /etc/locale.conf by using the command
## ( nano /etc/locale.conf )
and added the line LANG=en_US.UTF-8 and saved and exitted the editor.
Since I did not edit the keyboard, I do not have to add anything to the /etc/vconsole.conf file.
Now, I created the hostname file and editted the first line to myhostname.
## (nano /etc/hostname )
I typed in myhostname and saved and exitted. Next, I added entries to /etc/hosts using
## (nano /etc/hosts )
and added 
127.0.0.1       hostname
::1             hostname
127.0.1.1       myhostname
and saved and exitted.
Then, I began to check to see if my network configuration was all done correctly. First, I typed in the command
## ( ip link )
to ensure I had a connection which I did called ens33. I ensured I had an IP address by typing in
## ( ip address show )
I realized that I need to download a DHCP package and have to check if this works, so I typed in the command
## ( pacman -S dhcpcd )
I was nervous to play with this, so I decided to wait to do this until a problem came up. I moved on to set the root passord by typing the command
## ( passwd )
which I made the password Tulsa120! and then retyped it to confirm.
Now, I have to choose and install a boot loader and since I have an intel CPU, I must also enable microcode updates. I looked around the wiki for boot loader where I found a note saying "Loading Microcode updates requires adjustments in boot loader configuration.", so I must figure out exactly what this means and how to do it. I also remember Sal briefly talking about this to me because he did the ARCH Install with a mac as well. Reading further, I found a wiki page saying "Microcode on Intel CPUs is no longer loaded automatically, as it needs to be loaded very early in the boot process. This requires adjustments in the bootloader. If you have an Intel CPU, please follow the instructions in the wiki."
In the microcode wiki, I chose to use the early-install process which started with downloading intel-ucode because I have an intel processor.
## ( pacman -S intel-ucode )
Reading further in the microcode wiki, it gets pretty confusing, but it states that the updates must be enabled by adding /boot/intel-ucode.img as the first initrd in the bootloader config file.
The wiki continues to explain how to perform these updates, but says "In the following sections replace cpu_manufacturer with your CPU manufacturer, i.e. amd or intel.", before you start, so I will do this.
In. the "configuration" section of the guide, it said something about custom kernels, which I had no clue about, so I looked into the /boot/intel-ucode.img file.
## ( nano /boot/intel-ucode.img )
This showed me absolutely nothing because it is a bunch of random letters numbers and symbols, so I searched through the text file for CONFIG because in the wiki, it says to change something in this which I could not find. I moved on from this because I believe this is extar and for something else, but if there is an error, I will come back to this.
I will be using the GRUB bootoader, so I first attempted to type
## ( grub-mkconfig -o /boot/grub/grub.cfg )
to no avail because I did not download it yet, so I opened the grub wiki and looked how to install it. It says to install grub as well as efibootmgr because "GRUB is the bootloader while efibootmgr is used by the GRUB installation script to write boot entries to NVRAM.", so I did exactly that.
## ( pacman -S grub )
## ( pacman -S efibootmgr )
I am following the steps which say to mount the EFI system partition to the /efi file which I tried.
## ( mount /dev/sda1 /efi )
which did not work. This was attempting to mount the EFI system partition (/dev/sda1) to /efi. I think I need to do /boot/efi.
## ( mount /dev/sda1 /boot/efi )
This also did not work, so I tried the second option for mount pounts which says to mount the EFI system partition to /boot.
## ( mount /dev/sda1 /boot )
This gave me an error saying system call failed: Cannot allocate memory.
After failing this, I read further in the wiki where it says to perform the mount for /efi, you need to first create the directory, so I shall do this first.
## ( mkdir /efi )
which created the /efi directory, so I can continue to mount.
## ( mount /dev/sda1 /efi )
This gave me the error EXT-4 (sda1): unable to read superblock.
I researched why this was and after a while, I realized that my EFI partition was not an EFI partition, but an extended partition, so I went into /dev/sda using fdisk.
## ( fdisk /dev/sda )
and changed the partition type from extended to EFI system partition.
## ( t )
Chose the partition number 1 for sda1 which is the EFI.
## ( 1 )
and lastly changed the type to 
## ( ef )
and saved and exitted fdisk.
## ( w )
I retried to mount using 
## ( mount /dev/sda1 /efi )
which gave me the same error.
This made things more complicated...
I read further and found "GRUB requires a BIOS boot partition with type BIOS boot when installing GRUB to a GPT partitioned disk.", so I must do this as well which is slowly spiraling me down a dangerous path, but I shall succeed.
I read even further, seeing that this is unneccesary, but I also read in the EFI partition wiki that I made a mistake whilst making the EFI partition. I did not format the EFI partition correctly, so I will attempt to fix this, but if I meed to I can retstart this whole project from scratch...
In order to format the EFI system partition correctly, I first have to install dosfstools using pacman.
## ( pacman -S dosfstools )
After installing, I attempted to format using
## (mkfs.fat -F32 /dev/sda1 )
which gave me the error "Too few blocks for viable filesystem".
After looking up this problem, I found that I need to use gparted to write the partition table to the device.
## ( pacman -S gparted )
After installing, I tried to run
## ( gparted /dev/sda )
which gave me a fat error, so I think I have to leave chroot before I do this, so I left using
## ( exit )
and then retyped
## ( gparted /dev/sda )
which claims that this is not installed, probably because I was in the root, so I reinstalled it.
## ( pacman -Sy gparted )
I got yet another error, so at this point, I think I am going to restart the project from scratch and do everything right because I am so lost on how to fix this problem...

I made a new file and retraced my steps, doing everything the same besides my mistakes which is what I will be talking about from here on out, until I catch up to my previous spot.
Once, I caught up to the partitioning section, I made sure to ensure there were no errors in what I did.
First, I entered into .dev/sda to create the correct partitions, starting with the EFI system partition.
## ( fdisk /dev/sda )
Then, I created a new partition,
## ( n )
Chose primary for partition type.
## ( p )
Chose partition 1,
## ( 1 )
Chose default for first sector and +500M for last sector making the partition 500Mb.
## ( Enter )
## ( +500M )
I then, cahnged the partition type to EFI system partition.
## ( t )
## ( EF )
Next, I used the same concept to make the second partitionwhich will be for the arch OS.
## ( n ) 
Creates new partition
## ( p )
Sets the type to primary
## ( 2 )
Sets the partition to 2.
## ( ENTER )
## ( ENTER )
Setting the second partition to become the rest of the space.
## ( w )
which saves and exits fdisk.
Now, I have to download dosftools to format the partition before continuing.
## ( pacman -S dosftools )
Then, I formatted the partition as a FAT32 by using the command
## ( mkfs.fat -F32 /dev/sda1 )
Next, I made the directory /efi
## ( mkdir /efi )
and proceeded to mount the ESP to /efi.
## ( mount /dev/sda1 /efi )
After I mounted the partition I moved on to the second partition where I created an ext4 file system on /dev/sda2.
## ( mkfs.ext4 /dev/sda2 )
I quickly realized that I have to mount the EFI partition to /mnt/efi, not /efi, so I unmounted the /dev/sda1 partition, delete the /efi directory, create a new directory /mnt/efi and remount it correctly.
## ( umount /dev/sda1 )
## ( rmdir /efi )
## ( mkdir /mnt/efi )
## ( mount /dev/sda1 /mnt/efi )
Then, I mounted /dev/sda2 to /mnt per usual.
## ( mount /dev/sda2 /mnt )
Next, I went through what I previously did, making sure to not make mistakes.
I ran reflector, which organizes the mirror list correctly.
## ( reflector )
Then, I ran pacstrap to install the base package, Linux kernel and firmware for common hardware.
## ( pacstrap /mnt base linux linux-firmware )
Now, I installed all other packages that I needed before entering into chroot such as man pages, nano, and text info.
## ( pacman -S man-db )
## ( pacman -S man-pages )
## ( pacman -S nano )
## ( pacman -S texinfo )
After reading slightly further, it claims, I have to be in chroot in order for these installations to work, so I will redownload these once I enter chroot.
For now, I will continue through the installation steps.
I generated an fstab file using
## ( genfstab -U /mnt >> /mnt/etc/fstab )
Now, I change rooted into the new system using
## ( arch-chroot /mnt )
Then, I redinstalled all of the packages I needed.
## ( pacman -S man-db )
## ( pacman -S man-pages )
## ( pacman -S nano )
## ( pacman -S texinfo )
There was an error performing the first command which said something about localizing, so I will fix this before continuing.
I set the time zone by using
## ( ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime )
and then generated /etc/adjtime using
## ( hxclock --systohc )
Then, I redinstalled all of the packages I needed.
## ( pacman -S man-db )
## ( pacman -S man-pages )
## ( pacman -S nano )
## ( pacman -S texinfo )
which worked this time.
I generated the locales by using
## ( locale-gen )
I created the locale.conf file and added the line LANG=en_US.UTF-8 to it.
## ( nano /etc/locale.conf )
I typed in LANG=en_US.UTF-8 into the text editor, saved and exitted.
Next, I created the host name file.
## ( nano /etc/hostname )
Then, I added the matching host entries into /etc/hosts.
## ( nano /etc/hosts )
I added the lines 
127.0.0.1	localhost
::1		    localhost
127.0.1.1	myhostname
Next, I set the root password by using
## ( passwd )
and making it Tulsa120!
Now for the hard part, the microcode part...
First, I downloaded intel-ucode once again.
## ( pacman -S intel-ucode )
Then, I moved on to configuration of microcode.
I have to use GRUB to configure and update microcode, so I will atempt to instal lthis again.
I first installed grub as well as efibootmgr.
## ( pacman -S grub )
## ( pacman -S efibootmgr )
Since, I already mounted my EFI partition to /mnt/efi, I can skip the first step of mounting.
I tried to perform the grub efi installation with no avail.
## ( grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=GRUB )
Where I got the error that it could not find the path, so I believe I have to remount in chroot. 
I remade the directory and also mounted the EFI to it.
## ( mkdir /efi )
## ( mount /dev/sda1 /efi )
Now, I continued to try the install where it worked fine.
## ( grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=GRUB )
Then, I finally could configure and update microcode using GRUB.
## ( grub-mkconfig -o /boot/grub/grub.cfg )
After that finished, I believe I have successfully installed the boot-loader.
I exitted the chroot by typing
## ( exit )
Then, I rebooted which gave me a screen asking me to login using myhostname.
I typed into the login: 
## ( root )
and typed in my password and was logged in successfully.
