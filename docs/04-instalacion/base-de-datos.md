# Instalación y configuración de MySQL

> **Estado:** Borrador  
> **Autor:** vjp-victorMC  
> **Fecha:** 2026-06-04

## 1. Instalación de MySQL 8.0

```bash
sudo apt install mysql-server -y
sudo systemctl enable mysql
sudo systemctl start mysql
```

## 2. Asistente de seguridad inicial

```bash
sudo mysql_secure_installation
# Responder:
# - Validate password plugin: Y
# - Password strength: 2 (STRONG)
# - Remove anonymous users: Y
# - Disallow root login remotely: Y
# - Remove test database: Y
# - Reload privilege tables: Y
```

## 3. Creación de bases de datos y usuarios

Conectarse como root:

```bash
sudo mysql -u root -p
```

Crear bases de datos y usuarios:

```sql
-- Base de datos para el sitio web
CREATE DATABASE db_web CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'web_user'@'localhost' IDENTIFIED BY 'Passw0rd_Web!2026';
GRANT SELECT, INSERT, UPDATE, DELETE ON db_web.* TO 'web_user'@'localhost';

-- Base de datos para gestión interna
CREATE DATABASE db_gestion CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'gestion_user'@'localhost' IDENTIFIED BY 'Passw0rd_Gest!2026';
GRANT SELECT, INSERT, UPDATE, DELETE ON db_gestion.* TO 'gestion_user'@'localhost';

FLUSH PRIVILEGES;
EXIT;
```

## 4. Configuración de seguridad

Verificar que MySQL solo escucha en localhost:

```bash
sudo grep bind-address /etc/mysql/mysql.conf.d/mysqld.cnf
# bind-address = 127.0.0.1
```

Si no está configurado, editarlo:

```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
# Añadir o modificar:
# bind-address = 127.0.0.1
sudo systemctl restart mysql
```

## 5. Verificación

```bash
mysql -u web_user -p -e "SHOW DATABASES;"
# Debe mostrar db_web

mysql -u gestion_user -p -e "SHOW DATABASES;"
# Debe mostrar db_gestion
```
