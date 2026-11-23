# Plan de pruebas

Juego de pruebas para validar la solución. Cada prueba indica desde qué equipo se lanza y el resultado esperado. En la columna **Resultado** se anota lo obtenido y la evidencia (captura o gif). Los comandos de las pruebas funcionales están en la comprobación de cada equipo.

## Pruebas funcionales

| ID | Prueba | Desde | Resultado esperado | Resultado |
| :--- | :--- | :--- | :--- | :--- |
| F1 | Interfaces, reenvío y reglas del router ([comprobación](administrador/router.md#comprobación-de-r-nnc)) | `R-NNC` | IPs correctas, `ip_forward = 1`, reglas cargadas | Pendiente |
| F2 | Servicios de `B-NNC` ([comprobación](administrador/b-nnc.md#comprobación-de-b-nnc)) | `B-NNC` | `ssh`, `named`, `isc-dhcp-server` y `mysql` en estado `active` | Pendiente |
| F3 | Resolución DNS directa e inversa | `PC-Linux` | `r` y `r-ncc` → `10.0.20.1`; `w-nnc` → `10.0.10.10`; `-x 10.0.20.1` → `r-nnc.sitioitb.cat.` | Pendiente |
| F4 | DHCP en Linux y Windows ([clientes](administrador/clientes.md)) | `PC-Linux`, `PC-W11` | IP del rango `10.0.20.100`-`10.0.20.200`, puerta de enlace `10.0.20.1`, DNS `10.0.20.10` | Pendiente |
| F5 | Datos cargados en MySQL | `B-NNC` | `SELECT COUNT(*)` devuelve más de 0 filas | Pendiente |
| F6 | Web ([comprobación](administrador/web.md#comprobación-de-w-nnc)) | `PC-Linux` | `HTTP/1.1 200 OK` y la página muestra los datos | Pendiente |
| F7 | FTP: login, listado, descarga y subida con FTPS ([comprobación](administrador/ftp.md#comprobación-de-f-nnc)) | `PC-Linux` | Todo correcto | Pendiente |
| F8 | Salida a Internet con NAT | `PC-Linux` | `ping -c2 8.8.8.8` y `ping -c2 google.com` responden | Pendiente |
| F9 | Acceso a GitHub desde `B-NNC` | `B-NNC` | `ssh -T git@github.com` responde "successfully authenticated" | Pendiente |

## Pruebas de seguridad

| ID | Prueba | Desde y comando | Resultado esperado | Resultado |
| :--- | :--- | :--- | :--- | :--- |
| S1 | La DMZ no llega a la base de datos | `F-NNC`: `nc -zv -w 3 10.0.20.10 3306` | Sin respuesta (timeout) | Pendiente |
| S2 | La web sí llega a la base de datos | `W-NNC`: `nc -zv -w 3 10.0.20.10 3306` | `succeeded` | Pendiente |
| S3 | La DMZ no llega al SSH de la INTRANET | `F-NNC`: `nc -zv -w 3 10.0.20.10 22` | Sin respuesta (timeout) | Pendiente |
| S4 | El SSH del router solo se abre desde la INTRANET | `F-NNC`: `nc -zv -w 3 10.0.10.1 22` | Sin respuesta (timeout) | Pendiente |
| S5 | FTP anónimo rechazado | `PC-Linux`: `curl ftp://10.0.10.20/` | Error `530` | Pendiente |
| S6 | Usuario fuera de la lista blanca rechazado | `PC-Linux`: `curl -u root:prueba ftp://10.0.10.20/` | Error `530` | Pendiente |
| S7 | El chroot impide salir del home | `PC-Linux`: `curl -u bchecker:bchecker121 ftp://10.0.10.20/etc/` | Error `550`: `/etc` no existe dentro del chroot | Pendiente |
| S8 | Solo están abiertos los puertos previstos | `PC-Linux`: `nmap -Pn 10.0.10.10 10.0.10.20` | `W-NNC`: 22 y 80. `F-NNC`: 21 y 22 | Pendiente |
| S9 | FTPS rechaza TLS 1.0 | `PC-Linux`: ver comando debajo | El handshake falla (ver [riesgos](gestion.md#riesgos-técnicos-y-mejoras)) | Pendiente |

La prueba S8 necesita `nmap` en `PC-Linux` (`sudo apt-get install -y nmap`).

Comando de la prueba S9:

```bash
openssl s_client -connect 10.0.10.20:21 -starttls ftp -tls1 -cipher 'DEFAULT:@SECLEVEL=0'
```

## Pruebas de monitorización

| ID | Prueba | Desde y comando | Resultado esperado | Resultado |
| :--- | :--- | :--- | :--- | :--- |
| M1 | Estado de los servicios | Cada servidor: `systemctl is-active` con sus servicios | Todos `active` | Pendiente |
| M2 | Servicios fallidos | Cada servidor: `systemctl --failed` | `0 loaded units listed.` | Pendiente |
| M3 | Registro de accesos FTP | `F-NNC`: `sudo tail -n 20 /var/log/vsftpd.log` | Aparecen el login y las transferencias de F7 | Pendiente |
| M4 | Registro de accesos web | `W-NNC`: `sudo tail -n 20 /var/log/nginx/access.log` | Aparecen las peticiones de F6 con código `200` | Pendiente |
| M5 | Registro de concesiones DHCP | `B-NNC`: `sudo journalctl -u isc-dhcp-server -n 20` | Aparece `DHCPACK` para los clientes de F4 | Pendiente |
| M6 | Monitor de red ([monitorización](administrador/monitorizacion.md)) | Interfaz de la herramienta | Todos los servidores aparecen y están en verde; al parar un servicio salta la alerta | Pendiente |

## Pruebas de contingencia

| ID | Prueba | Cómo | Resultado esperado | Resultado |
| :--- | :--- | :--- | :--- | :--- |
| C1 | Reinicio de cada servidor | `sudo reboot` y repetir M1 | Todos los servicios vuelven solos en menos de 2 minutos | Pendiente |
| C2 | Caída y recuperación del FTP | `F-NNC`: `sudo systemctl stop vsftpd`, repetir F7, `sudo systemctl start vsftpd`, repetir F7 | F7 falla con el servicio parado y vuelve a funcionar al arrancarlo | Pendiente |
| C3 | Caída del router | Apagar `R-NNC` y probar desde `PC-Linux` | Los nombres locales y MySQL siguen funcionando; la web, el FTP e Internet no | Pendiente |
| C4 | Copia y restauración de la base de datos | `B-NNC`: comandos de debajo | Mismo número de filas en `equipaments` y en `equipaments_restore` | Pendiente |
| C5 | Caída de `B-NNC` | Apagar `B-NNC` y probar desde `PC-Linux` y `W-NNC` | La web se queda sin datos y no se resuelven nombres; los clientes mantienen la IP hasta que caduca la concesión | Pendiente |

Comandos de la prueba C4:

```bash
sudo mysqldump equipaments > /tmp/equipaments.sql
sudo mysql -e "CREATE DATABASE equipaments_restore CHARACTER SET utf8mb4;"
sudo mysql equipaments_restore < /tmp/equipaments.sql
sudo mysql -e "SELECT COUNT(*) FROM equipaments.equipaments; SELECT COUNT(*) FROM equipaments_restore.equipaments;"
sudo mysql -e "DROP DATABASE equipaments_restore;"
```

## Pruebas de automatización

| ID | Prueba | Cómo | Resultado esperado | Resultado |
| :--- | :--- | :--- | :--- | :--- |
| A1 | Despliegue de `B-NNC` con el script | `B-NNC` recién instalado: `sudo bash scripts/b-nnc.sh` | Todas las comprobaciones en `[OK]` | Pendiente |
| A2 | El script se puede repetir | Volver a ejecutar el script y después `grep -c b-nnc /etc/hosts` | Todo en `[OK]` y el `grep` devuelve `1` (sin líneas duplicadas) | Pendiente |

## Pruebas de rendimiento

| ID | Prueba | Desde y comando | Resultado esperado | Resultado |
| :--- | :--- | :--- | :--- | :--- |
| R1 | Tiempo de respuesta de la web | `PC-Linux`: `curl -o /dev/null -s -w '%{http_code} %{time_total}\n' http://sitioitb.cat/` (10 veces) | `200` y menos de 0,2 s | Pendiente |
| R2 | Tiempo de respuesta del DNS | `PC-Linux`: `dig w-nnc.sitioitb.cat @10.0.20.10` | `Query time` menor de 50 ms | Pendiente |
| R3 | Velocidad FTP | `PC-Linux`: comandos de debajo | 10 MB/s o más (10485760 bytes/s) | Pendiente |
| R4 | Consulta a MySQL desde la web | `W-NNC`: `time mysql -h 10.0.20.10 -u bchecker -pbchecker121 equipaments -e "SELECT COUNT(*) FROM equipaments;"` | Menos de 1 s (requiere el cliente `mysql`) | Pendiente |
| R5 | Tiempo hasta obtener IP | `PC-W11`: `ipconfig /release` y `ipconfig /renew` | Menos de 10 s | Pendiente |

Comandos de la prueba R3 (`curl` muestra la velocidad en bytes por segundo):

```bash
dd if=/dev/zero of=prueba100M.bin bs=1M count=100
curl -s -o /dev/null -w '%{speed_upload}\n' -u bchecker:bchecker121 -T prueba100M.bin ftp://10.0.10.20/ftp/
curl -s -o /dev/null -w '%{speed_download}\n' -u bchecker:bchecker121 ftp://10.0.10.20/ftp/prueba100M.bin
```

---

[Volver al índice](../README.md)
