# Monitor — Base de Datos

Repositorio independiente con el esquema de MySQL usado por el Backend del
proyecto Monitor.

## Estructura

```
Base-de-Datos/
├── 01_schema.sql     # Creación de la base de datos y las 4 tablas
├── 02_seed.sql        # Usuario, hosts y alertas de prueba
├── diagrama-er.png     # Diagrama Entidad-Relación (imagen)
└── diagrama-er.md       # Mismo diagrama en formato Mermaid (se ve directo en GitHub)
```

## Tablas

| Tabla | Qué representa | Llave primaria | Llaves foráneas |
|---|---|---|---|
| `usuarios` | Quién puede iniciar sesión | `id_usuario` | — |
| `hosts` | Un equipo monitoreado, pertenece a un usuario | `id_host` | `id_usuario` → `usuarios.id_usuario` |
| `lecturas` | Una medición de CPU/RAM/Disco de un host | `id_lectura` | `id_host` → `hosts.id_host` |
| `alertas_umbrales` | Un umbral de alerta configurado para un host | `id_alerta` | `id_host` → `hosts.id_host` |

## Relaciones

```
usuarios (1) ──< (N) hosts (1) ──< (N) lecturas
                          │
                          └──< (N) alertas_umbrales
```

- Un usuario puede tener muchos hosts, pero cada host pertenece a un solo usuario.
- Un host puede tener muchas lecturas y muchas alertas, pero cada lectura/alerta
  pertenece a un solo host.
- Todas las llaves foráneas usan `ON DELETE CASCADE`: si se borra un host, se
  borran también sus lecturas y alertas (no quedan registros huérfanos).

## Cómo usarla

Estos scripts los ejecuta automáticamente `docker-compose.yml` (en el repo de
Backend) la primera vez que se levanta el contenedor de MySQL — no hace falta
correrlos a mano. Si quieres probarlos manualmente:

```bash
mysql -u root -p < 01_schema.sql
mysql -u root -p monitor_db < 02_seed.sql
```

## Datos de prueba incluidos

- Usuario: `admin@monitor.com` / contraseña `admin123`
- 3 hosts (`equipo-1`, `equipo-2`, `equipo-3`), cada uno con su `ip_mac` — esa
  MAC es la que usa el colector de Python del Backend para identificarse al
  enviar lecturas (ver el repo de Backend para más detalle).
- 3 umbrales de ejemplo (CPU y RAM en `equipo-1`, Disco en `equipo-2`).
