# Sprint 1 - Despliege de la Base de Datos.

## Projecte 
**Proyecto 0.1 - Despliege extagram**

## 1. Entorno de trabajo

| Elemento | Valor |
|--------|------|
| Plataforma | AWS EC2 |
| Sistema Operativo | Amazon Linux 2023 |
| Motor de BBDD | MySQL Server |
| Sprint | Sprint 1 (instal·lacion nativa, sin Docker) |

---

## 2. Creacion de la instancia EC2

- Tipo de instancia: `t2.micro` (free tier)
- Sistema: `Amazon Linux 2023`
- Security Group configurado con:
  - Port **22/TCP** → SSH
  - Port **80/TCP** → HTTP
  - Port **3306/TCP** → MySQL (solo para connexiones internas)

---

## 3. Connexion a la instancia

- Connexion por ssh.
- Comando: **ssh -i "labsuser.pem" ec2-user@<IP_PUBLICA>**

---

## 4. Instalacion i configuracion del servicio

Para actualizar la bibliotecas o instalar programas en "Amazon Linux 2023" hay que substituir el comando de linux **apt** por **dnf**. Ya que no acepta la orden apt.
- **sudo dnf install mysql-server -y**

Una vez instalado el servicio requiere que se inicie i habilite manualmente.
- **sudo systemctl start mysqld**
- **sudo systemctl enable mysqld**

Para la configuracion inical de seguridad del servicio hay que ejecutar:
- **sudo mysql_secure_instalation**
Al ejectuar el comando te da varias opciones las que se han seleccionado son las siguientes:
    - Assignar contraseña al usuario root.
    - Eliminacion de los usuarios anonimos.
    - Bloquear conexiones remotas del root.
    - Eliminar la base de datos de prueba.
Una vez terminado esto el servicio de base de datos esta listo para su uso.

---

## 5. Creacion de la Base de Datos

Para acceder a al servicio para poder crear la base de datos se usa uno de estos comandos:
- **sudo mysql**
- **sudo -u root -p**

Cuando se ha realizado la conexion lo primero es crear la base de datos que vamos a utilizar. Para crear la base de datos se usa este comando:
- **CREATE DATABASE extagram_db;**

Para una base de datos nueva hay que crear un usuario administrador que tenga privileguios completos en ella. Para crear el usuario se ha usado el comando:
- **CREATE USER 'extagram_admin'@'%' IDENTIFIED BY 'contraseña_segura';**
Una vez creado es un usuario comun para que tenga privilegios sobre una base de datos concreta hay que ejecutar:
- **GRANT ALL PRIVILEGES ON extagram_db.*** **TO 'extagram_admin'@'%';**
Para hacerlos efectivos i permanentes finalmente hace falta ejecutar un ultimo comando:
- **FLUSH PRIVILEGES;**
Con esto se ha creado la base de datos, un usuario con privilegios i se han aplicado.

---

## 6. Creacion del esquema de base de datos

Con la base de datos creada para poder crear tablas dentro primero hay que acceder a ella con este comando:
- **USE extagram_db;**

Para crear una tabla hay que darle un nombre pero tambien crear columnas donde se almacenaran los datos:
- **CREATE TABLE posts (**
    **post TEXT NOT NULL,**
    **photourl TEXT**
  **);**
Con esta estructura podemos almacenar:
- El texto de cada publicacion.
- El nombre identificador de la imagen associada.

Si se hicieran falta mas columnas o tablas se pueden crear a medida que aparezca la necessidad de ellas.

---
