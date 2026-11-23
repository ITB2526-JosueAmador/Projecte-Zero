# Clientes de la INTRANET

`PC-Linux` y `PC-W11` no necesitan configuración manual: lo reciben todo por DHCP desde `B-NNC`.

| Equipo | Comando | Resultado esperado |
| :--- | :--- | :--- |
| `PC-Linux` | `ip -br a` y `resolvectl status` | IP entre `10.0.20.100` y `10.0.20.200`; DNS `10.0.20.10` |
| `PC-W11` | `ipconfig /all` | IP entre `10.0.20.100` y `10.0.20.200`; puerta de enlace `10.0.20.1`; DNS `10.0.20.10` |

> **Pendiente:** el enunciado pide que **todos** los sistemas tengan el usuario `bchecker` / `bchecker121`. Hay que crearlo también en `PC-Linux` y `PC-W11` y documentar cómo se ha hecho (en Linux con `useradd` y `chpasswd`, como en los servidores; en Windows desde Configuración o con `net user`).

Para el uso de la web y del FTP desde estos equipos, ver la [documentación de cliente](../cliente.md).

---

[Volver al índice](../../README.md)
