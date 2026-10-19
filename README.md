# Projecte-Zero

## Estructura de Red

*La red se estructura en torno a un router principal (R-NCC) configurado con tres interfaces de red independientes para cumplir con la segmentación requerida: NAT (WAN), DMZ e Intranet.*

![Estructura de la red.](/Imagenes/RedProyecto.png)

## Distribución de Zonas y Servicios

1. **Zona NAT / WAN:** Conecta el router a la red externa/Internet, permitiendo la salida de tráfico mediante traducción de direcciones.
2. **Zona DMZ (Demilitarized Zone):** Aloja los servicios accesibles públicamente o expuestos.
    * **Servidor WEB (W-NNC):** Muestra la interfaz de la aplicación que consulta los datos.
    * **Servidor FTP (F-NNC):** Gestiona la transferencia de archivos.
3. **Servidor de Base de Datos y Servicios (B-NCC):** Ejecuta el motor MySQL con el conjunto de datos de equipamiento educativo de Barcelona, además del servidor DNS y DHCP.
4. **Clientes:** Dos ordenadores locales (uno con SO Windows y otro con SO Linux).
