<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
iframe.embeddedMessagingFrame {
            position: fixed !important; /* Para que se quede sobre todo */
            top: 50% !important;
            left: 50% !important;
            transform: translate(-50%, -50%) !important; /* Centrado exacto */
            width: 95vw !important; /* Ocupa casi toda la pantalla */
            height: 95vh !important; /* Ocupa casi toda la pantalla */
            max-width: 1200px; /* Opcional, para no exagerar en pantallas muy grandes */
            max-height: 800px; /* Opcional */
            border: none !important;
            z-index: 9999 !important; /* Por encima de todo */
            box-shadow: 0 0 20px rgba(0,0,0,0.5); /* Para que se vea más destacado */
            border-radius: 8px; /* Opcional, bordes redondeados */
        }
        body {
            margin: 0;
            padding: 0;
            overflow: hidden; /* Evita scroll cuando el chat está centrado */
        }

</style>
</head>
<body>
    Hola
    <script>
  window.addEventListener("onEmbeddedMessagingReady", () => {
    console.log("✅ onEmbeddedMessagingReady fired");
  });
</script>
<script>
  function initEmbeddedMessaging() {
    try {
      embeddedservice_bootstrap.settings.language = "es";
      embeddedservice_bootstrap.settings.hideChatButtonOnLoad = false; // o true si lo ocultas
      embeddedservice_bootstrap.init(
        "00DfZ0000004KZd",
        "Chat_Area_Abierta",
        "https://endesab2c--prejun25.sandbox.my.site.com/ESWMLChatAreaPrivada1757594052632",
        { scrt2URL: "https://endesab2c--prejun25.sandbox.my.salesforce-scrt.com" }
      );
      console.log("✅ init() ejecutado");
    } catch (e) {
      console.error("❌ Error initEmbeddedMessaging:", e);
    }
  }

  window.addEventListener("onEmbeddedMessagingReady", () => {
    console.log("✅ onEmbeddedMessagingReady fired");
  });

  window.addEventListener("onEmbeddedMessagingButtonCreated", () => {
    console.log("✅ onEmbeddedMessagingButtonCreated fired -> launchChat()");
    embeddedservice_bootstrap.utilAPI.launchChat()
      .then(() => console.log("✅ launchChat OK"))
      .catch(e => console.error("❌ launchChat error", e));
  });
</script>
<script
  type="text/javascript"
  src="https://endesab2c--prejun25.sandbox.my.site.com/ESWChatAreaAbierta1766997065183/assets/js/bootstrap.min.js"
  onload="initEmbeddedMessaging()">
</script>

</body>
</html>
