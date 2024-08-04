

Para esta nueva práctica habia que implementar **4 maquinas virtuales**:

1. Balanceador.
2. Frontal Web 1.
3. Frontal Web 2.
4. Servidor de Base de Datos MySQL.

La arquitectura de estas máquinas virtuales, se destinarán para los siguientes propositos:

1. Un **balanceador de carga**, implementado con un **Apache HTTP Server** configurado como **proxy inverso**.
2. Una capa de **front-end**, formada por **dos servidores web** con **Apache HTTP Server**.
3. Una capa de **back-end**, formada por un servidor **MySQL**.

Para este caso específico se hara uso de un **balanceador de carga no fisico**, es decir por **software** el cual se encargará de ir balanceando peticiones **cliente-servidor**, el sentido de que sea **proxy inverso** es que maneja el tráfico desde fuera hacia dentro, permitiendo en este caso el balanceo, en este caso tráfico **HTTP** y **HTTPS**.

# Estructura de Directorios:

````
.
├── README.md
├── conf
│   ├── load-balancer.conf
│   └── 000-default.conf
└── scripts
    ├── .env
    ├── install_load_balancer.sh
    ├── install_lamp_frontend.sh
    ├── install_lamp_backend.sh
    ├── setup_letsencrypt_https.sh
    └── deploy.sh
````



# 1. Configuración del Proxy inverso:

`Desde la el install_load_balancer.sh de la maquina balanceadora`


Incluiré las variables del **.env** variables que a continuación van a ser imprescindibles:

````
source .env
````

Se procede con la instalación del servidor de **Apache**:

````
apt install apache2 -y
````

Para poder configurarlo como **proxy inverso**, y que balancee, procedo con la **activación** de los módulos necesarios de Apache para configurarlo como **proxy inverso**

````
a2enmod proxy
a2enmod proxy_http
a2enmod proxy_balancer
````

Seguidamente paso con la habilitación del algoritmo de balanceo de carga, el más común es el **Round Robin**:

````
a2enmod lbmethod_byrequests
````

Despues pasamos a copiar archivo el archivo de configuracion del balanceador hacia el **sites-available**

````
cp ../conf/load-balancer.conf /etc/apache2/sites-available
````
De manera automatizada haciendo referencia a las variables del **.env**, reemplazamos las variables de la plantilla con las **direcciones ip privada** de las
maquinas virtuales **frontales**:

````
sed -i "s/IP_HTTP_SERVER_1/$IP_HTTP_SERVER_1/" /etc/apache2/sites-available/load-balancer.conf
````
````
sed -i "s/IP_HTTP_SERVER_2/$IP_HTTP_SERVER_2/" /etc/apache2/sites-available/load-balancer.conf
````

Mediante el comando **a2ensite** procedo a habilitar el sitio del **virtualhost** actual:

````
a2ensite load-balancer.conf 
````
Luego procedo a deshabilitar el por defecto **000-default.conf**:

````
a2dissite 000-default.conf 
````

Reiniciamos:

````
systemctl restart apache2
````

**Tras esto ya se comprueba que funciona el balanceo de carga por HTTP, y tiramos el Let’s Encrypt para que tambien sea por HTTPS**

# 2. Back-end:

`Desde la maquina del backend: deploybackend.sh`


Variables:

````
source .env
````
Creacion de la base de datos:

````
mysql -u root <<< "DROP DATABASE IF EXISTS $WORDPRESS_DB_NAME"
mysql -u root <<< "CREATE DATABASE $WORDPRESS_DB_NAME"
mysql -u root <<< "DROP USER IF EXISTS $WORDPRESS_DB_USER@$IP_CLIENTE_MYSQL"
mysql -u root <<< "CREATE USER $WORDPRESS_DB_USER@$IP_CLIENTE_MYSQL IDENTIFIED BY '$WORDPRESS_DB_PASSWORD'"
mysql -u root <<< "GRANT ALL PRIVILEGES ON $WORDPRESS_DB_NAME.* TO $WORDPRESS_DB_USER@$IP_CLIENTE_MYSQL"
````

`Desde la maquina del backend: install_lamp_backend.sh `

Instalamos el SGBD **MySQL**

````
apt install mysql-server -y
````
Importo las variables del **.env**

````
source .env
````
Para que Mysql tambien acepte conexiones **no solo locales** si no tambien **remotas**, en este caso de **ip privada**, la cual llevará la dirección del **Back-end** para que escuche a través de esa interfaz: 

````
sed -i "s/127.0.0.1/$MYSQL_PRIVATE/" /etc/mysql/mysql.conf.d/mysqld.cnf
````

Reiniciamos servicio:

````
systemctl restart mysql
````
# 3. Frontales:

Despues se pasaría a ejecutar en los **frontales** los scripts correspondientes al **install_lamp_frontend.sh** y **deployfrontend.sh**.

En clase se paso a hacer la prueba de crear en **/var/www/html** **2 index.html**, donde en uno ponia un texto en referencia al **frontal 1** y en otro hacia el **frontal 2**,
teniendo en cuenta borrar ciertas configuraciones del **Wordpress** para hacer la **prueba de balanceo**.
