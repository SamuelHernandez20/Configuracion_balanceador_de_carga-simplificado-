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

Para este caso específico se hara uso de un **balanceador de carga no fisico**, es decir por **software** el cual se encargará de ir balanceando peticiones **cliente-servidor**:

![](images/estructurascripts.png)

