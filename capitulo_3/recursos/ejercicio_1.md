![WideImg](https://fictizia.com/img/github/Fictizia-plan-estudios-github.jpg)

# [→ Máster en Big Data y Machine Learning](https://fictizia.com/formacion/master-big-data)
### Big Data, Machine Learning, Tensor Flow, Data Science, Data Analytics, Arquitecturas Big Data, Plataformas Big Data

## Capítulo 3 - Ejercicio 01: Manipulación de bases de datos relacionales ##

El objetivo de este ejercicio es crear un contenedor que contenga una base de datos MySQL para el almacenamiento de información y que nos permite realizar consultas sobre ella. 

### Desplegando nuestro contenedor MySQL

Docker nos permite desplegar de forma sencilla contenedores utilizando imágenes previamente creadas, para aprender como reutilizar estas imágenes vamos a desplegar un servidor de bases de datos MySQL. 

**Paso 1: Descargando la imagen**

En primer lugar vamos a descarga la imagen que queremos instalar, para comprobar que imágenes tenemos disponibles podemos ir acceder al listado de imágenes del servidor [MySQL](https://hub.docker.com/r/mysql/mysql-server/) disponibles en docker. 

```
$ docker pull mysql/mysql-server:latest
```

En este caso vamos a descargar la última imagen para ellos utilizamos el tag "latest" que indica que queremos descarga la última opción disponible. Si quisieramos descargar una opción específico podría utilizar el comando pull cambiando el valor del tag. 

```
$ docker pull mysql/mysql-server:5.6
```

A continuación comprobaremos si la imagen se ha descargado correctamente y está disponible en nuestro repositorio local de imágenes, mediante el siguiente comando:

```
$ docker images 
```

Obteniendo la siguiente salida que nos indica que hemos descargado la imagen mysql en su versión (tag) 5.7 hace 6 semanas. 

```
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
mysql                     5.7                 383867b75fd2        6 weeks ago         373MB
```

A continuación se muestran la opciones del comando images:

```
Usage:	docker images [OPTIONS] [REPOSITORY[:TAG]]

List images

Options:
  -a, --all             Show all images (default hides intermediate images)
      --digests         Show digests
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print images using a Go template
      --no-trunc        Don't truncate output
  -q, --quiet           Only show numeric IDs
```

**Paso 2: Descargando la imagen**

A continuación desplegamos el contenedor mediante la utilización del comando run indicando el nombre que le queremos dar al contenedor, que en este caso será mysql1 y la imagen que queremos desplegar. 

```
$ docker run --name mysql1 mysql/mysql-server:latest
```

Tras la ejecución de este comando observamos que nuestro terminal se ha bloqueado y vemos la actividad del contenedor mediante log en tiempo real 

```
[Entrypoint] MySQL Docker Image 8.0.18-1.1.13
[Entrypoint] No password option specified for new database.
[Entrypoint]   A random onetime password will be generated.
[Entrypoint] Initializing database
2019-10-29T08:59:41.201658Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.18) initializing of server in progress as process 21
2019-10-29T08:59:44.851538Z 5 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
```

Para evitar esto debemos utilizar la opción (Deatached Mode) __-d__ que nos permite ejecutar el contenedor en segundo plano (background). 

```
$ docker run --name mysql1 -d mysql/mysql-server:latest
```

**Paso 3: Identificando la contraseña**

Los servidores MySQL obligan a crear una contraseña durante su intalación, en este caso no hemos indicando ningún tipo de constraseña para nuestro servidor, por lo que deberemos identificar cual es la constraseña del usuario root. Para identificar la contraseña debemos visualizar el log mediante el siguiente comando:


```
$ docker logs mysql1
```

Tras la ejecución del comando obteneremos algo como esto 

```
[Entrypoint] MySQL Docker Image 8.0.18-1.1.13
[Entrypoint] No password option specified for new database.
[Entrypoint]   A random onetime password will be generated.
[Entrypoint] Initializing database
2019-10-29T08:36:20.190954Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.18) initializing of server in progress as process 20
2019-10-29T08:36:23.461129Z 5 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
[Entrypoint] Database initialized
2019-10-29T08:36:27.121122Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.18) starting as process 67
```

Esto indica que mi servidor se ha creado sin ningún tipo de password para el usuario root por lo que será muy recomdable cambiarlo. 

**Paso 4: Accediendo al contenedor**

Para modificar el password del usuario root es necesario acceder al contenedor mediante la utilización del siguiente comando:

```
$ docker exec -it mysql1 /bin/bash
```

Aunque en el caso de mysql podemos acceder directamente al servidor MySQL utilizando el comando de acceso de MySQL

```
$ docker exec -it mysql1 mysql -u root -p
```

Al intentar ejecutar este comando por primer vez el contenedor nos solicitará un password que no conocremos, por lo que se generá de manera automática y podrá visualizarse de nuevo en el log obteniendose la siguiente salida:

```
[Entrypoint] MySQL Docker Image 8.0.18-1.1.13
[Entrypoint] No password option specified for new database.
[Entrypoint]   A random onetime password will be generated.
[Entrypoint] Initializing database
2019-10-29T08:36:20.190954Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.18) initializing of server in progress as process 20
2019-10-29T08:36:23.461129Z 5 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
[Entrypoint] Database initialized
2019-10-29T08:36:27.121122Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.18) starting as process 67
2019-10-29T08:36:27.782666Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2019-10-29T08:36:27.803181Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.18'  socket: '/var/lib/mysql/mysql.sock'  port: 0  MySQL Community Server - GPL.
2019-10-29T08:36:27.939200Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: '/var/run/mysqld/mysqlx.sock'
Warning: Unable to load '/usr/share/zoneinfo/iso3166.tab' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/leapseconds' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/tzdata.zi' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/zone.tab' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/zone1970.tab' as time zone. Skipping it.
[Entrypoint] GENERATED ROOT PASSWORD: ]uRF3PRuRuvmansYM0DGeL*orT
```
Donde el password de acceso es __uRF3PRuRuvmansYM0DGeL*orT__. Ahora ya si que podemos entrar en nuestro servidor MySQL

**Paso 5: Accediendo a nuestro servidor desde el exterior**

Ahora tenemos un servidor MySQL operativo pero sólo podemos utilizar si accedemos directamente al contenedor, para ello tenemos que abrir los puertos necesarios para permitir el acceso a los diferentes servicios que ha desplegado nuestro contenedor. En este caso queremos acceder al servidor a través del puerto __3306__ del servidor MySQL, para ello debemos incluir una nueva opción en nuestro comando de arranque

``` 
$ docker run -p 3306:3306 --name mysql1 -d mysql/mysql-server:latest
``` 

Para comprobar si mi contenedor se ha levantado correctamente puedo utilizar el siguiente comando 

```
$ docker ps -a
```

Obteniendo la siguiente salida que nos indica que nuestro contenedor mysql1 está escuchando en nuestro puerto 3306

```
CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS                            PORTS                               NAMES
d80d489b03bb        mysql/mysql-server:latest   "/entrypoint.sh mysq…"   5 seconds ago       Up 4 seconds (health: starting)   0.0.0.0:3306->3306/tcp, 33060/tcp   mysql1
```

**Paso 6: Buenas prácticas**

La forma en la que hemos levantado el servidor MySQL es correcta, pero hay ciertos pasos que se pueden simplificar incluyendo variables de sesion, que son utilizadas para la configuración de ciertos elementos del servidor. Por ejemplo, el password del usuario se puede definir durante la ejecución el comando run, utilizando la variable de entorno **MYSQL_ROOT_PASSWORD** 

``` 
$ docker run --name mysql1 -e MYSQL_ROOT_PASSWORD=fictizia -d mysql/mysql-server:latest 
``` 

### Cargando datos en nuestra base de datos (Semillas)

**Paso 1: Reconfigurando nuestra base de datos**

Vamos a cargar nuestro contenedor creando un volumen para acceder a la información de configuración y añadiendo el password a nuestro usuario root mediante una variable de entorno. Para ello es necesario crear un directorio para almacenar los ficheros de configuración:

``` 
mkdir mysql_files
``` 

A continuación podemos lanzar el contenedor

``` 
$ docker run --name mysql_data --env="MYSQL_ROOT_PASSWORD=prueba" -v ./mysql_files/conf.d:/etc/mysql/conf.d -d mysql/mysql-server:latest
``` 

**Paso 2: Compartiendo los datos de las bases de datos**

Los contenedores son elementos sin información persistente de forma que si reconstruimos el contenedor o creamos una nueva versión de la imagen vamos a perder nuestros datos, por lo que sería muy útil crear un directorio compartido donde se mantenga la información de nuestras bases de datos. Para ello crearemos un nuevo directorio de la siguiente manera:

``` 
mkdir mysql_data
``` 

A continuación podemos lanzar el contenedor añadiendo un nuevo volumen. Si se produce un error al intentar crear un volumen, es necesario modificar  

``` 
$ docker run --name mysql_data --env="MYSQL_ROOT_PASSWORD=prueba" -v ./mysql_files/conf.d:/etc/mysql/conf.d -v ./mysql_data:/var/lib/mysql -d mysql/mysql-server:latest
``` 

**Paso 3: Generando un fichero de despliegue mediante docker compose**

Ahora vamos a crear un fichero de despliegue para cargar los datos en nuestra base de datos y configurar el password del root mediante una variable de entorno. Para ello vamos a crear un fichero de despliegue denominado __docker_compose.yaml__ que contendrá la siguiente información:

```
version: '3.4'
services:
  mysql-db: 
    restart: always
    image: mysql/mysql-server:latest
    container_name: mysql_data
    expose:
      - "3306"
    ports:
      - "3306:3306"
    environment: 
      - MYSQL_ROOT_PASSWORD=fictizia
    networks:
      fictizia:
        ipv4_address: 172.18.10.3
    volumes:
      - ./mysql_files/conf.d:/etc/mysql/conf.d
      - ./mysql-data:/var/lib/mysql

networks:
  fictizia:
    driver: bridge
    driver_opts:
      com.docker.network.enable_ipv6: "true"
    ipam:
      driver: default
      config:
        - subnet: 172.18.10.0/24
```

Una vez creado el fichero, lo ejecutaremoe mediante el comando siguiente comando, generándose una nueva red cuyo nombre se construirá con el nombre incluido en la configuración del fichero añadiéndose el nombre de carpeta que contiene el fichero de despligue.

```
docker-compose -f ./docker-compose.yml up --build -d
```

Este comando levantará un máquina con nuestro servidor de base de datos y lo incluirá en una red denomindo __ejercicio31_fictizia__. Para comprobar que la red ha sido creada correctamente, se puede utilizar el siguiente comando:

```
docker network ls
```

Obteniendo la siguiente salida:

```
NETWORK ID          NAME                    DRIVER              SCOPE
7f7619778d4b        bridge                  bridge              local
453942b4fa3b        host                    host                local
b22524d9d769        none                    null                local
69bac7002f24        ejercicio31_fictizia    bridge              local
```

**Paso 4: Generando nuestro proceso semilla**

Una vez que hemos levantado nuestro servidor MySQL vamos a incluir toda la información necesario para cargar la información de nuestra base de datos de prueba. Para ellos vamos a utilizar la [base de datos](./recursos/ejercicio_1/database.sql) que contiene una pequeña base de datos de prueba. Para por poder realizar el proceso de carga tenemos que crear una nueva carpeta 

```
mkdir scripts
```

A continuación es necesario modificar nuestro fichero de despliegue incluyendo nuesvas opciones. En este caso incluiremos un nuevo volumen que se enlazará con la carpeta __/docker-entrypoint-initdb.d/__. Todos los scripts almacenados en esta carpeta se ejecutarán cuando se despligue el contenedor. 

```
version: '3.4'
services:
  mysql-db: 
    restart: always
    image: mysql/mysql-server:latest
    container_name: mysql_data
    expose:
      - "3306"
    ports:
      - "3306:3306"
    environment: 
      - MYSQL_ROOT_PASSWORD=fictizia
    networks:
      fictizia:
        ipv4_address: 172.18.10.3
    volumes:
      - ./mysql_files/conf.d:/etc/mysql/conf.d
      - ./mysql-data:/var/lib/mysql      
      - ./scripts/:/docker-entrypoint-initdb.d/

networks:
  fictizia:
    driver: bridge
    driver_opts:
      com.docker.network.enable_ipv6: "true"
    ipam:
      driver: default
      config:
        - subnet: 172.18.10.0/24
```


**Paso 5: Interactuando con nuestro base de datos**

Ahora que nuestra base de datos está cargada y desplegada podemos comenzar a interactura con ella. Para ello vamos a construir un connector básico mediante python. Creando un nuevo fichero denominado __connector.py__ en una nueva carpeta denominada __mysql_connector__. 

```
mkdir mysql_connector
```

A continuación es necesario crear un entorno virtual mediante el siguiente comando

```
virtualenv .
```

y activarlo utilizando el siguiente comando:

```
source ./bin/activate
```

Una vez creado el entorno virtual podemos construir nuestro connector para mysql utilizando la librería __mysql-connector-python__ que debe ser instalada mediante pip utilizando el siguiente comando:

```
pip3 install mysql-connector-python
```

Tras la instalación del driver de mysql podemos escribir nuestro primer script de conexión, utilizando el siguiente código:

```
import mysql.connector
from mysql.connector import Error

try:
    connection_config = {
        'user': 'root',
        'password': 'fictizia',
        'host': 'localhost',
        'database': 'universidad',
        'raise_on_warnings': True,
        'use_pure': False,
        'autocommit': True,
        'pool_size': 5
    }

    connection = mysql.connector.connect(**connection_config)

    if connection.is_connected():
        cursor = connection.cursor()
        cursor.execute("SELECT * FROM asignatura;")
        records = cursor.fetchall()
        
        for record in records:
            print(record)

except Error as e:
    print("Error while connecting to MySQL", e)
finally:
    if (connection.is_connected()):
        cursor.close()
        connection.close()
        print("MySQL connection is closed")
```
