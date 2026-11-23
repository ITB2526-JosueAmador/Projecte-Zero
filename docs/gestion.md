# Gestión del proyecto

## Planificación

| Fase | Tareas principales | Responsable | Fecha prevista | Documentado en el repositorio |
| :--- | :--- | :--- | :--- | :--- |
| Análisis y diseño | Esquema de red, direccionamiento, matriz de conexiones y justificación técnica | Pendiente | Pendiente | 19/10/2026 - 29/10/2026 |
| Router `R-NNC` | Interfaces, reenvío, cortafuegos y NAT | Pendiente | Pendiente | 11/11/2026 |
| Servidor web `W-NNC` | nginx y aplicación web | Pendiente | Pendiente | 22/10/2026 (configuración de nginx) |
| Servidor `B-NNC` | MySQL, carga del CSV, DNS, DHCP, SSH y script | Pendiente | Pendiente | 12/11/2026 |
| Servidor FTP `F-NNC` | vsftpd, FTPS, lista blanca y chroot | Pendiente | Pendiente | 23/11/2026 |
| Pruebas | Ejecución del [plan de pruebas](pruebas.md) y recogida de evidencias | Pendiente | Pendiente | Pendiente |
| Documentación | Documentación técnica, de administrador y de cliente | Pendiente | Pendiente | Continua |

## Planificación en ProofHub

El enunciado pide planificar las tareas en ProofHub con un backlog y sprints quincenales de 10 horas (5 horas por semana). El proyecto dura 6 semanas hasta el 18/11/2026, así que son 3 sprints.

| Campo | Valor |
| :--- | :--- |
| Proyecto en ProofHub | `P0.0-ASIXc2gC-G05` (**Pendiente:** sustituir `g` por la letra del grupo, A o B) |
| Enlace al proyecto | **Pendiente** |
| Duración | 6 semanas, hasta el 18/11/2026 |
| Sprints | 3 sprints quincenales de 10 horas |

