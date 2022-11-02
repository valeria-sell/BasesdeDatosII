###### Bases de Datos II, Profesor Nereo Campos
###### Resumen #5-#6, Valeria Sell Sáenz
---
En sí, Spanner es un sistema de base de datos relacional de Google que implementa un sistema de esquema fuertemente tipado y un procesador de consultas SQL. Solía ser un almacén "clave-valor" que ofrecía transacciones de varias filas y conmutación por error transparente en todos los centros de datos. Implementa un dialecto de SQL (SQL estándar) y procesador está diseñado para servir a una combinación de cargas de trabajo transaccionales y analíticas, y para admitir consultas de baja latencia y de larga ejecución. A medidad que la ejecución de consultas ha evolucionado, esto a forzado a Spanner a evolucionar de manera concurrente. 

La motivación principal de esta evolución fue impulsada por las experiencias de los desarrolladores de Google, los cuales intentaron construir sobre sistemas de almacenamiento "clave-valor" anteriores. El ejemplo prototípico de tal sistema clave-valor es Bigtable, el cual sigue siendo de uso masivo en Google para una variedad de aplicaciones. Sin embargo, los desarrolladores de muchas aplicaciones OLTP encontraron que era difícil construir estas aplicaciones sin un sistema de esquema sólido, transacciones entre filas, replicación consistente y un potente lenguaje de consulta. Actualmente, Spanner es ampliamente utilizado como un gestor de bases de datos OLTP sistema para datos estructurados en Google, y se encuentra disponible públicamente en versión beta como Cloud Spanner1 en Google Cloud Platform (GCP).

El *query processor* de Spanner está diseñado para servir a una combinación de cargas de trabajo transaccionales y analíticas, y para admitir consultas de baja latencia como de larga duración.

En lo que refiere a su arquitectura, Spanner es un sistema de base de datos relacional compartido y geo-replicado, se encuentra fragmentado horizontalmente en rangos de filas. Dentro de un centro de datos, los fragmentos se distribuyen en varios servidores. Seguido esto, los fragmentos se replican en varios centros de datos separados geográficamente. Las transacciones de Spanner utilizan un registro de rehacer de escritura anticipada replicado, y se implementa el algoritmo de consenso de Paxos para obtener réplicas de manera que se pueda acordar en el contenido de cada entrada de registro. En particular, cada fragmento de la base de datos se le asigna exactamente a un grupo Paxos (replicated state machine). Es posible que un grupo determinado se le asignen varias particiones. Todas las transacciones que involucran datos en un grupo particular escriben en un registro lógico de escritura anticipada de Paxos, cada entrada de registro se confirma al replicarla correctamente en un quórum de réplicas. Seguidamente, el líder es elegido y puede confirmar varias entradas de registro en paralelo.

**Coprocessor framework**: determina cuál grupo (o grupos) Paxos es propietario de los datos a los que se dirige y encuentra la réplica más cercana de ese grupo que esté suficientemente actualizada para el modo de simultaneidad especificado.

**Colossus**: es un sistema de archivos distribuido basado en árboles de fusión estructurados en registros. El compilador de consultas Spanner primero transforma una consulta de entrada en un árbol de álgebra relacional, luego usa propiedades de esquema y datos para reescribir el árbol en un plan de ejecución eficiente a través de reglas de transformación, como por ejemplo empujar predicados hacia exploraciones, seleccionar índices o eliminar subconsultas.

El *procesador de consultas distribuídas* de Spanner ejecuta código en paralelo en múltiples máquinas que alojan los datos para servir consultas en línea y de larga ejecución.

**Compilación de consultas distribuidas**. 
El compilador de consultas SQL de Spanner crea un árbol de operadores de álgebra relacional y lo optimiza mediante reescrituras. La distribución de consultas se representa mediante operadores explícitos en el árbol de álgebra. 
*Unión distribuida*: se utiliza para enviar una subconsulta a cada fragmento de los datos persistentes o temporales subyacentes y para concatenar los resultados. 

**Ejecución distribuida** 
En tiempo de ejecución, una Unión distribuida minimiza la latencia mediante el marco de coprocesador Spanner para enrutar una solicitud de subconsulta dirigida a particionamiento a una de las réplicas más cercanas que pueden atender la solicitud. 
*Shard pruning*: se utiliza para evitar consultar particiones irrelevantes, aprovecha las claves de rango de las particiones y depende de las condiciones de empuje hacia abajo en las claves de particionamiento de las tablas a los escaneos subyacentes. Durante la ejecución, una unión distribuida puede detectar que las particiones de destino están alojadas localmente en el servidor y evita realizar la llamada remota ejecutando su subconsulta localmente. 
La *afinidad de particiones* es típica de las bases de datos pequeñas que pueden caber en un único servidor o de las particiones que comparten el prefijo de clave. Esta es una optimización de latencia importante, ya que la mayoría de las consultas operan en filas de varias tablas que comparten la misma clave de particionamiento.

**Joins distribuidos**
Spanner implementa un operador de aplicación distribuida extendiendo la unión distribuida e implementando un join de varias maneras: como dos uniones, una unión distribuida que aplica lotes de filas desde la entrada a la subconsulta remota y otra que aplica filas de cada lote a la subconsulta de la unión original localmente en una partición. 
Su resultado, el operador *Distributed Apply*, combina la funcionalidad de *Cross Apply* o *Outer Apply* con la unión distruibuida. El *Distributed Apply* le permite a Spanner minimizar el número de llamadas entre máquinas para uniones basadas en claves y paralelizan la ejecución. También permite convertir escaneos de tablas completas en una serie de escaneos de rango mínimo.

