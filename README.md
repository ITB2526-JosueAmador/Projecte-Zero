# Projecte-Zero

Proyecto **P0.0 – Desplegament de la infraestructura** del grupo ASIXc2C-G05: despliegue de una infraestructura de red multicapa (WAN, DMZ e INTRANET) para el dominio `sitioitb.cat`, con servicios web, FTP, base de datos, DNS y DHCP.

![Estructura de la red.](Imagenes/RedProyecto.png)

> **Pendiente:** el enunciado pide que el repositorio se llame `P0.0-ASIXc2gC-Gnn` (`g` = grupo A o B, `nn` = número de grupo). Hay que renombrarlo en GitHub (Settings, General, Repository name), corregir el nombre del grupo en este párrafo y actualizar la URL del `git clone` en [Control de versiones](docs/administrador/git.md).

> Las credenciales que aparecen en este documento (`bchecker` / `bchecker121`) son las que fija el enunciado y se usan **solo en el entorno de laboratorio**.

## Cómo leer esta documentación

La documentación está separada según quién la va a leer:

| Perfil | Qué encontrará | Sección |
| :--- | :--- | :--- |
| Técnico / evaluador | Objetivos, análisis de necesidades, elección de tecnologías, arquitectura, seguridad y criterios de rendimiento y disponibilidad | [Documentación técnica](docs/tecnica/README.md) |
| Administrador de sistemas | Instalación y configuración de cada equipo, dependencias, mantenimiento y resolución de problemas | [Documentación de administrador](docs/administrador/README.md) |
| Cliente (usuario final) | Cómo conectarse a la red, usar la web y el FTP, y qué hacer si algo falla | [Documentación de cliente](docs/cliente.md) |
| Equipo y profesorado | Juego de pruebas, planificación, recursos, control de tareas y riesgos | [Plan de pruebas](docs/pruebas.md) y [Gestión del proyecto](docs/gestion.md) |

## Índice

- [Documentación técnica](docs/tecnica/README.md)
  - [Objetivos y destinatarios](docs/tecnica/objetivos.md)
  - [Análisis de necesidades](docs/tecnica/analisis-necesidades.md)
  - [Estudio de tecnologías](docs/tecnica/tecnologias.md)
  - [Arquitectura de red](docs/tecnica/arquitectura-red.md)
  - [Direccionamiento IP](docs/tecnica/direccionamiento.md)
  - [Firewall y conexiones](docs/tecnica/firewall.md)
  - [Medidas de seguridad](docs/tecnica/seguridad.md)
  - [Criterios de rendimiento y disponibilidad](docs/tecnica/rendimiento-disponibilidad.md)
- [Documentación de administrador](docs/administrador/README.md)
  - [Orden de despliegue y dependencias](docs/administrador/despliegue.md)
  - [Router R-NNC](docs/administrador/router.md)
  - [Servidor web W-NNC](docs/administrador/web.md)
  - [Servidor FTP F-NNC](docs/administrador/ftp.md)
  - [Servidor B-NNC (BBDD, DNS, DHCP y SSH)](docs/administrador/b-nnc.md)
  - [Clientes de la INTRANET](docs/administrador/clientes.md)
  - [Monitorización de red](docs/administrador/monitorizacion.md)
  - [Control de versiones con Git y GitHub](docs/administrador/git.md)
  - [Mantenimiento](docs/administrador/mantenimiento.md)
  - [Resolución de problemas](docs/administrador/problemas.md)
- [Documentación de cliente](docs/cliente.md)
  - [Servicios disponibles](docs/cliente.md#servicios-disponibles)
  - [Conectar el equipo a la red](docs/cliente.md#conectar-el-equipo-a-la-red)
  - [Usar la web](docs/cliente.md#usar-la-web)
  - [Instalar el cliente FTP](docs/cliente.md#instalar-el-cliente-ftp)
  - [Usar el FTP](docs/cliente.md#usar-el-ftp)
  - [Buenas prácticas](docs/cliente.md#buenas-prácticas)
  - [Si algo no funciona](docs/cliente.md#si-algo-no-funciona)
- [Plan de pruebas](docs/pruebas.md)
- [Gestión del proyecto](docs/gestion.md)
- [Fuentes y documentación de referencia](docs/fuentes.md)
- [Estructura del repositorio](#estructura-del-repositorio)

## Estructura del repositorio

```
projecte-zero/
├── README.md                      portada e índice de la documentación
├── Imagenes/                      esquema de red y gifs de los servicios
│   ├── RedProyecto.png
│   ├── webserver.gif
│   ├── ftpserver.gif
│   └── db_dns_dhcp_server.gif
├── docs/
│   ├── tecnica/                   documentación técnica
│   │   ├── README.md
│   │   ├── objetivos.md
│   │   ├── analisis-necesidades.md
│   │   ├── tecnologias.md
│   │   ├── arquitectura-red.md
│   │   ├── direccionamiento.md
│   │   ├── firewall.md
│   │   ├── seguridad.md
│   │   └── rendimiento-disponibilidad.md
│   ├── administrador/             documentación de administrador
│   │   ├── README.md
│   │   ├── despliegue.md
│   │   ├── router.md
│   │   ├── web.md
│   │   ├── ftp.md
│   │   ├── b-nnc.md
│   │   ├── clientes.md
│   │   ├── monitorizacion.md
│   │   ├── git.md
│   │   ├── mantenimiento.md
│   │   └── problemas.md
│   ├── cliente.md                 documentación de cliente
│   ├── pruebas.md                 plan de pruebas
│   ├── gestion.md                 planificación, ProofHub, recursos y riesgos
│   └── fuentes.md                 fuentes y documentación de referencia
└── scripts/
    └── b-nnc.sh                   configuración automática de B-NNC
```

> **Pendiente:** subir la carpeta `scripts/` con `b-nnc.sh`; todavía no está en el repositorio.
