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
    window.addEventListener("load", () => {
      const iframe = document.getElementById("embeddedMessagingFrame");
      // Envía el idioma dinámico al iframe
      if (iframe && iframe.contentWindow) { // evita error si aún no existe
        iframe.contentWindow.postMessage(
          {
            type: "SET_LANGUAGE",
            language: "Spanish",
            hostUrl: window.location.origin
          },
          "https://endesab2c--prejun25.sandbox.my.site.com" // dominio destino exacto
        );
      }
    });

    function getUrlParams() {
      const params = {};
      const queryString = window.location.search.substring(1);
      const regex = /([^&=]+)=([^&]*)/g;
      let m;
      while ((m = regex.exec(queryString))) {
        params[decodeURIComponent(m[1])] = decodeURIComponent(m[2]);
      }
      return params;
    }

    const urlParams = getUrlParams();
    console.log("urlParams: ", urlParams);
    localStorage.setItem("chatLanguage", urlParams['language']);
    console.log('localStorage: ', localStorage);

    function initEmbeddedMessaging() {
      try {
        embeddedservice_bootstrap.settings.language = 'es'; // For example, enter 'en' or 'en-US'
        embeddedservice_bootstrap.settings.disableReconnect = true; // evita crear nueva sesión tras finalizar

        window.addEventListener("onEmbeddedMessagingReady", () => {
          // Disparamos un evento global con el language
          const event = new CustomEvent('externalLanguage', { detail: { language: 'Spanish' } });
          window.dispatchEvent(event);
          console.log("✅ onEmbeddedMessagingReady");

          //embedded_svc.settings.language = urlParams['language'];
          embeddedservice_bootstrap.prechatAPI.setVisiblePrechatFields({
            "_lastname": {
              "value": "Jane",
              "isEditableByEndUser": false
            },
            "_language": {
              "value": "Spanish",
              "isEditableByEndUser": false
            },
            "c__language": {
              "value": "Spanish",
              "isEditableByEndUser": false
            },
            "language": {
              "value": "Spanish",
              "isEditableByEndUser": false
            }
          });

          // === Manejo de cierre: parseo del entryPayload + acciones finales ===
          window.addEventListener('onEmbeddedMessagingSessionStatusUpdate', async (evt) => {
            const d = evt?.detail || evt;
            // Log completo para depurar
            console.log("📡 onEmbeddedMessagingSessionStatusUpdate (raw):", d);

            // El estado real viene en entryPayload como JSON string:
            const payloadRaw = d?.conversationEntry?.entryPayload;
            let payload = null;
            try {
              payload = payloadRaw ? JSON.parse(payloadRaw) : null;
            } catch (e) {
              console.warn("⚠️ No se pudo parsear entryPayload:", payloadRaw, e);
            }

            const status =
              payload?.sessionStatus || d?.status || d?.sessionStatus || 'Unknown';

            console.log("🧭 SessionStatus detectado:", status,
              "| EndedBy:", payload?.sessionEndedByRole || 'N/A');

            if (status === 'Ended') {
              console.log("🏁 Sesión finalizada: ejecutando cleanup (clearSession + remove iframe)");
              try {
                if (embeddedservice_bootstrap?.userVerificationAPI?.clearSession) {
                  console.log("🧾 Llamando clearSession(true)...");
                  await embeddedservice_bootstrap.userVerificationAPI.clearSession(true);
                }
              } catch (e) {
                console.error("❌ Error en clearSession:", e);
              }

            }
          });
          // === FIN manejo de cierre ===
        });

        // Inicializar Embedded Service con el language en la URL
        const urlParams = getUrlParams();
        console.log("urlParams: ", urlParams);
        const langua = urlParams['language'];
        const baseUrl = 'https://endesab2c--prejun25.sandbox.my.site.com/ESWMLChatAreaPrivada1757594052632';
        const urlWithParams = `${baseUrl}?language=${encodeURIComponent(langua)}`;
        //Fin de añadido

        embeddedservice_bootstrap.init(
          '00DfZ0000004KZd',
          'ML_Chat_Area_Privada',
          //urlWithParams,
          'https://endesab2c--prejun25.sandbox.my.site.com/ESWMLChatAreaPrivada1757594052632',
          {
            scrt2URL: 'https://endesab2c--prejun25.sandbox.my.salesforce-scrt.com'
          }
        );
      } catch (err) {
        console.error('❌ Error loading Embedded Messaging: ', err);
      }
    };
  </script>

  <script
    type='text/javascript'
    src='https://endesab2c--prejun25.sandbox.my.site.com/ESWMLChatAreaPrivada1757594052632/assets/js/bootstrap.min.js'
    onload='initEmbeddedMessaging()'>
  </script>

  <h1>Hola Mundo 4</h1>
</body>
</html>
