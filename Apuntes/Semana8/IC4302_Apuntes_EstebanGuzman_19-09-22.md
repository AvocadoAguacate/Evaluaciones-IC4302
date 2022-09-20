Apuntes del viernes 16 de septiembre del 2022 (semena 8)
Esteban Guzmán Ramírez 

#### Anuncios
- Tarea 01 se pospone hasta el domingo 18 de septiembre a medio día
- Inicia oficialmente el proyecto el martes 20 de septiembre


# Proyecto 

## RabbitMQ 
Es un elemento nuevo en el curso, con la funcionalidad de un server de mensajería. Este permite implementar el patrón de diseño productor/consumidor, para más información ver el siguiente [link](https://www.netmentor.es/entrada/patron-productor-consumidor). 

## Jobs 
Serán archivos JSON con las siguientes carácteristicas
```JSON
{
/*job_id es un identificador único para cada job*/
    "job_id": "job606",
/*status inicialmente no necesario, es usado durante el proceso,
así que puede ser ignorado agrendado "" o simplemente new*/ 
    "status": "new",

    "msg": "",
/*data_sources en este proyecto serán 4 JSON que nos permiten
definir de donde viene la información, con la capacidad de 
ampliarce a más sources en caso de ser necesario en un futuro*/
    "data_sources": [
        {   

            "type": "mysql",
/*name solo sería un identificado único */
            "name": "people_db",
/*url en este caso, por ser pods dentro de un mismo namespace,
funcionaría con el nombre del pod*/
            "url": "databases-mariadb-people",
/*port sería el puerto expuesto según el pod requerido*/
            "port": "3306",

            "usuario": "XXXXXXX",

            "password": "XXXXXXX"

        },

        {

            "type": "elasticsearch",

            "name": "destination_es",
/*En el caso de los sources elastic hay que indicar el protoclo
y el servicio Elasticsearch*/
            "url": "https://elastic-endpoint",

            "port": "9200",

            "usuario": "XXXXXXXXXX",

            "password": "XXXXXXXXX"

        }

    ],
/*Para este proyecto siempre será un pod de elastic*/
    "control_data_source": "destination_es",
/*Aquí definimos donde se sacará la infomrmación requeridad
y el query necesario para obtenerla*/
    "source": {
/*nombre definido en data_sources, que tiene que ser un 
data_sources de type mysql*/
        "data_source": "people_db",
/*Se recomienda usar ORDER BY*/
        "expression": "SELECT * FROM persona ORDER BY cedula",
/*tamaño de los grupos de documentos que requieren ser
procesados a la vez*/
        "grp_size": "100"
    },
/*En este proyecto se maneja 3 stages, extract, transform y load*/
    "stages" : [

        {
/*Siempre extract*/
            "name": "extract",
/*El nombre de la cola en RabbitMQ donde se obtendrán los mensajes para coordinar esta etapa*/
            "source_queue": "extract",
/*Nombre de la cola destino donde se publicarán los mensajes una vez procesada en esta etapa*/
            "destination_queue": "%{transform->transformation->add_car}%"

        },

        {
/*Aqui tenemos las transformaciónes que podrán tener los datos, también se 
ve el uso de variables con el formato %{nombre_variable}%*/
            "name": "transform",

            "transformation": [

                {

                    "name": "add_car",

                    "type": "sql_transform",

                    "table": "car",

                    "expression": "SELECT %{field_description}% FROM %{table}% WHERE %{field_owner}% = %{doc_field}%",
/*Origen de los datos*/
                    "source_data_source": "database_car",
/*Siempre elastic para este proyecto*/
                    "destination_data_source": "destination_es",
/*es una variable en expression*/
                    "doc_field": "id",

                    "source_queue": "sql_queue",

                    "destination_queue": "%{transform->transformation->myregex}%",

                    "fields_mapping": {

                        "field_description": "description",

                        "field_owner": "owner"

                    }

                },

                {

                    "name": "myregex",

                    "type": "regex_transform",

                    "regex_config": {
/*El regex define dos grupos separados por una -*/
                        "regex_expression": "^.* ([a-zA-z]{3}-[0-9]{3}) .*$",
/*Aquí se escoge entre uno de los 2 grupos definidos por el regex*/
                        "group": "1",

                        "field": "description"

                    },

                    "field_name": "placa",

                    "source_queue": "regex_queue",

                    "destination_queue": "%{load}%"

                }

            ]

        },

        {
/*Encargado de entregar los resultados a la DB indicada, para este
proyecto siempre será elastic*/
            "name": "load",

            "source_queue": "ready",
/*Para este proyecto siempre será elastic*/
            "destination_data_source": "destination_es",

            "index_name": "persona"

        }

    ]

}
```

## Monitoreo

- Toda la aplicación debe ser monitoriada con Prometheus y Grafana
- Para el Orquestador, mysql connector, mysql connector, sql processor, regex processor y elasticsearch publisher se deberá dar metricas del número de grupos procesados, tiempo promedio de procesamiento de grupos y tiempo total de procesamiento.
- Se deben crear Service Monitors para que Prometheus los descubra automáticamente

## Otros 
- Se darán puntos extra por implementar extras de seguridad. 
- No importa como se carguen los datos a MariaDB
- Existirá un componente llamado Orquestador, en Python,  que vigilará los jobs. Inicialmente modificará el `status` a `"In progress"` . Además usa la expresión `SELECT COUNT(1) FROM (root.source_exression` para obtener el total de registros necesarios. Con el total de registros y `root.source.grp_size` calcula la cantidad de grupos mediante una división $\mathbf{roundup}\left ( \frac{\mathbf{total de registros}}{\mathbf{root.source.grpsize}} \right )$ . Con esta información en elastic crea los documentos donde se iran guardando los documentos mientras se procesan
```json 
{
/*Nombre obtenido en el documento del job*/
	"job_id": "myjob011"
/*nombre del job - número de grupo 
Ej: con un grp_size de 10 y 30 documentos los número de grupo serían
[0, 10, 20]*/
	"group_id": "myjob011-0"
}
```



