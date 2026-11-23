# Objetivos y destinatarios

**Problema.** Hay que publicar una aplicación web que consulta los datos de equipamientos educativos de Barcelona (Open Data BCN) y un servicio de intercambio de ficheros, sin poner en riesgo la red interna ni la base de datos donde están esos datos.

**Objetivos:**

- Segmentar la red en tres zonas (WAN, DMZ e INTRANET) con un router-cortafuegos que controle todo el tráfico entre ellas.
- Publicar en la DMZ los servicios accesibles desde fuera: web (`W-NNC`) y FTP (`F-NNC`).
- Mantener en la INTRANET los servicios internos: MySQL, DNS y DHCP (`B-NNC`).
- Cargar en MySQL el conjunto de datos de equipamientos educativos de Open Data BCN y mostrarlo en la web.
- Administrar todos los servidores por SSH desde la INTRANET y versionar la configuración y la documentación en GitHub.

**Destinatarios:**

- Usuarios de la INTRANET (puestos Linux y Windows 11): usan la web y el FTP, y reciben la configuración de red automáticamente.
- Visitantes de Internet: solo pueden llegar a los servicios publicados en la DMZ.
- Administradores: despliegan y mantienen la infraestructura.

---

[Volver al índice](../../README.md)
