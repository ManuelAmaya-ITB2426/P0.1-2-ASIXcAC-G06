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

La infraestructura se ha diseñado utilizando múltiples redes Docker con el objetivo de separar los diferentes tipos de tráfico:

- **Red de acceso (proxy)**: permite la comunicación entre el proxy inverso (s1-nginx) y los servicios backend.
- **Red de servicios**: conecta los servicios PHP y los servidores de contenido.
- **Red de datos**: dedicada exclusivamente a la comunicación con la base de datos MySQL.

Los contenedores **s2, s3 y s4** están conectados a las tres redes, ya que necesitan:
- Recibir peticiones desde el proxy **s1-nginx**
- Acceder a la base de datos **s7-mysql**
- Comunicarse con los servicios de almacenamiento y contenido estático

Los contenedores **s5 y s6** solo tienen conectividad con el proxy y la red de servicios, ya que no requieren acceso directo a la base de datos.

El contenedor **s7-mysql** únicamente está conectado a la red de datos con s2,s3 i s4 para limitar su exposición y mejorar la seguridad.

---

