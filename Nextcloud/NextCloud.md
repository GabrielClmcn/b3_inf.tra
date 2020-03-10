## Mise en place du serveur Nextcloud:

#### Install LAMP
```
sudo yum install -y epel-release
sudo yum install -y http://rpms.remirepo.net/enterprise/remi-release-7.rpm
sudo yum install -y yum-utils
sudo yum-config-manager --disable remi-php54
sudo yum-config-manager --enable remi-php73
sudo yum update
sudo yum -y install vim httpd php wget unzip php-cli php-mysqlnd php-zip php-devel php-gd php-mcrypt php-mbstring php-curl php-xml php-pear php-bcmath php-json php-pdo php-pecl-apcu php-pecl-apcu-devel

MARIADB :

cat <<EOF | sudo tee /etc/yum.repos.d/MariaDB.repo
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.4/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
EOF

sudo yum makecache fast
sudo yum -y install MariaDB-server MariaDB-client
```

#### Start
```
sudo systemctl start httpd 
sudo systemctl enable httpd 
sudo systemctl start mariadb
sudo systemctl enable mariadb
```

#### Gestion de firewalld
```
sudo firewall-cmd --zone=public --add-service=http --permanent
sudo firewall-cmd --reload
```

#### VirtualHost

```
sudo mkdir /etc/httpd/sites-available
sudo mkdir /etc/httpd/sites-enabled
sudo vi /etc/httpd/conf/httpd.conf
```
------
```
<VirtualHost *:80>
  ServerName files.example.com
  ServerAdmin admin@example.com
  DocumentRoot /var/www/html/nextcloud
  <directory /var/www/html/nextcloud>
    Require all granted
    AllowOverride All
    Options FollowSymLinks MultiViews
    SetEnv HOME /var/www/html/nextcloud
    SetEnv HTTP_HOME /var/www/html/nextcloud
  </directory>
</VirtualHost>

```

#### Création base de données

```
CREATE DATABASE nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
GRANT ALL on nextcloud.* to nextcloud@localhost identified by 'pAssw0rd';
FLUSH PRIVILEGES;
```

#### Install Nextcloud

````
sudo mkdir -p /var/www/html/nextcloud/
sudo wget https://download.nextcloud.com/server/releases/latest-18.zip
unzip latest-18.zip
sudo mkdir /var/www/html/nextcloud/data
sudo chown apache:apache -R /var/www/html/nextcloud
```
#### Avec certificat ssl

- Pour générer un certificat:
```
openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout server.key -out server.crt
```
##### ou 

```
wget https://dl.eff.org/certbot-auto
chmod a+x certbot-auto
sudo mv certbot-auto /usr/local/bin

export DOMAIN="files.example.com"
export EMAIL="admin@example.com"
certbot-auto certonly --standalone -d $DOMAIN --preferred-challenges http --agree-tos -n -m $EMAIL --keep-until-expiring

```
- VirtualHost
```
<VirtualHost *:80>
   ServerName files.example.com
   ServerAdmin admin@example.com
   RewriteEngine On
   RewriteCond %{HTTPS} off
   RewriteRule ^(.*)$ https://%{HTTP_HOST}$1 [R=301,L]
</VirtualHost>

<IfModule mod_ssl.c>
   <VirtualHost *:443>
     ServerName files.example.com
     ServerAdmin admin@example.com
     DocumentRoot /var/www/html/nextcloud
     <directory /var/www/html/nextcloud>
        Require all granted
        AllowOverride All
        Options FollowSymLinks MultiViews
        SetEnv HOME /var/www/html/nextcloud
        SetEnv HTTP_HOME /var/www/html/nextcloud
    </directory>
     SSLEngine on
     SSLCertificateFile /etc/letsencrypt/live/files.example.com/fullchain.pem
     SSLCertificateKeyFile /etc/letsencrypt/live/files.example.com/privkey.pem
   </VirtualHost>
</IfModule>


```