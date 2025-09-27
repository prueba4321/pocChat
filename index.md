<!DOCTYPE html>
<meta name="viewport" content="width=device-width, initial-scale=1, minimum-scale=1">
<html lang="es">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Hola Mundo 2</title>
</head>
<body>
<script type='text/javascript'>
	function initEmbeddedMessaging() {
		try {
			embeddedservice_bootstrap.settings.language = 'es'; // For example, enter 'en' or 'en-US'
			//Añadido
			/*window.addEventListener("onEmbeddedMessagingReady", () => {            
			console.log( "Inside Prechat API!!" );
			window.addEventListener("onEmbeddedMessagingReady", e => {
			  embeddedservice_bootstrap.prechatAPI.setVisiblePrechatFields({
			   "_firstName": {
			      "value": "Jane",
			      "isEditableByEndUser": false
			    },
			    "dropdown_prechat": {
			      "value": "A2",
			      "isEditableByEndUser": false
			    },
			    "language": {
			      "value": "Spanish",
			      "isEditableByEndUser": false
			    }
			  });
			});
			});*/
			//Fin de añadido

			embeddedservice_bootstrap.init(
				'00DfZ0000004KZd',
				'ML_Chat_Area_Privada',
				'https://endesab2c--prejun25.sandbox.my.site.com/ESWMLChatAreaPrivada1757594052632',
				{
					scrt2URL: 'https://endesab2c--prejun25.sandbox.my.salesforce-scrt.com?language=Spanish'
				}
			);
		} catch (err) {
			console.error('Error loading Embedded Messaging: ', err);
		}
	};
</script>
<script type='text/javascript' src='https://endesab2c--prejun25.sandbox.my.site.com/ESWMLChatAreaPrivada1757594052632/assets/js/bootstrap.min.js' onload='initEmbeddedMessaging()'></script>
  <h1>Hola Mundo 3</h1>
</body>
</html>
