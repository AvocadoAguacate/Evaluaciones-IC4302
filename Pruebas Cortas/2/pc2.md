Prueba Corta #2
Esteban Guzmán Ramírez - 2015095861
Tecnológico de Costa Rica 
Escuela de Ingeniería en Computación 
Bases de datos II (IC 4302)

1. Explique el concepto de shard, replica y partition
	a. Shard:  esto es dividir la información en diferentes servers pero usando el mismo schema para guardar la información, ejemplo 2 servidores de trafico aero, uno para cada aeropuerto en Costa Rica pero ambos usan el mismo schema al guardar la información que les corresponda. 
	b. Replica : también conocido como standby, es el backup de la base de datos primaria 
	c. Partition: son divisiones que se les hace a tablas enormes con el fin de obtener la información más rápido, por ejemplo si tenemos una tabla con 5 años en ventas se podría hacer una partición por años. 
2. Explique la diferencia entre Strong Consistency Eventual Consistency.
	R/ Strong Consistency permite que las replicas no puedan responder a peticiones con información desactualizada, eventual consistency responder con la información actualizada en algún momento ( se permite que responda con información desactualizada mientras se actualiza )
3. ¿En que consiste warm replicas y hot replicas?
	R/ Warm y hot son replicas que se diferencian en la cantidad de información que almacenan y por eso los recursos que deben tener varían. En el caso de hot replica se debe solo la información del último mes y la warm replica podría tener los últimos 3 meses, esta diferencia hace que hot replica deba tener más memoria pero menos disco. 
4. ¿En que consiste consiste switch over y fail over?
	Ambos son procesos que sirven cuando una base primaria no está disponible por alguna razón, en el caso de fail over ocurre actuamaticamente el cambio a una base secundaria y en switch over es un cambio a base secundaria manual. Switch over se puede planear para evitar interruciones en el servicio de una base de datos primaria y un fail over simplemente ocurre cuando hay algún problema inesperado en la base de datos primaria. 