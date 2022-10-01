Prueba Corta #3
Esteban Guzmán Ramírez - 2015095861
Tecnológico de Costa Rica 
Escuela de Ingeniería en Computación 
Bases de datos II (IC 4302)

1. Explique el concepto de Write Concern en MongoDB
	Es una parte de las operaciones de escritura donde se define que la seguridad de escritura con un numero de 0 a n, donde 0 es que no se espera confirmación de escritura en ningún pod de la base, 1 donde se espera la confirmación de escritura en un nodo, n donde se espera la confirmación de escritura en n pods. 
2. Explique diferencias entre bases de datos NoSQL y SQL, tome como ejemplo las bases de datos estudiadas en clase y utilizadas en proyectos y tareas
	La diferencia más caracteristica de las bases de datos NoSQL con respecto a las bases SQL es la flexibilidad que se tiene con ACID. Ser flexible con ACID ha permitido el desarrollo de bases NoSQL más rápidas en tiempos de lectura, escritura, escalamiento y cambios en la estructura datos. Otra diferencia es el concepto de documento en las bases NoSQL y las tablas  en SQL, esto permite mayor flexibilidad en las bases NoSQL ya que estos documentos son dinamicos en comparación a las tablas fijas en SQL. También se puede comentar que NoSQL con su patrón master-slave tiene mayor facilidad con el escalamiento horizontal y SQL han sido pensadas desde hace mucho tiempo con escalamiento vertical.  
3. Desde un punto de vista de una base de datos de series de tiempo, ¿Porqué la localidad de datos es relevante para la escogencia del hardware a utilizar?, puede justificar su respuesta utilizando los data tiers de algún motor de bases de datos como Elasticsearch
	La localidad de datos es importante ya que nos permite guardar datos muy viejos ( con poca posibilidad de uso ) en hardware más barato y datos recientes en hardware más rápido/caro. 
4. Explique el concepto de Federated Queries y el impacto que tienen estas en el rendimiento de bases de datos
	Las Federated Queries son queries con 2 o más datasets en diferentes bases. El rendimiento se ve afectado ya que necesitará un arbitro que cordine las bases de datos y los resultados en cada base para poder obtener un resultado entre varias bases. 