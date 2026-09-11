# Diagrama Entidad-Relación — Monitor

```mermaid
erDiagram
    USUARIOS {
        int id_usuario PK
        varchar nombre
        varchar contrasena
        varchar email UK
    }

    HOSTS {
        int id_host PK
        int id_usuario FK
        varchar nombre_host
        varchar ip_direccion
        varchar ip_mac UK
    }

    LECTURAS {
        int id_lectura PK
        int id_host FK
        decimal cpu
        decimal ram
        decimal disco
        timestamp fecha_hora
    }

    ALERTAS_UMBRALES {
        int id_alerta PK
        int id_host FK
        enum metrica
        decimal valor_limite
        timestamp created_at
        boolean activo
    }

    USUARIOS ||--o{ HOSTS : "posee"
    HOSTS ||--o{ LECTURAS : "genera"
    HOSTS ||--o{ ALERTAS_UMBRALES : "configura"
```

## Notas de diseño

- **`hosts.id_usuario`** es la diferencia clave respecto a versiones
  anteriores del proyecto: ahora cada equipo monitoreado pertenece a un
  usuario específico, no es global para todos los usuarios.
- **`ip_mac` es única** (`UNIQUE`) porque se usa para identificar de qué
  host viene cada lectura que manda el colector de Python — dos hosts no
  pueden compartir la misma MAC.
- **`lecturas`** usa una fila por medición con columnas separadas
  (`cpu`, `ram`, `disco`) en vez de una fila por métrica, porque el
  colector siempre manda las tres juntas y así una sola consulta trae
  todo para graficar.
- **`activo`** en `alertas_umbrales` permite desactivar una alerta sin
  borrarla del historial.
