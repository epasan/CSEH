# 3.1. Installation of KMS Server on Ubuntu Server 16.04
For this laboratory we will use a server virtualized Ubuntu 16.04 with VMware Workstation 14, next, we will describe the steps to follow to realize this laboratory from 0

## 3.1.1 Laboratory prerequisites
The following are all the tools that we will use in this Laboratory.

+ Virtualization
    - VMware Workstation 14 (used): You can download the trial version from [here](https://my.vmware.com/en/web/vmware/info/slug/desktop_end_user_computing/vmware_workstation_pro/14_0)
    - VirtualBox (Alternative): You can download the tool from [here](https://www.virtualbox.org/wiki/Downloads)
+ Operating Systems
    - Ubuntu Server 16.04: You can download it from [here](https://www.ubuntu.com/download/server)
+ Necessary Tools
    - SSH Tools
        - MobaXterm (used): It is one of the most useful tools for remote connections to Linux systems and you can download the Home version for free from [here](https://mobaxterm.mobatek.net/download.html)
        - Putty (Alternative): You can download it for free from [here](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
    - SFTP Tool
        - WinScp (Not necessary with MobaXterm): The best FTP and SFTP tool in my opinion you can download it for free from [here](https://winscp.net/eng/download.php)
        - FileZilla (Alternative): You can download it for free from [here](https://filezilla-project.org/download.php)
+ Decompression tools
    - 7zip: You can download it for free from [here](https://www.7-zip.org/download.html)
    - WinRAR: you can download it from [here](https://www.winrar.es/descargas/winrar)

## 3.1.2 Installing VMware Workstation 14
We proceed to the installation of the Virtualization tool VMware Workstation 14, for it we will follow the following steps:

1. Download the tool from the URL previously provided.
2. We give the following, next, next ... or if you want to watch the next video.

<a href="http://www.youtube.com/watch?feature=player_embedded&v=VxTO0xxN6-M" target="_blank"><img src="http://img.youtube.com/vi/VxTO0xxN6-M/0.jpg" alt="Clik aqui para ver en Youtube" width="940" height="580" border="10" /></a>

3. Restart the PC

> ** Note: ** You have to take into account at least two things:
> + VMware and HiperV windows can not work simultaneously, the video indicates how to disable it
> + Remember that you must enable Virtualization from the BIOS of you PC

## 3.1.3. Installing the OS in VMware

We proceed to the installation of Ubuntu Server 16.04 in a virtual machine with VMware Workstation 14 and for this we will follow the following steps:

1. Download the OS from the URL provided above.
2. The steps for installation are at the end of this post

## 3.1.4 Post-Installation Steps

After completing the installation of the operating system and logging in, we need to make some simple adjustments to the system.

+ Activate the root user: by default in the Ubuntu systems the root user is disabled by security to activate it, we will execute the following commands.

	```console
	sudo passwd root
	```
	
	This will ask us for the password of the user that we have created and then the password that we want from root.

	![alt text](/vlmcsd/binaries/Linux/images/passwdroot.JPG "passwd root")

+ We loging with root:

	```console
	su - root
	```

+ Activate SSH access to the machine

	```console
	apt-get install openssh-server -y
	service sshd start
	```

	![alt text](/vlmcsd/binaries/Linux/images/installSSHServer.JPG "install ssh-server")

	With this we have installed the ssh server to connect to the machine with mobaXterm or Putty, but we need to enable in the configuration so that it can be accessed with root, we will change the configuration of the file /etc/ssh/sshd_config where we will replace the line `PermitRootLogin prohibit-password` by `PermitRootLogin yes`

	```console
	vi /etc/ssh/sshd_config
	```

	![alt text](/vlmcsd/binaries/Linux/images/PermitRootLogin.JPG)

	The next thing will be to restart the sshd service so that it loads the configuration that we just modified.

    ```
    service sshd restart
    ```

	After performing these steps we can connect via SSH to the machine, using MobaXterm or Putty

	![alt text](/vlmcsd/binaries/Linux/images/ConectSSH.JPG)

+ Installing the git tool in Ubuntu

	With this tool you can download everything you need to install the KMS server, for this we will execute the following commands.

	```console
	apt-get install git -y
	```

	![alt text](/vlmcsd/binaries/Linux/images/gitInstall.JPG)

## 3.1.5 KMS server installation steps

After the post installation steps we can proceed to install the KMS server, for this we will follow the following steps always with the root user in Ubuntu:

+ Download the KMS server code from the GitHub server

	```console
	cd /opt/
	git clone https://github.com/rafaeljimenez85/kmsServer.git
	```

	![alt text](/vlmcsd/binaries/Linux/images/GitClone.JPG)

+ We make a copy of the executable `vlmcsd-x64-glibc` and then rename it to `vlmcsd`

	```console
	cd /opt/kmsServer/vlmcsd/binaries/Linux/intel/glibc
	cp vlmcsd-x64-glibc vlmcsd-x64-glibc.original
	mv vlmcsd-x64-glibc vlmcsd
	```

	![alt text](/vlmcsd/binaries/Linux/images/renameFiles.JPG)

+ We copy the KMS executable to the directory `/usr/local/sbin`

	```console
	cp /opt/kmsServer/vlmcsd/binaries/Linux/intel/glibc/vlmcsd /usr/local/sbin/
	```

+ We give execution permission to the executable `vlmcsd`

	```console
	cd /usr/local/sbin/
	chmod +x vlmcsd
	```

+ We verify that the correct functioning of the executable verifying its version.

	```console
	./vlmcsd -V
	``` 

	The result has to be the following:

	```txt
	root@kms:/usr/local/sbin# ./vlmcsd -V
	vlmcsd 1111, built 2017-06-17 00:53:13 UTC 64-bit
	Compiler: x86_64-linux-gcc 4.9.1
	Intended platform: Intel x86_64 Linux glibc little-endian
	Common flags:
	vlmcsd flags:
	```

	![alt text](/vlmcsd/binaries/Linux/images/moveKMS.JPG)

+ We create the file `/lib/systemd/system/vlmcsd.service` which is the configuration file for the automatic start of the daemon in service mode in case of stops, starts and restarts

	```console
	vi /lib/systemd/system/vlmcsd.service
	```

	The result has to be the following:

```txt
[Unit]
Description=Microsoft KMS emulator
After=network.target auditd.service

[Service]
ExecStart=/usr/local/sbin/vlmcsd -D
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure

[Install]
WantedBy=multi-user.target
Alias=vlmcsd.service
```

![alt text](/vlmcsd/binaries/Linux/images/vlmcsd-service.JPG)

+ Reload the data from the systemctl configuration files
	```console 
	systemctl daemon-reload
	```

+ Enable the new vlmcsd service 
	```console
	systemctl enable vlmcsd.service
	```

+ We started the new service vlmcsd
	```console
	systemctl start vlmcsd.service
	```

![alt text](/vlmcsd/binaries/Linux/images/startDaemon.JPG)

## 3.1.6. Status, stop and start commands

These commands are simply for later management if necessary

+ Start

	```console
	systemctl start vlmcsd.service
	```

+ Stop

	```console
	systemctl stop vlmcsd.service
	```

+ Status

	```console
	systemctl status vlmcsd.service
	```

+ Disable the service

	```console
	systemctl disable  vlmcsd.service
	```

## 3.1.7 Video tutorial (Ubuntu 16.04)

<a href="http://www.youtube.com/watch?feature=player_embedded&v=mv0gsA21P1o" target="_blank"><img src="http://img.youtube.com/vi/mv0gsA21P1o/0.jpg" alt="Clik aqui para ver en Youtube" width="940" height="580" border="10" /></a>

***

# 3.2. Installation of the KMS Server emulator on the router with OpenWrt
For this laboratory we will use a WT3020H router with an OpenWrt operating system, this roouter is very small and simple but at the same time with the installation of the S.O. OpenWrt becomes a very powerful and versatile tool as you can see in the GitHub of our colleague **Juan Espin (Pollonegro)** https: //github.com/pollonegro/Lan-Dropbox, to whom I thank him for his collaboration and patience.

## 3.2.1 Prerequisites of the Laboratory

The following are all the tools that we will use in this Laboratory.

+ Virtualization
    - VMware Workstation 14 (used): You can download the trial version from [here](https://my.vmware.com/en/web/vmware/info/slug/desktop_end_user_computing/vmware_workstation_pro/14_0)
    - VirtualBox (Alternative): You can download the tool from [here](https://www.virtualbox.org/wiki/Downloads)
+ Operating Systems
    - OpeWrt: You can download it from [here](http://archive.openwrt.org/chaos_calmer/15.05.1/ramips/mt7620/)
+ Device:
    - Router WT3020H [here](https://www.amazon.es/WT3020H-Portable-802-11n-Repeater-Wireless/dp/B0183OZESM)
    - 2G USB for installing KMS on the router
+ Necessary Tools
    - SSH Tools
        - MobaXterm (used): It is one of the most useful tools for remote connections to Linux systems and you can download the Home version for free from [here](https://mobaxterm.mobatek.net/download.html)
        - Putty (Alternative): You can download it for free from [here](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
    - SFTP Tool
        - WinScp (Not necessary with MobaXterm): The best FTP and SFTP tool in my opinion you can download it for free from [here](https://winscp.net/eng/download.php)
        - FileZilla (Alternative): You can download it for free from [here](https://filezilla-project.org/download.php)
+ Decompression tools
    - 7zip: You can download it for free from [here](https://www.7-zip.org/download.html)
    - WinRAR: you can download it from [here](https://www.winrar.es/descargas/winrar)

## 3.2.2 Installing VMware Workstation 14

You can find the steps [here](https://github.com/rafaeljimenez85/kmsServer/wiki/3.-Instalaci%C3%B3n-de-Servidor-KMS#312-instalaci%C3%B3n-de-vmware-workstation-14)

## 3.2.3 Steps Installing OpenWrt in WT3020H

You can find all the OpenWrt Installation steps [here](https://github.com/pollonegro/Lan-Dropbox/wiki/Walkthrough#1---install-openwrt)

> The default username and password of the router appear on the back of the router.

## 3.2.4 OpenWrt Post Installation Steps

Next, we will follow the next post installation steps to leave the router ready to install the KMS Emulator.

+ We will connect the USB of at least 2 GB in the Router

+ We will connect to the Router by SSH: `ssh root@192.168.8.1`
	![alt text](/vlmcsd/binaries/Linux/images/sshRouter.jpg)

+ We will execute the following commands:

	```console
	opkg update
	opkg install block-mount
	opkg install fdisk
	opkg install kmod-usb-storage
	opkg install kmod-fs-ext4
	opkg install e2fsprogs
	```

	These commands are to install the necessary tools to format the USB and to store data in it.

	![alt text](/vlmcsd/binaries/Linux/images/comandos%20opkg%201.jpg)

+ Check the disk status by executing the following command `fdisk -l`

	![alt text](/vlmcsd/binaries/Linux/images/fdisk-l-1.jpg)

	As you can see the USB (/dev/sda1) is in FAT32 format so you have to format it in EXT4

+ We will erase the partition and create a new one by executing the following commands:

	```console
	root@OpenWrt:~# fdisk /dev/sda1
	Command (m for help): d
	Selected partition 1
	Partition 1 has been deleted.

	Command (m for help): w
	The partition table has been altered.
	Calling ioctl() to re-read partition table.
	Syncing disks.

	Create the new partition, 
	root@OpenWrt:~# fdisk /dev/sda1

	Welcome to fdisk (util-linux 2.25.2).
	Changes will remain in memory only, until you decide to write them.
	Be careful before using the write command.

	Command (m for help): n
	Partition type
	 p primary (0 primary, 0 extended, 4 free)
	 e extended (container for logical partitions)
	Select (default p): p
	Partition number (1-4, default 1): 
	First sector (2048-61489151, default 2048): 
	Last sector, +sectors or +size{K,M,G,T,P} (2048-61489151, default 61489151):

	Created a new partition 1 of type 'Linux' and of size 29.3 GiB.

	Command (m for help): w
	The partition table has been altered.
	Calling ioctl() to re-read partition table.
	Syncing disks.
	```

+ We give EXT4 format to the disk with the following command `mkfs.ext4 /dev/sda1`

	![alt text](/vlmcsd/binaries/Linux/images/ext4.jpg)

+ We create the assembly point and assemble the previously created unit executing. 

	```console
	mkdir /mnt/sda1
	mount /dev/sda1 /mnt/sda1
	```

+ We copy the current FileSystem to the USB for it we execute the following ones.

	```console
	mkdir -p /tmp/cproot
	mount --bind / /tmp/cproot
	tar -C /tmp/cproot -cvf - . | tar -C /mnt/sda1 -xf -
	umount /tmp/cproot
	```

+ We configure the automatic mounting of the fileSystem after each restart by executing the following:

	```console
	vi /etc/config/fstab
	```

	The file must contain the following:

	```txt
	config 'global'
        option  anon_swap       '0'
        option  anon_mount      '0'
        option  auto_swap       '1'
        option  auto_mount      '1'
        option  delay_root      '5'
        option  check_fs        '0'

	config 'mount'
        option target /
        option device /dev/sda1
        option fstype ext4
        option options rw,sync
        option enabled 1
        option enabled_fsck 0
	```

	![alt text](/vlmcsd/binaries/Linux/images/fstab.jpg)

+ Restart the router to verify that it starts correctly with the command `reboot`

## 3.2.5 Installation of KMS Emulator in OpenWrt

Next we will install the KMS emulator in OpenWrt, for this we will perform the following steps:

+ We will download the packages `luci-app-vlmcsd_1.0.2-1_all.ipk` and `vlmcsd_svn1111-1_ramips_24kec.ipk` that kindly gives us [pollonegro](https://github.com/pollonegro/Lan-Dropbox), for that we will download to our pc from a browser packages from the following URLs
	- https://github.com/pollonegro/Lan-Dropbox/blob/master/firmware/OpenKMS/luci-app-vlmcsd_1.0.2-1_all.ipk
	- https://github.com/pollonegro/Lan-Dropbox/blob/master/firmware/OpenKMS/vlmcsd_svn1111-1_ramips_24kec.ipk

	![alt text](/vlmcsd/binaries/Linux/images/luci-app-vlmcsd_1.0.2-1_all.jpg)
	![alt text](/vlmcsd/binaries/Linux/images/vlmcsd_svn1111-1_ramips_24kec.jpg)

+ CWe create the folder vlmcsd on the router with the following command `mkdir /tmp/vlmcsd`

+ We copy the packages from our download folder to the media router the scp command

	```console
	scp ./luci-app-vlmcsd_1.0.2-1_all.ipk root@192.168.8.1:/tmp/vlmcsd
	scp ./vlmcsd_svn1111-1_ramips_24kec.ipk root@192.168.8.1:/tmp/vlmcsd
	```

	![alt text](/vlmcsd/binaries/Linux/images/scp_vlmcsd.jpg)

+ We install the packages:

	```console
	cd /tmp/vlmcsd
	opkg install vlmcsd_svn1111-1_ramips_24kec.ipk
	opkg install luci-app-vlmcsd_1.0.2-1_all.ipk
	opkg update
	``` 

	![alt text](/vlmcsd/binaries/Linux/images/instalacion-KMS-paquetes.jpg)

After performing all these steps the KMS emulator is already installed, to check it you can execute the following command `/usr/bin/vlmcsd -V` and you have to return the following:

```console
root@OpenWrt:/usr/sbin# /usr/bin/vlmcsd -V
vlmcsd private build 32-bit
Compiler: ccache_cc 4.8.3
Intended platform: MIPS mips32r2 Linux uclibc little-endian
Common flags:
vlmcsd flags:

```

## 3.2.6 Configuration files, binaries, scripts and PID

### 3.2.6.1 Configuration file:

The configuration files of the MMS in the router are in:

+ /etc/config/vlmcsd
+ /etc/vlmcsd.ini

### 3.2.6.2 Binaries

The KMS binaries are in:

+ /usr/bin/vlmcsd

### 3.2.6.3 Scripts

The restart, stop and start script are in:

+ /etc/init.d/vlmcsd
	- Restart --> **/etc/init.d/vlmcsd restart**
	- Stop --> **/etc/init.d/vlmcsd stop**
	- Start --> **/etc/init.d/vlmcsd start**

### 3.2.6.4 PID

The file with the PID with which the KMS is running can be found in:

+ /tmp/vlmcsd.pid
