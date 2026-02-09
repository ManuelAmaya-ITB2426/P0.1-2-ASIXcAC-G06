# Sprint 2 - Despliege del contenedor de Base de Datos.

## Projecte 
**Proyecto 0.1 - Despliege extagram**

## 1. Preparacion de Docker

Como el contenedor de base de datos ha sido el primero en crearse es con el que tambien se ha instalado i habilitado el uso sin sudo de los comandos de docker.

Para habilitar el uso sin sudo se usa el comando:
- **sudo usermod -aG docker ec2-user**

---

## 2. Estructura de directorios para S7

Para almacenar los archivos de los contenedores se ha creado un directorio padre para ellos llamdo Sprint2 donde estaran los subdirectorios de todos los contenedores para mantenerlos en un lugar organizados.
- **sudo mkdir -p sprint2**

Dentro del cual se crean los subdirectorios numerados como pone en el documento S*. En el caso de la BBDD es S7.

Dentro del subdirectorio S7 hay dos directorios llamados **data** y **init**. Y un archivo Dockerfile.

Dentro de directorio **init** hay un archivo .sql que crea la base de datos en el primer inicio del contenedor.

El DockerFile contiene lo necessario para que al crear el contenedor i ejecutarlo:

- Se utilize la imagen oficial mysql:8.0.
- MYSQL_ROOT_PASSWORD defina la contraseña del root de MySQL.
- MYSQL_DATABASE cree la base de datos inicial extagram_db.
- Los scripts dentro de init/ se ejecutaran solo la primera vez que se cree el contenedor.

El contenido de el Dockerfile esta en la carpeta de media.

---

## 3. Contruccion del Contenedor

Para poder construir el contenedor hay que posicionarse en el directorio **sprint2/S7** una vez alli se ejecutar el comando para construir el contenedor:
- **docker build -t s7-mysql .**

Para ejecutarlo hay que usar el comando **docker run** combinado con opciones para la persistencia i el puerto de la base de datos ya que previamente se ha apagado i dehabilitado el servicio de la maquina host:
- **sudo run -d \ ***
- **--name s7 \ ***
- **-v $(pwd)/data:/var/lib/mysql \ ***
- **-p 3306:3306 \ ***
- **s7**

Un cop creat cal copmrovar que esta en up amb la comanda **docker ps** que lista los contenedores activos.

## 4. Comprovaciones desde el contenedor

Para entrar al contenedor directamente en el servicio de base de datos que es lo que nos interesa para comprovar que se ha creado gracias al archivo **init.sql** se usa el comando:
- **docker exec -it s7-mysql mysql -u root -p**

La clave del root esta especificada en el Dockerfile del contenedor. Esto abrira directament el terminal del servicio de base de datos dentro del contenedor. 

I para comprovar que se ha creado con exito hay que ejecutar esto uno por uno:

- **SHOW DATABASES;** 
- **USE extagram_db;**
- **SHOW TABLES;**

Si se han creado correctamente veremos la misma estructura que hay en la maquina host pero sin ningun dato en la tabla.

---