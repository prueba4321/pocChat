<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1, minimum-scale=1" />
  <title>pocChat</title>

  <style>
    iframe.embeddedMessagingFrame {
      position: fixed !important;
      top: 50% !important;
      left: 50% !important;
      transform: translate(-50%, -50%) !important;
      width: 95vw !important;
      height: 95vh !important;
      max-width: 1200px;
      max-height: 800px;
      border: none !important;
      z-index: 9999 !important;
      box-shadow: 0 0 20px rgba(0,0,0,0.5);
      border-radius: 8px;
    }
    body { margin: 0; padding: 0; overflow: hidden; }
  </style>
</head>

<body>
  <h1 style="padding:16px;margin:0;">Hola Mundo</h1>

  <script>
    function getUrlParams() {
      const p = new URLSearchParams(window.location.search);
      return {
        language: p.get("language") || "Spanish",
        marketer: p.get("marketer") || "",
        firstName: p.get("firstName") || p.get("FirstName") || "",
        lastName: p.get("lastName") || p.get("LastName") || "",
        email: p.get("Email") || p.get("email") || "",
        nif: p.get("NIF") || p.get("nif") || ""
      };
    }

    function initEmbeddedMessaging() {
      try {
        const params = getUrlParams();

        embeddedservice_bootstrap.settings.language = "es";
        embeddedservice_bootstrap.settings.disableReconnect = true;

        // Se ejecuta SOLO una vez aunque el evento se dispare más veces
        window.addEventListener("onEmbeddedMessagingReady", () => {
          console.log("✅ onEmbeddedMessagingReady (once)");

          // Visible fields (solo los que tengas en Pre-Chat Form)
          embeddedservice_bootstrap.prechatAPI.setVisiblePrechatFields({
            FirstName: { value: params.firstName, isEditableByEndUser: true },
            LastName:  { value: params.lastName,  isEditableByEndUser: true },
            NIF:       { value: params.nif,       isEditableByEndUser: true },
            Email:     { value: params.email,     isEditableByEndUser: true }
          });

          // Hidden fields (solo si los tienes configurados en Hidden Pre-Chat Fields)
          if (embeddedservice_bootstrap.prechatAPI.setHiddenPrechatFields) {
            embeddedservice_bootstrap.prechatAPI.setHiddenPrechatFields({
              market:   { value: params.marketer, isEditableByEndUser: false },
              language: { value: params.language, isEditableByEndUser: false }
            });
          }
        }, { once: true });

        embeddedservice_bootstrap.init(
          "00DfZ0000004KZd",
          "Chat_Area_Abierta",
          "https://endesab2c--prejun25.sandbox.my.site.com/ESWChatAreaAbierta1766997065183",
          { scrt2URL: "https://endesab2c--prejun25.sandbox.my.salesforce-scrt.com" }
        );

      } catch (err) {
        console.error("❌ Error loading Embedded Messaging:", err);
      }
    }
  </script>

  <script
    type="text/javascript"
    src="https://endesab2c--prejun25.sandbox.my.site.com/ESWChatAreaAbierta1766997065183/assets/js/bootstrap.min.js"
    onload="initEmbeddedMessaging()">
  </script>
</body>
</html>
