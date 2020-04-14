# Edit Event - Python Version

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


[< Volver al Laboratorio 06 ](../../lab-06#crear-endpoint-3) 
