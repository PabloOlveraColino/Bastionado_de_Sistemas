https://educacionadistancia.juntadeandalucia.es/centros/cadiz/pluginfile.php/685806/mod_resource/content/1/Pra%CC%81ctica%20-%20Next-Gen%20Firewall.pdf

Vamos a configurar un Firewall Next Gen (PfSense) en una máquina virtual con tres interfaces.

Esta guía partirá de tres tarjetas de red virtuales en VirtualBox:
- Una WAN conectada a internet (NAT)
- Una LAN (Red adaptador-puente)
- Una DMZ (Red NAT). Esta no la configuramos de momento.

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image.png]]

Accedemos a la interfaz web una vez el servidor nos da la IP correcta en la LAN. Es la 192.168.0.19 en mi caso.

```
https://192.168.0.19/
```

>**Usuario**: admin **Contraseña**:pfsense

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-1.png]]

# Configurar PfSense como Firewall Next Gen

Instalamos los paquetes adicionales para añadir nuevas funciones avanzadas de firewall.

Hacemos clic en "System" y luego en "Package Manager".

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-2.png]]

Le damos a "Available Packages" y esperamos unos segundos para visualizar los paquetes.

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-3.png]]

Instalamos los siguientes paquetes:

- squid (cifrado SSL)
- Suricata (sistema IPS)
- pfBlockerNG (filtrado de contenido y control de acceso basada en la ubicación)

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-4.png]]

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-5.png]]

# Instalación y configuración de PfBlockerNG

Seleccionamos en el menú "Firewall" y pfBlockerNG para la configuración.

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-6.png]]

Activamos el bloque de listas maliciosas.

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-7.png]]

En "DNSBL IPs" y "List Action" hacemos que deniegue tanto el tráfico de salida como el de entrada para listas de IPs que vamos a definir. ~~También dejamos por defecto "Enable" en la opción "Enable Logging" para que nos haga un log del intento de tráfico.~~

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-8.png]]

Vamos a la pestaña "DNSBL"  y "DNSBL Groups" para agregar listas de IPs de terceros desde Internet. Le damos a "+ Add".

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-9.png]]

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-10.png]]

Guardamos y accedemos a la pestaña "Feeds" donde nos aparecen las listas ya incluidas en el paquete. Buscamos Easylist y EasyPrivacy.

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-11.png]]

Seleccionamos "Unbound" como hicimos previamente.

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-12.png]]

Ya tendríamos configurado el bloqueo para nuestra red LAN. Para la WAN lo hacemos entrando a  la pestaña "IP" y luego "IPv4". Añadimos una serie de URLs. Les dejamos en OFF si necesitamos hace pruebas.

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-13.png]]

Level 1:

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-14.png]]

Level 2:

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-15.png]]

Level 3:

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-16.png]]

Para la configuración de bloque por GeoIP hay que registrarse en [**maxmind** ](https://www.maxmind.com/en/accounts/1147714/people/c86ab01d-e700-4fcf-9abf-387a0505a861)y generar una licencia para introducirla en pfsense.

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-17.png]]

Ahora vamos a la pestaña de "IP" y nos dirigimos al apartado para introducir la licencia.

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-18.png]]

Pegamos la ID de la cuenta y la clave de licencia.

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-19.png]]

Guardamos y vamos a la pestañade "Update" para aplicar los cambios. Le damos al botón "Run".

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-20.png]]

De esta forma podemos bloquear el tráfico y spam de los países a nuestra elección.

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-21.png]]

Bloqueamos el tráfico de algunos países y guardamos.

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-22.png]]

Automáticamente nos crea reglas de firewall.

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-23.png]]

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-24.png]]

# Configuración de Suricata

En "Global Settings" nos permite establecer reglas gratuitas. Para las reglas de Snort tenemos que crear una cuenta en snort.org. Accedemos a "Download Rules" y copiamos el nombre del fichero que queremos asociar a nuestro pfsense y el OinkMaster code que podemos encontrar en la cuenta.

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-25.png]]

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-26.png]]

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-27.png]]

Nos dirigimos a "services" y luego "Suricata" para la configuración global.

![[Bastionado_de_Sistemas/UD4/Guia_Firewall_Pfsense/images/image-28.png]]

En "Global Settings" activamos la casilla de "Install ETOpen Emerging Threats rules".

![[image-29.png]]

También habilitamos las reglas de Snort indicando el fichero de reglas de antes, nuestro okinmaster code y activamos "Install Snort GPLv2 Community Rules".

![[image-30.png]]

Indicamos que las reglas se actualicen cada cierto tiempo y activamos "GeoLite 2 DB Update" indicando la licencia de MaxMind que teníamos.

