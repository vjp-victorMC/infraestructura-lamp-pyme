# Guía de operación y mantenimiento

> **Estado:** Borrador  
> **Autor:** vjp-victorMC  
> **Fecha:** 2026-06-04

## 1. Tareas de mantenimiento diario

### Verificación del estado de los servicios

```bash
sudo systemctl status apache2 mysql netdata haproxy ufw
```

### Revisión de logs de errores

```bash
# Logs de Apache
sudo tail -n 50 /var/log/apache2/error.log

# Logs de MySQL
sudo tail -n 50 /var/log/mysql/error.log

# Logs del sistema
sudo journalctl -p err --since "24 hours ago"
```

### Verificación de los backups

```bash
ls -lht /backups/bd/ | head -5
ls -lht /backups/web/ | head -5
```

## 2. Tareas de mantenimiento semanal

- Revisar el uso de disco: `df -h`
- Revisar los logs de acceso en busca de actividad sospechosa.
- Verificar que fail2ban está activo y revisar los IPs bloqueadas.
- Comprobar las actualizaciones de seguridad disponibles: `sudo apt list --upgradable`.

## 3. Tareas de mantenimiento mensual

- Aplicar actualizaciones de seguridad del sistema operativo.
- Renovar o verificar la vigencia del certificado SSL.
- Revisar y actualizar la política de contraseñas.
- Probar la restauración de un backup.

## 4. Tareas de mantenimiento del balanceador HAProxy

### Verificar el estado de HAProxy

```bash
sudo systemctl status haproxy
```

### Revisar estadísticas de HAProxy

Acceder al panel de estadísticas (si está habilitado):

```
http://localhost:8404/stats
```

### Añadir un nuevo servidor backend a HAProxy

Editar `/etc/haproxy/haproxy.cfg`:

```haproxy
backend servidores_web
    balance roundrobin
    server web1 127.0.0.1:8080 check
    server web2 192.168.1.11:8080 check  # Nuevo servidor
```

Reiniciar HAProxy sin interrumpir el servicio:

```bash
sudo haproxy -f /etc/haproxy/haproxy.cfg -c  # Validar configuración
sudo systemctl reload haproxy
```

## 5. Gestión de usuarios en el sistema

```bash
# Añadir un nuevo administrador
sudo adduser nuevo_admin
sudo usermod -aG sudo nuevo_admin

# Revocar acceso a un usuario
sudo usermod -L usuario_a_bloquear
```
