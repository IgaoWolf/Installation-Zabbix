# Instalação do Zabbix Simplificada

Este guia fornece um passo a passo para instalar e configurar o Zabbix em um servidor Ubuntu 24.04. O Zabbix é uma solução de monitoramento de código aberto que pode ser utilizada para monitorar a disponibilidade e o desempenho de sistemas.

## 1. Instalando os Repositórios do Zabbix

Primeiro, adicione o repositório oficial do Zabbix ao seu sistema:

```bash
wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_7.0-2+ubuntu24.04_all.deb
dpkg -i zabbix-release_7.0-2+ubuntu24.04_all.deb
apt update
```

## 2. Instalando o Zabbix Server e Componentes

Instale o servidor Zabbix, o frontend PHP, e outros componentes necessários:

```bash

apt install zabbix-server-mysql zabbix-frontend-php zabbix-nginx-conf zabbix-sql-scripts zabbix-agent
```

## 3. Instalando o MySQL

Instale o MySQL e execute o script de configuração segura:

```bash
sudo apt install mysql-server -y
sudo mysql_secure_installation
```

## 4. Criar Banco de Dados e Usuário para o Zabbix

Acesse o MySQL como root e crie o banco de dados e o usuário para o Zabbix:

```bash

mysql -uroot -p
```

Dentro do MySQL:

```sql

create database zabbix character set utf8mb4 collate utf8mb4_bin;
create user zabbix@localhost identified by 'zabbix-password';
grant all privileges on zabbix.* to zabbix@localhost;
set global log_bin_trust_function_creators = 1;
quit;
```

## 5. Importar os Schemas do Zabbix

Importe os esquemas padrão do Zabbix para o banco de dados:

```bash

zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
```

## 6. Reverter Configuração de Acesso ao Banco

Acesse novamente o MySQL e reverta a configuração de acesso:

```bash

mysql -uroot -p
```

Dentro do MySQL:

```sql

set global log_bin_trust_function_creators = 0;
quit;
```

## 7. Configurar Senha do Banco no Arquivo de Configuração do Zabbix

Edite o arquivo de configuração do Zabbix Server para definir a senha do banco de dados:

```bash

vim /etc/zabbix/zabbix_server.conf
```

Pressione ? para procurar por "DBPassword" e descomente a linha correspondente. Insira a senha zabbix-password para o usuário zabbix.

## 8. Configurar o Nginx

Edite o arquivo de configuração do Nginx para ajustar as configurações do servidor:

```bash

vim /etc/zabbix/nginx.conf
```
Descomente e ajuste as linhas para:

```nginx

listen 8080;
server_name example.com;
```

## 9. Reiniciar e Habilitar Serviços

Reinicie e habilite os serviços do Zabbix e do Nginx:

```bash

systemctl restart zabbix-server zabbix-agent nginx php8.3-fpm
systemctl enable zabbix-server zabbix-agent nginx php8.3-fpm
```

## 10. Finalizar Instalação via Navegador Web

Abra o navegador e acesse a interface web do Zabbix para concluir a instalação:

http://<IP-servidor-Zabbix>:8080/

Substitua <IP-servidor-Zabbix> pelo endereço IP do seu servidor Zabbix.
