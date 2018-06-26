# 3.1. Instalación de Servidor KMS en Ubuntu Server 16.04
Para este laboratorio utilizaremos un servidor Ubuntu 16.04 virtualizado con VMware Workstation 14, a continuación, describiremos los pasos a seguir para realizar este laboratorio desde 0

## 3.1.1 Prerrequisitos del Laboratorio
Los siguientes son todas las herramientas que utilizaremos en este Laboratorio.

+ Virtualización
	- VMware Workstation 14 (usada): Podéis descargar la versión de prueba desde [aqui](https://my.vmware.com/en/web/vmware/info/slug/desktop_end_user_computing/vmware_workstation_pro/14_0)
	- VirtualBox (Alternativa): Podéis descarga la herramienta desde [aquí](https://www.virtualbox.org/wiki/Downloads)
+ Sistemas Operativos
	- Ubuntu Server 16.04: Podéis descargarlo desde [aquí](https://www.ubuntu.com/download/server)
+ Herramientas Necesarias
	- SSH Tools
		- MobaXterm (usada): Es una de las herramientas más útiles para conexiones remotas a sistemas Linux y podéis descargar gratis la versión Home desde [aquí](https://mobaxterm.mobatek.net/download.html)
		- Putty (Alternativa): Podéis descargarla gratis desde [aqui](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
	- SFTP Tool
		- WinScp (No necesaria con MobaXterm): La mejor herramienta de FTP y SFTP en mi opinión podéis descargarla gratis desde[aquí](https://winscp.net/eng/download.php)
		- FileZilla (Alternativa): Podéis descargarla gratis desde [aquí](https://filezilla-project.org/download.php)
+ Herramientas de descompresión
	- 7zip: Puedes descargarlo gratis desde [aquí](https://www.7-zip.org/download.html)
	- WinRAR: puedes descargarlo desde [aquí](https://www.winrar.es/descargas/winrar)

## 3.1.2 Instalación de VMware Workstation 14
Procedemos a la instalación de la herramienta de Virtualización VMware Workstation 14, para ello seguiremos los siguientes pasos:

1. Descargamos la herramienta desde la URL antes facilitada.
2. Le damos a siguiente, siguiente, siguiente... o si quieres mira el siguiente video.

<a href="http://www.youtube.com/watch?feature=player_embedded&v=VxTO0xxN6-M" target="_blank"><img src="http://img.youtube.com/vi/VxTO0xxN6-M/0.jpg" alt="Clik aqui para ver en Youtube" width="940" height="580" border="10" /></a>

3. Reinicia el PC

> **Nota:** Hay que tener en cuanta al menos dos cosas: 
> + VMware y HiperV de windows no pueden funcionar simultáneamente, en el video indica como deshabilitarlo
> + Recuerda que debes habilitar la Virtualización desde la BIOS de ti PC


## 3.1.3. Instalacion del SO en VMware

Procedemos a la instalación de Ubuntu Server 16.04 en una máquina virtual con VMware Workstation 14 y para ello seguiremos los siguientes pasos:

1. Descargamos el SO desde la URL facilitada anteriormente.
2. Los pasos para la instalación están al final de este post

## 3.1.4 Pasos Post-Instalación

Tras finalizar la instalación del sistema operativo y logarnos necesitamos realiza algunos ajustes simples al sistema.

+ Activar el usuario root: por defecto en los sistemas Ubuntu en usuario root viene deshabilitado por seguridad para activarlo ejecutaremos los siguientes comandos.

	```console
	sudo passwd root
	```
	
	Esto nos solicitara la contraseña del usuario que hemos creado y posteriormente la contraseña que deseamos de root.


![](/vlmcsd/binaries/Linux/images/passwdroot.JPG)

+ Nos logamos con root:

	```console
	su - root
	```

+ Activar Acceso SSH a la maquina

	```console
	apt-get install openssh-server -y
	service sshd start
	```

	![alt text](/vlmcsd/binaries/Linux/images/installSSHServer.JPG "install ssh-server")

	Con esto hemos instalado el servidor ssh para conectarnos a la maquina con mobaXterm o Putty, pero nos falta habilitar en la configuración para que se pueda acceder con root, cambiaremos la configuración del fichero /etc/ssh/sshd_config donde sustituiremos la línea `PermitRootLogin prohibit-password` por `PermitRootLogin yes`

	```console
	vi /etc/ssh/sshd_config
	```

	![alt text](/vlmcsd/binaries/Linux/images/PermitRootLogin.JPG)

	Lo siguiente sera reiniciar el servicio sshd para que cargue la configuración que acabamos de modificar.

    ```
    service sshd restart
    ```

	Tras realizar estos pasos ya nos podremos conectar mediante SSH a la máquina, utilizando MobaXterm o Putty

	![alt text](/vlmcsd/binaries/Linux/images/ConectSSH.JPG)

+ Instalación de la herramienta git en Ubuntu

	Con esta herramienta podréis descargar todo lo necesarios para instalar el servidor KMS, para ello ejecutaremos los siguientes comandos.

	```console
	apt-get install git -y
	```

	![alt text](/vlmcsd/binaries/Linux/images/gitInstall.JPG)

## 3.1.5 Pasos de instalación del servidor KMS

Tras los pasos de post instalación ya podemos proceder a instalar el servidor KMS, Para ello seguiremos los siguientes pasos siempre con el usuario root en Ubuntu:

+ Descargar el código de servidor KMS desde el servidor de GitHub

	```console
	cd /opt/
	git clone https://github.com/rafaeljimenez85/kmsServer.git
	```

	![alt text](/vlmcsd/binaries/Linux/images/GitClone.JPG)

+ Realizamos una copia del ejecutable `vlmcsd-x64-glibc` y posteriormente lo renombramos a `vlmcsd`

	```console
	cd /opt/kmsServer/vlmcsd/binaries/Linux/intel/glibc
	cp vlmcsd-x64-glibc vlmcsd-x64-glibc.original
	mv vlmcsd-x64-glibc vlmcsd
	```

	![alt text](/vlmcsd/binaries/Linux/images/renameFiles.JPG)

+ Copiamos el ejecutable de KMS al directorio `/usr/local/sbin`

	```console
	cp /opt/kmsServer/vlmcsd/binaries/Linux/intel/glibc/vlmcsd /usr/local/sbin/
	```

+ Damos permiso de ejecución al ejecutable `vlmcsd`

	```console
	cd /usr/local/sbin/
	chmod +x vlmcsd
	```

+ Verificamos que el correcto funcionamiento del ejecutable verificando la versión del mismo.

	```console
	./vlmcsd -V
	``` 

	El resultado tiene que ser el siguiente

	```txt
	root@kms:/usr/local/sbin# ./vlmcsd -V
	vlmcsd 1111, built 2017-06-17 00:53:13 UTC 64-bit
	Compiler: x86_64-linux-gcc 4.9.1
	Intended platform: Intel x86_64 Linux glibc little-endian
	Common flags:
	vlmcsd flags:
	```

	![alt text](/vlmcsd/binaries/Linux/images/moveKMS.JPG)

+ Creamos el fichero `/lib/systemd/system/vlmcsd.service` que es el fichero de configuración para el arranque automático del demonio en modo servicio en caso de paradas, arranques y reinicios

	```console
	vi /lib/systemd/system/vlmcsd.service
	```

	El fichero debe contener lo siguiente:

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

+ Recargar los datos de los ficheros de configuración de systemd

	```console 
	systemctl daemon-reload
	```

+ Habilitar el nuevo servicio vlmcsd 
	```console
	systemctl enable vlmcsd.service
	```

+ Arrancamos el nuevo servicio vlmcsd

	```console
	systemctl start vlmcsd.service
	```

![alt text](/vlmcsd/binaries/Linux/images/startDaemon.JPG)

## 3.1.6. Comandos de status, parada y arranque

Estos comandos son simplemente para la posterior gestión en caso de ser necesarios

+ Arranque

	```console
	systemctl start vlmcsd.service
	```

+ Parada

	```console
	systemctl stop vlmcsd.service
	```

+ Estado

	```console
	systemctl status vlmcsd.service
	```

+ Deshabilitar el servicio

	```console
	systemctl disable  vlmcsd.service
	```

## 3.1.7 Video tutorial (Ubuntu 16.04)

<a href="http://www.youtube.com/watch?feature=player_embedded&v=mv0gsA21P1o" target="_blank"><img src="http://img.youtube.com/vi/mv0gsA21P1o/0.jpg" alt="Clik aqui para ver en Youtube" width="940" height="580" border="10" /></a>

***

# 3.2. Instalación del emulador de Servidor KMS en router con OpenWrt
Para este laboratorio utilizaremos un router WT3020H con un sistema operativo OpenWrt, este roouter es muy pequeño y simple, pero a su vez con la instalación del S.O. OpenWrt se convierte en una herramienta muy potente y versátil tal como se pude ver en el GitHub de nuestro colega **Juan Espin (Pollonegro)** https://github.com/pollonegro/Lan-Dropbox, a quien le agradezco su colaboración y paciencia.


## 3.2.1 Prerrequisitos del Laboratorio

Los siguientes son todas las herramientas que utilizaremos en este Laboratorio.

+ Virtualización
	- VMware Workstation 14 (usada): Podéis descargar la versión de prueba desde [aqui](https://my.vmware.com/en/web/vmware/info/slug/desktop_end_user_computing/vmware_workstation_pro/14_0)
	- VirtualBox (Alternativa): Podéis descarga la herramienta desde [aquí](https://www.virtualbox.org/wiki/Downloads)
+ Sistemas Operativos
	- OpeWrt: Podéis descargarlo desde [aquí](http://archive.openwrt.org/chaos_calmer/15.05.1/ramips/mt7620/)
+ Dispositivo:
	- Router WT3020H [aquí](https://www.amazon.es/WT3020H-Portable-802-11n-Repeater-Wireless/dp/B0183OZESM)
	- USB de 2G para la instalación de KMS en el router
+ Herramientas Necesarias
	- SSH Tools
		- MobaXterm (usada): Es una de las herramientas más útiles para conexiones remotas a sistemas Linux y podéis descargar gratis la versión Home desde [aquí](https://mobaxterm.mobatek.net/download.html)
		- Putty (Alternativa): Podéis descargarla gratis desde [aqui](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
	- SFTP Tool
		- WinScp (No necesaria con MobaXterm): La mejor herramienta de FTP y SFTP en mi opinión podéis descargarla gratis desde[aquí](https://winscp.net/eng/download.php)
		- FileZilla (Alternativa): Podéis descargarla gratis desde [aquí](https://filezilla-project.org/download.php)
+ Herramientas de descompresión
	- 7zip: Puedes descargarlo gratis desde [aquí](https://www.7-zip.org/download.html)
	- WinRAR: puedes descargarlo desde [aquí](https://www.winrar.es/descargas/winrar)

## 3.2.2 Instalación de VMware Workstation 14

Podéis encontrar los pasos [aquí](https://github.com/rafaeljimenez85/kmsServer/wiki/3.-Instalaci%C3%B3n-de-Servidor-KMS#312-instalaci%C3%B3n-de-vmware-workstation-14)

## 3.2.3 Pasos Instalación de OpenWrt en WT3020H

Podéis encontrar todos los pasos de Instalación de OpenWrt [aquí](https://github.com/pollonegro/Lan-Dropbox/wiki/Walkthrough#1---install-openwrt)

> El usuario y contraseña por defecto del router aparecen en la parte trasera del mismo.

## 3.2.4 Pasos de Post Instalación de OpenWrt

A continuación, seguiremos los siguientes pasos de post instalación para dejar el router preparado para instalar el Emulador de KMS.

+ Conectaremos el USB de al menos 2 GB en el Router

+ Nos conectaremos al Router por SSH: `ssh root@192.168.8.1`
	![alt text](/vlmcsd/binaries/Linux/images/sshRouter.jpg)

+ Ejecutaremos los siguientes comandos:

	```console
	opkg update
	opkg install block-mount
	opkg install fdisk
	opkg install kmod-usb-storage
	opkg install kmod-fs-ext4
	opkg install e2fsprogs
	```

	Estos comandos son para instalar las herramientas necesarias para dar formato al USB y poder almacenar datos en el mismo.

	![alt text](/vlmcsd/binaries/Linux/images/comandos%20opkg%201.jpg)

+ Verificar el estado del disco ejecutando el siguiente comando `fdisk -l`

	![alt text](/vlmcsd/binaries/Linux/images/fdisk-l-1.jpg)

	Como podéis ver el USB (/dev/sda1) está en formato FAT32 por lo cual hay que formatearlo en EXT4

+ Borraremos la partición y crearemos una nueva ejecutando los siguientes comandos:

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

+ Damos formato EXT4 al disco con el siguiente comando `mkfs.ext4 /dev/sda1`

	![alt text](/vlmcsd/binaries/Linux/images/ext4.jpg)

+ Creamos punto de montaje y montamos la unidad antes creada ejecutando 

	```console
	mkdir /mnt/sda1
	mount /dev/sda1 /mnt/sda1
	```

+ Copiamos el FileSystem actual al USB para ello ejecutamos los siguiente.

	```console
	mkdir -p /tmp/cproot
	mount --bind / /tmp/cproot
	tar -C /tmp/cproot -cvf - . | tar -C /mnt/sda1 -xf -
	umount /tmp/cproot
	```

+ Configuramos el montaje automático del fileSystem despues de cada reinicio ejecutando los siguiente:

	```console
	vi /etc/config/fstab
	```

	El fichero debe contener lo siguiente:

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

+ Reiniciamos el router para verificar que arranque correctamente con el comando `reboot`

## 3.2.5 Instalación de Emulador de KMS en OpenWrt

A continuación instalaremos en OpenWrt el emulador de KMS, para ello realizaremos los siguientes pasos:

+ Descargaremos los paquetes `luci-app-vlmcsd_1.0.2-1_all.ipk` y `vlmcsd_svn1111-1_ramips_24kec.ipk` que amablemente nos facilita [pollonegro](https://github.com/pollonegro/Lan-Dropbox), para ello descargaremos a nuestro pc desde un navegador los paquetes desde las siguientes URLs
	- https://github.com/pollonegro/Lan-Dropbox/blob/master/firmware/OpenKMS/luci-app-vlmcsd_1.0.2-1_all.ipk
	- https://github.com/pollonegro/Lan-Dropbox/blob/master/firmware/OpenKMS/vlmcsd_svn1111-1_ramips_24kec.ipk

	![alt text](/vlmcsd/binaries/Linux/images/luci-app-vlmcsd_1.0.2-1_all.jpg)
	![alt text](/vlmcsd/binaries/Linux/images/vlmcsd_svn1111-1_ramips_24kec.jpg)

+ Creamos la carpeta vlmcsd en el router con el siguiente comando `mkdir /tmp/vlmcsd`

+ Copiamos los paquetes desde nuestra carpeta de descargas al router mediante scp

	```console
	scp ./luci-app-vlmcsd_1.0.2-1_all.ipk root@192.168.8.1:/tmp/vlmcsd
	scp ./vlmcsd_svn1111-1_ramips_24kec.ipk root@192.168.8.1:/tmp/vlmcsd
	```

	![alt text](/vlmcsd/binaries/Linux/images/scp_vlmcsd.jpg)

+ Instalamos los paquetes:

	```console
	cd /tmp/vlmcsd
	opkg install vlmcsd_svn1111-1_ramips_24kec.ipk
	opkg install luci-app-vlmcsd_1.0.2-1_all.ipk
	opkg update
	``` 

	![alt text](/vlmcsd/binaries/Linux/images/instalacion-KMS-paquetes.jpg)

Tras realizar todos estos pasos el emulador de KMS ya se encuentra instalado, para comprobarlo puedes ejecutar el siguiente comando `/usr/bin/vlmcsd -V` y tiene que devolverte lo siguiente:

```console
root@OpenWrt:/usr/sbin# /usr/bin/vlmcsd -V
vlmcsd private build 32-bit
Compiler: ccache_cc 4.8.3
Intended platform: MIPS mips32r2 Linux uclibc little-endian
Common flags:
vlmcsd flags:

```

## 3.2.6 Ficheros de configuración, binarios, scripts y PID

### 3.2.6.1 Fichero de configuración:

Los ficheros de configuración del KMS en el roouter se encuentran en:

+ /etc/config/vlmcsd
+ /etc/vlmcsd.ini

### 3.2.6.2 Binarios

Los binarios del KMS están en:

+ /usr/bin/vlmcsd

### 3.2.6.3 Scripts

El script de reinicio, parada y arranque se encuentran en:

+ /etc/init.d/vlmcsd
	- Reinicio --> **/etc/init.d/vlmcsd restart**
	- Parada --> **/etc/init.d/vlmcsd stop**
	- Arranque --> **/etc/init.d/vlmcsd start**

### 3.2.6.4 PID

el fichero con el PID con el que se esta ejecutando el KMS se encuentra en:

+ /tmp/vlmcsd.pid