## 4. Validar licencias de windows contra el servidor KMS

Tras la instalación de servidor KMS, lo único que nos queda es probar su funcionamiento a la hora de validar licencias de windows. Para ello instalaremos una máquina virtual de windows 10 Pro sin licencia, al finalizar esta instalación solos nos queda ejecutar 3 comando que explicaremos a continuación.

![alt text](/vlmcsd/binaries/Linux/images/winSinLicencia.JPG "Windows sin Activar")

## 4.1 Comandos de configuración y activación

+ **slmgr /ipk [setup key] :** Este comando le indica a Windows las licencias por volumen (que proporciona Microsoft) que debe usar para el servidor KMS.(para más detalle mira el apartado 5 [aqui](https://github.com/rafaeljimenez85/kmsServer/wiki/5.-Licencias-por-volumen-para-Servidores-KMS)).
+ **slmgr -skms [KMS IP] :** Con este comando le indicamos que windows el nuevo servidor KMS para validar la licencias
+ **slmgr -ato :** Con este comando realizamos el proceso de validación de la licencia contra el servidor KMS

A continuación, os indico los valores necesarios para validar una licencia windows 10 Pro

+ Abrimos un CMD con privilegios de administrador

	![alt text](/vlmcsd/binaries/Linux/images/cmdAdministrador.JPG "cmd con privilegios de administrador")

+ Ejecutamos los siguiente:

	```console
	slmgr /ipk NRG8B-VKK3Q-CXVCJ-9G2XF-6Q84J
	slmgr -skms 192.168.119.170
	slmgr -ato
	```

	![alt text](/vlmcsd/binaries/Linux/images/ipk.JPG "Comando ipk")

	![alt text](/vlmcsd/binaries/Linux/images/skms.JPG "Comando skms")

	![alt text](/vlmcsd/binaries/Linux/images/ato.JPG "Comando ato")

Una vez realizado esto, Ya tenemos nuestro windows Activado...

![alt text](/vlmcsd/binaries/Linux/images/winActivado.JPG "Windows Activado")

## 4.2 Video Tutorial

<a href="http://www.youtube.com/watch?feature=player_embedded&v=vf6KDX3eT0I" target="_blank"><img src="http://img.youtube.com/vi/vf6KDX3eT0I/0.jpg" alt="Clik aqui para ver en Youtube" width="940" height="580" border="10" /></a>