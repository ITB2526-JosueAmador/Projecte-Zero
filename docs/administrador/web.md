# Servidor web W-NNC

![Gif Web Server.](../../Imagenes/webserver.gif)

> **Pendiente:** documentar el sistema operativo y la configuración de red de `W-NNC`.

## Aplicación de consulta de datos

El apartado de pruebas del enunciado pide desplegar una pequeña aplicación que muestre el contenido de las tablas cargadas en MySQL (`equipaments` en `B-NNC`).

> **Pendiente:** la configuración de nginx de abajo solo sirve ficheros estáticos (`web.html`), así que todavía no hay nada que consulte la base de datos. Hay que documentar:
>
> - Lenguaje y forma de ejecución (por ejemplo, PHP con `php-fpm` detrás de nginx, o una aplicación con su propio servicio y nginx como proxy inverso).
> - Paquetes instalados desde los repositorios oficiales.
> - Fichero de conexión a MySQL: host `10.0.20.10`, puerto `3306`, base de datos `equipaments` y usuario `bchecker`.
> - Código de la aplicación, guardado en el repositorio.
> - Cambios en la configuración de nginx para ejecutar la aplicación.
> - Captura o gif de la web mostrando los datos (evidencia de la prueba F6 del [plan de pruebas](../pruebas.md#pruebas-funcionales)).

## Configuración de nginx

```nginx
events {
    worker_connections 1024;
}

http {
    server {
        listen 80;
        listen [::]:80;

        server_name sitioitb.cat;
        root /var/www/html/;
        index web.html web.htm;

        location / {
        try_files $uri $uri/ =404;
        }
    }
}
```

Después de cada cambio se comprueba la sintaxis y se recarga el servicio sin cortar las conexiones abiertas:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

## Comprobación de W-NNC

```bash
systemctl is-active nginx ssh
curl -I http://10.0.10.10/
nc -zv -w 3 10.0.20.10 3306
```

| Comprobación | Resultado esperado |
| :--- | :--- |
| Servicios | `active` |
| Respuesta HTTP | `HTTP/1.1 200 OK` |
| Acceso a MySQL en `B-NNC` | `succeeded` |

---

[Volver al índice](../../README.md)
