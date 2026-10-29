# Projecte-Zero

## Estructura de Red

*La red se estructura en torno a un router principal (R-NNC) configurado con tres interfaces de red independientes para cumplir con la segmentación requerida: NAT (WAN), DMZ e Intranet.*

![Estructura de la red.](Imagenes/RedProyecto.png)

# Justificación Técnica de la Estructura y Componentes de Red

A continuación se detalla y justifica la arquitectura de red diseñada, explicando el rol de cada subred, interfaz y servidor en la topología.

---

## 1. Router / Puerta de Enlace (`R-NNC`)

* **Función y Rol:** Actúa como el elemento central de encaminamiento y frontera de seguridad entre la red exterior (Internet) y las redes privadas de la organización.
* **Interfaces y Justificación:**
  * **`Eth 0` (IP WAN / INTERNET):** Conecta hacia la red pública/Internet mediante traducción de direcciones (NAT). Protege la topología privada de ataques externos directos y gestiona el enrutamiento hacia el exterior.
  * **`Eth 1` (`10.0.10.1/24`):** Puerta de enlace (*Gateway*) para la subred asignada a la **Zona DMZ** (`10.0.10.0/24`).
  * **`Eth 2` (`10.0.20.1/24`):** Puerta de enlace (*Gateway*) para la subred de la **INTRANET / Red Interna** (`10.0.20.0/24`).
* **Seguridad (Aislamiento de Zonas):** Separa el tráfico público del privado. Permite aplicar reglas de cortafuegos (*firewall*) para restringir el acceso desde la DMZ hacia la INTRANET, garantizando que un compromiso en los servidores públicos no comprometa la red privada.

---

## 2. Zona DMZ (Demilitarized Zone) — Subred `10.0.10.0/24`

Aísla los servidores que deben ser accesibles o estar expuestos a peticiones externas o de los clientes, minimizando el impacto de potenciales vulnerabilidades.

### A. Servidor WEB (`W-NNC`)
* **Dirección IP:** `10.0.10.10/24`
* **Servicios:** `WEB SRV` / `SSH`
* **Justificación:**
  * **Servicio Web:** Muestra la interfaz gráfica/aplicación pública que consulta el conjunto de datos de equipamiento educativo de Barcelona.
  * **Acceso SSH:** Permite la administración remota segura de la máquina mediante consola sin exponer puertos innecesarios.
  * **Ubicación en DMZ:** Al alojar contenido público, debe estar separado de la base de datos crítica y de los clientes para aislar amenazas web (ej. ataques HTTP/HTTPS).

### B. Servidor FTP (`F-NNC`)
* **Dirección IP:** `10.0.10.20/24`
* **Servicios:** `FTP SRV` / `SSH`
* **Justificación:**
  * **Servicio FTP:** Permite la subida, descarga y transferencia gestionada de archivos de datos o recursos.
  * **Acceso SSH:** Utilizado para la gestión y mantenimiento técnico seguro del servidor.
  * **Ubicación en DMZ:** El protocolo FTP expone servicios de archivos que deben mantenerse al margen de la INTRANET para evitar la propagación de software malicioso o accesos no autorizados a la red local.

---

## 3. Zona INTRANET (Red Privada Interna) — Subred `10.0.20.0/24`

Es el núcleo privado de la organización. Aloja los servicios críticos de infraestructura interna y los equipos de los usuarios finales.

### A. Servidor de Base de Datos y Servicios Centrales (`B-NNC`)
* **Dirección IP:** `10.0.20.10/24`
* **Servicios:** `DNS` / `DHCP` / `BBDD`
* **Justificación:**
  * **Base de Datos (BBDD):** Ejecuta el motor MySQL conteniendo la información sensible sobre equipamiento educativo. Al estar en la INTRANET, queda protegida contra accesos directos desde Internet o desde la DMZ (solo el servidor Web realizará consultas autorizadas).
  * **Servicio DHCP:** Asigna automáticamente direcciones IP, máscaras de red y puertas de enlace a las máquinas clientes de la INTRANET.
  * **Servicio DNS:** Resuelve los nombres de dominio locales para la infraestructura interna y permite el reenvío de consultas externas.

### B. Cliente Linux
* **Configuración de Red:** Dinámica mediante `DHCP`
* **Justificación:** Representa un puesto de trabajo con sistema operativo Linux en la red privada. Recibe su configuración (IP en el rango `10.0.20.X`) automáticamente desde `B-NNC` para navegar, realizar consultas a los servicios corporativos y administrar los servidores mediante SSH.

### C. Cliente Windows 11 (`W11`)
* **Configuración de Red:** Dinámica mediante `DHCP`
* **Justificación:** Representa un equipo cliente con entorno Windows 11 dentro de la red corporativa. Facilita verificar la compatibilidad multiplataforma de la infraestructura (DHCP, resolución DNS y consumo del servicio web).

---

## 4. Resumen de la Estructura Segregada

| Zona | Subred / Rango | Propósito Principal | Máquinas / Funciones |
| :--- | :--- | :--- | :--- |
| **WAN / NAT** | Asignada por Proveedor | Conexión e interacción hacia Internet. | Interface `Eth 0` del Router `R-NNC` |
| **DMZ** | `10.0.10.0/24` | Exposición de servicios públicos hacia el exterior. | `W-NNC` (`10.0.10.10`) y `F-NNC` (`10.0.10.20`) |
| **INTRANET** | `10.0.20.0/24` | Servicios de infraestructura críticos y puestos de trabajo. | `B-NNC` (`10.0.20.10`), **Cliente Linux** y **Cliente W11** |

