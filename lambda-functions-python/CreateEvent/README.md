# CreateEvent - Python Version

Primero tenemos que crear la funcion lambda, de la misma forma que en [lab-03](../lambda-functions-python/EventsList), pero el código fuente es el siguiente:

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


[< Volver al Laboratorio 06 ](../../lab-06#crear-endpoint) 
