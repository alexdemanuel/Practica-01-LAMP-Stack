# Practica-01-LAMP-Stack

# Instalamos composer


```
 php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" 
 
php -r "if (hash_file('SHA384', 'composer-setup.php') === '93b54496392c062774670ac18b134c3b3a95e5a5e5c8f1a9f115f203b75bf9a129d5daa8ba6a13e2cc8a1da0806388a8') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"

php composer-setup.php

php -r "unlink('composer-setup.php');"
```
## Instalamos las dependencias

- instalamos unzip
```
apt install unzip
```
- instalamos xml y curl

```
apt install php-xmlrcp
apt install php7.2-xml 
apt install php7.2-Curl
```
- accedemos al directorio de phpmyadmin e instalamos el composer
```
cd phpmyadmin

php ../composer.phar update --no-dev
```
- en el composer.json borramos las lineas de mbstring (hay 2 en el apartado required-dev)



```
composer update --no-dev
```
- clonamos el repositorio de phpmyadmin
```
git clone https://github.com/phpmyadmin/phpmyadmin.git
```


- movemos la carpeta php a /var/www/html
```
mv phpmyadmin /var/www/html
```

- cambiamos los permisos a la carperta
```
chown www-data:www-data phpmyadmin/ -R
```

## instalar adminer

- entramos en el directorio html 
```
cd /var/www/html
wget https://github.com/vrana/adminer/releases/download/v4.6.3/adminer-4.6.3.php 
```

- renombramos para encontrarlo con mayor facilidad
```
mv adminer-4.6.3.php adminer.php
```
- cambiamos los permisos
```
chown www-data:www-data phpmyadmin/ -R
```



## Instalamos go access
```
 echo "deb http://deb.goaccess.io/ $(lsb_release -cs) main" | sudo tee -a /etc/apt/sources.list.d/goaccess.list
 wget -O - https://deb.goaccess.io/gnugpg.key | sudo apt-key add -
 sudo apt-get update
 sudo apt-get install goaccess
```
## LOGS
		
- entramos en la carpeta de apache
```
cd /var/log/apache2/
goaccess access.log -c
```
esto nos data la informacion de los logs y estadisticas de nuestra pagina graficamente 
Seleccionamos common log format dandole al espacio y luego le damos al intro para acceder a los logs

- creamos carpeta llamada logs y le cambiamos los permisos
```
mkdir /var/www/html/logs
chown www-data:www-data logs/ -R
```

### ver logs dentro del navegador en tiempo real
 - dentro de la carpeta de apache 
Para ver los logs desde 192.168.x.x/report.html 
```
goaccess access.log -o /var/www/html/logs/index.html --log-format=COMBINED --real-time-html
```

## Proteger un directorio con htaccess
	

### 5.5.1 Paso 1
- Crear el directorio stats

```
mkdir /var/www/html/stats
```
### 5.5.2 Paso 2
- Lanzar el proceso de goaccess en background para generar los informes en segundo plano.

```
sudo goaccess /var/log/apache2/access.log -o /var/www/html/logs/index.html --log-format=COMBINED --real-time-html
```
### 5.5.3 Paso 3
- Crear el archivo de contraseñas para el usuario que accederá al directorio stats. El archivo de contraseñas lo guardamos en un directorio seguro. En nuestro caso lo podemos guardar en /home/usuario.

```
sudo htpasswd -c /home/usuario/.htpasswd usuario
```
### 5.5.4 Paso 3
- Editamos el archivo configuración de Apache.

```
sudo nano /etc/apache2/sites-enabled/000-default.conf
```
- Añadimos la siguiente sección dentro de las etiquetas de <VirtualHost *:80> y </VirtualHost>.

```
<Directory "/var/www/html/stats">
  Options Indexes FollowsymLinks
                AllowOverride All
                Require all granted


</Directory>
```


- creamos el .htaccess en /var/www/html/stats 
```
 cd /var/www/html/stats
 sudo nano .htaccess
 ```
- escribimos lo siguiente en su interior
	
```
 AuthType Basic
  AuthName "Restricted Content"
  AuthUserFile /home/usuario/.htpasswd
  Require valid-user
```
### 5.5.5 Paso 4
- Reiniciar el servicio de Apache
```
sudo /etc/init.d/apache2 restart
```


