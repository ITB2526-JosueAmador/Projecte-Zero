# Criterios de rendimiento y disponibilidad

## Capacidad según la configuración

Límites que se deducen de la configuración desplegada:

| Servicio | Parámetro | Valor | Capacidad resultante |
| :--- | :--- | :--- | :--- |
| Web | `worker_connections` (sin `worker_processes`, que por defecto vale 1) | `1024` | Hasta unas 1024 conexiones simultáneas |
| FTP | `pasv_min_port` y `pasv_max_port` | `40000`-`40100` | 101 puertos: como máximo 101 transferencias pasivas a la vez |
| FTP | `max_clients` y `max_per_ip` | Sin definir (`0`) | Sin límite de clientes (ver riesgos) |
| DHCP | `range` | `10.0.20.100`-`10.0.20.200` | 101 direcciones para clientes |
| DHCP | `default-lease-time` / `max-lease-time` | `600` / `7200` s | Concesiones de 10 minutos (máximo 2 horas) |
| DNS | `$TTL` | `86400` s | Los clientes guardan las respuestas hasta 24 horas: un cambio de IP puede tardar ese tiempo en verse |

## Objetivos medibles

Objetivos para el entorno de laboratorio. Cada uno se comprueba con una prueba del [plan de pruebas](../pruebas.md), donde se anota el valor medido.

| Criterio | Objetivo | Prueba |
| :--- | :--- | :--- |
| Tiempo de respuesta de la web desde la INTRANET | Código `200` en menos de 0,2 s | R1 |
| Tiempo de respuesta del DNS para la zona local | Menos de 50 ms | R2 |
| Velocidad de transferencia FTP (fichero de 100 MB) | 10 MB/s o más | R3 |
| Consulta a MySQL desde `W-NNC` | Menos de 1 s | R4 |
| Obtención de IP por DHCP | Menos de 10 s | R5 |
| Servicios disponibles tras reiniciar un servidor | Arrancan solos en menos de 2 minutos | C1 |
| Recuperación del FTP tras una caída | Vuelve a funcionar al arrancar el servicio | C2 |
| Restauración de la base de datos desde una copia | Mismo número de filas que el original | C4 |

## Análisis de disponibilidad

En esta fase no hay redundancia: cada servicio está en un solo equipo.

- **`R-NNC` es un punto único de fallo.** Si cae, la INTRANET se queda sin Internet y sin acceso a la DMZ, aunque DNS, DHCP y MySQL siguen funcionando dentro de la INTRANET.
- **`B-NNC` concentra MySQL, DNS y DHCP.** Si cae, la web se queda sin datos, los clientes no resuelven nombres y, cuando caduca la concesión DHCP (600 s por defecto), pierden la IP.
- **Recuperación:** todos los servicios están habilitados para arrancar con el sistema, la configuración completa está en este repositorio y `B-NNC` se puede reconstruir con su script. La base de datos se protege con copias (`mysqldump`, ver [mantenimiento](../administrador/mantenimiento.md)).

---

[Volver al índice](../../README.md)
