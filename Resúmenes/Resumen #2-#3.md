###### Bases de Datos II, Profesor Nereo Campos
###### Resumen #2-#3, Valeria Sell Sáenz
###### Entrega: Martes 30 de Agosto, II Semestre 2022

----

Es posible manejar datos estructuras diseñados para escalar a un gran tamaño. __Bigtable__ es uno de los sistemas que se encargue de hacerlo, con las características de amplia aplicabilidad, escalabilidad, alto rendimiento y alta disponibilidad. Además, comparte muchas estrategias de implementación con bases de datos pero no da soporte a un modelo enteramente relacional. En su lugar implementa un modelo de datos sencillo que da soporte a un control dinámico sobre el layout de los datos y su formato. 

Los datos son indexados usando nombres de filas y columnas que pueden ser cadenas arbitrarios. En el caso de __Bigtable__, los datos son tratados como cadenas no interpretados, y es posible definir su localidad mediante los _schemas_. También presenta la opción de controlar dinámicamente si usar datos de la memoria o del disco. 

Los datos pueden estar ordenados en un mapa multidimensional disperso, distribuido y persistente. Dicho mapa está indexado por una fila clave, clave de columna y una marca de tiempo. En ellos, las claves de fila de una tabla son cadenas arbitrarias de hasta 64 KB de tamaño. Cada lectura o escritura de datos de una sola fila es atómica.

Los datos se mantienen en orden lexicográfico por llave de fila. El rango de filas de una tabla se divide dinámicamente. Cada rango de filas se denomina tableta (__tablet__), la unidad de distribución y equilibrio de carga. Como resultado, los rangos de filas son eficientes y, por lo general requieren comunicación con solo un pequeño número de máquinas.

Las claves de columna se agrupan en conjuntos llamados familias de columnas (__families__), los cuales forman la unidad básica de control de acceso. Todos los datos almacenados en una familia de columnas suelen ser del mismo tipo. Se debe crear una familia de columnas antes de poder almacenar los datos. Una clave de columna se denomina mediante la sintaxis: __familia:calificador__. Los nombres de familia de las columnas deben poder imprimirse, pero los calificadores pueden ser cadenas arbitrarias. El control de acceso y la contabilidad de disco y memoria se realizan a nivel de familia de columnas.Cada celda puede contener varias versiones de los mismos datos; estas se indexan por marca de tiempo. Las marcas de tiempo son enteros de 64 bits, pueden ser asignados de acuerdo al "tiempo real" o explícitamente

El __manejo de versiones de datos__ se da automáticamente mediante un recolector de basura de celdas. management of versioned data, o garbage-collect cell versions automatically. Es posible especificar que sólo se conserven las últimas n versiones, o que sólo se conserven versiones lo suficientemente nuevas.

Algunos API proporcionan funciones para crear y eliminar tablas y familias de columnas. También proporcionan funciones para cambiar la familia de clústeres, tablas y columnas de metadatos, como los derechos de control de acceso.

>__Transacciones de una sola fila__: Pueden ser usadas para realizar secuencias atómicas de lectura, modificación y escritura en datos almacenados bajo una clave de una sola fila.

El formato de archivo __SSTable__ de Google se utiliza internamente para almacenar datos, proporciona un mapa inmutable ordenado de claves a valores, donde ambos son cadenas arbitrarias de bytes. 

Internamente, cada SSTable contiene una secuencia de bloques, normalmente cada bloque tiene un tamaño de 64 KB, pero ese tamaño es configurable. Un índice de bloque almacenado al final de la SSTable, se utiliza para localizar los bloques. El índice está cargado en la memoria al abrir la  SSTable. Una búsqueda se puede realizar con una sola búsqueda de disco: primero se encuentra el bloque apropiado mediante una búsqueda binaria en el índice en memoria y, a continuación, se lee el índice del bloque desde el disco. Opcionalmente, un SSTable se puede mapear completamente en la memoria, lo que permite realizar búsquedas y escaneos sin tocar el disco.


