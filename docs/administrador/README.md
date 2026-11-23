# Documentación de administrador

Explica cómo instalar, configurar, comprobar y mantener cada equipo. Los comandos se ejecutan con el usuario `bchecker`, que tiene permisos de `sudo`.

| Documento | Contenido |
| :--- | :--- |
| [Orden de despliegue y dependencias](despliegue.md) | En qué orden se monta cada equipo y por qué |
| [Router R-NNC](router.md) | Interfaces, reenvío de paquetes, cortafuegos y NAT con nftables |
| [Servidor web W-NNC](web.md) | nginx y aplicación web que muestra los datos de MySQL |
| [Servidor FTP F-NNC](ftp.md) | vsftpd con FTPS, lista blanca y chroot |
| [Servidor B-NNC (BBDD, DNS, DHCP y SSH)](b-nnc.md) | MySQL y carga del CSV, BIND 9, isc-dhcp-server y SSH |
| [Clientes de la INTRANET](clientes.md) | Comprobación de `PC-Linux` y `PC-W11` |
| [Monitorización de red](monitorizacion.md) | Herramienta de monitorización de la infraestructura |
| [Control de versiones con Git y GitHub](git.md) | Clave SSH, comandos de Git y flujo de subida a producción |
| [Mantenimiento](mantenimiento.md) | Tareas periódicas de cada equipo |
| [Resolución de problemas](problemas.md) | Problemas encontrados durante el despliegue y errores habituales |

---

[Volver al índice](../../README.md)
