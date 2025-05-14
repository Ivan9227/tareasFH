Práctica chroot

- Instalamos la Debian sobre la que vamos a trabajar.

![](1.png)

![](2.png)

![](3.png)

- Creamos un usuario con nuestro nombre (vale cualquiera, ya que una vez tengamos la jaula montada podremos acceder al archivo `/etc/passwd` y ver los usuarios del sistema).
- Una vez instalado, apagamos la máquina e introducimos una live (usaré Kali en este caso, pero nos sirve cualquier distribución Linux) que utilizaremos para montar la jaula sobre su kernel y poder lanzar el chroot. 
  ![](4.png)
- Comenzamos abriendo la terminal en el live de Kali:
  - Lo primero que haremos será cambiar el idioma del teclado a castellano:
    - `sudo setxkbmap es`
  - Accedemos a la consola de root como administrador a través de los permisos configurados con el comando:
    - `sudo su`
  - Mostramos el sistema de ficheros montado, es decir, los que están en uso y podemos utilizar en este sistema operativo live Debian. 

    ![](5.png)

  - Listamos la tabla de particiones del disco `/dev/sda`. 
    ![](6.png)

- Creamos la ubicación donde vamos a crear la jaula para poder montar en ella los directorios `/dev`, `/sys`, `/proc` y lanzarnos en el SO base como root desde la live.

  - `mkdir /mnt/recuperar && ls /mnt/recuperar`
- Montamos la partición 1 del disco duro `/dev/sda` en el directorio del sistema operativo creado en el paso anterior `/mnt/recuperar` en la máquina live. Con la opción `-t auto` solicitamos al comando `mount` la autodetección del sistema de ficheros montado. Podemos ver este sistema de ficheros con el comando `lsblk -f`:
  - `mount /dev/sda3 /mnt/recuperar`
  - `lsblk -f`
- Montamos el directorio `/proc` dentro de la ruta `/mnt/recuperar/proc` para poder tener acceso a los procesos del sistema y kernel de Kali Linux gracias a la distribución live:
  - `mount --bind /proc /mnt/recuperar/proc`
- Montamos el directorio `/sys` dentro de `/mnt/recuperar/sys` para poder tener acceso al hardware y kernel de Kali Linux gracias a la distribución live:
  - `mount --bind /sys /mnt/recuperar/sys`
- Montamos el directorio `/dev` dentro de `/mnt/recuperar/dev` para poder tener acceso a todos los dispositivos reconocidos por la distribución live:
  - `mount --bind /dev /mnt/recuperar/dev`
- Creamos la jaula mediante el comando:
  - `chroot /mnt/recuperar /bin/bash`
  ![](7.png)
- Una vez suplantado el usuario root, ya podemos ejecutar cualquier comando sobre la máquina base, como la creación de usuarios o cambio de contraseñas:
  ![](8.png)
  - `passwd usuario`
- Desmontamos los directorios anteriormente montados para la recuperación del sistema, es decir, `/mnt/recuperar/dev`, `/mnt/recuperar/proc`, `/mnt/recuperar/sys` y `/mnt/recuperar`:
  ![](9.png)
- Extraemos el disco de la live de Kali e iniciamos la máquina con el SO base para comprobar si los cambios surtieron efecto:
  - Introducimos la nueva contraseña: 
    ![](10.png)
  - Y vemos que con la nueva contraseña nos deja iniciar sesión.

![](11.png)






