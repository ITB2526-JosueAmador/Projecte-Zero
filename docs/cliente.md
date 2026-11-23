# Documentación de cliente

Guía para las personas que usan los equipos de la INTRANET. No hacen falta conocimientos técnicos.

## Servicios disponibles

| Servicio | Dirección | Para qué sirve |
| :--- | :--- | :--- |
| Web de equipamientos educativos | `http://sitioitb.cat` | Consultar los equipamientos educativos de Barcelona |
| FTP | `ftp.sitioitb.cat` (puerto 21) | Subir y descargar ficheros con el usuario y la contraseña que da el administrador |
| Red | Automática | El equipo recibe su configuración al conectarse |

## Conectar el equipo a la red

Basta con conectar el equipo a la red de la INTRANET: la dirección IP, la puerta de enlace y el DNS se configuran solos.

Para comprobar que el equipo tiene red:

- **Windows 11:** abrir el Símbolo del sistema, escribir `ipconfig` y comprobar que la "Dirección IPv4" está entre `10.0.20.100` y `10.0.20.200`.
- **Linux:** abrir una terminal, escribir `ip -br a` y comprobar lo mismo.

## Usar la web

Abrir el navegador y escribir la dirección completa `http://sitioitb.cat`. La web muestra los equipamientos educativos de Barcelona guardados en la base de datos.

## Instalar el cliente FTP

Los navegadores actuales ya no abren direcciones `ftp://`, así que hace falta un programa cliente. Se recomienda FileZilla, que es gratuito y funciona en Windows y Linux.

- **Windows 11:** descargar FileZilla Client desde la web oficial, [filezilla-project.org](https://filezilla-project.org/), e instalarlo. Durante la instalación, rechazar cualquier programa adicional que se ofrezca.
- **Linux (Ubuntu o Debian):** instalarlo desde los repositorios oficiales con `sudo apt-get install -y filezilla`.

## Usar el FTP

1. Abrir FileZilla y entrar en el menú **Archivo**, opción **Gestor de sitios**. Pulsar **Nuevo sitio**.
2. Rellenar los datos de la conexión:

   | Campo | Valor |
   | :--- | :--- |
   | Protocolo | FTP |
   | Servidor | `ftp.sitioitb.cat` |
   | Puerto | `21` |
   | Cifrado | FTP explícito sobre TLS |
   | Modo de acceso | Normal |
   | Usuario y contraseña | Los que facilita el administrador |

3. Pulsar **Conectar**.
4. La primera vez aparece un aviso de **certificado desconocido**. Es normal: el servidor usa un certificado propio. Comparar la huella SHA-256 que muestra FileZilla con la que da el administrador; si coincide, marcar la casilla para confiar siempre en el certificado y aceptar.
5. A la izquierda se ven los ficheros del equipo y a la derecha los del servidor. Para subir o bajar un fichero, arrastrarlo de un lado a otro. Los ficheros se guardan en la carpeta `ftp`.

FileZilla usa el modo de transferencia **pasivo** por defecto, que es el único que funciona con este servidor.

**Desde la terminal de Linux** (opcional), con `curl`. Al no poner la contraseña en el comando, `curl` la pide al ejecutarlo:

```bash
# Ver el contenido de la carpeta ftp
curl --ssl-reqd -k -u usuario ftp://ftp.sitioitb.cat/ftp/

# Subir un fichero
curl --ssl-reqd -k -u usuario -T fichero.txt ftp://ftp.sitioitb.cat/ftp/

# Descargar un fichero
curl --ssl-reqd -k -u usuario -O ftp://ftp.sitioitb.cat/ftp/benvinguda.txt
```

## Buenas prácticas

- No guardar la contraseña en equipos compartidos. Si se guarda, FileZilla permite protegerla con una contraseña maestra.
- Mantener FileZilla actualizado. En Windows avisa él mismo; en Linux se actualiza con `sudo apt-get upgrade`.
- El FTP sirve para intercambiar ficheros, no es una copia de seguridad: conviene guardar siempre una copia propia.
- Si la huella del certificado cambia sin que el administrador lo haya avisado, no aceptarlo y comunicarlo.
- Las altas, bajas y cambios de contraseña se piden al administrador.

## Si algo no funciona

| Síntoma | Causa probable | Qué hacer |
| :--- | :--- | :--- |
| El equipo no tiene IP o tiene una que empieza por `169.254` | No recibe la configuración de la red | Comprobar el cable o la conexión de red y renovar la IP: en Windows, `ipconfig /release` y después `ipconfig /renew`; en Linux, desconectar y volver a conectar la red. Si sigue igual, avisar al administrador |
| La web no carga con el nombre pero sí con `http://10.0.10.10` | Falla la resolución de nombres (DNS) | En Windows, ejecutar `ipconfig /flushdns` y volver a probar. Si sigue igual, avisar al administrador |
| La web no carga de ninguna forma | El servidor web o el router no están disponibles | Avisar al administrador |
| FileZilla muestra `530 Login incorrect` o `530 Permission denied` | Usuario o contraseña incorrectos, o el usuario no tiene permiso de FTP | Revisar el usuario, las mayúsculas y la contraseña. Si sigue igual, pedir el alta al administrador |
| FileZilla avisa de un certificado desconocido | El certificado del servidor es propio (autofirmado) | Comprobar la huella con el administrador y aceptarlo |
| FileZilla conecta pero no muestra las carpetas | Se bloquea la conexión de datos | Comprobar que el modo de transferencia es pasivo. Si sigue igual, avisar al administrador |
| No se puede subir un fichero (`553 Could not create file`) | No hay permiso de escritura en esa carpeta | Subir el fichero dentro de la carpeta `ftp` |

---

[Volver al índice](../README.md)
