> ###### Fecha: 23 de Setiembre, 2022
> ###### Estudiante: Valeria Sell Sáenz

## Información acerca del proyecto

__Callback__: Llamada asincrónica que toma el mensaje y lo convierte en un .json. 
El __Dockerfile__ es el encargado de empaquetar la aplicación. Además, las imágenes se publican en _Docker Hub_ a medida de que se vayan construyendo. Una vez que estas son publicadas se levanta la aplicación. 
_Nota_: Las variables de entorno deben ser inyectadas al consumidor. 

__Primer paso__: Incluir lo realizado en la Tarea Corta (monitoreo y bd), los nombres son libres pero es importante que sean significativos. 
Los nombres de los _helmcharts_ definen los nombres de los recursos a usar.

Para automatizar el proceso, se deben conocer los nombres de los recursos a crear de antemano. Así se puede configurar el _secret_ y el rervicio del _RabbitMQ_. Si use sa el _ElasticSearch_ de ECK Operator, este genera los nombres con los valores que tiene por defecto. Es decir, todos los servicios llevan ese nombre por delante, de manera que son sencillos de identificar. 

_Detalle importante_: En el caso de realizar una desintalación, el disco virtual permanece. Esto puede llegar a generar un conflicto de contraseñas ya que el sistema intentará usar el disco anterior. Por ello, se recomienda borrar también el disco virtual (persistent volume) antes de volver a instalar.
___

## Materia de Clase
### Perfomance
#### ACID, __¿por qué es importante?__ 
Esto define el por qué una base de datos relacional no se comporta tan bien como una no-sql. Esto es debido a que las no-sql tienden a borrar una de estas características.
##### Atomicity
Las operaciones se ejecutan en un ciclo de reloj y son indivisibles. El problema radica en que  operaciones se han vuelto cada vez más complejas, por lo que un ciclo de reloj no es suficiente tiempo y se corre el riesgo de perder el procesador en media transacción. 

Un "Acceso Exclusivo" para un recurso compartido es difícil de garantizar si las operaciones no son atómicas. La idea consiste en garantizar que un conjunto de sentencias sean ejecutadas de manera atómica.

##### Consistency
¿En qué consiste hacer un commit? Es hacer un cambio _persistente_.
Al ejecutar una transacción y hacer un commit, ese cambio perdurará pase lo que pase. Si esto no sucede, la base de datos queda en un estado _inconsistente_. Para tener consitencia, es importante tener atomicidad. Para una base de datos no-sql, la consistencia no es tan importante ya que es lo "más libre". 

__Rollback en Cascada__: Un rollback es una operación que devuelve la base de datos a algún estado anterior. Por tanto, un rollback en cascada sucede cuando una transacción (T1) falla y se debe hacer un rollback. Otras transacciones que dependen de las acciones de T1 también deben revertirse debido a la falla de T1, provocando un efecto en cascada. [1] Una base de datos puede ser diseñada para que no se de el rollback en cascada. 
__lock__: es el bloque de un recurso compartido para establecer la necesidad de uso y regularlo. 
__read__: lock compartido
__write__: lock exclusivo

__Compatibilidad de locks:__
|  | read | write |
| --- | --- | --- |
| __read__ | X | --- |
| __write__ | --- | --- |

Una operación read no es compatible con una operación write debido a la consistencia, ya que el valor del dato puede cambiar. 

##### Isolation
Una transacción siempre debe estar corriendo sola, con la intención de no tener conflictos por medio de locks, pero eso realmente no se da. En su lugar, se da la _apariencia_ a la transacción de que está sola. 

##### Durability
La base de datos debe permanecer ante fallas de poder, caídas, errores, usuarios... Una vez que una transacción es ejecutada y se le hace commit, se debe garantizar que la transacción va a perdurar. 

#### ¿Cuál es el problema con los locks?
- __Overhead__
- __Blocking__: Baja el rendimiento de la base de datos.

_Multiversioning_: Como su nombre lo dice, es la creación y manejo de varias versiones de un producto. Esto elimina el concepto del lock de read en la mayor parte de los casos.
_Deadlocks_: Sucede cuando dos entidades usan los mismos recursos compartidos en orden diferente. Una posible solución para evitarlos, es procurar que las entidades usen los recursos en el mismo orden.
_Two-Phase Locking_: Reduce la probabilidad de un deadlock. Sus dos fases son Expanding/Growing y Shrinking/Retracting. 

##### Tipos de Two-Phase Locking
- __Conservative__: Obtiene todos los locks anten de que inicie la transacción. Sus problemas se traducen en un timeout para los usuarios y en caídas en rendimiento para los desarolladores. 
- __Strict__: Los locks adquiridos de tipo exclusivo se liberan solo cuando la transacción termine. Su ventaja es que evita los rollbacks en cascada pero su desventaja radica en que comparte los problemas del tipo Conservador.
- __Strong-Strict Two-Phase__: Suelta los locks hasta el final de la transacción, los va solicitando a medida que va avanzando.

Si la base de datos está en varios servidores, se necesita de un coordinador. 
__Two-Phase Commit Protocol__
1. _Commit Request Phase_: redo log - undo log.
2. _Commit Phase_: Failure o Success.

El Coordinador hace una petición a que sus nodos ejecuten la transacción distribuida, con dos posibles resultados: funciona o no funciona. Los nodos llegan hasta el punto del commit, dejando los recursos bloqueados. Cuando el coordinador recibe los resultados, es todo o nada. Con solo un nodo que no haya logrado la transacción, el commit phase arranca en modo failure (se hace rollback y se liberan recursos). Si todos son exitosos, entonces se arranca en modo success (se hace commit a disco y se hace la transacción persistente). 

- Redo log: operaciones que se van a ejecutar en la base de datos para hacer el commit.
- Undo log: operaciones que se van a ejecutar en la base de datos para hacer el rollback.

En este protocolo, se prepara para ambos escenarios.

## Referencias
> 1. tok.wiki. (n.d.). Rollback (gestión de datos) Retroceso en cascadaySQL. Retrieved October 4, 2022, from https://hmong.es/wiki/Rollback_(data_management)


