1. ### **Creamos las máquinas virtuales**

* **Clonamos la máquina base dos veces**:

   * Usamos la opción de **clonación enlazada**.
   * Marcamos la opción para **Generar nuevas direcciones MAC** en opciones adicionales. Esto evita la duplicidad de las MAC en las tarjetas de red, con los problemas que esto generaría.

* **Configuramos el segundo adaptador de red en ambas VMs**:
   * En VirtualBox, ve a **Configuración > Red > Adaptador 2** en las dos máquinas creadas.
   * Lo activamos y seleccionamos:
     * **Conectado a:** Red Interna.
     * **Nombre:** `intnet` (que es el que viene por defecto). 

***

2. ### **Configuramos la red enp0s8 de forma no permanente**

Con las máquinas encendidas, accedemos por terminal a ambas (Ctrl+Alt+T).

**Red IP de la red interna: 192.168.100.0/24**

* Dirección usable mínima: `192.168.100.1` (primera IP reservada generalmente para el gateway).
* **Asignaremos**:

  * Máquina A: `192.168.100.2`
  * Máquina B: `192.168.100.3`

* #### ️ En Máquina A:

```shell
sudo ip addr add 192.168.100.2/24 dev enp0s8
sudo ip link set enp0s8 up
```

* #### ️ En Máquina B:

```shell
sudo ip addr add 192.168.100.3/24 dev enp0s8
sudo ip link set enp0s8 up
```

---

3. ### **Comprobamos la conectividad**

Desde Máquina 1:

```shell
ping 192.168.100.3
```

Desde Máquina 2:

```shell
ping 192.168.100.2
```

> **Nota:** Esto funcionará por tiempo limitado ya que están configuradas de forma temporal.

---

4. ### **Hacemos que la configuración sea persistente**

#### En la máquina A (usando **Netplan**)

* Hacemos una **copia** del archivo de configuración de Netplan. Este paso es aconsejable hacerlo siempre que toquemos algún archivo de configuración, ya que si cometemos algún error, tendremos el **respaldo** del archivo antiguo.

    ```shell
    sudo cp /etc/netplan/01-netcfg.yaml /etc/netplan/01-netcfg.yaml.old
    ```

* **Editamos** el archivo de configuración de Netplan:

    ```shell
    sudo nano /etc/netplan/01-netcfg.yaml
    ```

* **Añadimos** este contenido al archivo:

    ```
    network:
      version: 2
      ethernets:
        enp0s8:
          dhcp4: no
          addresses:
            - 192.168.100.2/24
    ```

* **Aplicamos** la configuración:

    ```shell
    sudo netplan apply
    ```

---

#### En la máquina B (usando NetworkManager)

1. Abrimos mediante interfaz gráfica la **configuración de red**:

2. Buscamos la interfaz **enp0s8**:
    * Entramos en su configuración en la pestaña **IPv4**.
    * Método: **Manual**.
    * **Dirección:** 192.168.100.3
    * **Máscara:** 255.255.255.0
    * Salimos de la configuración.

3. **Apagamos y encendemos** la interfaz para aplicar los nuevos cambios.

4. Verificamos:
   * Reiniciamos el equipo y comprobamos con:
     ```shell
     ip addr show enp0s8
     ```

