# Sprint 3 - Conectividad de los contenedores

## Projecte 
**Proyecto 0.1 - Despliege extagram**

### 1.- Esquema IPs

| Contenedor       | IPs                   | Rol                         |
|----------------|---------------------------------------|-----------------------------|
| **s1-nginx**   | 172.25.0.x                             | Proxy invers / NGINX        |
| **s2-phpfpm**  | 172.24.0.x / 172.25.0.x / 172.23.0.x | PHP-FPM                     |
| **s3-phpfpm**  | 172.24.0.x / 172.25.0.x / 172.23.0.x | PHP-FPM                     |
| **s4-upload**  | 172.24.0.x / 172.25.0.x / 172.23.0.x | PHP-FPM / Upload            |
| **s5-storage** | 172.23.0.x / 172.25.0.x               | NGINX per imatges           |
| **s6-static**  | 172.23.0.x / 172.25.0.x               | NGINX per arxius estàtics  |
| **s7-mysql**   | 172.24.0.x                             | MySQL                       |


---

### 2.- Distribucion de la red

- Los contenedores con dos o mas redes forman parte de la red de servicios que son s2, s3, s4, s5 i s6.
- Los contenedores s2, s3 i s4 son los unicos que tienen conectividad en las tres redes creadas para poder connectar tanto con el proxy de s1 como con la bbdd de s7.


---