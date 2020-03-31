# Laboratorio 3. IAM

En esta sección crearemos las políticas y los roles IAM necesarios para la ejecución de las funciones lambda. Necesitaremos otorgarles permisos para:
-	Realizar operaciones CRUD en DynamoDB.
-	Dejar trazas en el servicio de log de AWS (Cloudwatch).

## Políticas IAM

Las políticas IAM permiten establecer las condiciones de seguridad para el acceso a recursos o funciones dentro de AWS. Las políticas pueden seleccionarse dentro de un conjunto de predefinidas por Amazon o crearse de manera específica. 
1.	En la consola de AWS, en el menú Services buscaremos y seleccionaremos “IAM”.
IMPORTANTE. IAM es un servicio global, por lo que no veras ninguna región seleccionada (aparecerá GLOBAL).
![](img)
2.	Primero vamos a crear la policy para dar privilegios a nuestras funciones lambda para el acceso a la tabla creada en DynamoDB. En el menú de navegación del servicio, en la parte izquierda de la pantalla, pulsamos “Policies” y en la ventana resultante el control “Create Policy”.
3.	En la ventana de creación de la policy, seleccionaremos “choose a service” y en el nombre del servicio buscaremos y seleccionaremos “DynamoDB”.
![](img)
4.	Una vez seleccionado el servicio, es necesario introducir las acciones que queremos permitir en el mismo. Para hacerlo introduciremos los nombres de las siguientes acciones para luego irlas seleccionando una a una:
a.	DeleteItem
b.	GetItem
c.	PutItem
d.	Query
e.	Scan
f.	UpdateItem
g.	DescribeTable
![](img)
También podemos pulsar la opción “expand all” para seleccionar manualmente las acciones o comprobar que las hemos seleccionado correctamente.
5.	A continuación, especificaremos el recurso sobre el que queremos aplicar estas acciones, pulsando  sobre “resources”:
![](img)
6.	En el área desplegada podremos especificar el ARN de la tabla o index al que queremos aplicar las acciones. En nuestro caso, como solo existe una tabla podemos seleccionar la opción “Any”.
![](img)
7.	Si pulsamos la pestaña “JSON” podremos visualizar en formato json la política que estamos creando:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "dynamodb:PutItem",
                "dynamodb:DescribeTable",
                "dynamodb:DeleteItem",
                "dynamodb:GetItem",
                "dynamodb:Scan",
                "dynamodb:Query",
                "dynamodb:UpdateItem"
            ],
            "Resource": "arn:aws:dynamodb:*:*:table/*"
        }
    ]
}
```
8.	Pulsando “review policy” podremos acceder al resumen de la política creada, le ponemos un nombre “event_ddb_policy” y pulsamos “create policy”.
9.	Ahora crearemos una segunda policy para que las funciones lambda puedan acceder al servicio de logs de AWS. Para crearla utilizaremos esta vez directamente json. Volvemos a pulsar “Create Policy”.
10.	En ventana pulsaremos la pestaña json y allí copiaremos:
```json
{
"Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
       "logs:CreateLogGroup",
       "logs:CreateLogStream",
       "logs:PutLogEvents"
],
            "Resource": ["*"]
        }
    ]
}
```
11.	De nuevo, pulsando “review policy” podremos acceder al resumen de la política creada, le ponemos un nombre “event_logs_policy” y pulsamos “create policy”.

Podemos ahora comprobar que hemos generado nuestras políticas volviendo a la sección “Policies” del servicio IAM e introduciendo en el filtro de consulta “event_”, lo que debería resultar en:
![](img)


## Rol IAM
La manera de asignar a una función lamdba una policy es a través de un Rol IAM, por lo que debemos crear uno para nuestra aplicación.
1.	Accedemos a la sección “Roles” del servicio IAM y pulsamos “Create Role”.
2.	En la ventana resultante:
a.	En el área “Select type of trusted entity” dejamos seleccionado “AWS service”.
b.	En “Choose a use case” seleccionamos “Lambda”.
![](img)
3.	Pulsamos “next:permissions”.
4.	En la sección “Attach permissions policies”, introducimos como filtro “event_” y de los resultados seleccionamos:
    * event_ddb_policy
    * event_logs_policy
5.	Pulsamos “Next: Tags”. 
6.	Pulsamos “Next: Review”.
7.	Introducimos como nombre del Rol “EventsRole”.
8.	Pulsamos “Create Role”.

Con ello nuestro rol estará creado y deberá ser accesible en la sección “Roles” del servicio IAM.
