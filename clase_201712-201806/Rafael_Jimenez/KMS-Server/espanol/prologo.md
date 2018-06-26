# Prologo

Este trabajo nace de la necesidad y curiosidad de aprender el uso y funcionamiento de un servidor KMS con el cual se podrán activar y validar licencias de los diferentes sistemas operativos Windows a través de la red interna.

Por este motivo he documentado un paso a paso del montaje de un laboratorio con la instalación de un servidor KMS en una maquina virtual **Ubuntu 16.07** o también en un **router \(Lan-Dropbox\)** con sistema operativo **OPEN-WRT** que permitirá realizar las activaciones de las licencias de windows con los datos y pasos proporcionados por Microsoft a través de su página web.

**¿Por qué un servidor KMS?** Como ya he comentado anteriormente el que el servidor KMS nos permita validar las licencias de los sistemas operativos windows mediante nuestra red interna es una gran ventaja ya que nos permite validar las licencias sin necesidad de salir a internet y validar dichas licencias en los servidores de Microsoft evitando así un gran trafico de red a la hora de validar una gran cantidad de licencias. Por lo cual también en un caso de caída de internet nos permitirá validar las licencias sin problema.

Aunque no se tocara en este trabajo, la implementación de un servidor KMS con las políticas de grupo del **Active Directory \(AD\)** o de un **Windows Server Update Service \(WSUS\)** le agregaría una versatilidad y potencia extra a nuestro entorno ya que las validaciones de las licencias se realizarían de manera automática.

> **Nota importante:** Este trabajo está diseñado solo para uso educacional y jamás debe de ser usado para validar licencias fuera de un entorno de laboratorio ya que se estaría cometiendo una violación de los términos y condiciones de Microsoft al no tratarse de un servidor KMS legítimo. Por este motivo no me ahogo responsable de uso inadecuado que se pueda realizar con este documento.

Para la instalación de Servidores KMS Server legítimos podéis seguir la información de los siguientes enlaces:

* [http://ivan.dretvic.com/2011/06/how-to-configure-a-kms-server-in-windows-server-2008-r2/](http://ivan.dretvic.com/2011/06/how-to-configure-a-kms-server-in-windows-server-2008-r2/)
* [https://www.windows-noob.com/forums/topic/12787-how-can-i-setup-kms-key-management-server-for-activating-windows-10/](https://www.windows-noob.com/forums/topic/12787-how-can-i-setup-kms-key-management-server-for-activating-windows-10/)



