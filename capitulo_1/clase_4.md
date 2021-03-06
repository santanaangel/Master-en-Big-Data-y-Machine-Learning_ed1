![WideImg](https://fictizia.com/img/github/Fictizia-plan-estudios-github.jpg)

# [→ Máster en Big Data y Machine Learning](https://fictizia.com/formacion/master-big-data)
### Big Data, Machine Learning, Tensor Flow, Data Science, Data Analytics, Arquitecturas Big Data, Plataformas Big Data

## Capítulo 1 - Clase 04

En esta clase se definirán algunos conceptos básicos sobre librerias de tratamientos de datos y sobre como manipular la información. 

* 1. Ciclo de vida de los datos
* 2. Tipos de infraestructuras (Distribuida, Cloud y Edge)
* 3. Algoritmia para datos
* 4. El valor de los datos
* 5. Legislación y normativa
* 6. Ética en el uso de la información

* 3. Algoritmia para datos *

La palabra algorirmia deriva de la palabra algoritmo y se puede definir como el proceso de construcción de algoritmos para resolver determinados problemas. En este caso nos centraremos en como construir algoritmos para la gestión de datos mediante python. 

** 3.1 Resolviendo mi primer problema de algoritmia **

Construir un fichero de datos que contenga toda la información de un dataset para ello vamos a utilizar los datos recopilados por la universidad de wisconsin (USA) referentes al cancer de mama. Para más información sobre el dataset o el estudio pueder visitar la siguiente [página web](https://archive.ics.uci.edu/ml/datasets/Breast+Cancer+Wisconsin+(Diagnostic)). 

* [Dataset de datos](https://archive.ics.uci.edu/ml/machine-learning-databases/breast-cancer-wisconsin/breast-cancer-wisconsin.data)

* [Información del dataset](https://archive.ics.uci.edu/ml/machine-learning-databases/breast-cancer-wisconsin/breast-cancer-wisconsin.names)

** Ejercicio de lectura y escritura de ficheros **

Para la realización del ejercicio es necesario descargar los dos ficheros y construir un script en python que genere un archivo json y un archivo csv a partir de los datos contenidos en ambos fichero. Ambos ficheros deberán incluir la información referente a los campos (names) y a los datos (data)

La solución propuesta para resolver este ejercició se puede descargar en el siguiente [enlace](https://github.com/Fictizia/Master-en-Big-Data-y-Machine-Learning_ed1/blob/master/capitulo_1/src/breast-cancer-wisconsin-reading.py)

** 3.2 Trabajando con Pandas **

Pandas es una librera open source (BSD-licensed) que facilita la manipulación de conjuntos de datos. Se puede instalar en python 
mediante la utilización del sistema de instalación pip mediante el siguiente comando. 

```
pip install pandas

```

Un vez instalada correctamente la librería podemos comenzar a trabajar con pandas mediante su importación en nuestro fichero 
python. Para ello crearemos un nuevo archivo python con el siguiente código:


```
#!/usr/bin/env python3

import pandas as pd

if __name__ == "__main__":
    print('Pandas ha sido importada correctamente')

```

*** 3.2.1 Series y DataFrames ***

Pandas ofrece dos elementos básicos para el almacenamiento de la información: 
* Series: Las series son secuencias de información almacenadas en forma de columna, es decir es una secuencia de valores que se almacenan una columna.
* DataFrame: Los dataframes son conjuntos de series almacenada como una tabla. De manera formal se pueden describir como un conjunto de series agrupadas mediante una tabla. 

![Ejemplo de representación de series y DataFrames](./img/pandas_series.png)

Desde el punto de vista de python, las series se corresponde con las listas (list) y los dataframes con los diccionarios (dict).  

*** 3.2.2 Construyendo series y dataframes ***

Para la construcción de las series y los dataframes podemos utilizar las estructuras básicas de python, mediante la creación 
de un diccionario y luego utilizando la clase DataFrame de pandas. 

```
#!/usr/bin/env python3

import pandas as pd

raw_data = {
    'manzanas': [3, 2, 0, 1], 
    'naranjas': [0, 3, 7, 2]
}

pedidos = pd.DataFrame(raw_data)

print(pedidos)

```

donde el resultado de este script de python generara el siguiente output:


```
   manzanas  naranajas
0         3          0
1         2          3
2         0          7
3         1          2

```

Además es posible definir el significado de los indices de la filas (algo así como el id de la fila)

```
#!/usr/bin/env python3

import pandas as pd

raw_data = {
    'manzanas': [3, 2, 0, 1], 
    'naranjas': [0, 3, 7, 2]
}

pedidos = pd.DataFrame(raw_data, index=['Junio', 'Julio', 'Agosto', 'Septiembre'])

print(pedidos)

```

donde el resultado de este script de python generara el siguiente output:


```
            manzanas  naranjas
Junio              3         0
Julio              2         3
Agosto             0         7
Septiembre         1         2
```

podemos seleccionar la información de una determinada fila mediante la operación location, donde la localización se corresponde con su índice

```
#!/usr/bin/env python3

import pandas as pd

raw_data = {
    'manzanas': [3, 2, 0, 1], 
    'naranjas': [0, 3, 7, 2]
}

pedidos = pd.DataFrame(raw_data, index=['Junio', 'Julio', 'Agosto', 'Septiembre'])

print(pedido.loc['Junio'])

```

Obteniendo como resultado la información de la fila y de sus columnas, que se corresponden con los valores de la diferentes series que conforman el dataFrame. 

```
manzanas    3
naranjas    0
Name: Junio, dtype: int64
```

En este caso nos indica en correspondiente valor para cada una de las series, el identificado de la fila y el tipo de datos de manera global. En este caso el tipo de ambas series es un entero de 64 bits, por lo que el tipo general es este. En caso de que tuvieramos series con diferentes tipos, nos indicara que el tipo general es "object" ya que hay valores de diferente tipo. 

```
manzanas    3
naranjas    a
Name: Junio, dtype: object
```

*** 3.2.3 Cargando datos desde fichero ***

Además de construir nuestros conjuntos de datos utilizando listas y diccionarios es posible cargar la información directamente desde diferentes tipos de ficheros. Para ellos vamos a utilizar el archivo que utilizamos en el anterior ejercicio relacionado con el cancer de mama. 

```
#!/usr/bin/env python3

import pandas as pd

if __name__ == "__main__":

    names = ["id", "clump_thickness", "unif_cell_size", "unif_cell_shape", "marg_adhesion", "single_epith_cell_size",
             "bare_nuclei", "bland_chrom", "norm_nucleoli", "mitoses", "class"]
    raw_data = pd.read_csv("./data/breast-cancer-wisconsin.data", ',', names=names)

    raw_data.to_csv(index=False, encoding='utf-8', path_or_buf='pandas.csv')
    raw_data.to_json(orient='records', path_or_buf='pandas.json')
```

Pero podemos hacer muchas más cosas con la librería pandas para preparar nuestro datasets:

Generando subconjunto de datos desde el inicio del datasets

```
raw_data.head()
```

Generando subconjunto de datos formado por dos elementos desde el inicio del datasets

```
raw_data.head(2)
```

Generando subconjunto de datos desde el final del datasets

```
raw_data.tail()
```

Generando subconjunto de datos formado por cinco elementos desde el final del datasets

```
raw_data.tail(5)
```

Generando subconjunto de datos aleatorios de 10 elementos

```
raw_data.sample(10)
```

Seleccionando sólo una de las series (columnas)
```
raw_data.id
```

Seleccionando sólo una de las series (columnas)
```
raw_data[['id', 'bare_nuclei']]
```

Aplicar condiciones sobre la selección de series

```
print(raw_data[raw_data.classs == 2])
```

article_read[['country', 'user_id']]
