##1 - Partitionning##

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