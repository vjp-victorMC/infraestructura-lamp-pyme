# Análisis de requisitos del cliente

> **Estado:** Primera versión completa  
> **Autor:** vjp-victorMC  
> **Fecha:** 2026-06-04

## 1. Descripción del cliente

Pequeña empresa (PYME) del sector servicios que necesita:

- Presencia web corporativa con CMS.
- Sistema de gestión interna accesible desde la red local.
- Acceso remoto seguro para el administrador.
- Sistema de monitorización del servidor.
- Copias de seguridad automáticas con política de retención.

## 2. Requisitos funcionales

| ID | Requisito | Prioridad |
|---|---|---|
| RF-01 | Servidor web Apache con PHP 8.2 | Alta |
| RF-02 | Base de datos MySQL 8.0 con dos esquemas | Alta |
| RF-03 | Acceso SSH seguro con autenticación por clave | Alta |
| RF-04 | Firewall UFW configurado | Alta |
| RF-05 | Monitorización con Netdata | Media |
| RF-06 | Backups automáticos diarios con retención 7 días | Alta |
| RF-07 | Certificado SSL/TLS con Certbot | Media |
| RF-08 | Balanceador de carga HAProxy | Media |

## 3. Requisitos no funcionales

- **Disponibilidad:** 99% en horario laboral.
- **Seguridad:** Acceso SSH solo desde IP de la oficina.
- **Mantenibilidad:** Documentación completa del sistema.
- **Escalabilidad:** Posibilidad de añadir nodos web en el futuro.

## 4. Restricciones técnicas

- Sistema operativo: Ubuntu Server 22.04 LTS.
- Hardware mínimo: 2 vCPU, 4 GB RAM, 40 GB SSD.
- Red: IP estática en el servidor.

## 5. Entregables acordados

1. Infraestructura LAMP operativa.
2. Documentación técnica completa.
3. Guía de mantenimiento para el cliente.
4. Plan de recuperación ante desastres.
