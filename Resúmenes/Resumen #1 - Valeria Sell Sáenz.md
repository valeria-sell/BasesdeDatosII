# Resumen #1 - Bases de Datos II
### Valeria Sell Sáenz

Los datos son los activos más valiosos para una empresa, de tal manera que deben almacenar los datos relevantes al regocio, dar acceso, poder analizar la información de diferentes maneras y destilarlos a la visión de negocio. Es común que las grandes empresas implementen "data warehouses" para el almacenamiento masivo de datos, a pesar de que tradicionalmente presentan varios desafíos. 
## Almacenamiento de datos en la nube
Tecnologías como __Amazon Redshift__ cambiaron como las empresas piensan acerca del almacenamiento de datos al bajar los costos y el esfuerzo comúnmente asociados al proceso, sin comprometer su funcionamiento.  

## Analítica moderna y Arquitectura de almacenamiento de datos 
Los datos entran a ser almacenados mediante sistemas transaccionales y otras bases de datos relacionales, y pueden encontrarse de manera estructurada, semi-estructurada y desestructurada. 

| Data Warehouses | Bases de Datos OLTP |
| ------ | ------ |
| Optimizado para operaciones de escritura por lotes y una alta lectura de volúmenes de datos. | Optimizado para operaciones de escritura continua y altos volúmenes de operaciones de lectura pequeñas.|
| Generalmente emplea esquemas desnormalizados como  __Star Schema__ y __Snowflake Schema__ debido a los altos requisitos de rendimiento de datos. | Generalmente emplea esquemas altamente normalizados, que son más adecuados para requisitos altos de rendimiento de transacciones. |

### Servicios de análisis 
Los servicios de análisis ayudan a las empresas a convertir sus datos en respuestas mediante servicios de análisis integrados, desde almacenes de datos en la nube a lagos de datos sin servidor.

### Arquitectura analítica 
Las "tuberías" analíticas están diseñadas para manejar grandes volúmenes de flujo de datos entrantes de fuentes como bases de datos, aplicaciones y dispositivos.

Típicamente, siguen las siguientes fases:
1. Recolecta los datos
2. Almacena los datos
3. Procesa los datos
4. Analiza y visualiza los datos

### Colección de datos
##### Datos  Transaccionales
Normalmente son almacenados en sistemas de gestión de bases de datos relacionales (RDBMS) o sistemas de bases de datos NoSQL. La elección depende del caso de uso y características de aplicación. 
• __NoSQL__: adecuada cuando los datos no están bien estructurados para encajar en un esquema definido, o cuando el esquema cambia frecuentemente. 
• __RDBMS__: adecuada cuando las transacciones ocurren en varias tablas las filas y las consultas requieren uniones complejas.
##### Logs
Son registros generados por el sistema, lo cual permite solucionar problemas, realizar auditorías y realizar análisis utilizando la información almacenada en los registros.
##### Datos de Streaming
Generados por las aplicaciones web, los dispositivos móviles y muchas aplicaciones y servicios de software. Todos deben ser recolectados, almacenados y procesados continuamente.
##### Datos de IoT
Los dispositivos y sensores de todo el mundo envían mensajes continuamente, esta información es capturada por empresas y se deriva inteligencia de ellos.
### Procesamiento de datos
La información extraída puede ser analizada en busca de inteligencia de manera que permita mejroar el negocio. La mejor práctica para recopilar esta inteligencia es cargar los datos sin procesar a un almacén de datos para realizar análisis adicionales. 

Existen dos tipos de flujos de trabajo de procesamiento: en lote y en tiempo real.
#### En lote 
##### ETL (Extract Transform Load)
Es el proceso de extracción de datos de múltiples fuentes para cargar en sistemas de almacenamiento de datos. Es un proceso continuo, con un flujo de trabajo bien definido. Durante este proceso, los datos se extraen inicialmente de una o más fuentes. Después de esto, son limpiados, enriquecidos, transformados y subidos en un almacén de datos.
##### ELT (Extract Load Transform)
Es una variante de ETL, los datos se cargan primero en el sistema de destino y después de que esten subidos en el almacén de datos, se realizan las transformaciones.
##### OLAP (Online Analytical Processing)
Almacenan agregados datos históricos en esquemas multidimensionales. Ampliamente utilizados para consultas, informes y análisis, permiten extraer datos y detectar tendencias en múltiples dimensiones. 

#### En tiempo real
Procesan los datos secuencial e incrementalmente sobre una base de registro por registro, o durante el tiempo de deslizamiento Windows. Estos datos son utilizados para una amplia variedad de análisis, como correlaciones, agregaciones, filtrado y muestreo. Este procesamiento requiere de una capa de procesamiento altamente concurrente y escalable.

