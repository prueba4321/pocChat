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
      if (iframe && iframe.contentWindow) {
        iframe.contentWindow.postMessage(
          {
            type: "SET_LANGUAGE",
            language: "Spanish",
            hostUrl: window.location.origin
          },
          "https://endesab2c--prejun25.sandbox.my.site.com"
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
        embeddedservice_bootstrap.settings.disableReconnect = true;

        window.addEventListener("onEmbeddedMessagingReady", () => {
          const event = new CustomEvent('externalLanguage', { detail: { language: 'Spanish' } });
          window.dispatchEvent(event);
          console.log("✅ onEmbeddedMessagingReady");

          embeddedservice_bootstrap.prechatAPI.setVisiblePrechatFields({
            "_lastname": { "value": "Jane", "isEditableByEndUser": false },
            "_language": { "value": "Spanish", "isEditableByEndUser": false },
            "c__language": { "value": "Spanish", "isEditableByEndUser": false },
            "language": { "value": "Spanish", "isEditableByEndUser": false }
          });

          // === BLOQUE DE DEPURACIÓN ===
          (function () {
            function disableComposerUI() {
              console.log("🧱 Deshabilitando composer...");
              const root = document.querySelector('[id^="embeddedMessaging"]') || document;
              const composer = root.querySelector('textarea, input[type="text"], [contenteditable="true"]');
              if (composer) {
                composer.setAttribute('disabled', 'true');
                composer.style.pointerEvents = 'none';
                composer.style.opacity = '0.5';
              }
              const sendBtn = root.querySelector('button');
              if (sendBtn) {
                sendBtn.setAttribute('disabled', 'true');
                sendBtn.style.pointerEvents = 'none';
                sendBtn.style.opacity = '0.5';
              }
            }

            async function hardEndSessionAndRemove() {
              console.log("🧹 Ejecutando hardEndSessionAndRemove()");
              try {
                if (embeddedservice_bootstrap?.userVerificationAPI?.clearSession) {
                  console.log("🧾 Llamando clearSession(true)");
                  await embeddedservice_bootstrap.userVerificationAPI.clearSession(true);
                }
                if (embeddedservice_bootstrap?.utilAPI?.removeAllComponents) {
                  console.log("🧾 Llamando removeAllComponents()");
                  embeddedservice_bootstrap.utilAPI.removeAllComponents();
                } else {
                  console.log("🧾 Ocultando contenedor manualmente");
                  const container = document.getElementById('embeddedMessagingFrame')?.closest('div');
                  if (container) container.style.display = 'none';
                }
              } catch (e) {
                console.error('❌ Error al limpiar sesión de Messaging:', e);
              }
            }

            try {
              const api = embeddedservice_bootstrap?.messagingAPI;
              if (api?.assignMessagingEventHandler && api?.MESSAGING_EVENT) {
                console.log("🎯 messagingAPI detectada. Registrando handlers...");

                const stopInput = (evt) => {
                  console.log("⚡ Evento detectado:", evt?.type || 'custom');
                  disableComposerUI();
                  hardEndSessionAndRemove();
                };

                if (api.MESSAGING_EVENT.PARTICIPANT_LEFT) {
                  api.assignMessagingEventHandler(api.MESSAGING_EVENT.PARTICIPANT_LEFT, (e) => {
                    console.log("👋 PARTICIPANT_LEFT detectado:", e);
                    stopInput(e);
                  });
                }

                if (api.MESSAGING_EVENT.CONVERSATION_ENDED) {
                  api.assignMessagingEventHandler(api.MESSAGING_EVENT.CONVERSATION_ENDED, (e) => {
                    console.log("🏁 CONVERSATION_ENDED detectado:", e);
                    stopInput(e);
                  });
                }
              } else {
                console.warn("⚠️ messagingAPI no encontrada, usando fallback...");
                window.addEventListener('onEmbeddedMessagingSessionStatusUpdate', (evt) => {
                  const d = evt?.detail || evt;
                  console.log("📡 onEmbeddedMessagingSessionStatusUpdate:", d);
                  if (d && (d.status === 'Ended' || d.sessionStatus === 'Ended')) {
                    disableComposerUI();
                    hardEndSessionAndRemove();
                  }
                });
              }
            } catch (e) {
              console.error('❌ No pude enganchar handlers de Messaging:', e);
            }

            // Observador visual de respaldo
            const obs = new MutationObserver(() => {
              const ended = Array.from(document.querySelectorAll('[id^="embeddedMessaging"]'))
                .some(n => /finalizad[oa]|ended|conversation\s+closed/i.test(n.textContent || ''));
              if (ended) {
                console.log("👀 Texto de finalización detectado en el DOM.");
                disableComposerUI();
              }
            });
            obs.observe(document.body, { subtree: true, childList: true, characterData: true });
          })();
          // === FIN BLOQUE DE DEPURACIÓN ===
        });

        const urlParams = getUrlParams();
        console.log("urlParams: ", urlParams);
        const langua = urlParams['language'];
        const baseUrl = 'https://endesab2c--prejun25.sandbox.my.site.com/ESWMLChatAreaPrivada1757594052632';
        const urlWithParams = `${baseUrl}?language=${encodeURIComponent(langua)}`;

        embeddedservice_bootstrap.init(
          '00DfZ0000004KZd',
          'ML_Chat_Area_Privada',
          // urlWithParams,
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
