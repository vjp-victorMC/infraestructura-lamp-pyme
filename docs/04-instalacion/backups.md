# Estrategia de copias de seguridad

> **Estado:** Borrador  
> **Autor:** vjp-victorMC  
> **Fecha:** 2026-06-04

## 1. Política de copias de seguridad

| Tipo | Frecuencia | Retención | Destino |
|---|---|---|---|
| Base de datos (mysqldump) | Diaria a las 02:00 | 7 días | /backups/bd/ |
| Archivos web (rsync) | Diaria a las 02:30 | 7 días | /backups/web/ |
| Backup completo del sistema | Semanal (domingo 03:00) | 4 semanas | /backups/sistema/ |

## 2. Script de backup de bases de datos

```bash
#!/bin/bash
# /usr/local/bin/backup-bd.sh

FECHA=$(date +%Y%m%d_%H%M%S)
DIR_BACKUP="/backups/bd"
RETENCION=7  # días

mkdir -p "$DIR_BACKUP"

# Backup de todas las bases de datos
mysqldump --user=root --password="$MYSQL_ROOT_PASS" \
    --all-databases \
    --single-transaction \
    --routines \
    --triggers \
    | gzip > "$DIR_BACKUP/backup_$FECHA.sql.gz"

if [ $? -eq 0 ]; then
    echo "[OK] Backup completado: backup_$FECHA.sql.gz"
else
    echo "[ERROR] Falló el backup de la base de datos"
    exit 1
fi

# Rotación: eliminar backups más antiguos que $RETENCION días
find "$DIR_BACKUP" -name "backup_*.sql.gz" -mtime +$RETENCION -delete
echo "[OK] Rotación completada: eliminados backups de más de $RETENCION días"
```

## 3. Script de backup de archivos web

```bash
#!/bin/bash
# /usr/local/bin/backup-web.sh

FECHA=$(date +%Y%m%d_%H%M%S)
DIR_BACKUP="/backups/web"
RETENCION=7

mkdir -p "$DIR_BACKUP"

# Sincronizar archivos web
rsync -az --delete \
    /var/www/ \
    "$DIR_BACKUP/web_$FECHA/"

if [ $? -eq 0 ]; then
    echo "[OK] Backup web completado: web_$FECHA/"
else
    echo "[ERROR] Falló el backup de archivos web"
    exit 1
fi

# Rotación
ls -dt "$DIR_BACKUP"/web_*/ | tail -n +$((RETENCION+1)) | xargs rm -rf
```

## 4. Configuración del cron

```bash
sudo crontab -e
```

Añadir las siguientes entradas:

```cron
# Backup de bases de datos todos los días a las 02:00
0 2 * * * /usr/local/bin/backup-bd.sh >> /var/log/backup-bd.log 2>&1

# Backup de archivos web todos los días a las 02:30
30 2 * * * /usr/local/bin/backup-web.sh >> /var/log/backup-web.log 2>&1
```

## 5. Verificación de los backups

```bash
# Listar backups disponibles
ls -lh /backups/bd/
ls -lh /backups/web/

# Verificar integridad de un backup de BD
gzip -t /backups/bd/backup_YYYYMMDD_HHmmss.sql.gz && echo "Backup íntegro"

# Restaurar un backup de BD (solo en caso de emergencia)
gunzip -c /backups/bd/backup_YYYYMMDD_HHmmss.sql.gz | mysql -u root -p
```
