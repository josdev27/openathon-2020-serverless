# Laboratorio 1. DynamoDB

[DynamoBD](https://docs.aws.amazon.com/es_es/dynamodb/?id=docs_gateway), nos permiterá crear los servicios de persistencia necesarios para nuestra aplicación. Es este caso, una tabla para almacenar los eventos creados llamada "eventos". Esta tabla será usada para almacenar los eventos registrados por los usuarios. 

Para crearla seguiremos los siguientes pasos:

1. En la consola de AWS, en el menú Services buscaremos y seleccionaremos “DynamoDB”.

<p align="center">
    <img src="resources/Picture1.png"/>
</p>

> <mark>IMPORTANTE</mark>. Hay que verificar que te encuentras en la región correcta. Cada uno de los servicios que se creen en los laboratorios (Cognito, API Gateway, Lambda y DynamoDB) deben pertenecer a la misma región. Para tener más información acerca de las regiones puedes acceder a este [enlace](https://docs.aws.amazon.com/es_es/AWSEC2/latest/UserGuide/using-regions-availability-zones.html). Para la elaboración de los laboratorios os sugerimos utilizar Irlanda.

1. Seleccionamos “Create table”.
2. Introducimos:
   * Nombre: “events”.
   * Primary Key. Estableceremos los campos que la forman, en nuestro caso id:
     * Partition key: id (string).
  3. En Table settings, quitamos la opción default settings, y ponemos la siguiente configuración:
      * En la sección Secondary indexes, hacemos click en add index:
        * En partition key, ponemos addedBy y type String.
        *  En index-name lo dejamos como está.
        *  En Projected attributes, ponemos ALL.
        *  Finalmente, hacemos click en add index.
     *  En la sección Read/Write capacity mode, lo dejamos en provisionado.
     *  En la sección Auto Scaling, quitamos las opciones Read Capacity y Write Capacity.
     *  En la sección Provisioned capacity, lo dejamos todo en 1.
     *  En la sección Encryption At Rest, lo dejamos en Default.
4. Pulsamos Create, llevará en torno a 15 segundos la creación de la tabla. Una vez creada podremos acceder a todos sus detalles en la sección “tables” del servicio “DynamoDB”.
5. En el panel de control de la tabla pulsamos la pestaña “ítems” y a continuación “create ítem” para crear nuestro primer evento.
6. En el dialogo introduciremos los datos de nuestro primer evento:

<p align="center">
    <img src="resources/Picture2.png"/>
</p>

7. A continuación, introduciremos el resto de los datos. Pulsando el “+” adyacente a “date” seleccionaremos “Append” y “String” indicando que queremos introducir en el ítem un dato de tipo cadena. Los ítems en las tablas de DynamoDB no tienen que cumplir una estructura obligatoria más allá de los que forman la primary key. De esta manera incorporaremos:
   * Key: location – Value: Málaga.
   * Key: description – Value: Lorem Ipsum is simply dummy text of the printing and type setting industry. Lorem Ipsum has been the industry's standard dummy.
   * Key: addedBy– Value: test-user@example.com.
  
   El item debe ser similar al siguiente json (pulsar en el botón Tree y elegir Text):

      ```json
      {
      "addedBy": "prueba@gmail.com",
      "date": "1997-01-01",
      "description": "dfnsbfiusduigsf",
      "id": "3a64b9fc-bac4-47cd-a591-1813f404475e",
      "location": "Barcelona",
      "title": "Hola"
      }
      ```

8. Pulsamos guardar. Hemos creado así el primer evento.