![[image-31.png]]

Los equipos sospechosos serán bloqueados durante 6 horas y los logs del módulo aparecerán dentro de los logs del firewall general.

![[image-32.png]]

Le damos a "Save" y vamos a la pestaña "Update" y actualizamos.

![[image-33.png]]

Vamos a la pestaña de "Interfaces" y "WAN Settings" para configurar dónde debe actuar el IPS.

![[image-34.png]]

Activamos la inspección dándole a "Enable".

![[image-35.png]]

En "Loggin Settings" marcamos la casilla "Send Alerts to System Log".

![[image-36.png]]

En "Alert and Block" marcamos "Block Offenders".

![[image-37.png]]

Guardamos y nos dirigimos a "WAN Categories" para seleccionar y actividad las categorías disponibles en los ficheros que hemos descargado. Activamos "Select All". 

![[image-38.png]]

En "WAN Rules" podemos ver y desglosar el conjunto de reglas que estamos aplicando para activar o desactivar las que queramos.

![[image-39.png]]

Una vez todo configurado, activamos la interfaz de Suricata en la interfaz seleccionada pulsando el play.

![[image-40.png]]

# Configuración de Squid

Ahora configuraremos el servidor proxy en pfsense con el módulo de Squid que también tiene un antivirus llamado Clam-AV. Instalamos Lightsquid y squidGuard también.

![[image-41.png]]

Filtramos el tráfico HTTPS, para ello vamos a "System" y luego "Cert. Manager".

![[image-42.png]]

![[image-43.png]]

Le ponemos un nombre descriptivo y activamos "Trust Store".

![[image-44.png]]

![[image-45.png]]

![[image-46.png]]

Nos dirigimos a "Services" y "Squid Proxy Server".

![[image-47.png]]

Accedemos a "Local Cache" y configuramos las siguientes opciones.

![](image-48.png)

Asignamos unos 2048 MB para "Hard Disk Cache Size". El tamaño mínimo y máximo de las webs que almacenaremos será de 4 MB.

![](image-49.png)

Activamos el proxy en la pestaña "General" y marcamos "Enable Squid Proxy".

![](image-50.png)

En "Proxy Interfaces" tienen que estar las interfaces de LAN y loopback señaladas.

![](image-51.png)

En la opción "Outgoing Network Interface" indicamos la WAN.

![](image-52.png)

Marcamos la casilla "Transparent HTTP Proxy" para que nadie detecte el tráfico que pasa por nuestro proxy.

![](image-53.png)

Activamos la casilla "HTTPS/SSL Interception" en el apartado "SSL Man In The Middle". Indicamos en "SSL/MITM Mode" como "Splite All" y en "CA" la autoridad certificadora de antes.

![](image-54.png)

Activamos los logs.

![](image-55.png)

Indicamos un nombre de proxy que aparecerá en los mensajes de error, el email y el idioma. Guardamos todos los cambios finalmente.

![](image-56.png)

Ahora nos dirigimos a "ACLs" donde podemos indicar direcciones de red, IPs de equipos, dominios, servicios de Google... De esta forma segmentamos el uso en la red de la organización.

![](image-57.png)

En "Traffic Mgmt" podemos configurar el tamaño de la descarga, la subida, entre otras.

![](image-58.png)

En las pestañas "Real Time" y "Status" podemos ver el estado real del proxy con estadísticas.

![](image-59.png)

Para activar el antivirus de Squid, vamos a la pestaña "Antivirus" y marcamos "Enable AV".

![](image-60.png)

En "Scan Type" lo dejamos como "All". Marcamos "Exclude Audio/Video Streams".

![](image-61.png)

## Configuración de SquidGuard Proxy filter

Nos dirigimos a "Services" y luego "SquidGuard Proxy filter".

![](image-62.png)

En "General Settings", activamos las casillas de logging para que se registre la actividad del módulo.

![](image-63.png)

Activamos la opción "Blacklist" y asignamos la siguiente URL en "Blacklist URL". Guardamos.

![](image-64.png)

En "Target Categories" creamos una categoría de IPs bloqueadas y otra de permitidas.

![](image-65.png)

![](image-66.png)

También podemos redirigir a una URL concreta cuando realice el bloqueo con la opción "Redirect Mode".

![](image-67.png)

Automatizamos el bloqueo de IPs accediendo a la pestaña "Blacklist" y descargando la lista de la URL que hemos introducido en las configuraciones generales.

![](image-68.png)

Una vez descargada, accedemos a "Common ACL", le damos a "+" y desplegamos el cuadro "Target Rules List". Aquí permitimos y denegamos.

![](image-69.png)





























































