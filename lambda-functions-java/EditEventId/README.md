
1. Creamos una nueva clase pulsando con el botón derecho sobre el paquete com.accenture.aws.functions->New ->Class
2. Le ponemos un nombre significativo, por ejemplo EditEventItem
3. Pegamos el siguiente código:

		 package com.accenture.aws.functions;
	
		import com.accenture.aws.model.Event;
		import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
		import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
		import com.amazonaws.services.dynamodbv2.document.DynamoDB;
		import com.amazonaws.services.dynamodbv2.document.Item;
		import com.amazonaws.services.dynamodbv2.document.Table;
		import com.amazonaws.services.dynamodbv2.document.UpdateItemOutcome;
		import com.amazonaws.services.dynamodbv2.document.spec.UpdateItemSpec;
		import com.amazonaws.services.dynamodbv2.document.utils.NameMap;
		import com.amazonaws.services.dynamodbv2.document.utils.ValueMap;
		import com.amazonaws.services.dynamodbv2.model.ReturnValue;
		import com.amazonaws.services.lambda.runtime.Context;
		import com.amazonaws.services.lambda.runtime.RequestHandler;
		
		
		public class EditEventItem implements RequestHandler<Event, Event> {
	  
		@Override
	    public Event handleRequest(Event event, Context context) {
	    	
		
			// Configuramos el cliente de dynamodb y la tabla    	    	 
			AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();	    	
		    DynamoDB dynamoDB = new DynamoDB(client);
		    Table table = dynamoDB.getTable("events");
		    
		    //Construimos la query
		    UpdateItemSpec updateItemSpec = new UpdateItemSpec().withPrimaryKey("id", event.getId())
		    		.withUpdateExpression("set #date=:valDate, #title=:valTitle, #addedBy=:valAddedBy, #description=:valDescription, #location=:valLocation")
		    		.withNameMap(new NameMap().with("#date","date")
		    				.with("#title","title")
		    				.with("#addedBy", "addedBy")
		    				.with("#description", "description")
		    				.with("#location", "location"))
		    		.withValueMap(new ValueMap().with(":valDate", event.getDate())
		    				.with(":valTitle", event.getTitle())
		    				.with(":valAddedBy", event.getAddedBy())
		    				.with(":valDescription", event.getDescription())
		    				.with(":valLocation", event.getLocation()))
		    		.withReturnValues(ReturnValue.UPDATED_NEW);
		    		
		            
		    UpdateItemOutcome  outcome = null;
	        try {
	            System.out.println("Updating item...");
	            outcome = table.updateItem(updateItemSpec);
	            System.out.println("UpdateItem succeeded:\n" + outcome.getUpdateItemResult().toString());
	
	        }
	        catch (Exception e) {
	            System.err.println("Unable to update item: " + event.getId());
	            System.err.println(e.getMessage());
	        }
	        
	        //Transformamos la respuesta en un objeto de tipo Event 
	        Item item = outcome.getItem();
		    Event eventItem = new Event(item.getString("id"),
	    			item.getString("title"), 
	    			item.getString("date"), 
	    			item.getString("addedBy"), 
	    			item.getString("description"), 
	    			item.getString("location"));
		    
		    return eventItem;
	
	    	}
	   	}
	



5. Subimos la función a AWS como explicamos en el [laboratorio 03](../EventsList#subir-la-funci%C3%B3n-a-aws)


[< Volver al Laboratorio 06 ](../../lab-06#crear-endpoint-3) 