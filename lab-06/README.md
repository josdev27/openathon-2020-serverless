# Laboratorio 6. Completar API Gateway

## Introducción



En los laboratorios anteriores creamos el endpoint para acceder a la función lambda que obtiene y devuelve todos los eventos creados en la base de datos utilizando API Gateway.

Ahora a dicho endpoint vamos a añadirle dos capas de seguridad para controlar quien puede acceder.
* **Token de usuario**: vamos a añadir la cabecera Authorization, de forma que solo los usuarios autenticados puedan ver los eventos.
* **API Key**: vamos a añadir la cabecera x-api-key, para limitar el uso de nuestra api añadiendo un plan de uso. El objeto de este plan de uso es garantizar que en el caso que alguien acceda a la URL de vuestra API, no pueda saturarla con miles de peticiones.

Para llevar a cabo la parte de token de usuario, debemos crear un Authorizer con el pool de usuarios que hemos creado en los laboratorios anteriores.

En este laboratorio crearemos también el resto de endpoints necesarios para nuestra nuestra APP y le añadiremos la seguridad antes mencionada.

## API Key y Usage Plan

Para crear el API Key hacemos los siguientes pasos:

1. Con la API abierta, hacemos click en la opción API Keys del menú de la izquierda.
2. Pinchamos en Actions, y luego en Create API Key.
3. En el formulario, ponemos un nombre (p. ej. "identification") y damos a Save.
4. Nos aparecerá la información del API Key. Si hacemos click en Show, podemos ver la clave. Guardala, porque la necesitaras más adelante. 

Ahora vamos a asociarle un plan de uso al API key:

1. Volvemos a la API y hacemos click en la opción Usages Plan del menú de la izquierda.
2. Pinchamos en Create.
3. En el formulario:
  * Name: indicamos un nombre significativo (p. ej. "EventsUsagePlan").
  * Enable throttling: lo dejamos habilitado. 
  * Rate: 1
  * Burst: 1
  * Enable quota: lo dejamos habilitado
  * Requests per: 200 per month
Como véis, hemos establecido los límites de peticiones por segundo que pueden recibir y el máximo total que nuestra API admitirá al mes.
4. Hacemos click en Next.
Ahora vamos a seleccionar la API y dentro de ella, el deploy stage al que queremos aplicar el Usage Plan. En nuestro caso solo tenemos un previamente creado ("prod"). 
5. Hacemos click en Add Api Stage.
6. En API, elegimos el nombre de nuestra API.
7. En Stage, elejimos el stage, ("prod" si hemos seguido las instrucciones anteriores). Hacemos click en el icono de Ok del registro añadido.
8. Hacemos click en Next.
9. Hacemos click en Add API Key to Usage Plan.
10. En name indicamos el nombre de nuestra API key ("identification" en nuestro ejemplo) y hacemos click en el icono de Ok.
11. Hacemos click en done. 

De esta forma, ya tenemos un plan de uso asociado al stage de nuestra API. 

## Creación de un Authorizer

Para poder hacer uso de la autorización a través de Cognito, es necesario crear un Authorizer con el pool de usuarios que hemos creado anteriormente.

1. Entramos en el servicio API Gateway y en el menú de la izquierda, pulsamos sobre Authorizers

<p align="center">
  <img src="resources/img_1.png">
</p>

2. Rellenamos el nombre del authorizer.
3. En la opción tipo, seleccionamos Cognito
4. En la sección Cognito User Pool introducimos el nombre del pool que hemos creado en cognito.
5. En Token source (Origen del token), escribimos Authorization.
6. Pulsamos Create 


## GET /events endpoint

Vamos a añadirle la capa de seguridad al endpoint GET /events de nuestra API:

1. Con la API abierta, pinchamos en /events > GET
2. Hacemos click en Method Request.
3. En la sección de settings:
 * En Authorization vamos a seleccionar elejimos la pool creada.
 * En OAuth Scopes, lo dejamos a None.
 * En Request Validator, lo dejamos a None.
 * En API Key Required, lo dejamos a True.
 
De esta forma, para usar el endpoint GET /events, se va a necesitar el Api key y un token de usuario.

## POST /events endpoint

A través de este endpoint se podrán crear nuevos eventos.

### Crear función lambda

Primero tenemos que crear la funcion lambda, de la misma forma que en lab-02, pero el código fuente es el siguiente:

```python
# This lambda function is integrated with the following API methods:
# /events POST
#
# Its purpose is to post events from our DynamoDB table

from __future__ import print_function
import boto3
import json
from boto3.dynamodb.conditions import Key
from botocore.exceptions import ClientError
import uuid

def lambda_handler(event, context):

    print('Initiating Events-ListFunction...')
    print("Received event from API Gateway: " + json.dumps(event, indent=2))
    
    # Create our DynamoDB resource using our Environment Variable for table name
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('events')
    try:
        event["id"] = str(uuid.uuid4())
        response = table.put_item(
            Item=event)
    except ClientError as e:
        print(e.response['Error']['Message'])
        print('Check your DynamoDB table...')
    else:
        print("PutItem succeeded:")
        print("Received response from DynamoDB: " + json.dumps(response, indent=2))
        return event

```

