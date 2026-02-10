# Sprint 3 - Manual de operaciones y Runbooks

## Projecte 
**Proyecto 0.1 - Despliege extagram**


## 1. Introducción

Este documento describe los procedimientos operativos del proyecto **Extagram**, una aplicación web desplegada mediante contenedores Docker con una arquitectura distribuida basada en múltiples servicios:

- (proxy inverso, servidores PHP, almacenamiento y base de datos)

El objetivo de este manual es proporcionar instrucciones claras para:

- Operaciones habituales del sistema
- Copias de seguridad y restauración
- Escalado de servicios
- Actuación ante fallos (failover)

---

## 2. Arquitectura general del proyecto

La infraestructura del proyecto está compuesta por los siguientes contenedores:

| Servicio | Contenedor | Función |
|--------|------------|---------|
| Proxy inverso | s1-nginx | Balanceo de carga y entrada HTTP |
| Backend PHP | s2-phpfpm, s3-phpfpm | Procesamiento de peticiones |
| Upload | s4-upload | Gestión de subida de archivos |
| Storage | s5-storage | Servidor de imágenes |
| Static | s6-static | Contenido estático |
| Base de datos | s7-mysql | MySQL con datos persistentes |

La comunicación entre contenedores se realiza mediante redes Docker internas para no exponer la base de datos.

---

## 3. Runbooks de Operación

### 3.1 Arranque del sistema

Para iniciar todos los contenedores del proyecto:

- **bash**
- **docker start s1-s1-nginx-1 s1-s2-phpfpm-1 s1-s3-phpfpm-1 s1-s4-upload-1 s1-s5-storage-1-1 s1-s6-static-1-1 s7-mysql**

Para verificar que los servicios estan activos se usa el comando:
- **docker ps**

En el caso de que uno o varios no aparezca se puede ejecutar **docker ps -a** que muestra todos los contenedores tanto activos como inactivos. Esto nos sirve para ver si ha habido un error al levantarlo o no existe.

Si por alguna razon hay que detener un contenedor o varios se usa el comando:
- **docker stop <nom_contenidor>**

Pueden pararse uno o varios a la vez separando los nombres con espacios.

---

## 4. Backup de la base de datos

### 4.1 Descripción

La base de datos MySQL (**s7-mysql**) almacena información crítica del proyecto (posts, usuarios, metadatos de imágenes).  
Para evitar la pérdida de datos, se realizan copias de seguridad periódicas mediante backups lógicos.

### 4.2 Procedimiento de backup manual

El backup se realiza desde el host utilizando **docker exec**.

**bash**
**docker exec s7-mysql mysqldump -u root -p extagram_db > extagram_backup.sql**

# Script
    #!/bin/bash
      # Variables
        CONTAINER_NAME="s7"           # Nombre del contenedor MySQL
        DB_NAME="extagram_db"         # Nombre de la base de datos
        USER="root"                   # Usuario MySQL
        PASSWORD="rootpass"            # Contraseña MySQL
        BACKUP_DIR="$(pwd)"            # Directorio donde se guarda el backup
        DATE=$(date +'%Y%m%d_%H%M%S')
        BACKUP_FILE="$BACKUP_DIR/${DB_NAME}_backup_$DATE.sql"
      # Ejecutar el backup
        docker exec $CONTAINER_NAME sh -c "exec mysqldump -u$USER -p$PASSWORD $DB_NAME" > $BACKUP_FILE
echo "Backup guardado en: $BACKUP_FILE"

El script de backup tiene permisos restringidos para que solo el administrador pueda ejecutarlo.

---
