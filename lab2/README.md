## Installation Steps

### 1. Basic Setup (Both VMs)
```bash
# Update system first
sudo apt update
sudo apt upgrade -y
```

### 2. VM1 Setup 
```bash
# Update system first
sudo apt update
sudo apt upgrade -y

# Install Zabbix repository
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_6.0+ubuntu24.04_all.deb
sudo dpkg -i zabbix-release_latest_6.0+ubuntu24.04_all.deb
sudo apt update

# Install ALL server components
sudo apt install -y zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent mysql-server

# Setup database (Critical Steps!)
sudo mysql
mysql> create database zabbix character set utf8mb4 collate utf8mb4_bin;
mysql> create user zabbix@localhost identified by 'password';
mysql> grant all privileges on zabbix.* to zabbix@localhost;
mysql> set global log_bin_trust_function_creators = 1;
mysql> quit;

# Import schema
zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix

# Configure database password
sudo nano /etc/zabbix/zabbix_server.conf
DBPassword=password  # Use your chosen password

# Start services
sudo systemctl restart zabbix-server zabbix-agent apache2
sudo systemctl enable zabbix-server zabbix-agent apache2
```

### 3. VM2 Setup
```bash
# Install Zabbix repository
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_6.0+ubuntu24.04_all.deb
sudo dpkg -i zabbix-release_latest_6.0+ubuntu24.04_all.deb
sudo apt update

# Install only Zabbix agent
sudo apt install -y zabbix-agent

# Configure agent
sudo nano /etc/zabbix/zabbix_agentd.conf
# Change these lines:
Server=<VM1-IP-ADDRESS>       # IP of your Zabbix server
ServerActive=<VM1-IP-ADDRESS> # Same IP
Hostname=TestWeb-Markus    # Remember this name!

# Start agent
sudo systemctl restart zabbix-agent
sudo systemctl enable zabbix-agent

# Install NGINX for test websites
sudo apt install -y nginx
```

## Accessing Zabbix

### 1. Login
```plaintext
URL: http://[VM1-IP-ADDRESS]/zabbix
Default credentials:
- Username: Admin (capital A!)
- Password: zabbix
```

### 2. Add VM2 as Host
1. Go to: Configuration → Hosts → Create host
2. Fill in:
   - Host name: TestWeb-YourName (same as in agent config!)
   - Groups: Linux servers
   - Agent interface: VM2's IP address
3. Add template: 'Linux by Zabbix agent'
4. Click Add
