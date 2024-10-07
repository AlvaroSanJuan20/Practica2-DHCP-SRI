# Practica 2: Instalación y configuración de DHCP en Debian
`por Alvaro San Juan - ASIR2`

En esta actividad vamos a crear una conexión entre maquinas con DHCP, para esto usaremos a PfSense como router, Debian 12.1 como servidor, Windows 10 y Debian 12 como clientes.

## Preparación del router

> Haremos los preparativos para el ejercicio 1, para ello vamos al PfSense y pondremos esta configuración.

![Imagen](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/1.png)

> Como podemos ver en este recorte de pantalla ya tengo configurado la IP, pero para esta actividad voy a mostrar los pasos cruciales para configurarlo.

![Imagen](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/2.png)

> Primero ponemos la opción 2 y elegimos la opción de LAN porque es lo que vamos a configurar, decimos que 'no' a configurarlo con DHCP. Nos pedirá la IP que pondremos 192.168.219.1 y luego ponemos la mascara, los demas pasos se tienen que poner 'no' o dejar vacio.

> (Recordar que tienen que estar en la misma red)

## Configuración del servidor Debian

![Imagen](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/3.png)

> En la máquina de Debian Server vamos a ir a interfaces, para ello vamos a hacer un **nano /etc/network/interfaces**. Cuando entremos en interfaces pondremos la configuración necesaria.

![Imagen](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/4.png)

> Podemos ver que nos hace ping a internet por lo que podemos transportar archivos. Ahora que sabemos que funciona el internet vamos a hacer un apt update para actualizar la máquina.

![Imagen](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/5.png)

> Al hacer el update vamos a instalar los servicios de isc-dhcp-server para esta actividad. usaremos el comando **apt-get install isc-dhcp-server** para hacer la descarga, esto no se podría hacer sin internet.

![Imagen](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/6.png)

![Imagen](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/7.png)

> Me ha dado un fallo pero igualmente los servicios estan instalados.

> Aquí el comando de **ss-ltun** para ver si está abierto el puerto 68/UDP.

![Image](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/9.png)

> Vamos a indicar que las interfaces de v4 van a ser enp0s3, para ello vamos a **/etc/default/isc-dhcp-server** con un nano y ponemos INTERFACESv4="enp0s3".

> Ahora para configurar el fichero dhcpd.conf haciendo **nano /etc/dhcp/dhcpd.conf**, bajaremos en el archivo hasta llegar a un apartado que hice *A slighty different configuration for an internal subnet*. En este apartado aparecerá con comentarios y ajustes por defecto, los vamos a cambiar.

![Image](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/10.png)

> Estos son los ajustes que voy a poner para el servidor, ahora pondremos la reserva para el cliente de Debian.

![Imagen](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/11.png)

> Si bajamos en el archivo de configuración de dhcpd tendremos un apartado para la reserva, le puse el nombre *UbuntuCliente*, en hardware ethernet ponemos la MAC y la fixed-address la reserva (RECORDATORIO DE BORRAR LOS # PARA QUE NO SEA UN COMENTARIO). 

> Ahora que esta todo configurado vamos a hacer un **systemctl restart isc-dhcp-server.service** que reiniciará los servicios y luego un **systemctl status isc-dhcp-server.service** para ver el status del servicio el cual está activo y funcionando.

![Imagen](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/12.png)

## Configuración de los clientes DHCP.

> Configuración de Debian cliente para DHCP.

![Imagen](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/13.png)

> Hacemos un ping al servidor y vemos que si se pueden pasar información, luego hacemos **ip a** para ver la IP y vemos que sí tiene la reserva.

![Imagen](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/14.png)

> Configuración de Windows es ir a red y poner la configuración de IPv4 en automatico para que sea DHCP.

> Hacemos un **ipconfig /all** para ver los parámetros del cliente como la IP, máscara, puerta de enlace, etc.

![Imagen](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/15.png)

> Aquí tenemos las IPs otorgadas por el servidor aunque no aparece la *10.0.219.60* porque es reservada.

![Imagen](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/16.png)

Ahora vamos a probar la conectividad.

> Debian 12 server pings.

![Imagen](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/18.png)

> Debian 12 cliente pings.

![Imagen](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/19.png)

> Windows 10 cliente pings

![Imagen](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/20.png)

## Funcionamiento del servicio

> Usaremos este comando **journalctl -f -u isc-dhcp-server** para ver los registros de las ultimas acciones del servidor.

![Imagen](https://github.com/AlvaroSanJuan20/Practica2-DHCP-SRI/blob/master/GitHubImages/17.png)


