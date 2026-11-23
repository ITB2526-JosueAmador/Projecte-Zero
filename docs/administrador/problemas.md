# Resolución de problemas

## Problemas encontrados durante el despliegue

**`systemctl enable bind9` falla.**
Error: `Refusing to operate on alias name or linked unit file: bind9.service`.
Causa: en Ubuntu 22.04 `bind9` es un alias del servicio `named`.
Solución: usar `systemctl enable --now named` (y `restart named`).

**El CSV da error de codificación.**
Error de Python: `UnicodeDecodeError: 'utf-8' codec can't decode byte 0xff`.
Causa: el CSV de Open Data BCN viene en UTF-16.
Solución: convertirlo con `iconv -f UTF-16 -t UTF-8` antes de cargarlo.

**Sin Internet después de poner la IP estática.**
Síntomas: `apt-get update` da `Network is unreachable` y los repositorios no tienen Release file.
Causa: con la IP estática `10.0.20.10` la salida depende del router (`10.0.20.1`), que tiene que reenviar paquetes y hacer NAT.
Solución: comprobar con ping a `10.0.20.1`, `8.8.8.8` y `google.com`. En el router tiene que estar `net.ipv4.ip_forward=1` y la regla `masquerade` de nftables en la interfaz WAN, además de la regla de forward de la INTRANET hacia la WAN.

**Pegar la clave pública en la terminal.**
La línea `ssh-ed25519 AAAA...` no es un comando. Se pega en GitHub (Deploy keys), no en la terminal, porque ahí da `command not found`.

## Errores habituales

| Síntoma | Causa probable | Solución |
| :--- | :--- | :--- |
| FTP: `500 OOPS: vsftpd: refusing to run with writable root inside chroot()` | El home del usuario es escribible y está activo el chroot | Mantener `allow_writeable_chroot=YES` o quitar el permiso de escritura de la raíz del home |
| FTP: el login funciona pero el listado se queda colgado o da `425` | No llegan las conexiones de datos en modo pasivo | Comprobar que el rango `40000-40100` está permitido en `R-NNC` y que el cliente usa modo pasivo |
| FTP: `530 Permission denied` | El usuario no está en `/etc/vsftpd.userlist` | Añadirlo con `tee -a` y reiniciar `vsftpd` |
| FTP: `530 Login incorrect` | Contraseña incorrecta o shell que no está en `/etc/shells` | Cambiar la contraseña con `sudo passwd` o revisar `pam_shells` (ver [alta de usuarios](ftp.md#alta-de-nuevos-usuarios-ftp)) |
| FTP: no responde a la INTRANET pero sí al router | Falta la ruta a `10.0.20.0/24` y la respuesta sale por `enp1s0` | Comprobar `ip r` y el fichero de Netplan, y aplicar con `sudo netplan apply` |
| DNS: un cambio en la zona no se ve | No se ha recargado `named` o el cliente tiene la respuesta en caché | `sudo rndc reload`, comprobar con `dig @10.0.20.10` y, en Windows, `ipconfig /flushdns` |
| DHCP: el cliente se queda con una IP `169.254.x.x` | No llega al servidor DHCP | `systemctl status isc-dhcp-server`, revisar `INTERFACESv4` y `sudo journalctl -u isc-dhcp-server` |
| Router: tras reiniciar, la INTRANET no sale a Internet | Reglas no cargadas o reenvío desactivado | `systemctl is-enabled nftables`, `sudo nft list ruleset` y `sysctl net.ipv4.ip_forward` |
| MySQL: `W-NNC` no conecta a `10.0.20.10` | `bind-address`, regla del router o usuario sin permiso desde esa IP | Comprobar que MySQL escucha en el `3306` (`ss -tln`), la regla `3306` en `R-NNC` y `SELECT user, host FROM mysql.user;` |

---

[Volver al índice](../../README.md)
