---
layout: post
title:  "Writeup: Tryhackme ColddBox."
description: En este post os voy a enseñar a como vulnerar y escalar privilegios en está maquina sencilla de TryHackme.

tags: ejpt,elearn,certificacio
---





# Enumeración
NMAP: Escaneo de puertos.

<a href="https://ibb.co/sySrwwy"><img src="https://i.ibb.co/J5LP775/mmap1.png" alt="mmap1" border="0" /></a>

NMAP: Analísis de los puertos abiertos.

<a href="https://ibb.co/bKJ5rqZ"><img src="https://i.ibb.co/NyS2LQP/image.png" alt="image" border="0" /></a>



Wappalyzer & Whatweb: Descubrir con que tecnologías esta hecho la web.

<a href="https://imgbb.com/"><img src="https://i.ibb.co/tbVN58T/image.png" alt="image" border="0" /></a>
<a href="https://imgbb.com/"><img src="https://i.ibb.co/b1DHTQ4/image.png" alt="image" border="0" /></a>




Ffuf: Enumeración de directorios(aka Fuzzing).

<a href="https://ibb.co/MM9s4QQ"><img src="https://i.ibb.co/0hQcSvv/image.png" alt="image" border="0" /></a>
<a href="https://imgbb.com/"><img src="https://i.ibb.co/phKtSFY/image.png" alt="image" border="0" /></a>



WPScan: Escaneo de WordPress, y enumeración de usuarios.

<a href="https://ibb.co/qYfHzvp"><img src="https://i.ibb.co/N2P0XMK/image.png" alt="image" border="0" /></a>
<a href="https://imgbb.com/"><img src="https://i.ibb.co/phKtSFY/image.png" alt="image" border="0" /></a>


# EXPLOTACION
WPScan: Bruteforce al login de WP (encontrado haciendo fuzzing) con los usuarios encontrados en WPScan.

<a href="https://ibb.co/7XkjZJq"><img src="https://i.ibb.co/bPbQCgk/image.png" alt="image" border="0" /></a>

No pudé hacer captura de los resultado pero aproximadamente en 1 minuto o menos la contraseña fue crackeado.




Accedemos a WP accediendo al login `wp-admin`:

<a href="https://ibb.co/JryMSL6"><img src="https://i.ibb.co/ygYbxTv/image.png" alt="image" border="0" /></a>



Comprobamos si somos administradores accediendo al apartado de usuarios, y efectivamente lo somos:

<a href="https://ibb.co/KL0wT6Y"><img src="https://i.ibb.co/GtxTz7X/image.png" alt="image" border="0" /></a>

Buscando en internet como conseguir una reverse shell siendo admin de WP encontré que podemos injectar una reverse shell en el fichero 404.php de la página web. En mi caso subí una reverse de php.

<a href="https://ibb.co/X5Cz0C6"><img src="https://i.ibb.co/09Gf4G1/image.png" alt="image" border="0" /></a>


Nos ponemos a la escucha...y ejecutamos la shell mediante  la ruta [http://ip/wp-content/themes/twentyfifteen/404.php](http://ip/wp-content/themes/twentyfifteen/404.php)

<a href="https://ibb.co/qpVPDwC"><img src="https://i.ibb.co/YhwqNGX/image.png" alt="image" border="0" /></a>

Mejoramos la shell

* script /dev/null
* Ctrl+Z    
* stty raw -echo;fg
* reset
* export TERM=xterm
* export SHELL=shell

<a href="https://ibb.co/Wt2gSF5"><img src="https://i.ibb.co/FVDgGJ3/image.png" alt="image" border="0" /></a>


Si exploramos el sistema encontramos /var/www/html/wp-config.php

<a href="https://imgbb.com/"><img src="https://i.ibb.co/sF7NQ8N/image.png" alt="image" border="0" /></a>

Nos podríamos loguear a la base de datos con los datos que acabamos de encontrar, pero si recordamos, tenemos un puerto alojando ssh. Podríamos probar a ver si estamos ante un caso de reutilización de credenciales…

<a href="https://imgbb.com/"><img src="https://i.ibb.co/f9dRJLg/image.png" alt="image" border="0" /></a>


# ESCALADA DE PRIVILEGIOS.
En esta máquina tenemos 3 "binarios" que se explotan usando la misma "mala configuración".
Para descubrir estos binarios ejecutamos "sudo -l"
Este comando nos arrojara que podemos ejecutar como sudo "FTP","VIM", y por último "CHMOD"

* FTP

Ejecutamos ftp como sudo y luego podemos ejecutar cualquier comandos en contexto privilegiado.

<a href="https://imgbb.com/"><img src="https://i.ibb.co/4s6gSVP/image.png" alt="image" border="0" /></a>


* CHMOD

Ejecutamos "sudo chmod 4777 /bin/bash" para asignar el bit suid a /bin/bash y poder ejecutarlo en un contexto privilegiado.
Con `sudo /bin/bash -p` ejecutas /bin/bash como root y con `-p` le indicamos que mantenga los privilegios al terminar de ejecutar el comando.

<a href="https://imgbb.com/"><img src="https://i.ibb.co/cvSGG1N/image.png" alt="image" border="0" /></a>
<a href="https://imgbb.com/"><img src="https://i.ibb.co/wYG2qfb/image.png" alt="image" border="0" /></a>


* VIM

Con VIM es tan simple como que VIM ejecutará lo que pongamos como el usuario que usemos. Si añadimos sudo, lo estaremos ejecutando en un contexto privilegiado.
<a href="https://imgbb.com/"><img src="https://i.ibb.co/jMyCLT8/image.png" alt="image" border="0" /></a>
