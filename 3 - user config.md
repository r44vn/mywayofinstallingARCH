##3 - USER CONFIG##
	
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