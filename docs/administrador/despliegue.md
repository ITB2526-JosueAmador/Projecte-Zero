# Orden de despliegue y dependencias

| Orden | Equipo | Sistema | Depende de | Motivo |
| :---: | :--- | :--- | :--- | :--- |
| 1 | `R-NNC` | Pendiente | - | Todas las zonas se comunican y salen a Internet a través de él |
| 2 | `B-NNC` | Ubuntu Server 22.04 LTS | `R-NNC` | Necesita salida a Internet para instalar paquetes y descargar el CSV, y da DNS y DHCP al resto |
| 3 | `W-NNC` | Pendiente | `R-NNC`, `B-NNC` | La web consulta MySQL en `B-NNC` |
| 3 | `F-NNC` | Ubuntu Server 24.04 LTS | `R-NNC`, `B-NNC` | Los clientes llegan a través del router y lo encuentran por nombre gracias al DNS |
| 4 | `PC-Linux`, `PC-W11` | Linux, Windows 11 | `B-NNC` | Reciben IP, puerta de enlace y DNS por DHCP |

> **Pendiente:** indicar el sistema operativo y la versión de `R-NNC` y `W-NNC`.

---

[Volver al índice](../../README.md)
