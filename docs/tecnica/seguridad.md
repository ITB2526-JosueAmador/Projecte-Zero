# Medidas de seguridad

- **Segmentación:** los servicios públicos están en la DMZ y los datos y la infraestructura interna en la INTRANET.
- **Cortafuegos con estado y denegar por defecto:** `R-NNC` solo deja pasar lo que está permitido de forma explícita, y el tráfico de respuesta se acepta por el estado de la conexión.
- **Mínima exposición:** desde Internet solo se ven los puertos de la web y del FTP; SSH no se publica.
- **Base de datos aislada:** desde la DMZ solo `W-NNC` llega a MySQL, y solo al puerto `3306`.
- **FTP endurecido:** sin acceso anónimo, lista blanca de usuarios, cada usuario encerrado en su home (chroot) y cifrado TLS disponible (FTPS).
- **Administración:** SSH solo desde la INTRANET, también para el propio router.
- **GitHub sin contraseñas:** los servidores se autentican con una clave SSH propia.

Los puntos débiles conocidos y cómo corregirlos están en [Riesgos técnicos y mejoras](../gestion.md#riesgos-técnicos-y-mejoras).

---

[Volver al índice](../../README.md)
