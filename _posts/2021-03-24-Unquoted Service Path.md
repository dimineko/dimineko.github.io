---
layout: post
title:  "Vulnerabilidad: Unquoted Service Path"
description: En este post veremos en que consiste la vulnerabilidad Unquoted Service Path, que requísitos tenemos que cumplir para poder explotarla, y como explotarla.

tags: vulnerabilidad
---



# Requisitos

* Tener una shell (Netcat, Meterpreter, etc...)
* Si la shell es de un usuario sin privilegios,nos bastaría con poder subir (si no se pueden subir ficheros, pero si crearlos, podría funcionar si somos capaces de crear un ejecutable completamente funcional)
* Tener en la máquina que estamos atacando un servicio corriendo cada cierto tiempo, el cual ejecuta un fichero.





# Explicación de la vulnerabilidad con PSEUDOCODIGO

En este fragmento de código podemos ver como, la función proceso ( que simula un servicio que se ejecuta periodicamente ) ejecuta la ruta.
```python
def servicio():  
  ejecutar("C:\CARPETA UNO\CARPETA DOS\CARPETA TRES\sql.exe") 
  #El proceso quiere ejecutar la 
  #ruta "C:\CARPETA UNO\CARPETA DOS\CARPETA TRES\sql.exe" (con comillas)
```

En este caso, al estar la ruta con comillas, no importa que tenga espacios, puesto que es una String.
Por tanto el servicio que se ejecuta periodicamente ejecutara sin problemas "C:\CARPETA UNO\CARPETA DOS\CARPETA TRES\sql.exe".



```python
def servicio():  
  ejecutar(C:\CARPETA UNO\CARPETA DOS\CARPETA TRES\sql.exe) 
  #El proceso quiere ejecutar la 
  #ruta C:\CARPETA UNO\CARPETA DOS\CARPETA TRES\sql.exe (sin comillas)
```
En este otro fragmento de código podemos ver como la función servicio ejecuta la ruta `C:\CARPETA UNO\CARPETA DOS\CARPETA TRES\sql.exe` , (vease que no lleva comillas)



# ¿Cómo ejecutara esto Windows?

Buscará subdirectorio por subdirectorio hasta dar con el fichero que busca(sql.exe). Es decir, que si encuentra el fichero antes, usará ese fichero y dejará de buscarlo.


Es decir,buscará en los siguientes directorios:
1. C:\sql.exe
2. C:\CARPETA UNO\sql.exe
3. C:\CARPETA UNO\CARPETA DOS\sql.exe
4. C:\CARPETA UNO\CARPETA DOS\sql.exe
5. C:\CARPETA UNO\CARPETA DOS\CARPETA TRES\sql.exe



# Explotación de la vulnerabilidad

Por tanto, visto lo anteriormente , y si cumplimos las condiciones previamente citadas, si colocamos por ejemplo un payload `sql.exe` hecho en powershell/cmd en una ruta previa a la ruta final (EJ: `C:\CARPETA UNO\sql.exe`)
conseguiremos una reverse shell como administrador (o el usuario que este ejecutando el servicio/tarea)



# Creacion de un payload en Msfvenom
<a href="https://ibb.co/QnJWCjJ"><img src="https://i.ibb.co/d6fNLJf/ups-msfvenom.png" alt="ups-msfvenom" border="0" /></a>


# Poniendonos a la escucha con Metasploit
<a href="https://ibb.co/kgH8tFy"><img src="https://i.ibb.co/XZ2sqT4/MSFCONSOLE-ESCUCHAR.png" alt="MSFCONSOLE-ESCUCHAR" border="0" /></a>
<a href="https://ibb.co/0yh4qJj"><img src="https://i.ibb.co/xqJ9h5S/EXPLOIT.png" alt="EXPLOIT" border="0" /></a>
