---
description: Realizando Tunneling (pivoting) y Port Forwarding 101
---

# Realizando Tunneling \(pivoting\) y Port Forwarding 101

Por lo general cuando hablamos de pivoting las personas en el mundo de la informatica conocen el termino y aseguran saber lo referente al tema. Sin embargo la realidad es distinto cuando pasas a la practica \(mi caso\) en el que muchos caemos. Sin embargo, me dedique el fin de semana a realizar el lab sobre pivoting para entender su proceso y diversas tecnicas que permitan realizar  el pivoting, tunneling o hasta un simple Port Forwarding, asi como aseguro en mi post de twiiter [https://twitter.com/jeg1200/status/1358634667054944258?s=20](https://twitter.com/jeg1200/status/1358634667054944258?s=20)

### Para este lab. vamos a necesitar 3 maquinas virtuales:

1. Kali
2. Ubuntu puede ser la version 18 o la ultima version
3. Metasploitable 2

Y para evitar aun mas que las maquinas puedan verse entre si usamos tanto VMware como VirtualBox.

Kali configurada en la VMware

Ubuntu y Metasploitable 2 en la VirtualBox



#### Configuraciones...parte 1

Para nuestra Kali vamos a tener la configuracion de su red en Bridged.

![](.gitbook/assets/image%20%284%29.png)

Nuestro Ubuntu tendra conexion con dos redes, la cual sera Bridged y un adaptador Only-Host \#4 que comparte con la metasploitable.

![](.gitbook/assets/image%20%2815%29.png)

Nuestra Metasploitable tendra solo conexion al adaptador Only-Host \#4 

![](.gitbook/assets/image%20%287%29.png)

De esta manera tendremos que: Nuestra Kali comparte el mismo segmento de red que nuestra ubuntu, y nuestra ubuntu tiene acceso a la misma red que nuestra metasploitable. Pero nuesra Kali no ve la Metasploitable.

De igual manera es importante no mantener confunsion en esta parte ya que el lab puede salir mal \(creanme puede salir todo mal..\). 

![](.gitbook/assets/image%20%2814%29.png)

#### Configuraciones...parte 2

Agregar APACHE 

#### 

#### 

### Port Forwarding

#### Utilizando Metasploit

Consiste en establecer una conexión segura entre un usuario remoto y las máquinas locales.

En este siguiente ejemplo podremos observar como nuestra Kali a travez del metasploit puede obtenere el acceso al ubuntu y seguido redireccionar lo nuestra ubuntu puede ver en el puerto 8080 a nuestro puerto 8081 en nuestra kali.

Para esto iniciamos el metasploit

```text
sudo msfconsole
```

ingresamos el siguiente parametro 

```text
use auxiliary/scanner/ssh/ssh_login 
set rhosts 192.168.1.139 
set username juss 
set password 1234 
exploit 
sessions -u 1 
sessions 2 
netstat -antp
```

Una vez ingresado estos parametros tendremos las siguientes salidas

![](.gitbook/assets/image%20%2812%29.png)

Seguido corremos nuestro netstat y podremos observar que:

1. claramente estamos dentro de la ubuntu
2. podemos que esta el apache corriendo sobre el puerto 8080

![](.gitbook/assets/image%20%281%29.png)

Conociendo ya esto info utilizaremos el siguiente comando para poder realizar nuestro Port Forwarding o redirecciòn de puertos.

```text
portfwd add -l 8081 -p 8080 -r 127.0.0.1
```

El comando portfwd, es un comando que funciona para realizar redirecciones de puertos. De esta manera podemos enviar todo el trafico de nuestro puerto 8080 \(Ubuntu\) a nuestro puerto 8081 \(kali\). El comando add para añadir. La bander -l indica cual sera nuestro puerto escucha, la bander -p es el puerto a cual queremos conectarnos y el flag -r es el ip de la maquina a la que nos estamos conectando.

![](.gitbook/assets/image%20%2810%29.png)

Seguido de esto realizamos la prueba abriendo nuestro navegador en la kali con la direccciòn del localhost:8081

![](.gitbook/assets/image%20%2811%29.png)

Y como asegura el Apache2.. It works!

#### Port Forwarding o redireccionamiento de puertos con SSH local

Se utiliza el protocolo SSH para reenviar los puertos de la aplicación desde una máquina cliente a la máquina atacante. El cliente SSH escucha las conexiones en un puerto que ha sido configurado y hace un túnel a un SSH cuando se recibe una conexión hacia nuestra Kali o maquina atacante. 

Para esto claramente conocemos previamente el user y pass de la maquina victima

```text
ssh -L 8081:localhost:8080 -N -f -l juss 192.168.1.139
```

Este comando se traduce de la siguiente manera:

* Tenemos el flag -L 8081:localhost  que representa el puerto escucha de nuestra maquina Kali
* :8080 que representa el puerto de la maquina que queremos escuchar \(Ubuntu\)
* -N para no ejecutar comandos remotos, el cual es especial para hacer forwarding de puertos.
* -f  para enviar nuestro comando de ssh al background, de esta manera se ejecutara los otros flags mientras tengamos nuestra conexiòn abierta
* -l para especificar el usuario de la maquina remota \(Ubuntu\)
* y seguido la ip de nuestra Ubuntu

![](.gitbook/assets/image%20%2818%29.png)

Como se menciono anteriormente nuestro ssh paso al background y revisamos en nuestro explorador la ruta localhost:8081

![](.gitbook/assets/image%20%2817%29.png)

De esta manera conseguimos realizar un portForwarding entre nuestras maquinas.

### Tunneling 

Este es el proceso que se utiliza para acceder a los recursos de una red desde una red publica. Quiere decir que a tavez de esta tecnica podremos llegar a una subnet dentro de una red a la que desde un principio no tenemos acceso.

Para este lab como se meciono anteriormente utilizaremos el Metasploitable como victima...

#### Sshuttle

Esta herramienta nos permite enrutar redes ssh como si fuese conexiones de vpn.

Con el Sshuttle realizaremos una conexiòn desde nuestra kali, hacia nuestro Ubuntu, llamando a la red o ip del metasploitable

Para este Lab necesitamos tener nuestras ips a mano.

Kali

![](.gitbook/assets/image%20%285%29.png)

Ubuntu

![](.gitbook/assets/image.png)

Metasploitable 2

![](.gitbook/assets/image%20%286%29.png)

Seguido, como primer paso debemos realizar la instalacion de nuestro Sshuttle

```text
apt install sshuttle
```

En este caso como ya tengo instalado el Sshuttle me saldra el siguiente mensaje

![](.gitbook/assets/image%20%2813%29.png)

Una vez ya instalado corremos la setencia 

```text
sshuttle -vr juss@192.168.1.139 172.16.250.129/16  
```

La cual detallamos de la sigueintee manera:

* -vr  este flag indica con la \(-v\) de verbose, de esta manera obtendremos una salida a la sentencia para poder ver lo que ocurre en el momento y la \(r\) permite ingresar un usuario.
* seguido el usuario junto con la direcciòn ip 192.168.1.139 \(ubuntu\) y la direcciòn del segmento o cidr 172.16.250.129/16 \(metasploitable\) 

![](.gitbook/assets/image%20%288%29.png)

Una vez ejecutado la sentencia podemos observar en nuestro explorador de la Kali la ip del metasploitable

![](.gitbook/assets/image%20%283%29.png)









