# Firewall y conexiones

Matriz de conectividad entre zonas (origen en filas, destino en columnas). La política por defecto del router es **denegar** todo lo que no esté permitido, y siempre se acepta el tráfico de respuesta de conexiones ya establecidas (`ESTABLISHED, RELATED`).

| Origen \ Destino | Internet | DMZ | INTRANET |
| :--- | :---: | :---: | :---: |
| **Internet** | - | Solo servicios publicados (HTTP/HTTPS a `W-NNC`, FTP a `F-NNC`) | No |
| **DMZ** | Limitado (DNS, NTP, HTTP/HTTPS y ping) | - | Solo `W-NNC` → `B-NNC` (`3306/tcp`) |
| **INTRANET** | Sí (con NAT) | Sí (web, FTP y SSH) | - |

Justificación de las reglas:

- **Internet → DMZ (restringido):** solo se exponen los puertos de los servicios públicos. El resto, SSH incluido, no es accesible desde fuera.
- **Internet → INTRANET (no):** la red privada no es accesible desde fuera y además está detrás de NAT.
- **DMZ → INTRANET (solo `W-NNC` → `B-NNC` por `3306/tcp`):** es la única excepción, porque la web consulta MySQL. Cualquier otro intento desde la DMZ hacia la red privada se bloquea.
- **INTRANET → Internet (sí, con NAT):** los clientes necesitan salida a Internet y `B-NNC` necesita reenviar consultas DNS externas y descargar paquetes.
- **INTRANET → DMZ (sí):** los clientes usan la web y el FTP, y los servidores de la DMZ se administran por SSH desde aquí.
- **DMZ → Internet (limitado):** solo DNS, NTP, HTTP/HTTPS y ping, lo necesario para resolver nombres, sincronizar la hora e instalar actualizaciones.

Puertos de cada servicio:

| Servicio | Puerto | Equipo | Accesible desde |
| :--- | :--- | :--- | :--- |
| Web | `80/tcp` | `W-NNC` | Internet e INTRANET |
| FTP (control) | `21/tcp` | `F-NNC` | Internet e INTRANET |
| FTP (datos, modo pasivo) | `40000-40100/tcp` | `F-NNC` | Internet e INTRANET |
| MySQL | `3306/tcp` | `B-NNC` | `W-NNC` e INTRANET |
| DNS | `53/udp` y `53/tcp` | `B-NNC` | INTRANET |
| DHCP | `67/udp` | `B-NNC` | INTRANET |
| SSH | `22/tcp` | Todos | INTRANET |

---

[Volver al índice](../../README.md)
