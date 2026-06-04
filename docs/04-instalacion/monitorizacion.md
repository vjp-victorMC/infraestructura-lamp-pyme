# Monitorización con Netdata

> **Estado:** Borrador  
> **Autor:** vjp-victorMC  
> **Fecha:** 2026-06-04

## 1. Instalación de Netdata

```bash
wget -O /tmp/netdata-kickstart.sh https://get.netdata.cloud/kickstart.sh
sudo bash /tmp/netdata-kickstart.sh --stable-channel --dont-start-it
```

Habilitar e iniciar el servicio:

```bash
sudo systemctl enable netdata
sudo systemctl start netdata
```

## 2. Configuración básica

Editar el archivo principal de configuración:

```bash
sudo nano /etc/netdata/netdata.conf
```

Parámetros recomendados:

```ini
[global]
    hostname = servidor-pyme
    history = 3600
    update every = 1

[web]
    bind to = 127.0.0.1
    port = 19999
```

> **Nota de seguridad:** Netdata solo escucha en localhost. El acceso externo se gestiona mediante el proxy de Apache o solo desde la red local a través del puerto 19999.

## 3. Métricas monitorizadas

| Métrica | Umbral de alerta | Acción |
|---|---|---|
| CPU | > 85% durante 5 min | Notificación email |
| RAM | > 90% | Notificación email |
| Disco | > 80% ocupado | Notificación email |
| Servicio Apache | Caído | Reinicio automático + notificación |
| Servicio MySQL | Caído | Notificación urgente |

## 4. Configuración de alertas por email

```bash
sudo nano /etc/netdata/health_alarm_notify.conf
```

```ini
# Configurar email de notificaciones
EMAIL_SENDER="netdata@empresa.local"
DEFAULT_RECIPIENT_EMAIL="admin@empresa.local"
SENDEMAIL_OPTS="-S smtp=smtp.empresa.local"
```

## 5. Script de verificación del estado del sistema

```bash
#!/bin/bash
# /usr/local/bin/check-servicios.sh
# Verifica el estado de los servicios críticos

SERVICIOS=("apache2" "mysql" "netdata" "haproxy" "ufw")

for servicio in "${SERVICIOS[@]}"; do
    if systemctl is-active --quiet "$servicio"; then
        echo "[OK] $servicio está activo"
    else
        echo "[ALERTA] $servicio NO está activo"
        systemctl start "$servicio"
    fi
done
```

Hacer el script ejecutable y añadirlo al cron:

```bash
sudo chmod +x /usr/local/bin/check-servicios.sh
# Ejecutar cada 15 minutos
(crontab -l 2>/dev/null; echo "*/15 * * * * /usr/local/bin/check-servicios.sh >> /var/log/check-servicios.log 2>&1") | crontab -
```
