# Análisis de necesidades

| Necesidad | Solución adoptada |
| :--- | :--- |
| Publicar servicios sin exponer la red interna | DMZ separada (`10.0.10.0/24`) y política de denegar por defecto en `R-NNC` |
| Proteger la base de datos | MySQL en la INTRANET; desde la DMZ solo `W-NNC` llega al puerto `3306/tcp` |
| Intercambiar ficheros con usuarios autenticados | FTP con `vsftpd`, sin acceso anónimo, con lista blanca, chroot y FTPS |
| Resolver los nombres de la organización | DNS BIND 9 con la zona `sitioitb.cat` y sus zonas inversas |
| Configurar los puestos sin intervención manual | DHCP con `isc-dhcp-server` en la INTRANET |
| Dar salida a Internet a las redes privadas | NAT (`masquerade`) en la interfaz WAN de `R-NNC` |
| Administrar los equipos en remoto | SSH en todos los equipos, accesible solo desde la INTRANET |
| Saber qué se ha cambiado y cuándo | Repositorio Git en GitHub con la configuración y la documentación |

---

[Volver al índice](../../README.md)
