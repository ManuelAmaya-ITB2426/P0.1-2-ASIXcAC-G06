## 🚀 Estudio de tecnologías disponibles

---

## 🧩 Contexto del proyecto

El proyecto se basará en el despliegue de una aplicación web directamente sobre la infraestructura de **Amazon Web Services (AWS)**, utilizando un entorno servidor Linux. Todo el desarrollo y la configuración la realizaremos desde el inicio en AWS, evitando migraciones posteriores y trabajando desde el primer momento con un entorno real de producción.

La selección de tecnologías la haremos teniendo en cuenta cuatro criterios principales:

- **El rendimiento** asegurándonos de que todo lo que vayamos a configurar y lanzar funcione de manera eficaz, sin problemas de compatibilidad, ajustes o versiones. Buscamos que el software se gestione correctamente y funcione con un consumo eficiente de recursos.
- **Mantenimiento**, priorizando que todo lo configurado pueda editarse, actualizarse y mantenerse de forma sencilla, con configuraciones claras y sin dependencias innecesariamente complejas.
- **Uso real en entornos profesionales**, utilizando tecnologías ampliamente adoptadas en empresas reales, con soporte, documentación y aplicación directa en el mundo laboral.
- **Infraestructuras Cloud**, asegurando que todas las tecnologías estén pensadas para funcionar correctamente en AWS, permitiendo escalar, modificar o replicar la arquitectura sin rehacer configuraciones ni estructura.

---

## 🌐 Servidor web: NGINX vs Apache

Apache y NGINX han sido y siguen siendo los dos servidores web más utilizados. Ambos tienen pros y contras. Apache es muy flexible, muy documentado y sencillo de configurar, lo que facilita el mantenimiento. Sin embargo, su modelo basado en procesos consume más recursos y puede presentar problemas de rendimiento cuando hay muchas conexiones simultáneas.

Por otro lado, NGINX utiliza un modelo asíncrono, en el que un proceso no se queda bloqueado esperando a que una tarea finalice para hacer otra. Esto permite gestionar un gran número de conexiones simultáneas con un menor consumo de CPU y memoria, lo cual es perfecto para un entorno AWS.

Por lo cual en el proyecto usaremos **NGINX**, ya que ofrece mejor rendimiento, mayor eficiencia y una integración más natural con arquitecturas en AWS.

---

## ⚙️ Ejecución de PHP: PHP-FPM

PHP-FPM gestiona un conjunto de procesos PHP persistentes que se reutilizan, evitando crear un proceso nuevo por cada petición. Esto mejora notablemente el rendimiento y la estabilidad del sistema. Además, permite definir límites de memoria, número de procesos y usuarios, ayudando a controlar el consumo de recursos y reforzando la seguridad.

PHP-FPM permite ejecutar PHP como un servicio independiente del servidor web (NGINX). Esta separación mejora el mantenimiento y, sobre todo, la seguridad, ya que se pueden definir permisos y responsabilidades distintas para cada servicio. De este modo se reduce la superficie de ataque y se evita que un fallo en la aplicación PHP comprometa directamente al servidor web.

En entornos donde se utiliza NGINX, este enfoque es el más recomendado, ya que aporta estabilidad y facilita la escalabilidad y adaptación de la arquitectura en AWS. La separación de servicios es una práctica habitual en infraestructuras profesionales y cloud.

El funcionamiento es sencillo. NGINX no ejecuta PHP directamente. Su función es recibir peticiones HTTP y servir contenido estático, es decir, entregar archivos tal y como están almacenados en el servidor, sin ejecutar ningún tipo de código. Cuando un usuario solicita una imagen, un archivo CSS, JavaScript o una página HTML, NGINX simplemente lee el archivo del disco y lo envía al navegador.

Cuando una petición requiere ejecutar código PHP, NGINX la redirige a PHP-FPM. Este procesa el script, ejecuta la lógica necesaria y devuelve el resultado al servidor web, que finalmente lo entrega al cliente.

---

## 🗄️ Sistema gestor de bases de datos: MySQL

MySQL es un sistema gestor de bases de datos que se utiliza ampliamente en el entorno profesional y en aplicaciones web. Es estable, eficiente y compatible con Linux y con servicios desplegados en AWS.
Para el proyecto no necesitaremos una base de datos compleja, por lo que MySQL es la mejor opción.

---

## 📦 Contenerización: Docker

Docker nos va a permitir encapsular servicios y dependencias en contenedores, facilitando la consistencia del entorno y la portabilidad dentro de AWS, es decir, que la aplicación siempre se ejecutará con las mismas versiones y configuraciones, evitando errores por diferencias entre servidores, y que esos mismos servicios se pueden mover, replicar o escalar en instancias diferentes sin tener que reinstalar ni reconfigurar nada.

Docker también facilita el mantenimiento y la escalabilidad. Cada servicio (NGINX, PHP-FPM, MySQL) puede ejecutarse en su propio contenedor, lo que permite actualizar, reiniciar o modificar un componente sin afectar al resto del sistema. Esto encaja perfectamente con arquitecturas Cloud, donde es habitual separar servicios y escalar solo aquellos que lo necesitan.

Todo esto no es esencial para el proyecto, pero su uso aporta una mejora en la organización de la arquitectura y prepara el entorno para una infraestructura moderna y escalable en AWS. Con lo cual, vamos a usar Docker.

---

## 🧱 Stack tecnológico final

Las tecnologías elegidas permiten que la aplicación funcione de forma rápida, segura y ordenada. Cada parte se encarga de una función concreta: mostrar la web, ejecutar la aplicación y guardar los datos. Además, todo está preparado para crecer y funcionar correctamente dentro de AWS.:

- 🌐 **NGINX** como servidor web  
- ⚙️ **PHP-FPM** para la ejecución de PHP  
- 🗄️ **MySQL** como sistema gestor de bases de datos  
- 📦 **Docker** como herramienta de contenerización (opcional)
