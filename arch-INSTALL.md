	##1/ Partitionning##

uefi-gpt layout (the order is random);
boot part;	/boot			fat32		1gb
RAM part;	[swap]			swap(w mkswap)	4-10gb
home part; 	/home			ext4		user files
root part;	/			ext4		systemfiles (packages, etc..)

	partition creation;

##things to know; if you want a dualboot windows, putting the boot partition first is required

$ fdisk /dev/sda
$ g
create gpt disklabel part
$ n
part number default
first sector default
$ +2G
create a partition of 2 GiB
repeat for the other partitions (change size)

	partition formating;

create a fat32 partition on sda1
	# mkfs.fat -F 32 /dev/sda1
create a swap partition on sda2
	# mkswap /dev/sda2
create a etx4 partition on sda3
	# mkfs.ext4 /dev/sda3
etc..

	partition mounting;

##thing to know; always mount the / (root) partition first, then the other (required for genfstab)

create mountpoint / + mount /dev/sdaX on it
	# mount /dev/sdaX /mnt
create the mountpoint file 'mnt/boot' + mount the boot partition on it
	# mount --mkdir /dev/sdaX /mnt/boot
enable the swap volume with swapon
	# swapon /dev/sdaX
create the mountpoint file 'mnt/home' + mount the boot partition on it
	# mount --mkdir /dev/sdaX /mnt/home

	##2/ Installation##

	Select the mirror

Packages to be installed must be downloaded from mirror servers, which are defined in 
	/etc/pacman.d/mirrorlist
to modify the reflector in one cmd;
	# systemctl start reflector
and then;
	# pacman -Syyy

Installation of the Linux Kernel
	# pacstrap -K /mnt base linux linux-firmware

generation fstab to get the filesystem needed
	# genfstab -U /mnt >> /mnt/etc/fstab

verify that every partition that you have mounted as been seen by fstab, otherwise, do it all over again
	# cat /mnt/etc/fstab

then, chroot you into the sys to make changes directly in it
	# arch-chroot -S /mnt

set the time; ex:Paris (dont forget maj)
	# ln -sf /usr/share/zoneinfo/Europe/Paris /etc/localtime

Run hwclock to generate /etc/adjtime:
	# hwclock --systohc

To use the correct region and language specific formatting
	# locale-gen

then exit the chroot
	# exit

modify this file 
	# vim /etc/locale.conf

and type inside
	LANG=en_US.UTF-8

then modify this file 
	# vim /etc/vconsole.conf

and uncomment 
	KEYMAP=us

create your hostname by modifying this file 
	# vim /etc/hostname
	yourhostname

	##GRUB INSTALL##

dont be chrooted
	#exit

mount your partitions (lets say you have a /boot and a / partition)
	#mount /dev/XX /mnt
	#mount /dev/XX /mnt/boot
	#mount /dev/XX /mnt/home
	swapon /dev/XX
	

now, chroot
	#arch-chroot /mnt

install the packages required (grub and efibootmgr)
	#pacman -S grub 
	#pacman -S efibootmgr 

install the grub dir in /boot/grub
	# grub-install --target=x86_64-efi --efi-directory=[YourBootPartition(/boot)] --bootloader-id=GRUB

/***************************************/
	//BONUS//
if you wanna get a personnal setup on the grub menu;
modify this file by putting the script after the lines already there
	# vim /etc/grub.d/40_custom
here is an example;
/start

menuentry "myarch" {
    linux /vmlinuz-linux root=/dev/[ROOT PARTITION] rw
    initrd /initramfs-linux.img
}
menuentry "reboot" {
    reboot
}
menuentry "shutdown" {
   halt
}
menuentry "bios" {
    fwsetup
}


end/


/***************************************/

install the main config file(you are still chrooted)
	# grub-mkconfig -o /boot/grub/grub.cfg

double check if everything has been writted to the config file
	# grep "menuentry" /boot/grub/grub.cfg

	##USER CONFIG##
	
put yourself on sudo mode (be carefull with that :3)
	#su

generate X
	# pacman -S xorg-server xorg-init

	//network setup;

install and enable the right paquets;
	# pacman -S networkmanager
	# systemctl enable NetworkManager

to have a gui to set and manage the wifi, use
	# nmtui

	//uninstall a paquet (clean version);

check the paquets and what do they need to properly function
	# pacman -Qs [paq]

uninstall all of thoses paquets
	# pacman -Rns [paq]

re-check the paquets and what do they need to properly function (are they gone ?)
	# pacman -Qs [paq]

	//setup user and superuser

install sudo 
	#pacman -S sudo

add a user
	# sudo usermod -aG wheel [username]

set the "wheel" group on sudo (special rights)
	#EDITOR=vim visudo

uncomment this line and save
	%wheel ALL=(ALL:ALL) ALL

reboot the sys
	#reboot

now you can use the sudo cmd instead of su yourself all the time (dangerous, dont do that)


