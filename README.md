Tema4 - Virtualización del Almacenamiento
=========================================
#### Antonio Melero Bello

### Ejercicio 1

¿Cómo tienes instalado tu disco duro? ¿Usas particiones? ¿Volúmenes lógicos?




### Ejercicio 2

Usar FUSE para acceder a recursos remotos como si fueran ficheros locales. Por ejemplo, sshfs para acceder a ficheros de una máquina virtual invitada o de la invitada al anfitrión.

Mediante una máquina anfitriona voy a acceder a los ficheros de una máquina virtual invitada. La máquina virtual será una Debian 7.3 que tengo instalada.

Instalamos tanto en la máquina virtual como en la anfitriona sshfs con la orden:

> sudo apt-get install sshfs

Desde la máquina virtual se va a crear un usuario al que accederemos y dentro de su directorio /home vamos a crear un directorio que contendrá varios archivos, por ejemplo varios ficheros de texto. Añadir el usuario con el que nos conectaremos remotamente al grupo FUSE. A continuación, desde la máquina anfitriona crearemos una carpeta remota en la cual montaremos todo el directorio /home de la máquina virtual. Ya podemos acceder a los recursos remotos con la orden sshfs pasandole el usuario remoto, la IP de la máquina virtual, la ruta del home de la maquina remota y la ruta del home de la máquina anfitriona.


> En la máquina virtual:
    -adduser alumno_iv
    -su alumno_iv
    -mkdir /home/directorio
    -touch pruebas1.txt
    -touch pruebas2.txt
    -usermod -G fuse -a alumno_iv

> En la máquina anfitriona:
    -mkdir directorio_remoto
    -sshfs alumno_iv@10.0.3.137:/home /home/antoniomelero/directorio_remoto



### Ejercicio 3


### Ejercicio 4


### Ejercicio 5


### Ejercicio 6


### Ejercicio 7


### Ejercicio 8


### Ejercicio 9



### Ejercicio 10