### Crear endpoint

Para crear el endpoint en nuestro API Gateway:

1. Pinchamos en /events
2. Hacemos click en Actions y luego en Create Method. Elejimos POST.
2. Hacemos click en method request.
3. En la sección de settings:
 * En Authorization, elejimos la pool creada.
 * En OAuth Scopes, lo dejamos a None.
 * En Request Validator, lo dejamos a None.
 * En API Key Required, lo dejamos a True.
4. Volvemos atrás, y hacemos click en Integration Request:
 * En Integration type, elejimos Lambda Function.
 * En Lambda Region, la region correspondiente.
 * En Lambda Function, la funcion lambda para crear eventos.


## GET /events/me endpoint

Este endpoint nos permitirá obtener los eventos del usuario logueado.

### Crear función lambda

En este caso no es necesario, ya que la función para obtener eventos ya tiene la lógica para obtener los eventos añadidos por un usuario.

```python
if "addedBy" in event:
  response = table.query(
      IndexName="addedBy-index",
      KeyConditionExpression=Key('addedBy').eq(event["addedBy"])
      )
else:
  response = table.scan()
```

### Crear endpoint

Para crear el endpoint en nuestro API Gateway:

1. Pinchamos en /events
2. Hacemos click en Actions y luego en Create Resource:
   * En Resource Name: Mis eventos
   * Resource Path: me
3. Hacemos click en Create Reosurce.
4. Hacemos click en /me. Luego En Actions y en Create Method. Elejimos GET.
4. Hacemos click en method request.
5. En la sección de settings:
 * En Authorization, elejimos la pool creada.
 * En OAuth Scopes, lo dejamos a None.
 * En Request Validator, lo dejamos a None.
 * En API Key Required, lo dejamos a True.
6. Volvemos atrás, y hacemos click en Integration Request:
 * En Integration type, elejimos Lambda Function.
 * En Lambda Region, la region correspondiente.
 * En Lambda Function, la funcion lambda para listar eventos.
 * En Mappings Template:
   * Hacemos click en When there are no templates defined (recommended)
   * Hacemos click en Add Mapping Template y escribimos application/json
   * En el editor que se nos ha abierto ponemos:
   ```json
   {
   "addedBy" : "$context.authorizer.claims.email"
   }
   ```
   * Hacemos click en Save

## GET /events/{eventsId} endpoint

Este endpoint nos permitirá obtener los eventos del usuario logueado.

### Crear función lambda

Primero tenemos que crear la funcion lambda, de la misma forma que en lab-02, pero el código fuente es el siguiente:

```python
# This lambda function is integrated with the following API methods:
# /events GET (list operation)
#
# Its purpose is to get events from our DynamoDB table

from __future__ import print_function
import boto3
import json
from boto3.dynamodb.conditions import Key
from botocore.exceptions import ClientError

def lambda_handler(event, context):

    print('Initiating Events-ListFunction...')
    print("Received event from API Gateway: " + json.dumps(event, indent=2))
    
    # Create our DynamoDB resource using our Environment Variable for table name
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('events')

    try:
        response_event = table.get_item(Key={'id': event["id"]})
        item = response_event["Item"]
    except ClientError as e:
        print(e.response['Error']['Message'])
        print('Check your DynamoDB table...')
    else:
        print("GetItem succeeded:")
        print("Received response from DynamoDB: " + json.dumps(response, indent=2))
        # Return only the Items and not the whole response from DynamoDB
        return item

```

### Crear endpoint

Para crear el endpoint en nuestro API Gateway:

1. Pinchamos en /events
2. Hacemos click en Actions y luego en Create Resource:
   * En Resource Name: Eventos por id
   * Resource Path: {eventid}
3. Hacemos click en Create Resource.
4. Hacemos click en {eventid}. Luego En Actions y en Create Method. Elejimos GET.
4. Hacemos click en method request.
5. En la sección de settings:
 * En Authorization, elejimos la pool creada.
 * En OAuth Scopes, lo dejamos a None.
 * En Request Validator, lo dejamos a None.
 * En API Key Required, lo dejamos a True.
6. Volvemos atrás, y hacemos click en Integration Request:
 * En Mappings Template:
   * Hacemos click en When there are no templates defined (recommended)
   * Hacemos click en Add Mapping Template y escribimos application/json
   * En el editor que se nos ha abierto ponemos:
   ```json
   {
   "id": "$input.params('eventid')"
   }
   ```
   * Hacemos click en Save

## PUT /events/{eventsId} endpoint

Este endpoint nos permitirá editar un evento.

### Crear función lambda

Primero tenemos que crear la funcion lambda, de la misma forma que en lab-02, pero el código fuente es el siguiente:

