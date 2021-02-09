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



Configuraciones...parte 1

Para nuestra Kali vamos a tener la configuracion de su red en Bridged.

![](.gitbook/assets/image.png)

Nuestro Ubuntu tendra conexion con dos redes, la cual sera Bridged y un adaptador Only-Host \#4 que comparte con la metasploitable.

![](.gitbook/assets/image%20%281%29.png)

Nuestra Metasploitable tendra solo conexion al adaptador Only-Host \#4 

![](.gitbook/assets/image%20%282%29.png)

De esta manera tendremos que: Nuestra Kali comparte el mismo segmento de red que nuestra ubuntu, y nuestra ubuntu tiene acceso a la misma red que nuestra metasploitable. Pero nuesra Kali no ve la Metasploitable.

De igual manera es importante no mantener confunsion en esta parte ya que el lab puede salir mal \(creanme puede salir todo mal..\). 

![](.gitbook/assets/image%20%284%29.png)

#### Port Forwarding



