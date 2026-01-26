# Desplegamento NGINX - Extagram Sprint 1

## Objetivo Sprint 1
Tener activo servicio NGINX: Servidor web/proxy rápido y ligero para miles de conexiones simultáneas. En Extagram: sirve estáticos, proxy PHP-FPM, /storage/ → uploads.

## Instalación Paquetes

```bash
sudo dnf install nginx
```
![Instalación NGINX][../../media/NGINX/cano_instalacion_nginx.png]

Estructura Carpetas Web
```text
/var/www/extagram/
├── src/              # PHP privado 
│   ├── extagram.php  # Muestra posts + form
│   ├── upload.php
├── static/           # Archivos fijos 
│   ├── style.css
│   └── preview.svg
└── uploads/          # Fotos usuarios
```

Comandos aplicados:
```bash
sudo mkdir -p /var/www/extagram/{src,static,uploads}
sudo chown -R nginx:nginx /var/www/extagram
sudo chmod -R 755 /var/www/extagram
```
![Carpetas creadas][../../media/NGINX/cano_creacion_carpetas.png]

Configuración NGINX
Archivo: /etc/nginx/conf.d/extagram.conf
Configuración específica Extagram incluida automáticamente por nginx.conf (include /etc/nginx/conf.d/*.conf;). Define root /var/www/extagram, rutas /static/ (CSS/SVG), /storage/ → /uploads/ (imágenes), proxy *.php → PHP-FPM. Cada app su archivo separado, recarga sin downtime.

```text
server {
    listen 80;
    server_name _;
    root /var/www/extagram;
    index extagram.php;

    # Ruteo principal → src/extagram.php
    location / { 
        try_files $uri $uri/ /src/extagram.php?$query_string; 
    }

    # Cache estáticos 1 año
    location /static/ { 
        expires 1y; 
        add_header Cache-Control "public, immutable"; 
    }

    # Fotos uploads
    location /storage/ { 
        alias /var/www/extagram/uploads/; 
        autoindex off; expires 30d; 
    }

    # PHP-FPM socket
    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass unix:/run/php-fpm/www.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```
![Config NGINX][../../media/NGINX/cano_extagram]

Activación:

```bash
sudo nginx -t && sudo systemctl enable --now nginx
```
![NGINX activo][../../media/NGINX/cano_enable_start_nginx.png]

Estado Actual
```text
NGINX v1.26.0 corriendo puerto 80
Estructura src/static/uploads perfecta
Config Sprint 1 completa
Pendiente: style.css / preview.svg en static/
URLs de Prueba
```
