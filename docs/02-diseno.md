# Diseño de la infraestructura

> **Estado:** Primera versión — incluye diagrama de red y tabla de versiones  
> **Autor:** vjp-victorMC  
> **Fecha:** 2026-06-04

## 1. Diagrama de red

```
[Internet]
    |
[Router/Firewall externo]
    |
[UFW — Firewall del servidor]   :22 solo 192.168.1.0/24
    |
[HAProxy — Balanceador]  :80/:443
    |
[Apache + PHP]  :8080
    |
[MySQL]  :3306 (solo 127.0.0.1)
    |
[Netdata]  :19999 (solo 192.168.1.0/24)
```

## 2. Tabla de versiones de software

| Componente | Versión | Puerto | Descripción |
|---|---|---|---|
| Ubuntu Server | 22.04 LTS | - | Sistema operativo |
| Apache | 2.4.57 | 8080 | Servidor web |
| PHP | 8.2 | - | Lenguaje de scripting |
| MySQL | 8.0 | 3306 | Base de datos |
| HAProxy | 2.6 | 80/443 | Balanceador de carga |
| Certbot | 2.9 | - | SSL/TLS automático |
| Netdata | latest | 19999 | Monitorización |
| UFW | - | - | Firewall |

## 3. Bases de datos

| Nombre BD | Propósito | Usuario |
|---|---|---|
| `db_web` | Contenido del sitio web corporativo | `web_user` |
| `db_gestion` | Sistema de gestión interna | `gestion_user` |

## 4. Estructura de directorios del servidor

```
/var/www/
├── html/           <- Sitio web corporativo
└── gestion/        <- Aplicación de gestión interna

/etc/apache2/
├── sites-available/
│   ├── web.conf
│   └── gestion.conf
└── sites-enabled/

/backups/
├── diarios/
└── semanales/
```

## 5. Decisiones de diseño

- Se separan los dos servicios web en Virtual Hosts de Apache.
- MySQL solo escucha en `127.0.0.1` para mayor seguridad.
- HAProxy se encarga del SSL para simplificar la configuración de Apache.
- Netdata se protege detrás de un proxy reverso para no exponerlo directamente.
