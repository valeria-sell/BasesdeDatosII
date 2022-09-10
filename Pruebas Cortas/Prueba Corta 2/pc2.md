###### Bases de Datos II, Profesor Nereo Campos
###### Prueba Corta # 2, Valeria Sell Sáenz
###### Entrega: Viernes 09 de Setiembre, II Semestre 2022

----

1. Explique el concepto de shard, replica y partition

Un __shard__ es una partición horizontal de datos en una base de datos o motor de búsqueda. Cada uno se mantiene en una instancia de servidor de base de datos independiente para distribuir la carga. Una __réplica__ es copia de una base de datos, con el fin de tener un respaldo. Una __partición__ es una sección del disco duro que está separada de otros segmentos, esto permite dividir un disco físico en secciones lógicas.

2. Explique la diferencia entre Strong Consistency y Eventual Consistency.

La diferencia radica en que la __Consistencia Eventual__ tiene el concepto de quórum donde puede establecer el número de nodos que los primeros datos deben copiarse antes de responder. Por otro lado, la __Consistencia Fuerte__ significa que los datos deben ser consistentes en todo momento. Todos los nodos del servidor deben contener el mismo valor que una entidad en cualquier momento.

3. ¿En que consiste warm replicas y hot replicas?

Hot: Son réplicas que contienen los datos indexados más recientes y los más buscados. 
Warm: Una vez que los datos en el nivel activo se buscan con menos frecuencia, se pueden mover esta réplica.

4. ¿En que consiste consiste switch over y fail over?

Un __switch over__ es un cambio manual de un sistema primario a un sistema de espera y un __fail over__ es el cambio a un sistema que está en espera si se produce un error en el sistema o componente inicial. 