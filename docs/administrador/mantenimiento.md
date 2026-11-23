# Mantenimiento

| Tarea | Equipo | Cómo | Cuándo |
| :--- | :--- | :--- | :--- |
| Actualizar paquetes | Todos | `sudo apt-get update` y `sudo apt-get upgrade` | Cada semana |
| Revisar servicios caídos | Todos | `systemctl --failed` | Cada día y después de cada cambio |
| Revisar registros web | `W-NNC` | `/var/log/nginx/access.log` y `/var/log/nginx/error.log` | Cada semana o ante una incidencia |
| Revisar registros FTP | `F-NNC` | `/var/log/vsftpd.log` | Cada semana o ante una incidencia |
| Revisar registros DNS y DHCP | `B-NNC` | `sudo journalctl -u named` y `sudo journalctl -u isc-dhcp-server` | Ante una incidencia |
| Revisar registros MySQL | `B-NNC` | `/var/log/mysql/error.log` | Ante una incidencia |
| Copia de la base de datos | `B-NNC` | `sudo mysqldump equipaments > equipaments_$(date +%F).sql` | Antes de cada cambio y cada semana |
| Añadir o cambiar un registro DNS | `B-NNC` | Editar la zona, subir el número de serie del SOA, `sudo named-checkzone sitioitb.cat /etc/bind/db.sitioitb.cat` y `sudo rndc reload` | Cuando haga falta |
| Alta de un usuario FTP | `F-NNC` | Ver [alta de nuevos usuarios FTP](ftp.md#alta-de-nuevos-usuarios-ftp) | Cuando lo pida un cliente |
| Regenerar el certificado FTPS | `F-NNC` | `sudo make-ssl-cert generate-default-snakeoil --force-overwrite` y `sudo systemctl restart vsftpd`; después, dar la nueva huella a los clientes | Si cambia el hostname o caduca el certificado |
| Cambiar el cortafuegos | `R-NNC` | Editar `/etc/nftables.conf`, `sudo nft -c -f /etc/nftables.conf` y `sudo systemctl restart nftables` | Cuando haga falta |
| Guardar los cambios de configuración | - | [Flujo de subida a producción](git.md#flujo-de-subida-a-producción) | Después de cada cambio |

---

[Volver al índice](../../README.md)
