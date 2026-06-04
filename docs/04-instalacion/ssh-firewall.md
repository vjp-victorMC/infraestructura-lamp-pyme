# Configuración de SSH y Firewall UFW

> **Estado:** Borrador  
> **Autor:** vjp-victorMC  
> **Fecha:** 2026-06-04

## 1. Configuración segura de SSH

### Generar par de claves en el cliente

```bash
ssh-keygen -t ed25519 -C "admin@empresa.local"
# Guardar en ~/.ssh/id_ed25519
# Establecer passphrase segura
```

### Copiar clave pública al servidor

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub usuario@IP_SERVIDOR
```

### Endurecer la configuración de SSH (`/etc/ssh/sshd_config`)

```bash
sudo nano /etc/ssh/sshd_config
```

Parámetros a modificar:

```ini
Port 22
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
MaxAuthTries 3
ClientAliveInterval 300
ClientAliveCountMax 2
```

Reiniciar SSH:

```bash
sudo systemctl restart ssh
```

## 2. Configuración de firewall con UFW

```bash
# Política por defecto: denegar todo el tráfico entrante
ufw default deny incoming
ufw default allow outgoing

# SSH para administración (solo desde red de la oficina)
ufw allow from 192.168.1.0/24 to any port 22

# Tráfico web
ufw allow 80/tcp    # HTTP
ufw allow 443/tcp   # HTTPS

# Netdata (solo acceso local)
ufw allow from 192.168.1.0/24 to any port 19999

# Activar UFW
ufw enable
```

## 3. Reglas UFW

- Permitir SSH solo desde IP de la oficina: `ufw allow from 192.168.1.0/24 to any port 22`
- Permitir tráfico web: `ufw allow 80/tcp` y `ufw allow 443/tcp`
- Activar fail2ban como complemento al UFW:

```bash
sudo apt install fail2ban -y
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

## 4. Verificación del firewall

```bash
sudo ufw status verbose
# Status: active
# To                         Action      From
# --                         ------      ----
# 22                         ALLOW       192.168.1.0/24
# 80/tcp                     ALLOW       Anywhere
# 443/tcp                    ALLOW       Anywhere
# 19999                      ALLOW       192.168.1.0/24
```
