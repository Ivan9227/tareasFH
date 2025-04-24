
### 1. Creamos dos máquinas virtuales
Primero, instalaremos dos maquinas con SO Ubuntu en nuestro software de virtualización (estoy utilizando VirtualBox). 
Vamos a llamarlas **máquina A** y **máquina B**.
Ambas maquinas tendrán dos tarjetas de red:

 - Una tarjeta estará en modo **NAT**, a través de ella nos conectamos a las maquinas desde la anfitriona y nos descargaremos el software necesario. En esta tarjeta tendremos que configurar el reenvío de puertos en cada una de las maquinas para poder conectarnos desde el anfitrión.
	 - Maquina A:  puerto anfitrión 2222 y puerto invitado 22.
	 -  Maquina B:  puerto anfitrión 2223 y puerto invitado 22.
 -  La otra tarjeta de red en **red interna**. con la que nos comunicaremos entre las dos maquinas virtuales.
 
 **Instalación de SSH**
 - Primero actualizaremos la lista de paquetes disponibles en los repositorios oficiales de cada una de las maquinas.  
 ```bash
sudo apt update
```
 - Después instalaremos el software.
  ```bash
 sudo apt install ssh
```

### 2. Conectar por SSH
Nos conectamos a los puertos SSH de las máquinas a través del CMD/PowerShell de la maquina anfitriona:
- **Máquina A**: puerto 2222
```bash
ssh -p 2222 ivan@localhost
```
- **Máquina B**: puerto 2223
```bash
ssh -p 2223 ivan@localhost
```

### 3. Crear usuarios
En la máquina A, creamos el usuario **Alex**:
```bash
sudo adduser alex
```

En la máquina B, creamos el usuario **Brais**:
```bash
sudo adduser brais
```
Para seguir con las pruebas 

### 4. Configurar máquina A como cliente y máquina B como servidor
No es posible dicha configuración ya que nos piden en los anteriores apartados que nos conectemos por ssh a las maquinas mediante los puertos 2222 y 2223. Esto no seria posible sin tener instalado ssh server en ambas maquinas.

**Este apartado se realizaría de la siguiente manera:**
Máquina A actuaría como cliente y máquina B como servidor.
- En la **máquina A**:
```bash
sudo apt install openssh-client
```
- En la **máquina B**:
```bash
sudo apt install openssh-server
```
A continuacion haremos uso de la terminal bajo las acciones de los usuarios creados con el comando :
```bash
su alex
su brais
```
### 5. Conexión de máquina A hacia B
Para conectar desde la máquina A hacia la máquina B, utilizaremos el siguiente comando SSH:
```bash
ssh brais@172.172.172.4 
```
Este comando establece una conexión SSH desde el cliente (máquina A) al servidor (máquina B). No usamos el parámetro -p porque por defecto usa el puerto 22 y es el que queremos para esta conexión.
- **ssh** es el programa que invoca la conexión a través del terminal a la otra maquina. 
- **brais** es el usuario de la otra maquina con el que queremos iniciar la conexión.
- **172.172.172.4** es la ip de la maquina a la que nos conectamos. Si disponemos de un DNS podríamos utilizar directamente el nombre que la maquina tenga asignado directamente.
- **¿Qué se crea cuando nos conectamos al servidor desde el cliente?** Lo que se crea es un archivo en /etc/ssh que contine la informacion de la conexion, por lo que las proximas conexiones que se haran ya no las interpreta como hosts desconocidos y no nos pedira confirmacion cuando queramos conectar las siguientes veces.


Al ser la primera conexión nos pregunta si estamos seguros, ya que no conoce la autenticidad del host y nos proporciona la huella digital de la clave. le decimos "yes".


### 6. Crear directorio "prueba" en máquina A y enviar al servidor
En la máquina A, creamos el directorio "prueba" en el directorio temporal junto con el archivo "prueba.txt":
```bash
mkdir /tmp/prueba
echo esto es una prueba > /tmp/prueba/prueba.txt
```
Envía el directorio al servidor (máquina B):
```bash
 scp -r /tmp/prueba brais@172.172.172.4:/tmp/
```

### 7. Crear directorio "prueba2" en máquina B y enviar al cliente
En la máquina B, creamos el directorio "prueba2" y el archivo "prueba2.txt":
```bash
mkdir /tmp/prueba2
echo Esta es la prueba 2 > /tmp/prueba2/prueba2.txt
```
Envía el directorio al cliente (máquina A):
```bash
scp -r /tmp/prueba2 alex@172.172.172.3:/tmp/
```

### 8. Transmitir directorios al ordenador anfitrión
Transmitimos los directorios "prueba" y "prueba2" al escritorio del ordenador anfitrión:
En la máquina A:
```bash
 scp -P 2222 -r /tmp/prueba ivan@192.168.1.133:C:\Users\ivan0\OneDrive\Desktop
 ```
 En la máquina B:
 ```bash
 scp -P 2223 -r /tmp/prueba2 ivan@192.168.1.133:C:\Users\ivan0\OneDrive\Desktop
```

### 9. Crear directorio "prueba3" en el servidor y transmitirlo al escritorio
En la máquina B, crea el directorio "prueba3" con 200 archivos .txt:
```bash
mkdir /tmp/prueba3
for i in {1..200}; do echo "Este es el Archivo $i" > /tmp/prueba3/archivo$i.txt; done
```
Transmite el directorio al escritorio del ordenador anfitrión:
 ```bash
 scp -P 2223 -r /tmp/prueba3 ivan@192.168.1.133:C:\Users\ivan0\OneDrive\Desktop
```

### 10. Generar par de claves en el cliente y conexión con el servidor
En la máquina A, generamos un **par de claves SSH**:
```bash
ssh-keygen -t rsa -N "servidorssh"
```
Esto genera una clave con frase de contraseña y se ubicaran por defecto en /home/alex/.ssh/)

Copiamos la **clave pública** al servidor (máquina B):
```bash
 ssh-copy-id brais@172.172.172.4
```
Nos conectamos al servidor **usando la clave**:
```bash
ssh brais@172.172.172.4
```
Nos pedirá la **passphrase** que generamos con las claves=servidorssh
