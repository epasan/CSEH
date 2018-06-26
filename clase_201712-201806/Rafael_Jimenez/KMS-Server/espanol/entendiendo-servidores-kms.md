## 2. Entendiendo KMS

Un Servidor KMS activa los equipos en una red local, eliminando la necesidad de que los equipos se conecten a Microsoft individualmente. Para hacer esto, KMS usa una topología cliente-servidor. Los equipos clientes KMS pueden ubicar los equipos KMS host usando el Sistema de nombres de dominio (DNS) o una configuración estática. Los clientes KMS se ponen en contacto con el KMS host mediante el uso de una llamada a procedimiento remoto (RPC). KMS puede alojarse en equipos que ejecutan los sistemas operativos Windows Vista, Windows 7, Windows Server 2003, Windows Server 2008 o Windows Server 2008 R2.

### 2.1. Requisitos mínimos del equipo
Al planear la activación de KMS, la red debe cumplir o superar el umbral de activación o la cantidad mínima de equipos que KMS requiere. También debe comprender cómo el KMS host rastrea la cantidad de equipos en la red.

### 2.2. Umbrales de activación KMS
KMS puede activar tanto las equipos físicos como las máquinas virtuales. Para calificar para la activación de KMS, una red debe cumplir con el umbral de activación: los KMS hosts activan las equipos cliente solo después de cumplir con este umbral. Para garantizar que se cumple el umbral de activación, un host KMS cuenta la cantidad de equipos que solicitan la activación en la red. Para equipos con Windows Server 2008 o Windows Server 2008 R2, el umbral de activación es cinco. Para equipos que ejecutan Windows Vista o Windows 7, el umbral de activación es 25. Los umbrales incluyen equipos cliente y servidores que se ejecutan en equipos físicas o máquinas virtuales.

Un KMS host responde a cada solicitud de activación válida de un cliente KMS con el recuento de cuántas equipos se han contactado con el host KMS para su activación. Los clientes que reciben un recuento por debajo de su umbral de activación no están activados. Por ejemplo, si los dos primeros equipos que entran en contacto con el host KMS están ejecutando Windows 7, el primero recibe un conteo de activación de 1, y el segundo recibe un conteo de activación de 2. Si el siguiente equipo es una máquina virtual Windows 7, recibe un conteo de activación de 3, y así sucesivamente. Ninguno de estos equipos está activado, porque los equipos con Windows 7 deben recibir un conteo de activación ≥25 para ser activados. Los clientes KMS en estado de gracia que no están activados porque el recuento de activación es demasiado bajo se conectan al host KMS cada dos horas para obtener el conteo de activación actual y se activarán cuando se cumpla el umbral.

Si el siguiente equipo que contacta al host KMS ejecuta Windows Server 2008 R2, recibe un recuento de activación de 4, porque los recuentos de activación son una combinación de equipos que ejecutan Windows Server 2008 R2 y Windows 7. Si una equipos ejecuta Windows Server 2008 o Windows Server 2008 R2 recibe un conteo de activación que es ≥5, está activado. Si un equipos con Windows 7 recibe un conteo de activación ≥25, se activará.

### 2.3. Caché del conteo de activación
Para rastrear el umbral de activación, el KMS host guarda un registro de las equipos cliente KMS que solicitan la activación. El host KMS otorga a cada equipos cliente de KMS una designación de identificación de máquina cliente (CMID) y el host KMS guarda cada CMID en una tabla. Cada solicitud de activación permanece en la tabla durante 30 días. Cuando una equipos cliente renueva su activación, el CMID en caché se elimina de la tabla, se crea un nuevo registro y el período de 30 días comienza nuevamente. Si un equipo cliente KMS no renueva su activación dentro de los 30 días, el host KMS elimina el CMID correspondiente de la tabla y reduce el recuento de activación en uno.