Las fechas de los sprints se han calculado hacia atrás desde el 18/11/2026; si en ProofHub son otras, hay que corregirlas aquí. En la última columna está lo que ya consta en el [historial de la documentación](#control-de-tareas) dentro de cada sprint.

| Sprint | Fechas | Objetivo | Tareas del backlog | Documentado en el repositorio |
| :--- | :--- | :--- | :--- | :--- |
| Sprint 1 | 07/10/2026 - 20/10/2026 | **Pendiente** | **Pendiente** | Estructura inicial, esquema de red, direccionamiento, flujo de interconexión y gifs (19/10) |
| Sprint 2 | 21/10/2026 - 03/11/2026 | **Pendiente** | **Pendiente** | Configuración de nginx (22/10), firewall y conexiones (29/10) |
| Sprint 3 | 04/11/2026 - 18/11/2026 | **Pendiente** | **Pendiente** | Router (11/11), `B-NNC` e índice (12/11) |

> **Pendiente:** añadir capturas del backlog y del tablero de ProofHub al final de cada sprint (por ejemplo en `Imagenes/proofhub/`).

## Recursos

| Máquina | Sistema | Interfaces | Software principal (repositorios oficiales) |
| :--- | :--- | :--- | :--- |
| `R-NNC` | Pendiente | `enp1s0`, `enp2s0` (sin uso), `enp3s0`, `enp4s0` | `nftables` |
| `W-NNC` | Pendiente | Pendiente | `nginx`, `openssh-server` |
| `F-NNC` | Ubuntu Server 24.04 LTS | `enp1s0`, `enp2s0` | `vsftpd`, `openssh-server` |
| `B-NNC` | Ubuntu Server 22.04 LTS | `enp3s0` | `bind9`, `isc-dhcp-server`, `mysql-server`, `openssh-server`, `git` |
| `PC-Linux` | Linux | 1 (DHCP) | Navegador y FileZilla |
| `PC-W11` | Windows 11 | 1 (DHCP) | Navegador y FileZilla |

> **Pendiente:** añadir la plataforma de virtualización y los recursos asignados a cada máquina virtual (vCPU, RAM y disco).

## Control de tareas

Historial de la documentación en el repositorio:

| Fecha | Cambio |
| :--- | :--- |
| 19/10/2026 | Estructura inicial, esquema de red, tabla de direccionamiento, flujo de interconexión y gifs de los servicios |
| 22/10/2026 | Configuración de nginx |
| 29/10/2026 | Firewalls y conexiones, revisión de la justificación técnica y corrección del identificador `R-NNC` |
| 11/11/2026 | Configuración del router y reglas de la DMZ hacia Internet |
| 12/11/2026 | Configuración de `B-NNC` e índice |
| 23/11/2026 | Configuración del servidor FTP |

El seguimiento de las tareas se hace en ProofHub (ver [Planificación en ProofHub](#planificación-en-proofhub)).

Diario de trabajo:

| Fecha | Miembro | Tarea | Horas | Estado | Evidencia |
| :--- | :--- | :--- | :---: | :--- | :--- |
| Pendiente | Pendiente | Pendiente | Pendiente | Pendiente | Pendiente |

## Riesgos técnicos y mejoras

| Riesgo | Impacto | Situación actual | Mejora propuesta |
| :--- | :--- | :--- | :--- |
| `F-NNC` tiene una segunda interfaz con salida directa a Internet | Se salta el cortafuegos de `R-NNC`. Además, las respuestas a clientes de Internet salen por `enp1s0` en lugar de por el router, así que el FTP publicado en la WAN no funciona | Ruta por defecto por `enp1s0` y ruta estática a la INTRANET por `R-NNC` | Desactivar `enp1s0` y usar `R-NNC` como puerta de enlace por defecto. Si se publica a Internet, anunciar en `pasv_address` la IP pública del router |
| TLS 1.0 habilitado en FTPS | Protocolo obsoleto (RFC 8996) | `ssl_tlsv1=YES` | Quitar la línea o poner `ssl_tlsv1=NO`: en vsftpd 3.0.5 (Ubuntu 24.04) TLS 1.2 y TLS 1.3 ya están activos por defecto. Validar con la prueba S9 |
| Cifrados débiles en TLS 1.2 | Según el manual de vsftpd 3.0.5, el valor por defecto de `ssl_ciphers` es `DES-CBC3-SHA` (3DES) | `ssl_ciphers` sin definir | Añadir `ssl_ciphers=HIGH` |
| FTPS opcional | Si el cliente no usa TLS, usuario, contraseña y ficheros viajan sin cifrar | `force_local_logins_ssl=NO` y `force_local_data_ssl=NO` | Ponerlos a `YES` cuando todos los clientes usen TLS |
| Certificado autofirmado | Los clientes podrían aceptar un certificado falso sin darse cuenta | Certificado `snakeoil` | Comprobar la huella SHA-256 o usar una CA interna |
| Sin límite de conexiones FTP | Agotamiento de recursos (denegación de servicio) | `max_clients` y `max_per_ip` sin definir (`0`, sin límite) | Fijar límites, por ejemplo `max_clients=50` y `max_per_ip=5` |
| Misma contraseña débil en todos los equipos | Con una sola credencial robada se obtiene `sudo` en toda la infraestructura | `bchecker121`, fijada por el enunciado | Fuera del laboratorio: contraseñas distintas, SSH con clave y `PasswordAuthentication no` |
| Usuario de MySQL con todos los permisos desde toda la DMZ | Si se salta el cortafuegos, cualquier equipo de la DMZ puede modificar los datos | `GRANT ALL` a `'bchecker'@'10.0.10.%'` | Usuario de solo lectura para la web, limitado a `10.0.10.10` (`GRANT SELECT`) |
| `nginx.conf` sin `include mime.types` | nginx solo reconoce por defecto los tipos `html`, `gif` y `jpg`; el resto (CSS, JS, PNG...) se sirve como `text/plain` | No se incluye | Añadir `include /etc/nginx/mime.types;` dentro de `http { }` |
| Puerto 443 redirigido sin servicio HTTPS | La web viaja sin cifrar y los intentos de HTTPS fallan | `R-NNC` redirige el 443, pero nginx solo escucha en el 80 | Configurar HTTPS en `W-NNC` (`listen 443 ssl` y certificado) o quitar el 443 de las reglas |
| DNS para la DMZ incoherente | `allow-query` admite la DMZ, pero el cortafuegos no deja pasar el puerto 53 de la DMZ a `B-NNC` | Configuración contradictoria | Decidir si la DMZ usa el DNS interno (abrir el 53 hacia `B-NNC`) o no (quitar la DMZ de `allow-query` y `allow-recursion`) |
| Sin validación DNSSEC | Las respuestas recursivas no se validan (riesgo de envenenamiento de caché) | `dnssec-validation no` | `dnssec-validation auto;` |
| ISC DHCP sin mantenimiento | ISC dejó de mantenerlo a finales de 2022 y recomienda Kea | `isc-dhcp-server` | Migrar a Kea en una fase posterior |
| Deploy key con permiso de escritura | Si se compromete `B-NNC`, se puede modificar el repositorio | "Allow write access" activado | Claves de solo lectura en los servidores que solo hacen `git pull` |
| Puntos únicos de fallo | La caída de `R-NNC` o de `B-NNC` deja servicios sin funcionar | Sin redundancia | Copias programadas de la base de datos y de la configuración; redundancia en fases posteriores |

El plan de prevención de riesgos laborales corresponde a las fases P0.2 y PF.

---

[Volver al índice](../README.md)
