# Laboratorio 6. Completar API Gateway

En los laboratorios anteriores creamos el endpoint para obtener todos los eventos creados en la base de datos. Ahora a dicho endpoint vamos a añadirle dos capas de seguridad:
* **Token de usuario**: vamos a añadir la cabecera Authorization, de forma que solo los usuarios autenticados puedan ver los eventos.
* **API Key**: vamos a añadir la cabecera x-api-key, para limitar el uso de nuestra api añadiendo un plan de uso.

Para llevar a cabo la parte de token de usuario, hemos tenido que crear un pool de usuarios en los laboratorios anteriores.

## API Key y Usage Plan

Para crear el API Key hacemos los siguientes pasos:

1. Con la API abierta, hacemos click en la opción API Keys del menú de la izquierda.
2. Pinchamos en Actions, y luego en Create API Key.
3. En el formulario, ponemos un nombre identification y damos a Save.
4. Nos aparecerá la información del API Key. Si hacemos click en Show, podemos ver la clave. Guardala, porque la necesitaras más adelante. 

Ahora vamos a asociarle un plan de uso al API key:

1. Volvemos a la API y hacemos click en la opción Usages Plan del menú de la izquierda.
2. Pinchamos en Create.
3. En el formulario:
  * Name: indicamos un nombre significativo.
  * Enable throttling: lo dejamos habilitado
  * Rate: 1
  * Burst: 1
  * Enable quota: lo dejamos habilitado
  * Requests per: 200 per moth
4. Hacemos click en Next.
5. Hacemos click en Add Api Stage.
6. En API, elejimos nuestra API.
7. En Stage, elejimos el stage, por ejemplo, prod. Hacemos click en el icono de Ok.
8. Hacemos click en Next.
9. Hacemos click en Add API Key to Usage Plan.
10. En name indicamos el nombre de nuestra API key y hacemos click en el icono de Ok.
11. Hacemos click en done. 

De esta forma, ya tenemos un plan de uso asociado al stage de nuestra API. 


## GET /events endpoint

Vamos a añadirle la capa de seguridad al endpoint GET /events:

1. Con la API abierta, pinchamos en /events > GET
2. Hacemos click en method request.
3. En la sección de settings:
 * En Authorization, elejimos la pool creada.
 * En OAuth Scopes, lo dejamos a None.
 * En Request Validator, lo dejamos a None.
 * En API Key Required, lo dejamos a True.
 
De esta forma, para usar el endpoint GET /events, se va a necesitar el Api key y un token de usuario.

## POST /events endpoint

## GET /events/me endpoint

## GET /events/{eventsId} endpoint

## PUT /events/{eventsId} endpoint

## DELETE /events/{eventsId} endpoint