El host KMS almacena en caché el doble del número de CMID que los clientes KMS requieren para ayudar a garantizar que el recuento de CMID no caiga por debajo del umbral de activación. Por ejemplo, en una red con equipos cliente que ejecutan Windows 7, el umbral de activación de KMS es 25, por lo que el host KMS almacena en caché los CMID de las 50 activaciones más recientes. El umbral de activación de KMS para Windows Server 2008 R2 es 5. Un host KMS al que solo contactan los equipos cliente KMS que ejecutan Windows Server 2008 R2 almacenará en caché los 10 CMID más recientes. Si un equipo cliente con Windows 7 se contacta más tarde con ese host KMS, KMS aumenta el tamaño del caché a 50 para acomodar el umbral más alto. KMS nunca reduce el tamaño de la caché.

### 2.4. Cómo funciona KMS
La activación de KMS requiere conectividad TCP/IP. De forma predeterminada, los hosts KMS y las equipos cliente usan DNS para publicar y encontrar el servicio KMS. Se pueden usar las configuraciones predeterminadas, que requieren poca o ninguna acción administrativa, o los hosts KMS y las equipos cliente se pueden configurar manualmente en función de la configuración de la red y los requisitos de seguridad.

Renovación de activación de KMS Las activaciones de KMS son válidas por 180 días: el intervalo de validez de la activación. Para permanecer activado, las equipos cliente KMS deben renovar su activación conectándose al host KMS al menos una vez cada 180 días. De forma predeterminada, las equipos cliente KMS intentan renovar su activación cada siete días. Si falla la activación de KMS, el cliente volverá a intentarlo cada dos horas. Después de renovar la activación de una equipos cliente, el intervalo de validez de activación comienza nuevamente.

### 2.5. Publicación del Servicio KMS
El servicio KMS usa registros de recursos de servicio (SRV) en DNS para almacenar y comunicar las ubicaciones de los hosts KMS. Los hosts KMS usan el protocolo de actualización dinámica de DNS, si está disponible, para publicar los RR SRM de KMS. Si la actualización dinámica no está disponible o el host KMS no tiene derechos para publicar los RR, los registros DNS deben publicarse manualmente, o debe configurar los equipos cliente para conectarse a hosts KMS específicos.

> Nota: Tenga en cuenta que los cambios en el DNS pueden tardar en propagarse a todos los hosts DNS, según la complejidad y la topología de la red.

### 2.6. Descubrimiento del cliente del servicio KMS
De forma predeterminada, los clientes KMS consultan DNS para obtener información del servicio KMS. La primera vez que un cliente KMS consulta el DNS para la información del servicio KMS, elige aleatoriamente un host KMS de la lista de RR SRV que devuelve el DNS.

La dirección de un servidor DNS que contiene los SRV RR se puede enumerar como una entrada con sufijo en los clientes KMS, que permite la publicación de SRV RR para KMS en un servidor DNS y los clientes KMS con otros servidores DNS primarios para encontrarlo.

Los parámetros de prioridad y peso se pueden agregar al valor de registro **DnsDomainPublishList** para KMS. El establecimiento de grupos de prioridad de host KMS y la ponderación dentro de cada grupo le permite especificar qué host KMS los clientes deben probar primero y equilibra el tráfico entre varios hosts KMS. Solo Windows 7 y Windows Server 2008 R2 proporcionan los parámetros de _prioridad_ y _peso_.

Si el host de KMS que selecciona un cliente no responde, el cliente de KMS elimina ese host de KMS de su lista de RR de SRV y selecciona aleatoriamente otro host de KMS de la lista. Cuando un host KMS responde, el cliente KMS guarda en caché el nombre del host KMS y lo usa para intentos posteriores de activación y renovación. Si el host KMS en caché no responde en una renovación posterior, el cliente KMS descubre un nuevo host KMS al consultar el DNS para KMS SRV RR.

