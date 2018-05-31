# Mini proyecto Sistemas Operativos

**Universidad ICESI**  
**Curso:** Sistemas Operativos  
**Docente:** Daniel Barragán C.  
**Tema:**  LXC/LXD Containers  


**Código:** A00054120  
**Nombre:** Laura Isabella Tabares

**Código:** A000569092  
**Nombre:** Estiven Landázury  

**Código:** A00242068  
**Nombre:** Angie Lorena Pérez 

**Repositorio GitHub:** https://github.com/estivenLandazury/so-project/tree/so-project  


## Objetivos

Conocer las caracteristicas principales de la virtualización por medio de contenedores LXC/LXD
Virtualizar servicios empleando contenedores LXC/LXD
Realizar configuraciones sobre sistemas operativos para el acceso a servicios (network, load balancing)


## Aproviosionamiento básico de máquina virtual: se instala el sistema operativo Ubuntu 16.04.4, se configura las interfaces de red, y se asignan 4 núcleos.

* La instalación de Ubuntu 16.04 que se encuentra en: http://releases.ubuntu.com/16.04/ se realiza sobre la herramienta VirtualBox.  

Con la siguiente configuración en los núcleos:
![](imagenes/nucleosCPU.png)

y la siguiente configuración de red:
![](imagenes/configuracionRed.png)  

Que terminará con un proceso exitoso en la instalación y configuración.  

**NOTA:** no olvides instalar ssh y activar el servidor.


## Instalación de LXC/LXD con permisos para el usuario operativos.

* Después de completar la instalación del sistema operativo ubuntu y crear el usuario de operativos.

 



Se ejecuta el comando: para instalar hypervisor LXD.

```Console  
$ sudo apt install lxd lxd-client
```

Hypervisor LXD es un administrador de contenedores de linux. Complemento que facilita el uso de contenedores por medio de comandos simples.  

* Tambien se instala el paquete de bridge-utils que contiene una utilidad necesaria para crear y administrar interfaces bridge. Para instalar este paquete se corre el siguiente comando:

```Console
$ sudo apt-get install bridge-utils
```

* Finalmente se instala **zfsutils-linux** que sirve para administrar los contenedores. Este paquete se instala con el siguiente comando.

```Console
$ sudo apt-get install zfsutils-linux
```
Ahora se agrega el usuario operativos al grupo de sudoers para que permita usar sudo al usuario operativos. LXC viene instalado previamente en Ubuntu.  
```
usermod -aG sudo operativos   
```  
Después se agrega el  usuario operativos al grupo lxd, luego se reloguea a la sesion operativos para que se actualize el grupo.  
```
sudo usermod --append --groups lxd operativos
``` 

### ¿Qué es un storage pool?

El **storage pool** (grupo de almacenamiento) es un conjunto de discos en un entorno de almacenamiento compartido, y que trabajen como un solo para el sistema. Las agrupaciones de almacenamiento pueden configurarse en diferentes tamaños y proporcionar una serie de beneficios, que incluyen mejoras de rendimiento, administración y proteccion de datos. Las agrupaciones pueden aprovisionarse para incluir cualquier cantidad de capacidad y utilizar cualquier combinación de espacio de almacenamiento físico en una red de area de almacenamiento(SAN). En entornos de servidor virtual, las máquinas virtuales (VM) se pueden almacenar en grupos dedicados, lo que garantiza que las máquinas virtuales críticas tengan acceso a la cantidad adecuada de almacenamiento[1].



### ¿Qué es ZFS y cuáles son sus ventajas?

**ZFS** es un sistema de archivos y administrador de volúmenes lógicos, construido por Sun Microsystems. para dirigir y controlar la ubicación, el almacenamiento y la recuperación de datos en sistemas informáticos. ZFS usa un esquema de direccionamiento de 128 bits y puede almacenar hasta 275 mil millones de TB por grupo de almacenamiento.

Algunas de las características mas relevantes son:

 * Integridad de los datos: ZFS está diseñado con un enfoque en la integridad de los datos. Es decir, está diseñado para proteger los datos en el disco contra la corrupción de datos silenciosos causada por podredumbre de bits, picos actuales, errores en el firmware del disco, escrituras fantasmas, lecturas / escrituras mal dirigidas, errores de paridad de memoria entre la matriz y la memoria del servidor, errores del controlador y sobrescribe accidentalmente. ZFS garantiza que los datos siempre sean coherentes en el disco utilizando una serie de técnicas, incluida la copia sobre escritura. Lo que esto significa es que cuando se cambian los datos, no se sobrescribe; siempre se escribe en un nuevo bloque y se suma la suma de comprobación antes de que los punteros a los datos se cambien. Los datos antiguos pueden conservarse, creando instantáneas de los datos a lo largo del tiempo a medida que se realizan los cambios. Las escrituras de archivos que usan ZFS son transaccionales: todo o nada está escrito en el disco.
