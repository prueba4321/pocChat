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
        embeddedservice_bootstrap.settings.language = 'es';
        embeddedservice_bootstrap.settings.disableReconnect = true; // evita crear nueva sesión tras finalizar

        // ===== Overlay que SOLO cubre el COMPOSER (parte inferior del iframe) =====
        let composerOverlay = null;
        let detachFns = [];

        // Ajusta estos límites si tu composer es más alto/bajo
        const MIN_H = 90;   // px
        const MAX_H = 220;  // px
        const FRACTION = 0.22; // 22% de la altura del iframe

        function positionOverlayOnComposer(overlay, iframeEl) {
          const rect = iframeEl.getBoundingClientRect();
          const composerHeight = Math.max(MIN_H, Math.min(MAX_H, Math.floor(rect.height * FRACTION)));

          overlay.style.position = 'fixed';
          overlay.style.left = rect.left + 'px';
          overlay.style.width = rect.width + 'px';
          overlay.style.height = composerHeight + 'px';
          overlay.style.top = (rect.bottom - composerHeight) + 'px';

          overlay.style.zIndex = '2147483647'; // sobre el iframe
          overlay.style.pointerEvents = 'auto'; // captura clicks/teclado SOLO en esa franja
          overlay.style.background = 'linear-gradient(to top, rgba(0,0,0,0.28), rgba(0,0,0,0.00))';
          overlay.style.userSelect = 'none';
          overlay.style.display = 'flex';
          overlay.style.alignItems = 'flex-end';
          overlay.style.justifyContent = 'center';
          overlay.style.padding = '10px';
        }

        function addComposerOverlay(texto = 'La conversación ha finalizado.') {
          if (composerOverlay) return;
          const iframe = document.getElementById('embeddedMessagingFrame');
          if (!iframe) { console.warn('Overlay: no se encontró embeddedMessagingFrame'); return; }

          const overlay = document.createElement('div');
          overlay.id = 'chat-composer-overlay';

          const chip = document.createElement('div');
          chip.textContent = texto;
          chip.style.background = '#fff';
          chip.style.padding = '8px 12px';
          chip.style.borderRadius = '999px';
          chip.style.boxShadow = '0 4px 14px rgba(0,0,0,0.15)';
          chip.style.fontFamily = 'system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif';
          chip.style.fontSize = '12px';
          chip.style.color = '#111';
          chip.style.marginBottom = '8px';
          overlay.appendChild(chip);

          positionOverlayOnComposer(overlay, iframe);

          const onResize = () => positionOverlayOnComposer(overlay, iframe);
          const onScroll = () => positionOverlayOnComposer(overlay, iframe);
          window.addEventListener('resize', onResize);
          window.addEventListener('scroll', onScroll, true);
          detachFns = [() => window.removeEventListener('resize', onResize),
                       () => window.removeEventListener('scroll', onScroll, true)];

          document.body.appendChild(overlay);
          composerOverlay = overlay;
          console.log('🛡️ Overlay del COMPOSER activado.');
        }

        function removeComposerOverlay() {
          if (composerOverlay) {
            detachFns.forEach(fn => fn());
            detachFns = [];
            composerOverlay.remove();
            composerOverlay = null;
            console.log('🧽 Overlay del COMPOSER retirado.');
          }
        }
        // ===== FIN overlay de composer =====

        window.addEventListener("onEmbeddedMessagingReady", () => {
          console.log("✅ onEmbeddedMessagingReady");

          embeddedservice_bootstrap.prechatAPI.setVisiblePrechatFields({
            "_lastname": { "value": "Jane", "isEditableByEndUser": false },
            "_language": { "value": "Spanish", "isEditableByEndUser": false },
            "c__language": { "value": "Spanish", "isEditableByEndUser": false },
            "language": { "value": "Spanish", "isEditableByEndUser": false }
          });

          // Detecta el fin de sesión y bloquea SOLO el composer (sin cerrar ni ocultar el chat)
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
              console.log("🏁 Sesión finalizada: BLOQUEO del composer activado.");
              addComposerOverlay('La conversación ha finalizado.');
            } else if (status === 'Active' || status === 'Waiting') {
              removeComposerOverlay();
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
