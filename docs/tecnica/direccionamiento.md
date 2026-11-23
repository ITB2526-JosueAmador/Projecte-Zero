# Direccionamiento IP

| Zona | Subred / rango | Propósito | Máquinas |
| :--- | :--- | :--- | :--- |
| WAN / NAT | Asignada por el proveedor | Salida a Internet | Interfaz `enp1s0` de `R-NNC` |
| DMZ | `10.0.10.0/24` | Servicios públicos | `W-NNC` (`10.0.10.10`) y `F-NNC` (`10.0.10.20`) |
| INTRANET | `10.0.20.0/24` | Infraestructura y puestos de trabajo | `B-NNC` (`10.0.20.10`), `PC-Linux` y `PC-W11` |

| Hostname | Zona | IP / subred | Servicios | Notas y credenciales |
| :--- | :--- | :--- | :--- | :--- |
| `R-NNC` | WAN, DMZ e INTRANET | WAN: DHCP; DMZ: `10.0.10.1/24`; INTRANET: `10.0.20.1/24` | Enrutamiento, NAT y cortafuegos | El DNS resuelve `R-NNC`, `R-NCC` y `R` a este equipo |
| `W-NNC` | DMZ | `10.0.10.10/24` | Web, SSH | Usuario `bchecker` / `bchecker121` |
| `F-NNC` | DMZ | `10.0.10.20/24` | FTP/FTPS, SSH | Usuario `bchecker` / `bchecker121` |
| `B-NNC` | INTRANET | `10.0.20.10/24` | MySQL, DNS, DHCP, SSH | Importa el CSV de datos. Usuario `bchecker` / `bchecker121` |
| `PC-W11` | INTRANET | Dinámica (DHCP) | Cliente Windows | IP del rango `10.0.20.100`-`10.0.20.200` |
| `PC-Linux` | INTRANET | Dinámica (DHCP) | Cliente Linux | IP del rango `10.0.20.100`-`10.0.20.200` |

## Flujo de interconexión

- **Resolución de nombres:** el DNS de `B-NNC` resuelve `R-NNC`, `R-NCC` y `R` hacia la IP del router, y `ftp` y `www` hacia los servidores de la DMZ.
- **Web a base de datos:** la aplicación de `W-NNC` (`10.0.10.10`) consulta MySQL en `B-NNC` (`10.0.20.10`) por el puerto `3306`, cruzando el router.
- **Clientes a la DMZ:** los puestos de la INTRANET llegan a la web y al FTP a través de `R-NNC`.
- **Administración:** todas las máquinas tienen la cuenta `bchecker` para administrarlas por SSH desde la INTRANET.

---

[Volver al índice](../../README.md)
