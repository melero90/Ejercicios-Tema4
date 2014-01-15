Tema4 - Virtualización del Almacenamiento
=========================================
#### Antonio Melero Bello

### Ejercicio 1

¿Cómo tienes instalado tu disco duro? ¿Usas particiones? ¿Volúmenes lógicos?

A continuación muestro las particiones de mi disco duro. A parte de las particiones en las que tengo instalados los sistemas operativos Windows8 y Ubuntu 13.10 tengo otras particiones que uso para datos. Tambien me he dado cuenta que tengo espacio sin asignar.

En esta captura se puede ver con el programa GParted la instalación del disco duro

![imagen1](https://dl.dropbox.com/s/xj4qe5kzeu3vmpt/tema4_ej1.png)


### Ejercicio 2

Usar FUSE para acceder a recursos remotos como si fueran ficheros locales. Por ejemplo, sshfs para acceder a ficheros de una máquina virtual invitada o de la invitada al anfitrión.

Mediante una máquina anfitriona voy a acceder a los ficheros de una máquina virtual invitada. La máquina virtual será una Debian 7.3 que tengo instalada.

Instalamos tanto en la máquina virtual como en la anfitriona sshfs con la orden:

    sudo apt-get install sshfs

Desde la máquina virtual se va a crear un usuario al que accederemos y dentro de su directorio /home vamos a crear un directorio que contendrá varios archivos, por ejemplo varios ficheros de texto. Añadir el usuario con el que nos conectaremos remotamente al grupo FUSE. A continuación, desde la máquina anfitriona crearemos una carpeta remota en la cual montaremos todo el directorio /home de la máquina virtual. Ya podemos acceder a los recursos remotos con la orden sshfs pasandole el usuario remoto, la IP de la máquina virtual, la ruta del home de la maquina remota y la ruta del home de la máquina anfitriona.

En la máquina virtual:

    adduser alumno_iv
    su alumno_iv
    mkdir /home/directorio
    touch pruebas1.txt
    touch pruebas2.txt
    usermod -G fuse -a alumno_iv

En la máquina anfitriona:

    -mkdir directorio_remoto
    -sshfs alumno_iv@10.0.3.137:/home /home/antoniomelero/directorio_remoto


### Ejercicio 3

Crear imágenes con estos formatos (y otros que se encuentren tales como VMDK) y manipularlas a base de montarlas o con cualquier otra utilidad que se encuentre.

### Ejercicio 4

Crear uno o varios sistema de ficheros en bucle usando un formato que no sea habitual (xfs o btrfs) y comparar las prestaciones de entrada/salida entre sí y entre ellos y el sistema de ficheros en el que se encuentra, para comprobar el overhead que se añade mediante este sistema

### Ejercicio 5

Instalar ceph en tu sistema operativo.

### Ejercicio 6

Crear un dispositivo ceph usando BTRFS o XFS. Avanzado Usar varios dispositivos en un nodo para distribuir la carga.

### Ejercicio 7

Almacenar objetos y ver la forma de almacenar directorios completos usando ceph y rados.

### Ejercicio 8

Tras crear la cuenta de Azure, instalar las herramientas de línea de órdenes (Command line interface, cli) del mismo y configurarlas con la cuenta Azure correspondiente

### Ejercicio 9

Crear varios contenedores en la cuenta usando la línea de órdenes para ficheros de diferente tipo y almacenar en ellos las imágenes en las que capturéis las pantallas donde se muestre lo que habéis hecho.

### Ejercicio 10

Desde un programa en Ruby o en algún otro lenguaje, listar los blobs que hay en un contenedor, crear un fichero con la lista de los mismos y subirla al propio contenedor. Muy meta todo.