```python
# This lambda function is integrated with the following API methods:
# /events POST
#
# Its purpose is to post events from our DynamoDB table

from __future__ import print_function
import boto3
import json
from boto3.dynamodb.conditions import Key
from botocore.exceptions import ClientError
import uuid

def lambda_handler(event, context):

    print('Initiating Events-ListFunction...')
    print("Received event from API Gateway: " + json.dumps(event, indent=2))
    
    # Create our DynamoDB resource using our Environment Variable for table name
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('events')
    try:
        
        response_event = table.get_item(Key={'id': event["id"]})
        item = response_event["Item"]
        item_put = event["body-json"]
        
        if item["addedBy"] == event["addedBy"]:
            response = table.update_item(
                Key={"id": event["id"]},
                ExpressionAttributeNames={
                    "#addedBy": "addedBy",
                    "#date": "date",
                    "#description": "description",
                    "#title": "title",
                    "#location": "location"
                },
                ExpressionAttributeValues= {
                    ":addedBy":item_put["addedBy"],
                    ":date":item_put["date"],
                    ":description":item_put["description"],
                    ":title":item_put["title"],
                    ":location":item_put["location"]
                },
                UpdateExpression =  ("SET #addedBy = :addedBy,"  
                                    "#date = :date,"  
                                    "#description = :description," 
                                    "#title = :title," 
                                    "#location = :location")
                )
        else:
            raise ClientError('You are not the author of event')
    except ClientError as e:
        print(e.response['Error']['Message'])
        print('Check your DynamoDB table...')
    else:
        print("PutItem succeeded:")
        print("Received response from DynamoDB: " + json.dumps(response, indent=2))
        return item_put

```

### Crear endpoint

Para crear el endpoint en nuestro API Gateway:

1. Hacemos click en {eventid}. Luego En Actions y en Create Method. Elejimos GET.
2. Hacemos click en method request.
3. En la sección de settings:
 * En Authorization, elejimos la pool creada.
 * En OAuth Scopes, lo dejamos a None.
 * En Request Validator, lo dejamos a None.
 * En API Key Required, lo dejamos a True.
4. Volvemos atrás, y hacemos click en Integration Request:
 * En Mappings Template:
   * Hacemos click en When there are no templates defined (recommended)
   * Hacemos click en Add Mapping Template y escribimos application/json
   * En el editor que se nos ha abierto ponemos:
   ```json
   {
   "body-json" : $input.json('$'),
   "id": "$input.params('eventid')",
   "addedBy" : "$context.authorizer.claims.email"
   }
   ```
   * Hacemos click en Save

## DELETE /events/{eventsId} endpoint

Este endpoint nos permitirá borrar un evento.

### Crear función lambda

Primero tenemos que crear la funcion lambda, de la misma forma que en lab-02, pero el código fuente es el siguiente:

```python
# This lambda function is integrated with the following API methods:
# /events GET (list operation)
#
# Its purpose is to get events from our DynamoDB table

from __future__ import print_function
import boto3
import json
from boto3.dynamodb.conditions import Key
from botocore.exceptions import ClientError

def lambda_handler(event, context):

    print('Initiating Events-ListFunction...')
    print("Received event from API Gateway: " + json.dumps(event, indent=2))
    
    # Create our DynamoDB resource using our Environment Variable for table name
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('events')

    try:
        response_event = table.get_item(Key={'id': event["id"]})
        item = response_event["Item"]
        if item["addedBy"] == event["addedBy"]:
            response = table.delete_item(Key={"id":event["id"]})
        
    except ClientError as e:
        print(e.response['Error']['Message'])
        print('Check your DynamoDB table...')
    else:
        print("DeleteItem succeeded:")
        print("Received response from DynamoDB: " + json.dumps(response, indent=2))
        # Return only the Items and not the whole response from DynamoDB
        return

```

### Crear endpoint

Para crear el endpoint en nuestro API Gateway:

1. Hacemos click en {eventid}. Luego En Actions y en Create Method. Elejimos GET.
2. Hacemos click en method request.
3. En la sección de settings:
 * En Authorization, elejimos la pool creada.
 * En OAuth Scopes, lo dejamos a None.
 * En Request Validator, lo dejamos a None.
 * En API Key Required, lo dejamos a True.
4. Volvemos atrás, y hacemos click en Integration Request:
 * En Mappings Template:
   * Hacemos click en When there are no templates defined (recommended)
   * Hacemos click en Add Mapping Template y escribimos application/json
   * En el editor que se nos ha abierto ponemos:
   ```json
   {
   "id": "$input.params('eventid')",
   "addedBy" : "$context.authorizer.claims.email"
   }
   ```
   * Hacemos click en Save

## Deploy API

Finalmente, para desplegar la API tenemos que llevar a cabo dos pasos más:
1. Click en Actions.
2. Click en Enable CORS
3. Click en Enable CORS and replace existing CORS headers
4. Volvemos atrás y hacemos click en Actions.
5. Click en Deploy API.
6. Elegimos el stage y damos a Deploy
