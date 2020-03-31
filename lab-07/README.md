# Laboratorio 7. Publicar la web en S3
## Crear bucket en S3

En esta sección crearemos un bucket para almacenar nuestro front-end angular (website bucket):
1.	En la consola de AWS, en el menú Services buscaremos y seleccionaremos “S3”.

> Hay que verificar que te encuentras en la región correcta. Cada uno de los servicios que se creen en los laboratorios (Cognito, API Gateway, Lambda y DynamoDB) deben pertenecer a la misma región.

2.	Creamos el “web bucket” que contendrá el front-end. Pulsamos “Create Bucket”, como nombre y en minúsculas estableceremos 
“events-web-xxxxxxx”. El nombre del bucket tiene que ser único en todo AWS, así que deberemos sustituir “xxxxxxx” por un identificador exclusivo, por ejemplo “evento-web-john-smith1234”.
3.	Pulsamos “create".

Con ello ya tendremos creados los contenedores para nuestra aplicación.

## Subir web al bucket
Ahora publicaremos la aplicación angular Events-ui para que pueda ser consumida desde cualquier lugar del mundo.
1.	En la consola de AWS, en el menú Services buscaremos y seleccionaremos “S3”.
2.	Seleccionamos el bucket que creamos en un paso anterior para el almacenamiento y acceso de los recursos front de la aplicación (events-web-xxxxxx), “xxxxxx” es el nombre exclusivo que le asignamos cada uno de manera individual.

<p align="center">
    <img src="resources/img_1.png">
</p>

3.	En la ventana resultado pulsamos “Upload”.
4.	Seleccionamos los ficheros que componen la aplicación angular. El fichero index.html tiene que quedar situado en la raíz de bucket.
5.	Pulsamos “Upload”.
6.	Pulsamos la pestaña “Permissions” y accedemos a ella.
7.	Pulsamos en “Block public Access”.
8.	Pulsamos “edit” y deseleccionamos todas las opciones.
9.	Pulsamos “Save” y confirmamos en cualquier aviso que se muestre, escribiendo la palabra “confirm” en el diálogo.
10.	Pulsamos en “Bucket Policy”.
11.	Introduce la siguiente política, reemplazando el nombre del bucket (“events-web-xxxxxxxxxxxx”) por que corresponda:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::events-web-xxxxxxxxxxxx/*"
        }
    ]
}
```
Nuestro bucket ahora aparecerá como de “Public Access”.

<p align="center">
    <img src="resources/img_2.png">
</p>

12.	Pulsamos en la pestaña de propiedades.
13.	Pulsamos “Static website hosting”.
14.	Activamos el hosting web:
a.	Seleccionamos “Use this bucket to host a website”.
b.	En “Index document” introducimos: “index.html”.
c.	Anotamos la Endpoint URL.
15.	Pulsamos “save".

