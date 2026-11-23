# Monitorización de red

El objetivo del enunciado incluye un **monitor de redes** como una de las capas de la infraestructura (Web Server, monitor de redes, SSH, BBDD, DHCP, DNS y FTP).

> **Pendiente:** elegir, desplegar y documentar la herramienta de monitorización. Esta página tiene que recoger:
>
> - Herramienta elegida y motivo (añadirla también en el [estudio de tecnologías](../tecnica/tecnologias.md)).
> - Equipo donde se instala, zona e IP, y si comparte máquina con otro servicio.
> - Instalación desde los repositorios oficiales y configuración.
> - Equipos y servicios que se vigilan (`R-NNC`, `W-NNC`, `F-NNC`, `B-NNC`) y cómo se comprueban (ping, puerto, agente).
> - Reglas que hay que abrir en el cortafuegos de `R-NNC` para que el monitor llegue a la DMZ (añadirlas en [Firewall y conexiones](../tecnica/firewall.md) y en `/etc/nftables.conf`).
> - Acceso a la interfaz con el usuario `bchecker` / `bchecker121`.
> - Comprobación del servicio y captura o gif como evidencia (prueba M6 del [plan de pruebas](../pruebas.md#pruebas-de-monitorización)).

Mientras tanto, la vigilancia es manual con `systemctl` y los registros de cada servicio, tal como se describe en [Mantenimiento](mantenimiento.md) y en las pruebas M1 a M5.

---

[Volver al índice](../../README.md)
