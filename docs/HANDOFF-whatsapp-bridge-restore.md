# Handoff — Restauración bidireccional WhatsApp

## Estado operativo antes de empezar
- Backup literal ya tomado en `/home/ubuntu/backups/vps-audit-20260726_123401/`:
  - `whatsapp-session-hermes412.tar`
  - `.env`
  - `config.yaml`
  - `cron-jobs.json`
- Repo fuente de operación: `/home/ubuntu/projects/hermes-vps-config` (ya limpio y con commits)
- Runtime de Hermes: `/home/ubuntu/.hermes/` **NO** tiene git; es fuente de verdad operativa
- `bridge.js` runtime: `/home/ubuntu/.hermes/hermes-agent/scripts/whatsapp-bridge/bridge.js`
- `bridge.js` repo fuente: `/home/ubuntu/projects/hermes-vps-config/scripts/whatsapp-bridge/bridge.js`
- Rama actual en el repo: `main` (commits limpios con tags `whatsapp-bridge`)

## Regla dura de trabajo
1. El repo `hermes-vps-config` es la **fuente de verdad versionable** de la operación.
2. `~/.hermes` es la **fuente de verdad operativa** en ejecución.
3. Nada se da por bueno solo porque funcione en runtime: cualquier cambio debe reflejarse y commitearse en el repo si es código/política.
4. No tocar `.env` ni `whatsapp/session-hermes412/` sin necesidad explícita de recuperación.

## Qué puede cambiar el agente ahora
- `scripts/whatsapp-bridge/bridge.js`: política allowlist, logging, reenvío de mensajes del owner, reintentos de conexión
- `systemd/*` y `config/*` solo si son parte explícita de la tarea y no implican movimiento de rutas
- Documentación operativa de la rama WhatsApp aquí mismo

## Qué NO debe tocar
- Sesiones de WhatsApp bajo `~/.hermes/whatsapp/session*`
- `.env`, tokens, credenciales, `auth.json`, `google_token.json`, `google_client_secret.json`
- DBs y caches: `*.db*`, `cache/`, `image_cache/`, `audio_cache/`
- Logs: `*.log`, `logs/`, `bridge.log`
- Cronjobs ni sus outputs: `~/.hermes/cron/`
- Otros proyectos bajo `/home/ubuntu/projects/` ajenos a `hermes-vps-config`

## Esquema de ramas/commits recomendado
- Trabajar en rama `whatsapp/bridge-restore` desde `main`
- Commits con prefijo: `fix(whatsapp-bridge):`, `chore(whatsapp-bridge):`, `docs(whatsapp-bridge):`
- Antes de modificar, comparar diff entre runtime y repo con:
  - `diff /home/ubuntu/.hermes/hermes-agent/scripts/whatsapp-bridge/bridge.js /home/ubuntu/projects/hermes-vps-config/scripts/whatsapp-bridge/bridge.js`

## Criterio de verificación
1. No introducirsecretos en `hermes-vps-config` (revisar diff/status completo antes de commit)
2. No borrar ni reemplazar `scripts/whatsapp-bridge/bridge.js` sin commit previo del estado actual
3. Dejar un mensaje final de handoff actualizado en este mismo documento

## Backups disponibles
- Backup pre-intervención: `/home/ubuntu/backups/vps-audit-20260726_123401/`
- Para restore puntual de sesión WhatsApp solo:
  - `sudo systemctl --user stop whatsapp-bridge.service 2>/dev/null || true`
  - `mv ~/.hermes/whatsapp/session-hermes412 ~/.hermes/whatsapp/session-hermes412.bak`
  - `mkdir -p ~/.hermes/whatsapp && tar -xf /home/ubuntu/backups/vps-audit-20260726_123401/whatsapp-session-hermes412.tar -C ~/.hermes/whatsapp`
  - Reiniciar bridge/service aplicable

## Notas especificas detectadas
- Runtime no tiene git; el repo sí tiene remote configurado en `origin` como `https://github.com/romensuarezr/hermes-vps-config.git`
- Ya existe diff entre runtime y repo en `bridge.js` por bloques de debug logging; no considerar ese diff como “error”, es estado intencional de auditoría
- Política de mensajes del owner: configurable por env `WHATSAPP_FORWARD_OWNER_MESSAGES`; no mezclar con allowlists de grupos
