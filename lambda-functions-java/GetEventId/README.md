
1. Creamos una nueva clase pulsando con el botón derecho sobre el paquete com.accenture.aws.functions->New ->Class
2. Le ponemos un nombre significativo, por ejemplo GetEventItem
3. Pegamos el siguiente código:

	 

		package com.accenture.aws.functions;

		import com.accenture.aws.model.Event;
		import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
		import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
		import com.amazonaws.services.dynamodbv2.document.DynamoDB;
		import com.amazonaws.services.dynamodbv2.document.Item;
		import com.amazonaws.services.dynamodbv2.document.Table;
		import com.amazonaws.services.dynamodbv2.document.spec.GetItemSpec;
		import com.amazonaws.services.lambda.runtime.Context;
		import com.amazonaws.services.lambda.runtime.RequestHandler;

		
		public class GetEventItem implements RequestHandler<Event, Event> {
	
	
		@Override
		public Event handleRequest(Event event, Context context) {
	
			System.out.println("Input "+event.getId());
						
			Item item = null;
			
			String eventId = event.getId();
			
			// Configuramos el cliente de dynamodb y la tabla
			AmazonDynamoDB client = AmazonDynamoDBClientBuilder.defaultClient();
			
			DynamoDB dynamoDB = new DynamoDB(client);
		    Table table = dynamoDB.getTable("events");
	
		    // Construimos la query
		    GetItemSpec spec = new GetItemSpec().withPrimaryKey("id",eventId);
	
		    try {
		        System.out.println("Attempting to read the item...");
		        item = table.getItem(spec);
		        System.out.println("GetItem succeeded: " + item);
	
		    }
		    catch (Exception e) {
		        System.err.println("Unable to read item: " +eventId);
		        System.err.println(e.getMessage());
		    }
		    
		    // Transformamos la salida en un evento de tipo Event
		    Event eventItem = new Event(item.get("id").toString(),
	    			item.get("title").toString(), 
	    			item.get("date").toString(), 
	    			item.get("addedBy").toString(), 
	    			item.get("description").toString(), 
	    			item.get("location").toString());
		    
		    return eventItem;
		}
	
		}




5. Subimos la función a AWS como explicamos en el [laboratorio 03](../EventsList#subir-la-funci%C3%B3n-a-aws)


[< Volver al Laboratorio 06 ](../lab-06#crear-endpoint-2) 