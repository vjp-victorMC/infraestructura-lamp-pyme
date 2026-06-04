# Instalación del servidor web Apache con PHP

> **Estado:** Borrador  
> **Autor:** vjp-victorMC  
> **Fecha:** 2026-06-04

## 1. Requisitos previos

- Ubuntu Server 22.04 LTS instalado y actualizado.
- Acceso SSH con privilegios `sudo`.
- IP estática configurada.

## 2. Actualización del sistema

```bash
sudo apt update && sudo apt upgrade -y
```

## 3. Instalación de Apache

```bash
sudo apt install apache2 -y
sudo systemctl enable apache2
sudo systemctl start apache2
```

Verificar que Apache responde:

```bash
curl http://localhost
# Debe mostrar la página por defecto de Apache
```

## 4. Instalación de PHP 8.2

```bash
sudo apt install php8.2 php8.2-mysql php8.2-curl php8.2-xml php8.2-mbstring libapache2-mod-php8.2 -y
```

Verificar la versión:

```bash
php -v
# PHP 8.2.x (cli)
```

## 5. Configuración de Virtual Hosts

### Sitio web corporativo (`/etc/apache2/sites-available/web.conf`)

```apache
<VirtualHost *:8080>
    ServerName empresa.local
    DocumentRoot /var/www/html
    ErrorLog ${APACHE_LOG_DIR}/web_error.log
    CustomLog ${APACHE_LOG_DIR}/web_access.log combined

    <Directory /var/www/html>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

### Aplicación de gestión (`/etc/apache2/sites-available/gestion.conf`)

```apache
<VirtualHost *:8080>
    ServerName gestion.empresa.local
    DocumentRoot /var/www/gestion
    ErrorLog ${APACHE_LOG_DIR}/gestion_error.log
    CustomLog ${APACHE_LOG_DIR}/gestion_access.log combined

    <Directory /var/www/gestion>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

Activar los sitios y reiniciar Apache:

```bash
sudo a2ensite web.conf gestion.conf
sudo a2dissite 000-default.conf
sudo a2enmod rewrite
sudo systemctl reload apache2
```

## 6. Sección de configuración del balanceador HAProxy

Apache se configura para escuchar en el puerto 8080, ya que HAProxy gestionará el tráfico externo en los puertos 80 y 443.

```bash
# Cambiar el puerto de escucha de Apache
sudo sed -i 's/Listen 80/Listen 8080/' /etc/apache2/ports.conf
sudo systemctl reload apache2
```

## 7. Verificación final

```bash
sudo apache2ctl configtest
# Syntax OK
sudo systemctl status apache2
```
