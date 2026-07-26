# Convención operativa — hermes-vps-config

Este repo es la representación versionable y curada de la parte no sensible de la operativa de Hermes en el VPS.

## Fuentes de verdad

- **VPS runtime:** `/home/ubuntu/.hermes` y `/home/ubuntu/.config/systemd/user`
- **Repo versionable:** `/home/ubuntu/projects/hermes-vps-config`

El repo solo contiene la parte **no sensible**: units de systemd, config activa no sensible,
scripts bridge (sin sesiones ni node_modules), y documentación operativa.

## Reglas de modificación

1. Trata `/home/ubuntu/.hermes` como fuente de verdad operativa.
2. Trata `/home/ubuntu/projects/hermes-vps-config` como espejo versionable y curado.
3. Nunca asumas que un cambio en el repo debe aplicarse al runtime sin confirmación explícita del usuario.
4. Nunca asumas que un cambio en el runtime debe commitearse sin revisar diff y alcance.
5. Antes de modificar cualquier cosa, compara VPS real vs repo y explica la diferencia.
6. Si un cambio afecta servicios, config activa o scripts usados por systemd, primero valida en el VPS.
7. Solo después, si procede, refleja la parte versionable en el repo.
8. No toques secretos, sesiones, caches, logs, tokens ni credenciales.
9. No muevas rutas actuales ni reestructures `/home/ubuntu/.hermes` sin aprobación explícita.

## Lo que nunca entra en el repo

- `.hermes/.env`, tokens, claves OAuth, sesiones WhatsApp
- `*.log`, caches, bases de datos SQLite
- `node_modules/`, `venv/`, archivos compilados
- Datos efímeros o runtime

## Backups y rollback

Este repo sirve para recuperar configuraciones ante regresiones operativas.
No reemplaza backups de datos de usuario ni de estado de WhatsApp.

## Despliegue en otro VPS

Ver README.md para el flujo manual. No hay despliegues automáticos desde este repo.