Esta característica permite que los archivos se mantengan correctamente, siendo capaz de detectar datos o archivos corruptos y arreglarlos automaticamente, esto se logra a través de un modelo transaccional.

  * Depuración: ZFS puede programarse para realizar un "scrub" sobre todos los datos en un pool de almacenamiento, verificando cada dato con su suma de comprobación correspondiente para verificar su integridad, detectar cualquier corrupción de datos silenciosos y corregir cualquier error donde sea posible.
Cuando los datos se almacenan de forma redundante, en una matriz espejada o de tipo RAID, se pueden autorreparar automáticamente y sin intervención del administrador. Dado que se registran daños en los datos, ZFS puede sacar a la luz los defectos en los módulos de memoria (u otro hardware) que hacen que los datos se almacenen en los discos duros de forma incorrecta.

La depuración tiene una prioridad baja de E / S, por lo que tiene un efecto mínimo en el rendimiento del sistema y puede funcionar mientras se usa el grupo de almacenamiento.

  * Snapshots: Son copias del sistema de archivo de fácil creación, permitiendo hacer respaldo de la información o copias de seguridad de forma casi inmediata, con la característica de que son sólo de lectura.

  * Clones: También se pueden crear instantáneas grabables ("clones"), lo que da como resultado dos sistemas de archivos independientes que comparten un conjunto de bloques. A medida que se realizan cambios en cualquiera de los sistemas de archivos clonados, se crean nuevos bloques de datos para reflejar esos cambios, pero todos los bloques sin cambios se continúan compartiendo, sin importar cuántos clones existan. Esto es posible debido al diseño de copiado sobre escritura.

  * Envío y recepción de Snapshots:Los Snapshots de los sistemas de archivos ZFS y los volúmenes se pueden enviar a hosts remotos a través de la red. Esta secuencia de datos puede ser un sistema de archivos completo o un volumen, o puede ser los cambios desde la última vez que se envió. Al enviar solo los cambios, el tamaño del flujo depende del número de bloques cambiados entre las instantáneas. Esto proporciona una estrategia muy eficiente para sincronizar copias de seguridad.[2]

* Para la configuración del puente LXD (LXD bridge configuration), por medio del cual, los contenedores se van a comunicar con el host y posteriormente con la internet. Se procede a ejecutar el siguiente comando.

```
$ sudo lxd init
```

Con el cual se configura la inicialización de los contenedores, como se puede ver en las siguientes imágenes.  

PASO 1 e indicar **SI**:
 ![](imagenes/configuracionBrigde1.png)  
 PASO 2 se selecciona el nombre de la interfaz:    
 ![](imagenes/Brigde2.png)  
 PASO 3 se indica **SI** para proveer la IPV4 para la conexión con otra redes:    
 ![](imagenes/brigde4.png)  
 PASO 4 se asigna la siguiente dirección IPV4:    
 ![](imagenes/brigde41.png)  
 PASO 5: se provee el numero de mascar de red, para delimitar el ambito de red:  
 ![](imagenes/brigde5.png)  
 PASO 6 se provee el primer DHCP que permite configurar la red de manera dinámica, es decir sin una intervencón especial:    
 ![](imagenes/brigde6.png)    
 PASO 7 se provee el último DHCP:  
 ![](imagenes/brigde7.png)    
 PASO 8 se provee el número de clientes para el  DHCP:     
 ![](imagenes/brigde8.png)   
 PASO 9 se slecciona que si se desea NAT para el protocolo IPV4 para el intercambio de paquetes entre redes:      
 ![](imagenes/brigde9.png)  
 PASO 10 el protocolo de IPV6 no se selecciona:        
 ![](imagenes/bride10.png)  
 Finalmente se puede observar en la imagen siguiente que se ha configurado exitosamente el contenedor:  
 ![](imagenes/brigde11.png)    
 
## Creación de contenedores con servicio web (dos contenedores web)
* Para crear los contenedor web ejecutamos el comando.

```
$ lxc launch ubuntu:16.04 server1
$ lxc launch ubuntu:16.04 server2
```

* Al ser la primera vez que se crea un contenedor en la máquina, el comando también descarga una imagen de ubuntu 16.04, que puede ser utilizada como plantilla para otros contenedores.
Una vez creados los contenedoresweb se ingresa a cada uno de estos para configurar el servidor

``` $ lxc exec webserver -- sudo --login --user server1 ```

Ingresamos al Shell del servidor a configurar y allí se instala **Nginx** para relizar la configuración 

``` $ sudo apt-get update ```
```$ sudo apt-get install nginx```

Al terminar la configuraciòn de los dos servidores web, nos dirigimos a la carpeta /var/www/html/ y se edita el archivo index.nginx-debian.html con los datos queremos vizualizar en la página principal cuando se acceda al servidor web.

