##2 - Installation##

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
	//BONUS GRUB//
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

if you wanna delete all other options in the grub menu and only keep the one you created
you will go to that dir
   /etc/grub.d

and you will make thoses files not executable;
   $sudo chmod -x 10_* 20_* 30_* 

/***************************************/

install the main config file(you are still chrooted)
you will need to do that every time you touch the grub's files (its like a save)
	# grub-mkconfig -o /boot/grub/grub.cfg

double check if everything has been writted to the config file
	# grep "menuentry" /boot/grub/grub.cfg