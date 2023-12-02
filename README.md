# Practica1-10

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




# 1. Configuración del Proxy inverso:

`Desde la el install_load_balancer.sh de la maquina balanceadora`