### Distribución de Zonas y Servicios

1. **Zona NAT / WAN:** Conecta el router a la red externa/Internet, permitiendo la salida de tráfico mediante traducción de direcciones.
2. **Zona DMZ (Demilitarized Zone):** Aloja los servicios accesibles públicamente o expuestos.
    * **Servidor WEB (W-NNC):** Muestra la interfaz de la aplicación que consulta los datos.
    * **Servidor FTP (F-NNC):** Gestiona la transferencia de archivos.
3. **Servidor de Base de Datos y Servicios (B-NNC):** Ejecuta el motor MySQL con el conjunto de datos de equipamiento educativo de Barcelona, además del servidor DNS y DHCP.
4. **Clientes:** Dos ordenadores locales (uno con SO Windows y otro con SO Linux).

### Tabla de Direccionamiento IP y Configuración

| Hostname  | Zona de Red         | Dirección IP/Subred                                         | Servicios y Funciones       | Notas y Credenciales                                          |
| --------- |---------------------|-------------------------------------------------------------|-----------------------------|:-------------------------------------------------------------:|
| R-NNC     | WAN/DMZ/INTRANET    | WAN: IP por DHCP, DMZ: 10.0.10.1/24, INTRANET: 10.0.20.1/24 | Router / Enrutamiento / NAT | DNS resuelve R-NNC y *-NNC a este equipo.                     |
| W-NNC     | DMZ                 | 10.0.10.10/24                                               | Servidor Web, SSH           | Usuario: bchecker / Pw: bchecker121                           |
| F-NNC     | DMZ                 | 10.0.10.20/24                                               | Servidor FTP, SSH           | Usuario: bchecker / Pw: bchecker121                           |
| B-NNC     | INTRANET            | 10.0.20.10/24                                               | MySQL, DNS, DHCP, SSH       | Importa el CSV de datos. Usuario: bchecker / Pw: bchecker121  |
| PC-W11    | INTRANET            | Dinámica (DHCP)                                             | Cliente Windows             | Configuración IP por DHCP.                                    |
| PC-Linux  | INTRANET            | Dinámica (DHCP)                                             | Cliente Linux               | Configuración IP por DHCP.                                    |

### Flujo de Interconexión

1. **Resolución de Nombres:** El servidor DNS en B-NNC resuelve los nombres R-NNC y R hacia la IP del router.
2. **Conexión Web - BBDD:** La aplicación web en W-NNC (10.0.10.10) realiza peticiones al motor MySQL en B-NNC (10.0.20.10) cruzando el router a través del puerto 3306.
3. **Gestión y Acceso Administrado:** Todos los servidores y máquinas disponen de cuenta SSH configurada con la credencial bchecker / bchecker121 para tareas de despliegue y administración.

### Firewalls y conexiones

Matriz de conectividad entre zonas (origen en filas, destino en columnas). La política por defecto del router es **denegar** todo lo que no esté permitido explícitamente, y se acepta siempre el tráfico de respuesta de conexiones ya establecidas (`ESTABLISHED, RELATED`).

| Origen \ Destino | Internet                  | DMZ                                                              | LAN                                           |
| ---------------- |---------------------------|------------------------------------------------------------------|:---------------------------------------------:|
| **Internet**     | -                         | Sí, solo servicios públicos (HTTP a `W-NNC`, FTP a `F-NNC`)      | No                                            |
| **DMZ**          | Sí                        | -                                                                | Solo `W-NNC` → `B-NNC` (3306/tcp)             |
| **LAN**          | Sí (con NAT)              | Sí (web, FTP y administración por SSH)                           | -                                             |

**Justificación de las reglas:**

* **Internet → DMZ (restringido):** Solo se exponen los puertos de los servicios públicos. El resto de puertos de la DMZ, incluido SSH, no son accesibles desde el exterior.
* **Internet → LAN (No):** La Intranet no es accesible desde fuera. Además, al estar detrás de NAT, no tiene direccionamiento público.
* **DMZ → LAN (solo `W-NNC` → `B-NNC` por 3306/tcp):** Es la única excepción necesaria, porque la aplicación web consulta la base de datos MySQL. Cualquier otro intento de la DMZ hacia la Intranet queda bloqueado, de modo que un servidor de la DMZ comprometido no puede moverse libremente por la red interna.
* **LAN → Internet (Sí, con NAT):** Los clientes necesitan salida a Internet y `B-NNC` necesita reenviar consultas DNS externas.
* **LAN → DMZ (Sí):** Los clientes consumen el servicio web y FTP, y los administradores gestionan los servidores de la DMZ por SSH desde la Intranet.
* **DMZ → Internet (Sí):** Permite a los servidores de la DMZ descargar actualizaciones y paquetes.


## Configuración Web Server

![Gif Web Server.](Imagenes/webserver.gif)

__Configuración de Nginx__

```
events {
    worker_connections 1024;
}

http {
    server {
        listen 80;
        listen [::]:80;

        server_name sitioitb.cat;
        root /var/www/html/;
        index web.html web.htm;
        
        

        location / {
        try_files $uri $uri/ =404;
        }
    }
}

```

## Configuración FTP Server

![Gif FTP Server.](Imagenes/ftpserver.gif)

## Configuración DDBB, DNS y DHCP Server (Todo junto en el mismo sistema)

![Gif DDBB, DNS y DHCP Server.](Imagenes/db_dns_dhcp_server.gif)
