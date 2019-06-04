# Configurar Servidor Web na Amazon

#### criar diretorio raiz do site
sudo mkdir -p /var/www/iocomunica.com/public_html

#### criar arquivo index na raiz do site
sudo nano /var/www/iocomunica.com/public_html/index.html

#### criar usuario ftp
sudo adduser ftpuser

#### definir diretorio do site como home do usuario
sudo usermod ftpuser -d /var/www/iocomunica.com

#### criar grupo em comum para usuario ftp e usuario apache
#### para que o wordpress possa fazer uploads para pasta do usuario

sudo groupadd publishers && <br>
sudo usermod -a -G publishers ftpuser && <br>
sudo usermod -a -G publishers www-data

#### verificar se os grupos foram atribuidos corretamente
groups ftpuser && groups www-data

#### or change owner to a specifc ftp user
sudo chown -R ftpuser:publishers /var/www/emkt/public_html

#### grant the propers permission to files and folders

sudo chown nobody:nogroup /var/www/

sudo chmod 2775 /var/www

sudo find /var/www -type d -exec chmod 2775 {} +

sudo find /var/www -type f -exec chmod 0664 {} +


## Apache Configuration
  
#### create domain configuration file
sudo nano /etc/apache2/sites-available/iocomunica.com.conf

--- DOMAIN-NAME.COM.CONF START ---

\<VirtualHost *:80>
  
  ServerAdmin suporte@iocomunica.com
  
  ServerName iocomunica.com
  
  ServerAlias www.iocomunica.com
  
  DocumentRoot /var/www/iocomunica.com/public_html
  
  ErrorLog ${APACHE_LOG_DIR}/error.log
  
  CustomLog ${APACHE_LOG_DIR}/access.log combined
  
\</VirtualHost>

--- DOMAIN-NAME.COM.CONF END ---


#### enable website 
sudo a2ensite iocomunica.com.conf

#### restart apache
sudo service apache2 restart



## Database Configuration
https://linuxize.com/post/how-to-create-mysql-user-accounts-and-grant-privileges/

sudo mysql -p

CREATE DATABASE db_name

CREATE USER 'user'@'%' IDENTIFIED BY 'password';

GRANT ALL PRIVILEGES ON db_name.* TO 'user'@'%';

SHOW GRANTS FOR 'user'@'%';

#### revogar privilegios
REVOKE ALL PRIVILEGES ON db_name.* TO 'user'@'%';

#### exluir usuario
DROP USER 'user'@'%';


## FTP Configuration
sudo apt install vsftpd


### create ftp user
sudo adduser ftpuser

#### change home for ftp user
sudo usermod ftpuser -d /var/www/iocomunica.com 

#### create and append a Group for ftp and apache users
groupadd publishers 

usermod -a -G publishers ftpuser

usermod -a -G publishers www-data

#### display groups for user
groups ftpuser

groups www-data



#### grant the propers permission to files and folders

sudo chown nobody:nogroup /var/www/ <br>

chmod 2775 /var/www <br>

sudo find /var/www -type d -exec chmod 2775 {} + <br>

sudo find /var/www -type f -exec chmod 0664 {} + <br>


#### configure ftp server
sudo nano /.../vstpd.conf <br>

--- VSTPD.CONF START ---

listen=NO

listen_ipv6=YES

anonymous_enable=YES

local_enable=YES

write_enable=YES

local_umask=022

dirmessage_enable=YES

use_localtime=YES

xferlog_enable=YES

connect_from_port_20=YES

data_connection_timeout=120

chroot_local_user=YES

allow_writeable_chroot=YES

secure_chroot_dir=/var/run/vsftpd/empty

pam_service_name=vsftpd

#userlist_enable=YES

tcp_wrappers=YES

#rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem

#rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key

#ssl_enable=YES

#rsa_cert_file=/etc/ssl/private/vsftpd.pem

#rsa_private_key_file=/etc/ssl/private/vsftpd.pem

#allow_anon_ssl=NO

#force_local_data_ssl=YES

#force_local_logins_ssl=YES

#ssl_tlsv1=YES

#ssl_sslv2=NO

#ssl_sslv3=NO

#require_ssl_reuse=NO

#ssl_ciphers=HIGH

force_dot_files=YES

pasv_enable=NO

#pasv_min_port=40000

#pasv_max_port=50000

--- VSTPD.CONF END ---
