# Estrategia de copias de seguridad

> **Estado:** Primera versión completa  
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
RETENCION=7

mkdir -p "$DIR_BACKUP"

mysqldump --user=root --password="$MYSQL_ROOT_PASS" \
    --all-databases \
    --single-transaction \
    --routines \
    --triggers \
    | gzip > "$DIR_BACKUP/backup_$FECHA.sql.gz"

if [ $? -eq 0 ]; then
    echo "[OK] Backup completado: backup_$FECHA.sql.gz"
else
    echo "[ERROR] Falló el backup"
    exit 1
fi

find "$DIR_BACKUP" -name "backup_*.sql.gz" -mtime +$RETENCION -delete
echo "[OK] Rotación completada"
```

## 3. Script de backup de archivos web

```bash
#!/bin/bash
# /usr/local/bin/backup-web.sh

FECHA=$(date +%Y%m%d_%H%M%S)
DIR_BACKUP="/backups/web"
RETENCION=7

mkdir -p "$DIR_BACKUP"

rsync -az --delete /var/www/ "$DIR_BACKUP/web_$FECHA/"

if [ $? -eq 0 ]; then
    echo "[OK] Backup web completado: web_$FECHA/"
else
    echo "[ERROR] Falló el backup web"
    exit 1
fi

ls -dt "$DIR_BACKUP"/web_*/ | tail -n +$((RETENCION+1)) | xargs rm -rf
```

## 4. Configuración del cron

```cron
0 2 * * * /usr/local/bin/backup-bd.sh >> /var/log/backup-bd.log 2>&1
30 2 * * * /usr/local/bin/backup-web.sh >> /var/log/backup-web.log 2>&1
```

## 5. Verificación

```bash
ls -lh /backups/bd/
gzip -t /backups/bd/backup_YYYYMMDD_HHmmss.sql.gz && echo "Backup íntegro"
gunzip -c /backups/bd/backup_YYYYMMDD_HHmmss.sql.gz | mysql -u root -p
```
