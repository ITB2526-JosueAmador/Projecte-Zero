# Arquitectura de red

La red se organiza alrededor del router `R-NNC`, con una interfaz para cada zona. En el esquema las interfaces aparecen como `Eth 0`, `Eth 1` y `Eth 2`; en la tabla está su nombre real en el sistema.

## Router y cortafuegos (`R-NNC`)

Es el elemento central de encaminamiento y la frontera de seguridad entre Internet y las redes privadas.

| Interfaz (esquema) | Interfaz (sistema) | IP | Red |
| :--- | :--- | :--- | :--- |
| `Eth 0` | `enp1s0` | DHCP | WAN / Internet (sale con NAT) |
| — | `enp2s0` | — | Reservada para una VPN WireGuard (no se usa) |
| `Eth 1` | `enp3s0` | `10.0.10.1/24` | Puerta de enlace de la DMZ |
| `Eth 2` | `enp4s0` | `10.0.20.1/24` | Puerta de enlace de la INTRANET |

Separa el tráfico público del privado y aplica las reglas del cortafuegos, de forma que un servidor de la DMZ comprometido no pueda llegar a la red privada.

## Zona DMZ (`10.0.10.0/24`)

Aísla los servidores que tienen que ser accesibles desde fuera, para limitar el impacto de una posible vulnerabilidad.

- **Servidor web `W-NNC` (`10.0.10.10/24`):** nginx y SSH. Aloja contenido público, así que está separado de la base de datos y de los clientes. Muestra la aplicación que consulta los datos de equipamientos educativos de Barcelona.
- **Servidor FTP `F-NNC` (`10.0.10.20/24`):** vsftpd y SSH. El servicio de ficheros queda fuera de la INTRANET para que un problema en él no se propague a la red local.

## Zona INTRANET (`10.0.20.0/24`)

Es el núcleo privado: servicios de infraestructura interna y equipos de usuario.

- **Servidor `B-NNC` (`10.0.20.10/24`):** MySQL, DNS, DHCP y SSH. La base de datos queda protegida de accesos directos desde Internet y desde la DMZ; solo el servidor web la consulta por el puerto `3306`. DHCP da IP, máscara, puerta de enlace y DNS a los clientes; DNS resuelve los nombres locales y reenvía el resto a servidores externos.
- **Cliente Linux (`PC-Linux`):** configuración por DHCP, recibe una IP del rango `10.0.20.100`-`10.0.20.200`.
- **Cliente Windows 11 (`PC-W11`):** configuración por DHCP. Sirve para comprobar que DHCP, DNS y la web funcionan también con Windows.

---

[Volver al índice](../../README.md)
