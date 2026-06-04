# Plan de recuperación ante desastres

> **Estado:** Borrador  
> **Autor:** vjp-victorMC  
> **Fecha:** 2026-06-04

## 1. Objetivos de recuperación

| Indicador | Objetivo |
|---|---|
| **RPO** (Recovery Point Objective) | Máximo 24 horas de pérdida de datos |
| **RTO** (Recovery Time Objective) | Servicio restaurado en menos de 4 horas |

## 2. Escenarios de desastre cubiertos

1. **Corrupción de base de datos** — Restauración desde backup mysqldump.
2. **Fallo del servidor web Apache** — Reinicio del servicio o reinstalación.
3. **Pérdida total del servidor** — Reinstalación completa desde cero con backups.
4. **Ataque de fuerza bruta SSH** — Revisión de fail2ban, cambio de claves.
5. **Disco lleno** — Limpieza de logs, ampliación de almacenamiento.

## 3. Procedimiento de restauración de base de datos

```bash
# 1. Identificar el backup más reciente
ls -lht /backups/bd/ | head -3

# 2. Detener el acceso a la aplicación (modo mantenimiento)
sudo a2ensite mantenimiento.conf
sudo a2dissite web.conf gestion.conf
sudo systemctl reload apache2

# 3. Restaurar la base de datos
gunzip -c /backups/bd/backup_YYYYMMDD_HHmmss.sql.gz | mysql -u root -p

# 4. Verificar la integridad
mysql -u root -p -e "SHOW DATABASES;"
mysql -u root -p -e "SELECT COUNT(*) FROM db_web.tabla_principal;"

# 5. Reactivar los sitios web
sudo a2dissite mantenimiento.conf
sudo a2ensite web.conf gestion.conf
sudo systemctl reload apache2
```

## 4. Procedimiento de reinstalación completa

1. Instalar Ubuntu Server 22.04 LTS.
2. Seguir el documento [servidor-web.md](04-instalacion/servidor-web.md).
3. Seguir el documento [base-de-datos.md](04-instalacion/base-de-datos.md).
4. Seguir el documento [ssh-firewall.md](04-instalacion/ssh-firewall.md).
5. Restaurar los backups de bases de datos (ver sección 3).
6. Restaurar los archivos web:

```bash
rsync -az /backups/web/web_YYYYMMDD_HHmmss/ /var/www/
```

7. Seguir el documento [monitorizacion.md](04-instalacion/monitorizacion.md).
8. Seguir el documento [backups.md](04-instalacion/backups.md).
9. Verificar todos los servicios.

## 5. Lista de verificación post-recuperación

- [ ] Apache responde en HTTP y HTTPS.
- [ ] MySQL acepta conexiones de `web_user` y `gestion_user`.
- [ ] SSH solo accesible desde red de la oficina.
- [ ] Netdata muestra métricas correctamente.
- [ ] Los cron de backup están activos.
- [ ] HAProxy balancea el tráfico correctamente.
- [ ] Certificado SSL válido.
- [ ] No hay alertas en Netdata.

## 6. Contactos de emergencia

| Rol | Nombre | Contacto |
|---|---|---|
| Administrador del sistema | vjp-victorMC | admin@empresa.local |
| Proveedor de hosting | - | soporte@hosting.com |
