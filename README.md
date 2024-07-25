# Instalação-Zabbix-Simplificada
 Instalação-Zabbix-Simplificada

# Instalando os repositorios
wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_7.0-2+ubuntu24.04_all.deb
dpkg -i zabbix-release_7.0-2+ubuntu24.04_all.deb
apt update 

# Instalar o Zabbix Server
apt install zabbix-server-mysql zabbix-frontend-php zabbix-nginx-conf zabbix-sql-scripts zabbix-agent

# Instalando o Mysql 
sudo apt install mysql-server -y
sudo mysql_secure_installation

# Criar banco de dados e usuário

mysql -uroot -p

create database zabbix character set utf8mb4 collate utf8mb4_bin;
create user zabbix@localhost identified by 'zabbix-password';
grant all privileges on zabbix.* to zabbix@localhost;
set global log_bin_trust_function_creators = 1;
quit; 

# Importar os schemas do zabbix padrão 

zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix 

# Retornar o acesso ao banco 

mysql -uroot -p

set global log_bin_trust_function_creators = 0;
quit; 

# Editar a senha do acesso ao banco nas configurações

vim /etc/zabbix/zabbix_server.conf 

# Clique a tecla de ? e insira a entrada "DBPassword"

Descomente a linha e insira a senha do usuário para "zabbix-password"

# Descomentar as linhas no arquivo  

vim /etc/zabbix/nginx.conf

listen 8080;
server_name example.com; 

# Restartar e Habilitar o Zabbix-Server 

systemctl restart zabbix-server zabbix-agent nginx php8.3-fpm
systemctl enable zabbix-server zabbix-agent nginx php8.3-fpm 

# Acessar para finalizar a instalação via navegador web.

http://<IP-servidor-Zabbix>:8080/