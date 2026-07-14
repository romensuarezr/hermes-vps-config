# Operativa — Hermes VPS

## Servicios

| Servicio | Unit | Puerto | Qué hace |
|----------|------|--------|----------|
| Gateway | `hermes-gateway.service` | configurable | Integración con plataformas |
| Dashboard | `hermes-dashboard.service` | 9119 | Dashboard TUI |
| WhatsApp bridge | `whatsapp-bridge.service` | 3001 | Bridge WhatsApp |
| Groups watcher | `whatsapp-groups-watcher.service` | — | Watcher de grupos conocidos |

## Refresh de token de Google

1. `hermes tools --revoke`
2. Re-autenticar desde TUI o CLI.

## Bridge WhatsApp

Parches activos:
- `owner_message_gate.js` → gate propietario
- `allowlist.js` → allowlist de grupos
- `outbound_ids.js` → control de IDs outbound

Tests: `cd scripts/whatsapp-bridge && npm test`

Cualquier cambio en bridge: actualizar aqui y en el VPS.

## Troubleshooting rápido

```bash
journalctl --user -u hermes-gateway -f
journalctl --user -u whatsapp-bridge -f
journalctl --user -u whatsapp-groups-watcher -f
```

## Backups y rollback

- `git log` para historial de config.
- Revertir `config/hermes.config.yaml` a versión anterior: `git checkout <sha> -- config/hermes.config.yaml && systemctl --user restart hermes-gateway`.
- Session de WhatsApp NO está en git; hacer backup manual de `~/.hermes/whatsapp/session/` si se requiere.
