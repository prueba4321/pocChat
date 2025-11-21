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

        // ===== Overlay que SOLO cubre el iframe del chat =====
        let chatOverlay = null;
        let repositionFns = [];

        function positionOverlayOverIframe(overlay, iframeEl) {
          const rect = iframeEl.getBoundingClientRect();
          overlay.style.position = 'fixed';
          overlay.style.left = rect.left + 'px';
          overlay.style.top = rect.top + 'px';
          overlay.style.width = rect.width + 'px';
          overlay.style.height = rect.height + 'px';
          overlay.style.zIndex = '2147483647'; // sobre el iframe
          overlay.style.pointerEvents = 'auto'; // captura clicks
          overlay.style.background = 'rgba(0,0,0,0.28)';
          overlay.style.backdropFilter = 'blur(1px)';
          overlay.style.display = 'flex';
          overlay.style.alignItems = 'center';
          overlay.style.justifyContent = 'center';
          overlay.style.userSelect = 'none';
        }

        function addChatEndedOverlay(texto = 'La conversación ha finalizado.') {
          if (chatOverlay) return;
          const iframe = document.getElementById('embeddedMessagingFrame');
          if (!iframe) { console.warn('Overlay: no se encontró embeddedMessagingFrame'); return; }

          const overlay = document.createElement('div');
          overlay.id = 'chat-ended-overlay';
          const card = document.createElement('div');
          card.style.background = '#fff';
          card.style.padding = '10px 14px';
          card.style.borderRadius = '10px';
          card.style.boxShadow = '0 4px 16px rgba(0,0,0,0.18)';
          card.style.fontFamily = 'system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif';
          card.style.fontSize = '13px';
          card.style.color = '#111';
          card.style.textAlign = 'center';
          card.textContent = texto;
          overlay.appendChild(card);

          // Coloca y escucha resize/scroll para mantenerlo exactamente sobre el iframe
          positionOverlayOverIframe(overlay, iframe);
          const onResize = () => positionOverlayOverIframe(overlay, iframe);
          const onScroll = () => positionOverlayOverIframe(overlay, iframe);
          window.addEventListener('resize', onResize);
          window.addEventListener('scroll', onScroll, true); // true: captura scrolls internos
          repositionFns = [() => window.removeEventListener('resize', onResize),
                           () => window.removeEventListener('scroll', onScroll, true)];

          document.body.appendChild(overlay);
          chatOverlay = overlay;
          console.log('🛡️ Overlay activado SOLO sobre el chat.');
        }

        function removeChatEndedOverlay() {
          if (chatOverlay) {
            repositionFns.forEach(fn => fn());
            repositionFns = [];
            chatOverlay.remove();
            chatOverlay = null;
            console.log('🧽 Overlay retirado.');
          }
        }
        // ===== FIN overlay =====

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

          // Detecta el fin de sesión y bloquea SOLO el chat (sin cerrarlo ni tocar la página)
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
              console.log("🏁 Sesión finalizada: BLOQUEO de interacción SOLO en el iframe.");
              // OJO: no llamamos a clearSession para no afectar visibilidad del widget
              addChatEndedOverlay('La conversación ha finalizado.');
            } else if (status === 'Active' || status === 'Waiting') {
              removeChatEndedOverlay();
            }
          });
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