Imagen… servidor 1
Imagen …Servidor 2

Para cumplir con la asignación de un procesador único para cada servicio web, se ejecutan los siguientes comandos:
Para el servidor1
``` $ lxc config set server1 limits.cpu 1´´

Para el servidor2
´´´$ lxc config set server2 limits.cpu 1 ´´



## Balanceador de carga
Se crea el balanceador por medio del comando 
``` $ lxc launch ubuntu:x loadBalancer ```
Se accede a este por medio del Shell para configurarlo, con el comando  ``` $ lxc exec webserver -- sudo --login --user  nombre del balanceador
Aquí también se debe instalar Nginx y se configura como balanceador de carga editando el archivo

```$ sudo nano /etc/nginx/conf.d/load-balancer.conf```


En este archivo se pone el siguiente contenido

´´upstream backend {
   least_conn;
   server 10.60.248.73; cambiar
   server 10.60.248.86; cambiar
}



server {
   listen 80; 

   location / {
      proxy_pass http://backend;
   }
}

Lo que se configuro aquí fue el bloque de servidores a los cuales llegaran las peticiones. Con el comando ´´least conn´´ se balancea la carga para los dos servidores. Se guarda el archivo.

Debemos eliminar la carpeta que aloja sitios web, para que, cuando lleguen las solicitudes al balanceador no retorne la pagina html, sino que pasen a los servidores. Adicionalmente el servicio nginx se debe reiniciar, asi el balanceador funcionara correctamente.
´´ $ sudo rm /etc/nginx/sites-enabled/default´´

´´ $ sudo service nginx restart´´
Imagen si la hay…


**Configuración del balanceador para recibir conexiones remotas: ** Para esto se debe alterar la iptable, que es la que permite el intercambio de tráfico entre el http del host y el balanceador de carga.

´´sudo iptables -t nat -A PREROUTING -p tcp -m conntrack --ctstate NEW --dport 80 -j DNAT --to-destination 10.60.248.59:80´´



**Validación de que el servicio para conexión remota y el balanceador están activos**

Imagen 

**Salida del comando lxc list con los contenedores creados y sus direcciones IP **
Imagen 

## Pruebas del funcionamiento del balanceador

Por medio del comando curl se hacen las peticiones a cada uno de los servicios web a través del balanceador, como se puede visualizar en la siguiente imagen:

Imagen 
Para realizar las pruebas de stress utilizamos la herramienta siege, la cual se instala de:


``` wget http://download.joedog.org/siege/siege-latest.tar.gz ```



** Configuración de los servidores:***

 servidores web con uso de CPU al 50%
* Para modificar el porcentaje de CPU vamos a ejecutar los siguientes comandos.

```lxc config set webserver1 limits.cpu.allowance 50%```

```
lxc config set webserver2 limits.cpu.allowance 50%
```

* Por medio de siege ejecutamos el siguiente comando, el cual asigna una concurrencia de 255 usuarios.

```
siege -c 255 10.60.248.59 --reps=1
```

* En el comando anterior indicamos la IP de nuestro balanceador de carga para que apunte a dicho contenedor.

* A continuación, se muestra el resultado de las pruebas.

![](imgs/ps/captura_1.PNG)

** servidores web con uso de CPU al 100%**

* Para poner la cpu de los servidores al 100%, ejecutaremos:

```
lxc config set server1 limits.cpu.allowance 100%
```

```
lxc config set server2 limits.cpu.allowance 100%
```

* Ahora volveremos a ejeutar el mismo comando de siege para ejecutar las pruebas de estrés. El resultado de las pruebas es el siguiente.

![](imgs/ps/captura_2.PNG)

* Podemos observar que se reduce que la concurrencia y el throughput con el resultado anterior disminuye

** Servidores web con 64MB de memoria RAM**
* Para configurar la memoria RAM de los contenedores web a 64MB, ejecutamos:

```
lxc config set server1 limits.memory 64MB
```

```
lxc config set server2 limits.memory 64MB
```

* Luego, usando el mismo comando para pruebas de estrés, ejecutaremos las mismas pruebas usando la utilidad siege. A continuación, se muestra el resultado de las pruebas.

![](imgs/ps/captura_3.PNG)

**Pruebas de estrés servidores web con 128MB de memoria RAM**

* Ejecutamos los siguientes comando para modificar el límte de memoria RAM utilizada por ambos servidores web a 128MB.

```Console
lxc config set webserver1 limits.memory 128MB
```

```Console
lxc config set webserver2 limits.memory 128MB
```  

## REFERENCIAS
[1] https://searchservervirtualization.techtarget.com/definition/storage-pools
[2] http://www.zeta.systems/zetavault/what-is-zfs/