El servicio __Chubby__ consta de cinco réplicas activas, una de las cuales es elegido para ser el _master_ y servir activamente a las solicitudes. El servicio está activo cuando se ejecutan la mayoría de las réplicas y estas pueden comunicarse entre sí. Chubby usa el _Algoritmo Paxos_ para mantener sus réplicas consistentes en caso de fracaso. Chubby proporciona un espacio de nombres que consta de directorios y archivos pequeños. Cada directorio o archivo puede ser utilizado como una cerradura, y las lecturas y escrituras en un archivo son atómicas. La biblioteca de cliente chubby proporciona un almacenamiento en caché coherente de los archivos. Además, cada cliente mantiene una sesión con un servicio. Esta sesión caduca si no puede renovar su concesión de sesión dentro del tiempo de vencimiento del arrendamiento. Cuando la sesión de un cliente expira, pierde las cerraduras y sus _open handles_.

Los servidores de tabletas se agregan, o eliminan, dinámicamente desde un clúster para acomodar los cambios en las cargas de trabajo. El _master_ es el responsable de asignar tabletas a __Tabletas Servidores__. Cada servidor de tabletas administra un conjunto de tabletas; el servidor de tableta controla la lectura y la escritura solicita a las tabletas que ha cargado y también divide tabletas que han crecido demasiado. Es importante notar que los datos del cliente __no__ se mueven a través del maestro y como resultado, el maestro se carga ligeramente en la práctica.

Un clúster almacena varias tablas, y cada una consta de un conjunto de tabletas. Además, cada tableta contiene todos los datos asociados a un intervalo de filas. Inicialmente, cada tabla consta de una sola tableta. A medida que una tabla crece, se divide automáticamente en múltiples tabletas. Cada tableta es asignada a un servidor de tableta a la vez. El _master_ realiza un seguimiento del conjunto de servidores de tabletas activas. Cuando una tableta no está asignada, y se tiene un servidor de tabletas con espacio suficiente para la tableta, el _master_ le asigna la tableta por enviar a una solicitud de carga de tableta al servidor de tabletas. El _master_ supervisa el directorio de servidores para detectar servidores de tabletas y un servidor de tabletas deja de servir si pierde su cerradura exclusiva.

Un servidor de tableta intentará volver a adquirir un bloqueo exclusivo en su archivo siempre que el archivo aún exista. Si el el archivo ya no existe, entonces el servidor se mata a sí mismo. Siempre que un servidor de tableta termina, intenta liberar su bloqueo para que el _master_ reasigne a sus tabletas lo más pronto posible. 

Si un servidor de tabletas deja de servir a sus tabletas, el _master_ pregunta periódicamente a cada servidor de tabletas el estado de su cerradura. Si un servidor de tableta informa que ha perdido su bloqueo, o bien, si el _master_ no pudo llegar a un servidor en sus últimos intentos, el maestro intenta adquirir un bloqueo exclusivo en el archivo del servidor. Si el maestro es capaz de adquirir la cerradura, entonces Chubby está activo y el servidor de la tableta está muerto o tiene problemas para llegar a Chubby, por lo que el _master_ elimina el archivo del servidor de tabletas.

Cuando la administración del sistema del clúster inicializa un _master_, necesita descubrir las asignaciones actuales de la tableta antes de que pueda cambiarlas. El _master_ ejecuta los siguientes pasos:
1. El _master_ toma una cerradura maestra única en Chubby, que evita las instancias _master_ simultáneas. 
2. El _master_ escanea el directorio de servidores en Chubby para encontrar los servidores activos. 
3. El _master_ se comunica con cada tableta en vivo para descubrir a qué tabletas ya están asignadas cada servidor. 
4. El _master_ escanea la tabla METADATA para aprender el conjunto de tabletas. 

