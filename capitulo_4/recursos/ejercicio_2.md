![WideImg](https://fictizia.com/img/github/Fictizia-plan-estudios-github.jpg)

# [→ Máster en Big Data y Machine Learning](https://fictizia.com/formacion/master-big-data)
### Big Data, Machine Learning, Tensor Flow, Data Science, Data Analytics, Arquitecturas Big Data, Plataformas Big Data

## Capítulo 4 - Ejercicio 02: Trabajando con datos en bathc ##

El objetivo de este ejercicio es desplegar un cluster de Apache Spark compuesto por un nodo maestro y tres nodos esclavos (trabajadores) con el fin de ejecutar un proceso de manipulación y acceso a datos recolectados mediante un proceso de tipo batch. 

### Recursos ###

Para el desarrollo de este ejercicio vamos a utilizar las diferentes tecnologías y recursos.

- [Python](https://www.python.org/) como lenguaje de programación para el desarrollo de nuestros procesos. 
- [Apache Spark]() como sistema de procesamiento y acceso a la información.
- [PySpark]() como librería de interacción entre Apache Spark y nuestro nodos del cluster Spark.
- [Docker](https://docs.docker.com/) para construir el contenedor donde se desplegará nuestro servidor. 

### Solución paso a paso ###

**Paso 1: Creación del proyecto**

Para la creación del proyecto se recomienda crear una nueva carpeta denominado ejercicio_1 que deberá contener los siguientes archivos y directorios.

```
drwxr-xr-x 7 momartin momartin 4096 nov  1 11:55 .
drwxr-xr-x 8 momartin momartin 4096 nov  1 11:55 ..
drwxrwxr-x 2 momartin momartin 4096 nov  1 11:54 bin
-rw-r--r-- 1 momartin momartin  288 oct 31 21:30 Dockerfile
drwxrwxr-x 2 momartin momartin 4096 nov  1 11:53 include
drwxrwxr-x 3 momartin momartin 4096 nov  1 11:53 lib
drwxrwxr-x 2 momartin momartin 4096 nov  1 11:53 local
-rw-r--r-- 1 momartin momartin  612 oct 31 21:11 requirements.txt
drwxr-xr-x 4 momartin momartin 4096 nov  1 12:56 src
```

Donde se deberán la carpeta config se corresponde con la configuración de las diferentes variables de entorno de los esclavos (workers) y el maestro. 

**Paso 2: Definión del fichero de configuración de los esclavos**

El segungo paso consiste en definir las diferentes variables de configuración del nodos esclavos. Es objetivo de crear un fichero común para todos los esclavos permite minimizar errores en la configuración utilizando exactamente la misma configuración para todos. Para ellos crearemos un fichero denominado __config_worker.sh__ en en la carpeta __config_spark__ con la siguiente información:

```
#Informacion Maestro

SPARK_MASTER=spark://fictizia-spark-master:7077

#Parametros de configuracion de los workers

SPARK_WORKER_CORES=1
SPARK_WORKER_MEMORY=1G
SPARK_DRIVER_MEMORY=128m
SPARK_EXECUTOR_MEMORY=256m
```

**Paso 3: Generación del fichero de despliegue I**

A continuación es necesario crear nuestro fichero de despliegue del cluster de Apache Spark. Para el despligue de los diferentes nodos vamos a utilizar la versión desplegada por [bde2020](https://hub.docker.com/r/bde2020/spark-master/) cuya versión es la más moderna de las disponibles en docker hub. Con el fin de simplificar la explicación, vamos a dividir el proceso de creación en dos fases. La primera fase consistirá en desplegar el nodo maestro y la red de comunicaiones y la segunda fase consistirá en desplegar los nodos esclavos (workers).

Para la primera fase debemos crear el fichero __docker_compose.yml__ que contendrá la siguiente información:

```
version: "3.4"
services:

  fictizia-spark-master:
    restart: always
    image: bde2020/spark-master:latest
    container_name: fictizia-spark-master
    hostname: fictizia-spark-master
    ports:
      - "8080:8080"
      - "7077:7077"
      - "6066:6066"      
    expose:
      - 8080
      - 7077
      - 6066
    networks: 
      fictizia:
        ipv4_address: 10.18.0.2
    volumes:
       - ./spark-apps:/opt/spark-apps
       - ./spark-data:/opt/spark-data
       - ./spark-conf/master:/conf       
    environment:
      - "SPARK_LOCAL_IP=fictizia-spark-master"
      - "SPARK_MASTER_PORT=7077"
      - "SPARK_MASTER_WEBUI_PORT=8080"
      - "SPARK_MASTER_LOG=/spark/logs"
      - "SPARK_CONF_DIR=/conf"      

networks:
  fictizia:
    driver: bridge
    ipam:
     driver: default
     config:
       - subnet: 10.18.0.0/16
```

Para ello crearemos un red denominada __fictizia__ cuya dirección de subred será 10.18.0.0 y un único nodo denominado __spark-master__ para la cual utilizaremos la imagen spark-master:latest. Además desplegaremos tres puertos:

- Puerto 8080 donde se desplegara la aplicación web de gestión de Apache Spark. 
- Puerto 7077 donde escucha (por defento) el maestro del cluster de spark.
- Puerto 6066 donde se encuentra desplegada la API de servicio del nodo maestro para que se comuniquen los esclavos con el. 

Además, crearemos dos volumenes compartidos para los datos y las diferentes aplicaciones que utilizará nuestro cluster de Spark; y un conjunto de variables de sessión para la configuración del cluster:

- SPARK_LOCAL_IP para la definición de la dirección IP del Maestro.
- SPARK_MASTER_PORT para la definición del puesto del nodo Maestro.
- SPARK_MASTER_WEBUI_PORT para definir el puerto de la aplicación web.
- SPARK_MASTER_LOG para la localización de los archivos de logs.

Una vez construido el fichero de despliegue debemos lanzar nuestro cluster mediante el siguiente comando:

```
docker-compose -f docker-compose.yml up --build -d
```

Una vez desplegado nuestro nodo maestro podremos comprobar el correcto funcionamiento del nodo mediante el comando docker ps obteniéndose la siguiente salida:

```
CONTAINER ID        IMAGE                         COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
7706ecdcee8f        bde2020/spark-master:latest   "/bin/bash /master.sh"   10 minutes ago      Up 10 minutes       0.0.0.0:7077->7077/tcp, 6066/tcp, 0.0.0.0:8080->8080/tcp   fictizia-spark-master

```

Además podremos acceder a la página web donde se puede observar el estado de nuestro cluster spark a través de la url http://localhost:8080

![Interfaz de web del cluster Spark](../img/spark_web_1.png)


**Paso 4: Generación del fichero de despliegue II**

Una vez que hemos conseguido desplegar nuestro Maestro, pasaremos a desplegar nuestros esclavos (trabajadores). La distribución más común para desplegar un entorno de pruebas de Apache Spark consiste en desplegar tres nodos esclavos, por lo que tendremos que introducir tres nuevos nodos en nuestro fichero de despliegue. Para ello debemos incluir la información de cada uno de nuestros workers en el fichero de configuración, siendo la configuración de cada uno de los nodos la siguiente:

```
fictizia-spark-worker-1:
  restart: always
  image: bde2020/spark-worker:latest
  container_name: fictizia-spark-worker-1
  hostname: fictizia-spark-worker-1
  depends_on:
    - fictizia-spark-master
  ports:
    - "8081:8081"
  env_file: ./config_spark/config_worker.sh
  environment:
    - "SPARK_LOCAL_IP=fictizia-spark-worker-1"        
  networks: 
    fictizia:
      ipv4_address: 10.18.0.3
  volumes:
     - ./spark-apps:/opt/spark-apps
     - ./spark-data:/opt/spark-data
     - ./spark-conf/worker-1:/conf         
     
```

Para cada uno de los trabajadores es necesarios definir una serie de variables exclusivas que son el nombre del contenedor y las siguientes propiedades:

- container_name: fictizia-spark-worker-[id del nodo]
- hostname: fictizia-spark-worker-[id del nodo]
- ports: "8081:8081" donde para cada uno de los nodos es necesario definir un puesto de mapeo diferente, por ejemplo para el primero trabajador mapearemos sobre el puerto 8081, para el segundo en el 8082 y así sucesivamente. 

Una vez incluidos nuestros tres trabajadores en el fichero de despliegue podremos desplegar nuestro cluster completo mediante el siguiente comando:

```
docker-compose -f docker-compose.yml up --build -d
```

Una vez desplegado nuestro nodo maestro podremos comprobar el correcto funcionamiento del nodo mediante el comando docker ps obteniéndose la siguiente salida:

```
CONTAINER ID        IMAGE                         COMMAND                  CREATED              STATUS              PORTS                                                      NAMES
3458f67efc46        bde2020/spark-worker:latest   "/bin/bash /worker.sh"   8 seconds ago        Up 7 seconds        0.0.0.0:8083->8081/tcp                                     fictizia-spark-worker-3
0306c62cd568        bde2020/spark-worker:latest   "/bin/bash /worker.sh"   About a minute ago   Up About a minute   0.0.0.0:8082->8081/tcp                                     fictizia-spark-worker-2
34c90136b7c6        bde2020/spark-worker:latest   "/bin/bash /worker.sh"   9 minutes ago        Up 9 minutes        0.0.0.0:8081->8081/tcp                                     fictizia-spark-worker-1
cd02e44b8fe6        bde2020/spark-master:latest   "/bin/bash /master.sh"   9 minutes ago        Up 9 minutes        0.0.0.0:7077->7077/tcp, 6066/tcp, 0.0.0.0:8080->8080/tcp   fictizia-spark-master

```

Además podremos acceder a la página web donde se puede observar el estado de nuestro cluster spark a través de la url http://localhost:8080

![Interfaz web del cluster Spark](../img/spark_web_2.png)


**Paso 4: Desplegando nuestro notebook de desarrollo**

A la hora de construir un proceso que utilice las capacidades del cluster de Spark es necesario realizar una serie de configuraciones en el contenedor. Aunque es posible simplificar de forma sencilla todo esto mediante la utilización de un servidor de notebooks de tipo (jupyter)[https://jupyter.org/]. Jupyter notebooks es un sistema de despliegue de entornos de desarrollo sobre python mediante la utilización de una interfaz web. 

![Interfaz web Jupyter Notebooks](../img/jupyter.gif)

Para poder desplegar nuestro entorno en jupyter vamos a utilizar un contenedor docker que ya ha sido configurado para apache spark. Este contenedor puede encontrarse en el docker hub de (Jupyter)[https://hub.docker.com/r/jupyter/pyspark-notebook/] que ya nos configura todos las variables de entorno necesarias para trabajar con pyspark. Para ellos debemos incluir un nuevo nodo en nuestro ficheros de despliegue:

```

  jupyter-spark:
    image: jupyter/pyspark-notebook:latest
    container_name: notebooks
    ports:
      - "8888:8888"
      - "4041-4080:4041-4080"
    volumes:
      - ./notebooks:/home/jovyan/work/notebooks/
    networks: 
      fictizia:
        ipv4_address: 10.18.0.10
```

Una vez introducido el nuevo contenedor podemos desplegar mediante el comando de despligue

```
docker-compose -f docker-compose.yml up --build -d
```

Una vez desplegado nuestro nodo maestro podremos comprobar el correcto funcionamiento del nodo mediante el comando docker ps obteniéndose la siguiente salida:

```
CONTAINER ID        IMAGE                         COMMAND                  CREATED              STATUS              PORTS                                                      NAMES
3458f67efc46        bde2020/spark-worker:latest   "/bin/bash /worker.sh"   8 seconds ago        Up 7 seconds        0.0.0.0:8083->8081/tcp                                     fictizia-spark-worker-3
0306c62cd568        bde2020/spark-worker:latest   "/bin/bash /worker.sh"   About a minute ago   Up About a minute   0.0.0.0:8082->8081/tcp                                     fictizia-spark-worker-2
34c90136b7c6        bde2020/spark-worker:latest   "/bin/bash /worker.sh"   9 minutes ago        Up 9 minutes        0.0.0.0:8081->8081/tcp                                     fictizia-spark-worker-1
cd02e44b8fe6        bde2020/spark-master:latest   "/bin/bash /master.sh"   9 minutes ago        Up 9 minutes        0.0.0.0:7077->7077/tcp, 6066/tcp, 0.0.0.0:8080->8080/tcp   fictizia-spark-master

```

Además podremos acceder a la página web donde se puede observar el estado de nuestro cluster spark a través de la url http://localhost:8888

![Interfaz web acceso Jupyter Notebooks](../img/notebook.png)

Al intentar acceder a nuestro servidor de notebooks, nos solicitará un token de acceso que podemos obtener si abrirmos el log del contenedor que ha desplegado el servidor. Para ello deberemos utilizar sel siguiente comando: 


```
docker logs notebooks
```

Donde podremos encontrar el token de acceso a nuestro servidor de notebooks

```
Executing the command: jupyter notebook
[I 15:06:06.065 NotebookApp] Writing notebook server cookie secret to /home/jovyan/.local/share/jupyter/runtime/notebook_cookie_secret
[I 15:06:08.152 NotebookApp] JupyterLab extension loaded from /opt/conda/lib/python3.7/site-packages/jupyterlab
[I 15:06:08.152 NotebookApp] JupyterLab application directory is /opt/conda/share/jupyter/lab
[I 15:06:09.954 NotebookApp] Serving notebooks from local directory: /home/jovyan
[I 15:06:09.954 NotebookApp] The Jupyter Notebook is running at:
[I 15:06:09.955 NotebookApp] http://d5278ebab486:8888/?token=a8b0c4fb268b3ac848f2ee5fcd6973a7d9cd89d274a5c9e7
[I 15:06:09.955 NotebookApp]  or http://127.0.0.1:8888/?token=a8b0c4fb268b3ac848f2ee5fcd6973a7d9cd89d274a5c9e7
[I 15:06:09.955 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 15:06:09.965 NotebookApp] 
    
    To access the notebook, open this file in a browser:
        file:///home/jovyan/.local/share/jupyter/runtime/nbserver-9-open.html
    Or copy and paste one of these URLs:
        http://d5278ebab486:8888/?token=a8b0c4fb268b3ac848f2ee5fcd6973a7d9cd89d274a5c9e7
     or http://127.0.0.1:8888/?token=a8b0c4fb268b3ac848f2ee5fcd6973a7d9cd89d274a5c9e7

```

Para poder acceder tendremos que utilizar la url que aparece en la última linea o introducir el token a través del formulario que nos aparecio en la anterior pantalla del interfaz web donde ya podremos crear nuestros notebooks 

![Interfaz web acceso Jupyter Notebooks](../img/notebook_2.png)

Además podremos acceder a la página web donde se puede observar el estado de nuestro servidor de notebooks a través de la url http://localhost:8888


**Paso 5: Creando nuestro primer script en nuestro servidor jupyter**

Ya hemos desplegado nuestro servidor jupyter y podemos comenzar a trabajar con nuestros primeros scripts en python, para ello debemos crear un nuevo notebook. Para ello utilizaremos el botón __new__ que se encuentra en la esquina superior derecha. Para ello selecionaremos un nuevo archivo que se ejecutará sobre python 3, ya que es la única opción que tenemos disponible. Una vez que hayamos creado nuestro notebooks, podemos introducir código python como si estuvieramos creando un archivo .py. Por lo que comenzaremos creando nuestro primer script en pyspark. Para ello, debemos introducir el siguiente código fuente:

```
from pyspark.sql import SparkSession

if __name__ == "__main__":

    spark = SparkSession.builder.master('spark://10.18.0.2:7077').appName('spark-cluster').getOrCreate()
    exit(0)
```

Donde debemos introducir en este caso la dirección IP del nodo máster __spark://10.18.0.2:7077__ bajo el protocolo spark que se encuentra escuchando en el puerto 7077 como configuramos previamente en el fichero de despliegue. A continuación podemos ejecutar el script pulsando el boton de __run__ que se corresponde con la flecha negra en el panel de control de la zona superior. Pero al intentar ejecutar este código obtendremos el siguiente error:

![Interfaz web acceso Jupyter Notebooks](../img/notebook_error.png)

Este error se debe a que la versión de python que utiliza el cluster de spark que hemos desplegado es la 2.7, por lo que al intentar ejecutar nuestro script de spark no somos capaces de ejecutarlo ya que las versiones de python que utilizamos en ambos servicios es diferente. 


**Paso 7: Creando nuestro propio servidor de servidores notebooks**

Con el fin de reparar este error tenemos que crear nuestro propio servidor de notebooks, para ellos deberemos crear una nueva carpeta en nuestro ejercicio, que denominaremos __jupyter__. Dentro de esta nueva carpeta tendremos que construir un archivo de generación de nuestro contenedor denominado __Dockerfile__ que deberá contener las siguientes instrucciones:

```
FROM jupyter/pyspark-notebook:latest

RUN conda create --quiet --yes -p $CONDA_DIR/envs/python2 python=2.7 ipython ipykernel kernda && conda clean --all -f -y
USER root
RUN $CONDA_DIR/envs/python2/bin/python -m ipykernel install && $CONDA_DIR/envs/python2/bin/kernda -o -y /usr/local/share/jupyter/kernels/python2/kernel.json
USER $NB_USER
```

Mediante este fichero extenderemos la funcionalidad del servidor de notebooks incluyendo la versión 2.7 de python con el fin de poder ejecutar nuestros scripts sobre nuestro cluster de tipo Apache Spark. A continuación es necesario modificar nuestro fichero de despligue modificando la estructura del contenedor notebooks, por lo que sustituiremos el código que hemos construido posteriormente por el siguiente:


```
  jupyter-spark:
    restart: always
    build: ./jupyter
    container_name: notebooks
    ports:
      - "8888:8888"
      - "4041-4080:4041-4080"
    volumes:
      - ./notebooks:/home/jovyan/work/notebooks/
    networks: 
      fictizia:
        ipv4_address: 10.18.0.10
```

Ahora podemos volver a desplegar nuestros notebooks en python 2.7 y podemos ejecutar correctamente nuestros scripts. 

**Paso 8: Creando nuestro stream de datos**

Como vimos en el ejercicio anterior, los RDD son los contenedores básicos de información utilizados por Apache Spark. Estos __super conjuntos__ de datos no permiten trabajar en paralelo de forma que podamos operar y manipular nuestro datos, pero para construir estos conjuntos de datos debemos esperar a que el conjunto esté complemente creado. En ocasiones debemos experar demasiado tiempo para poder generar nuestro conjuntos de datos, por lo que una opción es procesar la información en tiempo real según la vamos recibiendo. Para poder realizar este proceso, podemos utilizar un D-Stream que es como una secuencia de datos a nivel temporal (eventos), esto produce un discontinuo de datos, que dependerá de la información que vayamos recibiendo. Cada uno de los elementos de la secuencia de datos puede ser considerado como un evento lo discretizamos, lo congelamos en un momento, luego otro momento y luego otro momento. 

Esto nos permite no tener que esperar a un super para crear un __super conjunto__ de datos (RDD), sino que vamos procesando pequeños conjuntos de datos, de forma más rapida. Para ellos vamos a crear un nuevo proceso en Apache Spark utilizar la clase __[StreamingContext](https://spark.apache.org/docs/2.2.1/api/python/_modules/pyspark/streaming/context.html)__ que nos permite crear un contexto de tipo streaming utilizando un contexto de Spark previo. En este caso hemos definido un contexto de streaming sobre el contexto de spark (sc) y con una duración de 1 segundo para el procesamiento de cada uno de los mini batches (Cada uno de los elementos procesados en considerado un batch). 


```
from os import path
from operator import add, sub
from time import sleep
from pyspark import SparkContext
from pyspark.streaming import StreamingContext

if __name__ == "__main__":
    
    spark_url = 'spark://10.18.0.2:7077'
    app_name = 'big RDD app 2'
    
    sc = SparkContext(spark_url, app_name).getOrCreate()
    ssc = StreamingContext(sc, 1)
    
    try:
        
        rdd_queue = []
        
        for i in range(5):
            rdd_queue += [ssc.sparkContext.parallelize([i, i+1])]
            
        inputStream = ssc.queueStream(rddQueue)
        
        inputStream.map(lambda x: "Input: " + str(x)).pprint()
        inputStream.reduce(add).map(lambda x: "Output: " + str(x)).pprint()
        
        ssc.start()
            
    except Exception as e:
    
        print('Se ha producido un error')
        print(e)
        
    finally:
    
        ssc.awaitTerminationOrTimeout(10)
        ssc.stop()
        sc.stop()
 ```
