# Infraestructura LAMP para PYME

## Descripción del proyecto

Documentación técnica completa del despliegue de una infraestructura LAMP (Linux, Apache, MySQL, PHP) con monitorización, copias de seguridad y plan de recuperación ante desastres para una pequeña empresa.

## Autor

- **vjp-victorMC**

## Tecnologías documentadas

| Componente | Versión | Rol |
|---|---|---|
| Ubuntu Server | 22.04 LTS | Sistema operativo |
| Apache | 2.4.60 | Servidor web |
| PHP | 8.2 | Lenguaje de scripting |
| MySQL | 8.0 | Base de datos |
| UFW | - | Firewall |
| Netdata | latest | Monitorización |
| Certbot | 2.9 | SSL/TLS |

## Estructura de la documentación

```
infraestructura-lamp-pyme/
├── README.md
├── CHANGELOG.md
├── REVISION.md
├── tareas.md
└── docs/
    ├── 01-analisis.md
    ├── 02-diseno.md
    ├── 03-planificacion.md
    ├── 04-instalacion/
    │   ├── servidor-web.md
    │   ├── base-de-datos.md
    │   ├── ssh-firewall.md
    │   ├── monitorizacion.md
    │   └── backups.md
    ├── 05-operacion.md
    └── 06-recuperacion.md
```

## Índice de documentos

- [Análisis de requisitos](docs/01-analisis.md)
- [Diseño de infraestructura](docs/02-diseno.md)
- [Planificación del proyecto](docs/03-planificacion.md)
- [Instalación del servidor web](docs/04-instalacion/servidor-web.md)
- [Instalación de base de datos](docs/04-instalacion/base-de-datos.md)
- [SSH y Firewall](docs/04-instalacion/ssh-firewall.md)
- [Monitorización](docs/04-instalacion/monitorizacion.md)
- [Copias de seguridad](docs/04-instalacion/backups.md)
- [Guía de operación](docs/05-operacion.md)
- [Plan de recuperación ante desastres](docs/06-recuperacion.md)
- [Registro de cambios](CHANGELOG.md)
- [Reflexión del proyecto](REVISION.md)