Siempre que se encuentre este análisis una tableta que aún no está asignada, el _master_ agrega la tableta al conjunto de tabletas no asignadas. El conjunto de tabletas existentes solo cambia cuando se crea o elimina una tabla, se combinan dos tabletas existentes para formar una tableta más grande, o una tableta existente se divide. Las actualizaciones se confirman con un registro deconfirmación que almacena registros. De estas actualizaciones, las recientes se almacenan en la memoria en un orden ordenado búfer llamado __memtable__; las actualizaciones anteriores se almacenan en un secuencia de SSTables.

- __Minor Compaction__ (compactación menor): Cuando el tamaño de la tabla alcanza un umbral, el memtable se congela, se crea un nuevo memtable y el memtable congelado se convierte en un SSTable y se escribe a GFS.
- __Merging Compaction__ (compactación de fusión): Lee el contenido de algunos SSTables y el memtable, y escribe un nuevo SSTable.
- __Mayor Compaction__ (compactación mayor): Es una compactación de fusión que reescribe todos los SSTables en exactamente un SSTable.

Los clientes pueden agrupar varias familias de columnas en un grupo de localidades. Se genera una SSTable independiente para cada grupo de localidades en cada tableta. También pueden controlar si las SSTables para un grupo de localidades se comprimen y, de ser así, qué formato de compresión se utiliza. Se suele utilizae una versión personalizada de dos pasadas esquema de compresión. La primera pasada utiliza _Bentley_ y el _Esquema de McIlroy_, que comprime el común largo cadenas a través de una ventana grande. El segundo pase utiliza un algoritmo de compresión rápida que busca repeticiones en una pequeña ventana de 16 KB de los datos.

Para mejorar el rendimiento de lectura, los servidores de tabletas utilizan dos niveles de almacenamiento en caché. 

| Tipo | Descripción |
| --- | ----------- |
| __Scan Cache__  | Es un caché de nivel superior que almacena en caché los pares clave-valor devueltos por la interfaz de SSTable con el código del servidor de la tableta, es más útil para las aplicaciones que tienden a leer los mismos datos repetidamente. |
| __Block Cache__ | Es un caché de nivel inferior que almacena en caché los bloques de SSTables que eran leer de GFS y es útil para aplicaciones que tienden a leer datos que están cerca de los datos que leyeron recientemente. |

Para __reducir el número de accesos__, un filtro permite preguntar si una SSTable puede contener algún dato de algún tipo en particular. Este uso de filtros también implica que la mayoría de las búsquedas de filas inexistentes o las columnas no necesitan tocar el disco. 

Para __acelerar la recuperación de la tableta__, si el maestro mueve una tableta de un servidor de tableta a otro, el servidor de la tableta de origen hace una compactación menor en esa tableta. Esta compactación reduce el tiempo de recuperación al reducir la cantidad de estado no compactado en el registro de confirmación del servidor de tabletas. Después de finalizar esta compactación, el servidor de la tableta deja de servir a la tableta.

Puntos de referencia para la evaluación del desempeño:
- The sequential write benchmark
- The random write benchmark
- The sequential read benchmark
- The random read benchmark 
- The scan benchmark
-  The random reads (mem) benchmark

Un servidor puede ser de solo una tableta. A medida que se aumenta el número de tabletas servidores en el sistema, el rendimiento agregado aumenta drásticamente, en más de un factor de cien. Este comportamiento se produce porque el cuello de botella en el rendimiento de este punto de referencia es el CPU de servidor de tableta individual. Sin embargo, el rendimiento no aumenta linealmente.

Para el caso del CPU y red, el algoritmo de equilibrio de carga intenta lidiar con este desequilibrio, pero no puede hacer un trabajo perfecto por dos razones principales: el reequilibrio se limita a reducir el número de movimientos de comprimidos cuando se mueve, y la carga generada por los puntos de referencia cambia a medida que avanza el punto de referencia.

__Notas importantes__:
- Los grandes sistemas distribuidos son vulnerables a muchos tipos de fallas, no solo a las particiones de red estándar.
- Es importante retrasar la adición de nuevas características hasta que quede claro como el nuevo sistema las utilizará. 













