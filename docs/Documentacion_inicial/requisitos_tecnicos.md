## Análisis de requisitos técnicos y funcionales

---

## Contexto general

Este análisis define qué debe hacer la aplicación y qué necesita a nivel técnico para funcionar correctamente. El objetivo es identificar las funcionalidades esperadas, los componentes necesarios y cómo se relacionan entre sí dentro de una arquitectura desplegada en AWS.

Este documento servirá como base para el diseño de la arquitectura y el despliegue de la infraestructura.

---

## Requisitos funcionales

La aplicación web deberá permitir el acceso de usuarios a través de un navegador web mediante protocolo HTTP/HTTPS. Los usuarios podrán visualizar contenido web generado dinámicamente a partir de código PHP y datos almacenados en una base de datos.

La aplicación deberá ser accesible desde Internet, responder de forma correcta a las peticiones de los usuarios y mostrar la información de manera clara y funcional. No se contemplan funcionalidades avanzadas como autenticación compleja o alta concurrencia, ya que el alcance del proyecto es académico y controlado.

---

## Requisitos técnicos

La aplicación se desplegará sobre un servidor Linux en AWS, utilizando instancias EC2. Será necesario un servidor web NGINX para gestionar las peticiones HTTP y servir contenido estático.

La ejecución del código PHP se realizará mediante PHP-FPM, funcionando como un servicio independiente para mejorar rendimiento, mantenimiento y seguridad. La base de datos se gestionará con MySQL, donde se almacenará la información necesaria para el funcionamiento de la aplicación.

El sistema deberá contar con conectividad de red adecuada dentro de AWS, permitiendo la comunicación entre los distintos servicios y el acceso desde Internet de forma controlada.

---

## Dependencias entre servicios

NGINX depende de PHP-FPM para procesar las peticiones que requieren ejecución de código PHP. PHP-FPM, a su vez, depende de MySQL para acceder a los datos almacenados.

La base de datos no será accesible directamente desde el exterior, únicamente desde el servicio que ejecuta la aplicación, garantizando un mínimo de aislamiento y seguridad.

Cada servicio cumple una función concreta y está claramente separado del resto, siguiendo buenas prácticas de arquitectura.

---

## Flujo de datos

El flujo de funcionamiento comienza cuando un usuario realiza una petición desde su navegador. Esta petición llega al servidor NGINX, que decide si debe servir contenido estático o enviar la petición a PHP-FPM.

Si la petición requiere acceso a datos, PHP-FPM consulta la base de datos MySQL, procesa la información y devuelve el resultado a NGINX. Finalmente, NGINX envía la respuesta al navegador del usuario.

Este flujo garantiza una separación clara de responsabilidades y un funcionamiento ordenado del sistema.

---

## Consideraciones de contenedorización

El uso de Docker permitirá ejecutar cada servicio de forma aislada, facilitando la gestión de dependencias y asegurando que el entorno sea consistente dentro de AWS. Esto simplifica el mantenimiento, las actualizaciones y una posible escalabilidad futura.

---

## Conclusión

Los requisitos definidos cubren las necesidades funcionales de la aplicación y establecen una base técnica sólida. La separación de servicios, el uso de tecnologías estándar y la orientación a AWS garantizan una arquitectura clara, mantenible y alineada con entornos profesionales.
