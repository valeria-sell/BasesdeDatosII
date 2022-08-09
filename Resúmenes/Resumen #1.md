##### Resumen #1 - Bases de Datos II -  Valeria Sell Sáenz, 2015146840

Los datos son los activos más valiosos para una empresa, de tal manera que deben almacenar los datos relevantes al regocio, dar acceso, poder analizar la información de diferentes maneras y destilarlos a la visión de negocio. Es común que las grandes empresas implementen "data warehouses" para el almacenamiento masivo de datos.  

### Analítica moderna y Arquitectura de almacenamiento de datos 
Los datos entran a ser almacenados mediante sistemas transaccionales y otras bases de datos relacionales, y pueden encontrarse de manera estructurada, semi-estructurada y desestructurada. 

| Data Warehouses | Bases de Datos OLTP |
| ------ | ------ |
| Optimizado para operaciones de escritura por lotes y una alta lectura de volúmenes de datos. | Optimizado para operaciones de escritura continua y altos volúmenes de operaciones de lectura pequeñas.|
| Generalmente emplea esquemas desnormalizados como  __Star Schema__ y __Snowflake Schema__ debido a los altos requisitos de rendimiento de datos. | Generalmente emplea esquemas altamente normalizados, que son más adecuados para requisitos altos de rendimiento de transacciones. |

#### Servicios de análisis 
Los servicios de análisis ayudan a las empresas a convertir sus datos en respuestas mediante servicios de análisis integrados, desde almacenes de datos en la nube a lagos de datos sin servidor.

#### Arquitectura analítica 
Las "tuberías" analíticas están diseñadas para manejar grandes volúmenes de flujo de datos entrantes de fuentes como bases de datos, aplicaciones y dispositivos. Típicamente, siguen las siguientes fases:
1. Recolecta los datos
2. Almacena los datos
3. Procesa los datos
4. Analiza y visualiza los datos

#### Colección de datos
###### Datos  Transaccionales
Normalmente son almacenados en sistemas de gestión de bases de datos relacionales (RDBMS) o sistemas de bases de datos NoSQL. La elección depende del caso de uso y características de aplicación. 
###### Logs
Son registros generados por el sistema, lo cual permite solucionar problemas, realizar auditorías y realizar análisis utilizando la información almacenada en los registros.
###### Datos de Streaming
Generados por las aplicaciones web, los dispositivos móviles y muchas aplicaciones y servicios de software. Todos deben ser recolectados, almacenados y procesados continuamente.
###### Datos de IoT
Los dispositivos y sensores de todo el mundo envían mensajes continuamente, esta información es capturada por empresas y se deriva inteligencia de ellos.
#### Procesamiento de datos
##### En lote 
###### ETL (Extract Transform Load)
Es el proceso de extracción de datos de múltiples fuentes para cargar en sistemas de almacenamiento de datos. Es un proceso continuo, con un flujo de trabajo bien definido. 
###### ELT (Extract Load Transform)
Es una variante de ETL, los datos se cargan primero en el sistema de destino y después de que esten subidos en el almacén de datos, se realizan las transformaciones.
###### OLAP (Online Analytical Processing)
Almacenan agregados datos históricos en esquemas multidimensionales. Ampliamente utilizados para consultas, informes y análisis, permiten extraer datos y detectar tendencias en múltiples dimensiones. 

##### En tiempo real
Procesan los datos secuencial e incrementalmente sobre una base de registro por registro, o durante el tiempo de deslizamiento Windows. Estos datos son utilizados para una amplia variedad de análisis, como correlaciones, agregaciones, filtrado y muestreo. Este procesamiento requiere de una capa de procesamiento altamente concurrente y escalable.