### Almacenamiento de Datos
##### Lago de datos
Es un patrón arquitectónico que combina los mejores elementos de almacenes de datos y lagos de datos. Permiten consultar datos en su almacén de datos, lago de datos y bases de datos operativas para obtener conocimientos más rápidos y profundos que de otra manera no serían posibles. Los datos pueden ser almacenados en formatos de archivo abiertos en su lago de datos y pueden ser consultados en su lugar y ser unidos con los datos del almacén de datos.
##### Data Warehouse
Permiten ejecutar análisis rápidos en grandes volúmenes de datos y patrones ocultos en los datos aprovechando las herramientas de BI.
##### Data Mart
Es una versión simple de almacén de datos centrada en un área funcional específica o tema. Los data marts son fáciles de diseñar, construir y administrar.

## Opciones de Tecnología
### Bases de Datos orientadas a filas
Las bases de datos orientadas a filas almacenan filas enteras en un bloque físico. El alto rendimiento de las operaciones de lectura se logra a través de índices secundarios. Los almacenes de datos tradicionales basados en filas están limitados por los recursos disponibles en una solo máquina. 

Bases como __Oracle Database Server__, __Microsoft SQL Server__, __MySQL__ y __PostgreSQL__ son sistemas de bases de datos orientados a filas. 

### Bases de Datos orientadas a columnas
Las bases de datos orientadas a columnas organizan cada columna en su propio conjunto de bloques físicos en lugar de empaquetar todas las filas en un bloque. Esta funcionalidad les permite ser más eficiente para consultas de solo lectura. Las bases de datos orientadas a columnas son una mejor opción que las bases de datos orientadas a filas para almacenamiento de datos.

Algunas bases de datos orientadas a columnas que se utilizan para el almacenamiento de datos incluyen __Amazon Redshift__, __Vertica__, __Greenplum__, __Teradata Aster__, __Netezza__ y __Druid__.

### Arquitecturas de Procesamiento Paralelo Masivo
Permite utilizar todos los recursos disponibles en el clúster para procesamiento de datos, aumentando drásticamente el rendimiento de los datos.

## Amazon Redshift
Amazon Redshift presenta un rápido rendimiento de E/S y consultas para prácticamente cualquier tamaño de datos mediante el uso del _almacenamiento en columnas_ y la paralelización y distribución de consultas entre _varios nodos_.

### Rendimiento
Implementa múltiples tipos de nodos a escoger dependiendo de los requerimientos. Las consultas son aceleradas al ejecutar tareas de uso intensivo de datos, como filtrar y agregar más cerca a la capa de almacenamiento. Esto evita las limitaciones de ancho de banda de red al eliminar movimientos de datos innecesarios entre el almacén de daos y los clusters. También utiliza el almacenamiento en caché de resultados para entregar tiempos de respuesta de menos de un segundo para consultas repetidas. Cuando se ejecuta una consulta, __Amazon Redshift__ busca en el cache para ver si hay un resultado almacenado de una ejecución anterior.
### Durabilidad y Disponibilidad
__Amazon Redshift__ detecta y reemplaza automáticamente cualquier nodo con errores en el cluster de almacenamiento de datos. Eso hace que el nodo de reemplazo esté disponible inmediatamente y carga primero los datos accesados con mayor frecuencia para así reanudar las nuevas consultas lo más rápido posible.
### Elasticidad y Escalabilidad
__Amazon Redshift__ presenta una característica de cambio de tamaño elástico, con la capacidad de cambiar el tamaño de un cluster mediante la adición de nodos para obtener los recursos necesarios y para quitar nodos cuando se complete el trabajo para ahorrar costos. Los nodos adicionales se agregan o eliminan en minutos con una interrupción mínima de las consultas de lectura y escritura en curso. Este cambio de tamaño se puede automatizar mediante un programador.

De igual forma, presenta una función de escalabilidad simultánea al admitir usuarios y consultas prácticamente ilimitados, con un rendimiento constante en las consultas. Cuando se habilita el escalado simultáneo, __Amazon Redshift__ agrega automáticamente la capacidad de proceso adicional cuando la necesita para procesar el aumento de las consultas de lectura simultánea.
## Operaciones
### Patrones de Uso Ideales
__Amazon Redshift__ es ideal para el uso de OLAP, permitiendo lo siguiente: 
• Ejecución de BI empresarial e informes 
• Analizar datos de ventas globales para múltiples productos 
• Almacenar datos históricos de comercio de acciones 
• Analizar las impresiones y los clics de los anuncios 
• Analizar tendencias sociales 
• Medir la calidad clínica, la eficiencia operativa y el desempeño financiero en cuidado de la salud

También soporta datos semi-estructurados y extiende el almacén de datos al lago de datos. Esto permite:
• Ejecutar análisis según sea necesario en datos de eventos de gran volumen, como análisis de registros y redes sociales 
• Descargar los datos del historial a los que se accede con poca frecuencia fuera del almacén de datos 
• Unir el conjunto de datos externo con el almacén de datos directamente sin cargarlos en el almacén de datos
### Anti-Patrones
__Redshift__ no está idealmente diseñado para los siguientes patrones:
• OLTP
• Datos no estructurados
• Datos BLOB (Binary Large Object)
