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

        // --- Utilidades de UI: overlay para bloquear interacción y CSS para mantener visible el chat ---
        function ensureChatVisibleStyles() {
          if (document.getElementById('force-chat-visible')) return;
          const style = document.createElement('style');
          style.id = 'force-chat-visible';
          style.textContent = `
            #embeddedMessagingFrame,
            [id^="embeddedMessaging"] {
              display: block !important;
              visibility: visible !important;
              opacity: 1 !important;
            }
          `;
          document.head.appendChild(style);
        }

        function addChatEndedOverlay(texto = 'La conversación ha finalizado.') {
          // No duplicar
          if (document.getElementById('chat-ended-overlay')) return;

          // Asegura que el chat no se oculte por estilos del SDK
          ensureChatVisibleStyles();

          const iframe = document.getElementById('embeddedMessagingFrame');
          if (!iframe) {
            console.warn('Overlay: no se encontró el iframe embeddedMessagingFrame');
            return;
          }
          let wrapper = iframe.parentElement;
          if (getComputedStyle(wrapper).position === 'static') {
            wrapper.style.position = 'relative';
          }

          const overlay = document.createElement('div');
          overlay.id = 'chat-ended-overlay';
          overlay.setAttribute('role', 'note');
          overlay.style.position = 'absolute';
          overlay.style.inset = '0';
          overlay.style.display = 'flex';
          overlay.style.alignItems = 'center';
          overlay.style.justifyContent = 'center';
          overlay.style.background = 'rgba(0,0,0,0.3)';
          overlay.style.backdropFilter = 'blur(2px)';
          overlay.style.pointerEvents = 'auto';       // bloquea clicks al iframe
          overlay.style.zIndex = '2147483647';
          overlay.style.userSelect = 'none';

          const mensaje = document.createElement('div');
          mensaje.style.background = '#fff';
          mensaje.style.padding = '12px 20px';
          mensaje.style.borderRadius = '10px';
          mensaje.style.boxShadow = '0 2px 10px rgba(0,0,0,0.2)';
          mensaje.style.fontFamily = 'system-ui, sans-serif';
          mensaje.style.textAlign = 'center';
          mensaje.style.fontSize = '13px';
          mensaje.style.color = '#333';
          mensaje.textContent = texto;

          overlay.appendChild(mensaje);
          wrapper.appendChild(overlay);
          console.log('🛡️ Overlay añadido: bloqueando interacción sin cerrar ventana.');
        }

        function removeChatEndedOverlay() {
          const overlay = document.getElementById('chat-ended-overlay');
          if (overlay) {
            overlay.remove();
            console.log('🧽 Overlay retirado.');
          }
        }
        // --- Fin utilidades UI ---

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

          // === Manejo de cierre: parseo del entryPayload + bloqueo de interacción (sin cerrar chat) ===
          window.addEventListener('onEmbeddedMessagingSessionStatusUpdate', (evt) => {
            const d = evt?.detail || evt;
            console.log("📡 onEmbeddedMessagingSessionStatusUpdate (raw):", d);

            const payloadRaw = d?.conversationEntry?.entryPayload;
            let payload = null;
            try {
              payload = payloadRaw ? JSON.parse(payloadRaw) : null;
            } catch (e) {
              console.warn("⚠️ No se pudo parsear entryPayload:", payloadRaw, e);
            }

            const status = payload?.sessionStatus || d?.status || d?.sessionStatus || 'Unknown';
            console.log("🧭 SessionStatus detectado:", status, "| EndedBy:", payload?.sessionEndedByRole || 'N/A');

            if (status === 'Ended') {
              console.log("🏁 Sesión finalizada: BLOQUEO activado (sin cerrar ventana).");
              // Importante: NO llamar a clearSession ni removeAllComponents aquí
              addChatEndedOverlay('La conversación ha finalizado.');
            } else if (status === 'Active' || status === 'Waiting') {
              // Si vuelve a iniciar una conversación válida, retirar el overlay
              removeChatEndedOverlay();
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
