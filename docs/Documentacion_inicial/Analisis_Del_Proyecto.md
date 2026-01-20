# Análisis del proyecto y planteamiento inicial

## 1. Introducción

El proyecto **Extagram** consiste en el despliegue de una aplicación web que permite a los usuarios publicar mensajes acompañados de imágenes.

La aplicación está desarrollada en PHP y utiliza una base de datos MySQL para el almacenamiento de la información.

El objetivo principal del proyecto no es el desarrollo de la aplicación, sino el despliegue de una arquitectura de sistemas escalable, modular y tolerante a fallos.

---

## 2. Objetivos

El objetivo final es tener desplegada una aplicación web con:

- **Alta disponibilidad.**
- **Escalabilidad horizontal.**
- **Separacion de servicios.**
- **Capacidad de tolerancia de fallos en servidores criticos.**

La **alta disponibilidad** sirve para que la aplicacion este preparada para seguir ofreciendo el servicio incluso cuando uno o varios componentes fallen.
Esto se consigue mediante la redundancia de servicios críticos, como los servidores que procesan la lógica de la aplicación o los servidores que sirven contenido estático.
La alta disponibilidad permite minimizar el tiempo de inactividad y mejorar la fiabilidad del sistema.

La **escalabilidad horizontal** se encarga de que la arquitectura permita aumentar la capacidad del sistema añadiendo nuevos servidores en lugar de aumentar los recursos de uno existente. 
De esta forma, es posible absorber un mayor número de peticiones de usuarios distribuyendo la carga entre varios nodos, especialmente en la capa de aplicación (PHP) y en los servidores web.

Con la **separacion de servicios** nos encargamos de que cada componente del sistema cumpla una función concreta y este aislado del resto.
Esto implica separar el proxy inverso, los servidores de aplicación, los servidores de contenido estático y la base de datos.
Esta separación facilita el mantenimiento, la detección de errores, la seguridad y la evolución independiente de cada servicio.

La **capacidad de tolerancia de fallos en servidores criticos** esta porque el sistema debe ser capaz de adaptarse a la caída de servicios clave sin interrumpir completamente la funcionalidad de la aplicación.
Un ejemplo de ello es la capacidad de la base de datos para almacenar imágenes como respaldo cuando el servidor de ficheros no está disponible, o la existencia de múltiples servidores de aplicación detrás de un balanceador de carga.

---

## 3. Alcance del Sprint 1

En el primer sprint el objetivo final es:

- Analizar el proyecto y las tecnologias necesarias.
- Diseñar una arquitectura inicial.
- Desplegar un **versión funcional minima** de la aplicacion.
- Repasar la instalacion y configuracion manual de los servicios.

En este sprint no se implementaran los contenedores ni la alta disponibilidad.

---

## 4. Arquitectura del Sprint 1

Durante el Sprint 1 se implementa la siguiente arquitectura en una sola maquina sin contenedores:

- Servidor web (NGINX o Apache).
- PHP con soporte para ejecución de scripts.
- Base de datos MySQL.
- Almacenamiento de imágenes en el sistema de ficheros o en la base de datos.

Esta arquitectura permite validar el funcionamiento completo de la aplicación antes de evolucionar hacia un entorno distribuido.

---

## 5. Tecnologias escojidas

| Componente | Tecnología |
|----------|------------|
| Sistema Operativo | Amazon Linux (AWS EC2) |
| Servidor Web | NGINX |
| Lenguaje backend | PHP |
| Base de datos | MySQL |
| Control de versiones | Git + GitHub |
| Documentación | Markdown |
| Gestión del proyecto | ProofHub |

---

## 6. Evolucion en los Sprints 2 y 3

En los Sprints 2 y 3 se abordarán los siguientes aspectos:

- Contenerización de los servicios mediante Docker
- Implementación de proxy inverso y balanceo de carga
- Segregación de contenido dinámico y estático
- Preparación de la base de datos para escenarios de fallo del servidor de imágenes
- Diseño de la topología de red mediante Packet Tracer

El trabajo realizado en el Sprint 1 sirve como base para esta evolución progresiva.

---

## 7. Metodología de trabajo

El proyecto se desarrolla siguiendo una metodología basada en sprints, con:

- Reuniones de planificación (Sprint Planning)
- Revisión de resultados (Sprint Review)
- Documentación continua en Markdown
- Control de versiones mediante GitHub

Todos los miembros del grupo son responsables del conocimiento global del proyecto, independientemente de la parte implementada por cada uno.

---


## 8. Conclusión

El Sprint 1 permite comprender el funcionamiento global del proyecto Extagram y validar una primera versión funcional de la aplicación. A partir de esta base, el proyecto podrá evolucionar hacia una arquitectura más compleja, escalable y tolerante a fallos, cumpliendo los objetivos planteados en el enunciado del proyecto.

---