#### Almacenamiento de Datos
###### Lago de datos
Es un patrón arquitectónico que combina los mejores elementos de almacenes de datos y lagos de datos. Permiten consultar datos en su almacén de datos, lago de datos y bases de datos operativas para obtener conocimientos más rápidos y profundos que de otra manera no serían posibles. Los datos pueden ser almacenados en formatos de archivo abiertos en su lago de datos y pueden ser consultados en su lugar y ser unidos con los datos del almacén de datos.
###### Data Warehouse
Permiten ejecutar análisis rápidos en grandes volúmenes de datos y patrones ocultos en los datos aprovechando las herramientas de BI.
###### Data Mart
Es una versión simple de almacén de datos centrada en un área funcional específica o tema. Los data marts son fáciles de diseñar, construir y administrar.

### Opciones de Tecnología
#### Bases de Datos orientadas a filas
Las bases de datos orientadas a filas almacenan filas enteras en un bloque físico. El alto rendimiento de las operaciones de lectura se logra a través de índices secundarios. Los almacenes de datos tradicionales basados en filas están limitados por los recursos disponibles en una solo máquina. Bases como __Oracle Database Server__, __Microsoft SQL Server__, __MySQL__ y __PostgreSQL__ son sistemas de bases de datos orientados a filas. 

#### Bases de Datos orientadas a columnas
Las bases de datos orientadas a columnas organizan cada columna en su propio conjunto de bloques físicos en lugar de empaquetar todas las filas en un bloque. Esto les permite ser más eficiente para consultas de solo lectura. Las bases de datos orientadas a columnas son una mejor opción que las bases de datos orientadas a filas para almacenamiento de datos. Algunas bases de datos orientadas a columnas que se utilizan para el almacenamiento de datos incluyen __Amazon Redshift__, __Vertica__, __Greenplum__, __Teradata Aster__, __Netezza__ y __Druid__. 
### Amazon Redshift
##### Rendimiento
Implementa múltiples tipos de nodos a escoger dependiendo de los requerimientos. Las consultas son aceleradas al ejecutar tareas de uso intensivo de datos, como filtrar y agregar más cerca a la capa de almacenamiento. Esto evita las limitaciones de ancho de banda de red al eliminar movimientos de datos innecesarios entre el almacén de datos y los clusters. También utiliza el almacenamiento en caché de resultados para entregar tiempos de respuesta de menos de un segundo para consultas repetidas. Cuando se ejecuta una consulta, __Amazon Redshift__ busca en el cache para ver si hay un resultado almacenado de una ejecución anterior.
##### Durabilidad y Disponibilidad
__Amazon Redshift__ detecta y reemplaza automáticamente cualquier nodo con errores en el cluster de almacenamiento de datos. Eso hace que el nodo de reemplazo esté disponible inmediatamente y carga primero los datos accesados con mayor frecuencia para así reanudar las nuevas consultas lo más rápido posible.
##### Elasticidad y Escalabilidad
__Amazon Redshift__ presenta una característica de cambio de tamaño elástico, con la capacidad de cambiar el tamaño de un cluster mediante la adición de nodos para obtener los recursos necesarios y para quitar nodos cuando se complete el trabajo para ahorrar costos. Los nodos adicionales se agregan o eliminan en minutos con una interrupción mínima de las consultas de lectura y escritura en curso. Este cambio de tamaño se puede automatizar mediante un programador.
De igual forma, presenta una función de escalabilidad simultánea al admitir usuarios y consultas prácticamente ilimitados, con un rendimiento constante en las consultas. Cuando se habilita el escalado simultáneo, __Amazon Redshift__ agrega automáticamente la capacidad de proceso adicional cuando la necesita para procesar el aumento de las consultas de lectura simultánea.
### Operaciones
| Patrones de Uso Ideales | Anti-Patrones |
| ------ | ------ |
| Ejecutar análisis según sea necesario en datos de eventos de gran volumen, como análisis de registros y redes sociales | OLTP |
| Descargar los datos del historial a los que se accede con poca frecuencia fuera del almacén de datos  | Datos no estructurados |
| Unir el conjunto de datos externo con el almacén de datos directamente sin cargarlos en el almacén de datos | Datos BLOB (Binary Large Object) |
