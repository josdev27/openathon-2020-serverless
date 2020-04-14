# GetEventsMe - Python Version

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

## Probar la función

Creamos un test de prueba cuya entrada es la siguiente:

```json
{
  "addedBy": "prueba@gmail.com"
}
```
donde,
* **addedBy**: es el correo del autor del evento.

Si es correcto, nos devolverá los eventos asociados a ese usuario.

[< Volver al Laboratorio 07 ](../../lab-07) 
