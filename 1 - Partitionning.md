## 1 - Partitionning

uefi-gpt layout (the order is random);
    
    boot part;	/boot			fat32		1gb
    RAM part;	[swap]			swap(w mkswap)	4-10gb
    home part; 	/home			ext4		user files
    root part;	/			ext4		systemfiles (packages, etc..)

### partition creation;

#### if you want a dualboot windows

_   install windows firstly
    _   let at least 40 Gb for arch
    _   select GPT partition scheme for UEFI systems for your windows files
    _   disable secure boot and boot into your arch linux live iso

_   create gpt disklabel part

    $ fdisk /dev/sda
    $ g
    $ n
    
_   part number default (ENTER)
_   first sector default (ENTER)
    
_   create a partition of 2 GiB

    $ +2G
    
_   repeat for the other partitions (change size)

### partition formating;

_   create a fat32 partition on sda1
	
    $ mkfs.fat -F 32 /dev/sda1

_   create a swap partition on sda2

	$ mkswap /dev/sda2

_   create a etx4 partition on sda3

	$ mkfs.ext4 /dev/sda3

_   etc..

	partition mounting;

_   thing to know
    _   always mount the / (root) partition first, then the other (required for genfstab)

_   create mountpoint / + mount /dev/sdaX on it
	
    $ mount /dev/sdaX /mnt

_   create the mountpoint file 'mnt/boot' + mount the boot partition on it

	$ mount --mkdir /dev/sdaX /mnt/boot

_   enable the swap volume with swapon

	$ swapon /dev/sdaX

_   create the mountpoint file 'mnt/home' + mount the boot partition on it

	$ mount --mkdir /dev/sdaX /mnt/home


