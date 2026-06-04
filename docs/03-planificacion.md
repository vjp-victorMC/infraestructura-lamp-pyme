# Planificación del proyecto

> **Estado:** Borrador  
> **Autor:** vjp-victorMC  
> **Fecha:** 2026-06-04

## 1. Fases del proyecto

| Fase | Descripción | Duración estimada |
|---|---|---|
| 1. Análisis | Recogida de requisitos y diseño | 1 semana |
| 2. Instalación base | SO, Apache, PHP, MySQL | 2 días |
| 3. Seguridad | SSH, UFW, Certbot | 1 día |
| 4. Monitorización | Netdata y alertas | 1 día |
| 5. Backups | mysqldump + rsync + cron | 1 día |
| 6. Balanceador | HAProxy | 1 día |
| 7. Pruebas | Verificación de todos los servicios | 2 días |
| 8. Documentación | Guías para el cliente | 1 semana |

## 2. Diagrama de Gantt (simplificado)

```
Semana 1: [Análisis ████████]
                              [Instalación ████]
Semana 2:                                        [Seguridad ██][Monitor ██][Backups ██]
Semana 3: [HAProxy ██][Pruebas ████████]
Semana 4: [Documentación ████████████]
```

## 3. Riesgos identificados

| Riesgo | Probabilidad | Impacto | Mitigación |
|---|---|---|---|
| Fallo de hardware | Baja | Alto | Backups off-site y documentación de restauración |
| Versiones incompatibles | Media | Medio | Fijación de versiones en la instalación |
| Brecha de seguridad SSH | Baja | Alto | Autenticación por clave, fail2ban |
| Pérdida de datos | Muy baja | Crítico | Política de backups con rotación de 7 días |

## 4. Criterios de aceptación

- Apache sirve páginas en HTTP y HTTPS.
- MySQL acepta conexiones locales con usuarios diferenciados.
- SSH solo accesible desde red de la oficina.
- Netdata accesible desde la red interna.
- Backup se ejecuta automáticamente a las 02:00 y la retención es de 7 días.
