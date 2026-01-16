# Zabbix Monitoring Project – Full Command Reference

## Environment
- OS: Ubuntu Server 22.04
- Monitoring Tool: Zabbix 6.x
- Database: MariaDB
- Web Server: Apache

---

## 1. System Preparation (Server & Agent)
```bash
sudo apt update && sudo apt upgrade -y
sudo hostnamectl set-hostname zabbix-server
sudo timedatectl set-timezone Africa/Casablanca
```

---

## 2. Install Zabbix Repository
```bash
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-4+ubuntu22.04_all.deb
sudo dpkg -i zabbix-release_6.0-4+ubuntu22.04_all.deb
sudo apt update
```

---

## 3. Install Zabbix Server, Frontend & Agent
```bash
sudo apt install -y zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent mariadb-server
```

---

## 4. Configure MariaDB
```bash
sudo mysql_secure_installation
sudo mysql -u root -p
```

```sql
CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'StrongPassword';
GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

## 5. Import Initial Schema
```bash
zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql -u zabbix -p zabbix
```

---

## 6. Configure Zabbix Server
```bash
sudo nano /etc/zabbix/zabbix_server.conf
```

```conf
DBName=zabbix
DBUser=zabbix
DBPassword=StrongPassword
```

---

## 7. Configure PHP
```bash
sudo nano /etc/zabbix/apache.conf
```

```conf
php_value date.timezone Africa/Casablanca
```

---

## 8. Start and Enable Services
```bash
sudo systemctl restart zabbix-server zabbix-agent apache2
sudo systemctl enable zabbix-server zabbix-agent apache2 mariadb
```

---

## 9. Firewall Configuration
```bash
sudo ufw allow 22
sudo ufw allow 80
sudo ufw allow 10050
sudo ufw allow 10051
sudo ufw enable
```

---

## 10. Zabbix Agent Installation (Client)
```bash
sudo apt update
sudo apt install -y zabbix-agent
sudo nano /etc/zabbix/zabbix_agentd.conf
```

```conf
Server=ZABBIX_SERVER_IP
ServerActive=ZABBIX_SERVER_IP
Hostname=client-server
```

```bash
sudo systemctl restart zabbix-agent
sudo systemctl enable zabbix-agent
```

---

## 11. Custom Monitoring Scripts
```bash
sudo nano /usr/local/bin/disk_check.sh
```

```bash
#!/bin/bash
df -h | grep '^/dev'
```

```bash
sudo chmod +x /usr/local/bin/disk_check.sh
```

---

## 12. Logs & Verification
```bash
sudo tail -f /var/log/zabbix/zabbix_server.log
sudo systemctl status zabbix-server
```

---

## 13. Access Web Interface
```text
http://SERVER_IP/zabbix
```

---

## End of Commands
