# Get Event by id - Python Version

Primero tenemos que crear la función lambda, de la misma forma que en [lab-03](../lambda-functions-python/EventsList), pero el código fuente es el siguiente:

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
	print("Received response from DynamoDB: " + json.dumps(response_event, indent=2))
	# Return only the Items and not the whole response from DynamoDB
	return item




[< Volver al Laboratorio 06 ](../../lab-06#crear-endpoint-2) 