De forma predeterminada, los equipos cliente se conectan al host KMS para su activación utilizando RPC anónimos a través del **puerto TCP 1688.** (Puede cambiar el puerto predeterminado). Después de establecer una sesión TCP con el host KMS, el cliente envía un único paquete de solicitud. El host KMS responde con el conteo de activación. Si el conteo cumple o excede el umbral de activación para ese sistema operativo, el cliente se activa y la sesión se cierra. El cliente de KMS usa este mismo proceso para las solicitudes de renovación. **La comunicación en cada sentido es de 250 bytes**.

### 2.7. Activando el primer host KMS
Los hosts KMS en la red necesitan instalar una clave KMS, y luego deben activarse con Microsoft. La instalación de una clave KMS habilita el Servicio de administración de claves en el host KMS. Después de instalar la clave KMS, complete la activación del host KMS por teléfono o en línea. Más allá de esta activación inicial, un host KMS no comunica ninguna información a Microsoft.

Las claves KMS solo están instaladas en hosts KMS, nunca en clientes KMS individuales. Windows 7 y Windows Server 2008 R2 tienen medidas de seguridad para ayudar a prevenir la instalación inadvertida de claves KMS en los equipos cliente KMS. Cada vez que los usuarios intenten instalar una clave KMS

### 2.8. Activando hosts KMS subsiguientes
Cada clave KMS se puede instalar en hasta seis hosts KMS, que pueden ser equipos físicas o máquinas virtuales. Después de activar un host KMS, el mismo host se puede reactivar hasta nueve veces más con la misma clave.

Si la organización necesita más de seis hosts KMS, puede solicitar activaciones adicionales para la clave KMS de la organización llamando al Centro de llamadas de activación para solicitar una excepción. Para obtener más información, consulte el sitio web de Licencias por volumen en http://go.microsoft.com/fwlink/?LinkID=73076.

### 2.9. Actualización de hosts KMS existentes
Los hosts KMS que ejecutan Windows Server 2003, Windows Vista o Windows Server 2008 se pueden configurar para admitir clientes KMS que ejecuten Windows 7 y Windows Server 2008 R2. Para Windows Vista y Windows Server 2008, es necesario actualizar el host KMS con un paquete con archivos que admitan el cliente KMS expandido. Este paquete está disponible a través del Centro de descarga de Microsoft en http://www.microsoft.com/downloads. Una vez que el paquete se instala en el host KMS, se puede instalar y activar una clave KMS diseñada para admitir Windows 7 y Windows Server 2008 R2, como se describió anteriormente en esta guía. La clave KMS que admite las nuevas versiones de los sistemas operativos Windows también brinda soporte para las ediciones anteriores de Licencias por Volumen de Windows que actúan como clientes KMS.

En el caso de actualizar un servidor KMS de Windows Server 2003, todos los archivos necesarios se encuentran dentro del paquete descargable de KMS 1.2, que está disponible a través del Centro de descarga de Microsoft en http://www.microsoft.com/downloads.

### 2.10. Planeación de clientes KMS
De forma predeterminada, las equipos que ejecutan ediciones de Licenciamiento por Volumen de Windows Vista, Windows 7, Windows Server 2008 y Windows Server 2008 R2 son clientes KMS, y no se necesita configuración adicional. Los clientes KMS pueden ubicar un host KMS automáticamente al consultar DNS para SRV RR que publican el servicio KMS. Si el entorno de red no utiliza SRV RR, un cliente KMS se puede configurar manualmente para usar un host KMS específico.

Para configurar manualmente clientes KMS, siga los pasos en la sección titulada, "Especificación manual de un host KMS", más adelante en esta guía.

### 2.11. Activar como usuario estándar
Windows 7 y Windows Server 2008 R2 no requieren privilegios de administrador para la activación. Sin embargo, este cambio no permite que las cuentas de usuario estándar eliminen Windows 7 o Windows Server 2008 R2 del estado activado. Todavía se requiere una cuenta de administrador para otras tareas relacionadas con la activación o la licencia, como "rearm".