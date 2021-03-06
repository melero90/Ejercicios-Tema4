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

Vamos a probar los diferentes tipos de imágenes que soporta [QEMU] (http://en.wikibooks.org/wiki/QEMU/Images).
Todos pueden ser generados con el formato:

    qemu-img create -f [FORMATO] [NOMBRE_ARCHIVO] [TAMAÑO]

por ejemplo:

    raw: qemu-img create -f raw imagen-raw.img 100M
    vdi: qemu-img create -f vdi imagen-vdi.vdi 100M
    qcow2: qemu-img create -f qcow2 imagen-qcow2.qcow2 100M
    vmdk: qemu-img create -f vmdk imagen-vmdk.vmdk 100M
    
![imagen2](https://dl.dropbox.com/s/p1568eq0rcs05qd/ej3_1.png)

Una vez creados estos ficheros vamos a montarlos, aunque nos dará un error al hacerlo ya que no tienen ningún formato.

    sudo mount -o loop,offset=32256 fichero-raw.img /mnt/misImagenes


    sudo modprobe nbd max_part=16
    sudo qemu-nbd -c /dev/nbd1 fichero-raw.img 
    sudo partprobe /dev/nbd1
    sudo mount /dev/nbd1 /mnt/misImagenes

En ambos casos obtenemos el siguiente error: mount: debe especificar el tipo de sistema de archivos.

Para que funcione correctamente primero debemos convertir cada fichero en un dispositivo loop con losetup, formatearlo usando alguna de estas herramientas: fdisk, mkfs, gpartedy y una vez formateado ya podemos montar el dispositivo:

    sudo losetup -v -f imagen-vmdk.vmdk
    sudo mkfs.ext4 /dev/loop1
    sudo mount /dev/loop2 /mnt/images
    
Aqui vemos el resultado con la orden: 

    df -h 
    
![imagen3](https://dl.dropbox.com/s/q9eaa856hc3jqzv/ej3_2.png)


### Ejercicio 4

Crear uno o varios sistema de ficheros en bucle usando un formato que no sea habitual (xfs o btrfs) y comparar las prestaciones de entrada/salida entre sí y entre ellos y el sistema de ficheros en el que se encuentra, para comprobar el overhead que se añade mediante este sistema.

Creamos, como en el ejercicio anterior, dos sistemas de ficheros; uno con formato xfs y otro con formato btrfs:

    qemu-img create -f raw fichero-xfs.img 100M
    qemu-img create -f raw fichero-btrfs.img 100M

Con el comando losetup convertimos cada fichero en ficheros loop:

    sudo losetup -v -f fichero-xfs.img
    sudo losetup -v -f fichero-btrfs.img
    
![imagen4](https://dl.dropbox.com/s/44b0fg1eb5gljlr/ejercicio4_1.png)

Instalar xfs y btrfs:

    sudo apt-get install xfs xfsprogs
    sudo apt-get install btrfs-tools
    
Ahora le asignamos formato a cada uno:

    sudo mkfs.xfs /dev/loop2
    sudo mkfs.btrfs /dev/loop3

Ahora ya si, podemos montarlos:

    sudo mount /dev/loop2 /mnt/loop2
    sudo mount /dev/loop3 /mnt/loop3
    
![imagen6](https://dl.dropbox.com/s/m4v5yj64hgrrqkl/ejercicio4_2.png)

Comprobamos que los dos dispositivos se han montado correctamente:

![imagen5](https://dl.dropbox.com/s/xi7scq4wzdzgogw/ejercicio4_3.png)

### Ejercicio 5

Instalar ceph en tu sistema operativo.

Escribimos la siguiente orden:

    sudo apt-get install ceph-mds

### Ejercicio 6

Crear un dispositivo ceph usando BTRFS o XFS. Avanzado Usar varios dispositivos en un nodo para distribuir la carga.

Creamos los directorios donde se almacena la información ceph:

    sudo mkdir -p /srv/ceph/{osd,mon,mds}

Archivo de configuración ceph.conf:

    [global]
    auth cluster required = none
    auth service required = none
    auth client required = none
    auth supported = none
    log file = /var/log/ceph/$name.log
    pid file = /var/run/ceph/$name.pid
    [mon]
    mon data = /srv/ceph/mon/$name
    [mon.gpc]
    host = melero-Lenovo-IdeaPad-Z500
    mon addr = 127.0.0.1:6789
    [mds]
    [mds.gpc]
    host = melero-Lenovo-IdeaPad-Z500
    [osd]
    osd data = /srv/ceph/osd/$name
    osd journal = /srv/ceph/osd/$name/journal
    osd journal size = 1000
    [osd.0]
    host = melero-Lenovo-IdeaPad-Z500
    xfs devs = /dev/loop5
    
Creo el directorio a mano como dice el ejemplo:

    sudo mkdir /srv/ceph/osd/osd.0
    
Para comprobar que funciona ceph:

![imagen](https://dl.dropbox.com/s/sqh1geefwiudrml/t4ej6_1.png)

Y creo el sistema de ficheros de objetos:

    sudo /sbin/mkcephfs -a -c /etc/ceph/ceph.conf
    
Siguiente error:

![imagen](https://dl.dropbox.com/s/nxy0022vqiwp8dd/t4ej6.png)   

### Ejercicio 7

Almacenar objetos y ver la forma de almacenar directorios completos usando ceph y rados.

### Ejercicio 8

Tras crear la cuenta de Azure, instalar las herramientas de línea de órdenes (Command line interface, cli) del mismo y configurarlas con la cuenta Azure correspondiente.

Para poder instalar Azure debemos tener instalado en nuestro ordenador la librería node.js. La añadimos instalando los repositorios de la siguiente forma:

    sudo apt-get update
    sudo apt-get install -y python-software-properties python g++ make
    sudo add-apt-repository -y ppa:chris-lea/node.js
    sudo apt-get update
    
![t4ej81](https://dl.dropbox.com/s/puhhpozzrk6w23c/t4ej8_1.png)

![t4ej82](https://dl.dropbox.com/s/rw3t5ceju2hb16y/t4ej8_2.png)

El siguiente paso es instalar la librería node.js:

    sudo apt-get install nodejs

Cuando la librería se haya instalado ya podemos instalar windows azure de la siguiente forma:

    npm install azure-cli

![t4ej83](https://dl.dropbox.com/s/o1p4unugbhlpvff/t4_ej8_3.png)

![t4ej84](https://dl.dropbox.com/s/pgv2n76jgbwpe0r/t4_ej8_4.png)

Terminado de instalar azure debemos realizar la configuración para nuestra cuenta:

    azure account download

Esto abrirá el navegador predeterminado con una dirección. Iniciamos nuestra sesión y automáticamente se descarga un archivo de configuración ***publishsettings***

A continuación, importamos el archivo publishsettings ejecutando el siguiente comando:

    azure account import /Escritorio/IV/Azpad4533FS1912-1-11-2014-credentials.publishsettings

Podemos comprobar que nuestra cuenta se ha creado correctamento ejecutando el comando:

    azure account list

Ya podemos crear una cuenta de almacenamiento. Elegimos West Europe como ubicación:

    azure account storage create melero90

Generamos claves:

    azure account storage keys list melero90

Establecemos nuestras claves con la orden export de la siguiente manera y ya tenemos creada y configurada nuestra cuenta en Azure.

    export AZURE_STORAGE_ACCOUNT=melero90
    export AZURE_STORAGE_ACCESS_KEY=...==

### Ejercicio 9

Crear varios contenedores en la cuenta usando la línea de órdenes para ficheros de diferente tipo y almacenar en ellos las imágenes en las que capturéis las pantallas donde se muestre lo que habéis hecho.

### Ejercicio 10

Desde un programa en Ruby o en algún otro lenguaje, listar los blobs que hay en un contenedor, crear un fichero con la lista de los mismos y subirla al propio contenedor. Muy meta todo.

Antes de escribir el programa debemos instalar la gema para azure:

    sudo gem install azure

Programa en Ruby:

        #!/usr/bin/ruby
        require "azure"
    
        azure_blob_service = Azure::BlobService.new
    
        contenedores=azure_blob_service.list_containers()
    
        #Recorremos cada contenedor
        contenedores.each do |contenedor|
    
        #Por cada contenedor creamos un ".txt" con su nombre
        fichero = contenedor.name + ".txt"    
    
        #Abrimos el fichero creado en modo escritura
        File.open(fichero, "w") do |i|
            #Almacenamos en el su nombre y cada blob que contiene dentro
            i.puts "El nombre del fichero es: " + contenedor.name + " que contiene los siguientes blobs:"
    
            blobs=azure_blob_service.list_blobs(contenedor.name)
    
            blobs.each do |blob|
                i.puts " * " + blob.name
            end
        end
    
        #Por último subimos el fichero al contenedor
        content = File.open(fichero, "rb") { |file| file.read }
        blob = azure_blob_service.create_block_blob(contenedor.name, fichero, content)
    end
