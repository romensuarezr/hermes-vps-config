# hermes-vps-config

Repositorio privado para versionar la configuración y operativa de Hermes Agent en este VPS.  
No incluye secretos, tokens, sesiones ni datos runtime.

## Estructura

```
systemd/           # Units y overrides de systemd user
config/            # Configuración no sensible de Hermes
scripts/           # Scripts auxiliares (sin node_modules ni sesiones)
  ├── whatsapp-bridge/   # Bridge WhatsApp (patches aplicados, sin dependencias)
  └── whatsapp-groups-watcher
docs/              # Operativa y runbooks
```

## Lo que NO está aquí

- `~/.hermes/.env`
- `~/.hermes/whatsapp/session/` y `credentials.json`
- `~/.hermes/google_token.json`, `google_client_secret.json`
- `~/.hermes/auth.json`, `~/.hermes/sessions/`
- Logs (`~/.hermes/logs/`, `whatsapp/bridge.log`)
- Caches y node_modules

## Uso para despliegue reproducible

1. `git clone` en el VPS destino.
2. Copiar con cuidado:
   - `systemd/*.service` → `~/.config/systemd/user/`
   - `config/hermes.config.yaml` → `~/.hermes/config.yaml`
   - `scripts/whatsapp-bridge/` → `~/.hermes/hermes-agent/scripts/whatsapp-bridge/`
   - Crear `.env` en `~/.hermes/` con tus propios secretos.
   - Iniciar sesión de WhatsApp: el node generará credenciales en `~/.hermes/whatsapp/session/`.
3. `systemctl --user daemon-reload && systemctl --user enable --now hermes-gateway.service`
4. Ajusta rutas de `HERMES_HOME` en los units al entorno destino.

## Notas

- Todos los units referencian `/home/ubuntu` como `HERMES_HOME` y rutas. Ajustar si el VPS destino usa un usuario diferente.
- `bridge.js`, `owner_message_gate.js`, `allowlist.js` incluyen los patches de allowlist y owner gate aplicados.
- Para ver lista de archivos que nunca se versionan, consulta `.gitignore`.