**APIs de distribución de consultas** 
- *API de cliente único*: Se utiliza cuando un solo proceso de cliente consume el resultado de una consulta. 
- *API de consumidor paralelo*: se utiliza para consumir resultados de consultas en paralelo de múltiples procesos que generalmente se ejecutan en varias máquinas. 

La **extracción de intervalos de consulta** se refiere al proceso de analizar una consulta y determinar a qué partes de las tablas hace referencia la consulta. 
Algunos de los tipos de extracción de rango que emplea Spanner son la extracción de rango de distribución, la extracción de rango de búsqueda y la extracción de rango de bloqueo.

Las implentaciones de extracción de rango se basan en dos técnicas principales: 
- *En tiempo de compilación*: normalizan y reescriben una expresión de escaneo filtrada en un árbol de autouniones correlacionadas que extraen los rangos de columnas clave sucesivas. Además, la reescritura realiza varios pasos de normalización de expresiones. 
- *En tiempo de ejecución*: Utilizan una estructura de datos especial llamada árbol de filtros tanto para calcular los rangos a través de la aritmética de intervalos ascendentes como para la evaluación eficiente de las condiciones posteriores al filtrado.

**Filter Tree**
Es una estructura de datos en tiempo de ejecución desarrollada para extraer simultáneamente los rangos clave a través de la intersección o unión de intervalos de abajo hacia arriba, y para postfiltrar las filas emitidas por las uniones correlacionadas. El árbol es compartido entre todos los escaneos correlacionados producidos por la reescritura en tiempo de compilación y memoriza los resultados de predicados cuyos valores no han cambiado.

Los **query restarts** compensan automáticamente los errores, la repartición y las implementaciones binarias, afectando de manera mínima a las latencias de solicitud. También reanuda la ejecución de consultas de instantáneas.

Pueden usarse sin ciclos de reintento (un modelo de programación simple) y ante una latencia de cola mejorada para solicitudes en línea. Para admitir reinicios, Spanner amplió su mecanismo RPC con un parámetro adicional: un token de reinicio. Estos acompañan a todos los resultados de la consulta, enviados en lotes, un token de reinicio por lote. Esto impide que las filas ya devueltas al cliente se vuelvan a devolver. 

La implementación de reinicio debe superar: 
- *Dynamic resharding*: el procesador de consultas no tiene la garantía de que el servidor que cubre un rango de claves recibirá una subconsulta determinada una vez. 
- *No determinismo*: dificulta el avance rápido de la ejecución de consultas a un estado determinado sin realizar un seguimiento de grandes resultados intermedios. 
- *Reinicia en todas las versiones del servidor*: Los posibles cambios realizados en el procesador de consultas deben abordarse para preservar la capacidad de reinicio. 

Previamente se mencionó que Spanner implementa un dialecto de SQL llamado "Standard SQL”. Este consiste en un modelo de datos común, un sistema de tipos, una sintaxis, una semántica y una biblioteca de funciones que comparten los sistemas. Un desarrollador o analista de datos que escribe SQL contra la base de datos Spanner puede transferir su comprensión del lenguaje y no preocuparse por diferencias sutiles en la sintaxis, el manejo de NULL, etc.

Para garantizar la coherencia entre los sistemas, se crearon varios componentes compartidos como una colaboración entre equipos, conocida internamente como la biblioteca GoogleSQL. 
- **Front-end compiler**: realiza el análisis, la resolución de nombres, la comprobación de tipos y la validación semántica. También genera una estructura de datos llamada *Árbol de sintaxis abstracta resuelta* (AST por sus siglas en inglés), que contiene el tipo de información y los objetos de esquema vinculados.
- **Biblioteca de funciones escalares**: reduce las posibilidades de divergencia en casos de esquina y aporta consistencia a los errores de tiempo de ejecución, como la comprobación de desbordamiento. 
- **Marco de pruebas compartidas**: se dividen en prueba de cumplimientos y de cobertura.

    - *Pruebas de cumplimiento*: Son un conjunto de consultas escritas por desarrolladores, cada una con un resultado codificado o un procedimiento de verificación de resultados suministrado.
    - *Pruebas de cobertura*: Utilizan una herramienta de generación de consultas aleatorias y una implementación de motor de referencia para verificar los resultados de las consultas.
- **FilterScanNodeGenerator**: tiene bordes a un generador de expresiones lógicas que elige entre varios generadores de nodos que pueden generar nodos AST resueltos de expresión booleana para la condición, y un generador de exploración lógica que elige entre varios generadores de nodos que pueden producir nodos AST resueltos de exploración para la entrada de filtro.

El *Ressi Data Layout* almacena una base de datos como un árbol LSM, cuyas capas se compactan periódicamente. Organiza los datos en bloques en orden de fila principal. Divide los valores en un archivo activo, que contiene sólo los valores más recientes, y un archivo inactivo, que puede contener muchas versiones anteriores. 

La migración en vivo de SSTables a Ressi es capaz de realizar migraciones de datos en vivo a través del movimiento masivo de datos y transacciones. Conversión grupo por grupo a réplicas de nuevo formato, a réplicas de formato mixto para pruebas y verificación, o de nuevo a SSTables para reversión. Requiere un cuidado extraordinario en la conversión para garantizar la integridad de los datos y una implementación mínima para evitar picos de latencia visibles para el usuario.
