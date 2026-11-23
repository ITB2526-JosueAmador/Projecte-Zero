# Estudio de tecnologías

Todo el software es libre y se instala desde los repositorios oficiales de la distribución.

| Servicio | Tecnología elegida | Alternativas | Motivo de la elección |
| :--- | :--- | :--- | :--- |
| Cortafuegos y NAT | `nftables` | `iptables`, pfSense, OPNsense | `nftables` sustituye a `iptables` en Linux: una sola herramienta para filtrado y NAT, con conjuntos (`{ 80, 443 }`) y variables (`define`) que hacen las reglas más legibles. pfSense y OPNsense son distribuciones completas de cortafuegos; aquí el router ya es un Linux y basta con el filtrado del kernel. |
| Web | `nginx` | Apache HTTP Server | Arquitectura basada en eventos con poco consumo de memoria para servir contenido estático, y configuración centralizada sin ficheros `.htaccess`. |
| FTP | `vsftpd` | ProFTPD, Pure-FTPd, SFTP (OpenSSH) | Está en los repositorios oficiales de Ubuntu, su diseño prioriza la seguridad (chroot y separación de privilegios) y soporta FTPS. SFTP es un protocolo distinto que funciona sobre SSH; el proyecto pide un servidor FTP. |
| DNS | BIND 9 | dnsmasq, Unbound | Hace de servidor autoritativo y recursivo a la vez, con zonas directas e inversas en ficheros estándar. dnsmasq está pensado para redes pequeñas y Unbound solo es recursivo. |
| DHCP | `isc-dhcp-server` | Kea, dnsmasq | Configuración sencilla y muy documentada. ISC ya no lo mantiene y recomienda Kea (ver [riesgos](../gestion.md#riesgos-técnicos-y-mejoras)). |
| Base de datos | MySQL 8 | MariaDB, PostgreSQL | Permite cargar el CSV directamente con `LOAD DATA LOCAL INFILE`. |
| Configuración de red | Netplan (servidores), `ifupdown` (router) | NetworkManager, systemd-networkd | Netplan es el sistema por defecto de Ubuntu Server; el router usa `/etc/network/interfaces`. |
| Monitorización de red | Pendiente | Pendiente | Pendiente (ver [Monitorización de red](../administrador/monitorizacion.md)) |
| Control de versiones | Git y GitHub | GitLab, Gitea | Repositorio compartido del equipo, con autenticación por clave SSH (deploy key) desde los servidores. |

## Alternativas comerciales

| Necesidad | Servicio | Empresa | Por qué no se usa |
| :--- | :--- | :--- | :--- |
| Cortafuegos perimetral | FortiGate | Fortinet | Hardware y licencias de pago; el proyecto consiste en montar el cortafuegos con Linux. |
| DNS gestionado | Route 53 | Amazon Web Services | Pago por uso y la zona queda fuera de la infraestructura propia. |
| Transferencia de ficheros gestionada | AWS Transfer Family (SFTP, FTPS y FTP) | Amazon Web Services | Pago por uso; los ficheros se guardarían en la nube del proveedor. |
| Base de datos gestionada | Azure Database for MySQL | Microsoft | Pago por uso y dependencia del proveedor. |

En todos los casos el proyecto consiste en desplegar y administrar los servicios en máquinas virtuales propias, así que se descartan las opciones gestionadas.

---

[Volver al índice](../../README.md)
