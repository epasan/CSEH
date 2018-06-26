## 4. Validate windows licenses against the KMS server

After the installation of KMS server, all we have left is to test its operation when validating windows licenses. For this we will install a windows 10 Pro virtual machine without a license, at the end of this installation, we can only execute 3 commands, which we will explain below.

![alt text](/vlmcsd/binaries/Linux/images/winSinLicencia.JPG "Windows sin Activar")

## 4.1 Configuration and activation commands

+ **slmgr /ipk [setup key] :** This command tells Windows the volume licenses (provided by Microsoft) that you must use for the KMS server. (For more details, see section 5 [here](https://github.com/rafaeljimenez85/kmsServer/wiki/5.-Licencias-por-volumen-para-Servidores-KMS)).
+ **slmgr -skms [KMS IP] :** With this command we indicate that windows the new KMS server to validate the licenses.
+ **slmgr -ato :** With this command we perform the validation process of the license against the KMS server.

Next, I indicate the values necessary to validate a license windows 10 Pro

+ We open a CMD with administrator privileges

	![alt text](/vlmcsd/binaries/Linux/images/cmdAdministrador.JPG "cmd con privilegios de administrador")

+ We execute the following:

	```console
	slmgr /ipk NRG8B-VKK3Q-CXVCJ-9G2XF-6Q84J
	slmgr -skms 192.168.119.170
	slmgr -ato
	```

	![alt text](/vlmcsd/binaries/Linux/images/ipk.JPG "Comando ipk")

	![alt text](/vlmcsd/binaries/Linux/images/skms.JPG "Comando skms")

	![alt text](/vlmcsd/binaries/Linux/images/ato.JPG "Comando ato")

Once this is done, we have our windows activated ...

![alt text](/vlmcsd/binaries/Linux/images/winActivado.JPG "Windows Activado")

## 4.2 Video Tutorial

<a href="http://www.youtube.com/watch?feature=player_embedded&v=vf6KDX3eT0I" target="_blank"><img src="http://img.youtube.com/vi/vf6KDX3eT0I/0.jpg" alt="Clik aqui para ver en Youtube" width="940" height="580" border="10" /></a>