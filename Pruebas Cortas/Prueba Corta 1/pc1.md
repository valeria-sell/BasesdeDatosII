###### Bases de Datos II, Profesor Nereo Campos
###### Prueba Corta # 1, Valeria Sell Sáenz
###### Entrega: Viernes 26 de Agosto, II Semestre 2022

----

1. Explique en que consisten los siguientes conceptos:

a. __Data Warehouse__: Es un almacen de datos especializado, particularmente para generar _insights_. Estos consisten en conclusiones a las que se llega a partir del análisis de datos. Los data warehouses se encuentran optimizados para operaciones de escritura por lotes y una alta lectura de volúmenes de datos.

b. __Data Lake__: Contienen "raw data", es decir, datos sin procesar. Estos son almacenados en un object storage en cantidades masivas y a un bajo costo.

c. __Data Mart__: Son una una versión simple de almacén de datos centrada en un área funcional específica o tema. Los data marts son fáciles de diseñar, construir y administrar.

2. ¿De que forma se benefician las aplicaciones del uso de Columnar Storage?
Explique.

Las bases de datos orientadas a columnas organizan cada columna en su propio conjunto de bloques físicos en lugar de empaquetar todas las filas en un bloque. Esto beneficia las aplicaciones ya que les permite ser más eficientes para consultas de solo lectura. Además, otro gran beneficio es una compresión mejorada. Como cada columna está empaquetada en su propio conjunto de bloques, cada bloque físico contiene el mismo tipo de datos. Cuando esto ocurre y toda la información es del mismo tipo, es posible usar algoritmos de compresión extremadamente eficientes, de manera que se necesita menos almacenamiento comparado a una base de datos que usa Row Storage. [1]

3. ¿En que consiste streaming y batch processing?

__Streaming__: Los datos de streaming aparecen en un flujo constante, no se tiene un principio ni final propiamente para los datos y no es posible definir su tamaño en disco. Por esto, el procesamiento en streaming consiste en recolectar, almacenar y procesar esta información continuamente. 

__Batch Processing__: Consiste en procesar información por lotes, posiblemente en intervalos definidos. 

4. ¿En que consiste datos estructurados, semi estructurados y no estructurados?

__Datos estructurados__: Estos datos siguen un formato de esquema tabular, es decir, columnas con sus respectivos tipos de datos.

__No estructurados__: Estos datos no siguen ningún patrón. Algunos ejemplos pueden ser texto sin estructura, audio y/o video.

__Semi estructurados__: Estos datos no necesariamente tienen los mismos atributos. 

__Bibliografía__:
> 1. Amazon Web Services. (2021, Enero). Data Warehousing on AWS. Recuperado el 26 de Agosto, 2022
