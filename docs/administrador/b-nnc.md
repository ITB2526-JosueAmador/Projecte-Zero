# Servidor B-NNC (BBDD, DNS, DHCP y SSH)

![Gif DDBB, DNS y DHCP Server.](../../Imagenes/db_dns_dhcp_server.gif)

`B-NNC` es una máquina Ubuntu Server 22.04 (jammy) en la INTRANET, con MySQL, DNS, DHCP y SSH en el mismo sistema. Su interfaz `enp3s0` va conectada a la interfaz de la INTRANET del router (`enp4s0`, `10.0.20.1`).

El enunciado usa `B-NNC` y `B-NCC` indistintamente, por eso en `/etc/hosts` y en el DNS están los dos nombres.

## Script automático

Todo lo de esta sección está recogido en un script que se ejecuta con `sudo bash b-nnc.sh` (está en `scripts/b-nnc.sh`). Instala los paquetes, configura los servicios, descarga y carga el CSV, deja la IP fija y al final hace una batería de comprobaciones con `[OK]` / `[FAIL]`. Se puede ejecutar varias veces sin problema. Abajo está explicado paso a paso lo que hace.

> **Pendiente:** la carpeta `scripts/` todavía no está en el repositorio. Hay que subir `b-nnc.sh` antes de la entrega.

## Sistema base y usuario de B-NNC

```bash
sudo hostnamectl set-hostname b-nnc
echo "127.0.1.1 b-nnc.sitioitb.cat b-nnc b-ncc" | sudo tee -a /etc/hosts

sudo useradd -m -s /bin/bash bchecker
echo "bchecker:bchecker121" | sudo chpasswd
sudo usermod -aG sudo bchecker
```

Paquetes (los servicios ya venían instalados, solo había que configurarlos):

```bash
sudo apt-get update
sudo apt-get install -y openssh-server bind9 bind9utils dnsutils isc-dhcp-server mysql-server git curl python3
```

## Red estática

Fichero `/etc/netplan/01-b-nnc.yaml`:

```yaml
network:
  version: 2
  ethernets:
    enp3s0:
      dhcp4: no
      addresses: [10.0.20.10/24]
      routes:
        - to: default
          via: 10.0.20.1
      nameservers:
        addresses: [127.0.0.1]
        search: [sitioitb.cat]
```

```bash
sudo chmod 600 /etc/netplan/01-b-nnc.yaml
sudo netplan apply
ip -br a show enp3s0
ping -c2 10.0.20.1
```

Mientras tenía la IP por DHCP salía a Internet para instalar paquetes. Al poner la IP fija, la salida depende del router (ver [problemas encontrados](problemas.md#problemas-encontrados-durante-el-despliegue)).

## Acceso SSH a B-NNC

```bash
sudo systemctl enable --now ssh
systemctl status ssh
```

Se entra con `bchecker`. Para GitHub se usa otra clave distinta, explicada en [Control de versiones](git.md).

## DNS (bind9)

En Ubuntu 22.04 el servicio se llama `named` (`bind9` es solo un alias). El requisito del enunciado es que resuelva `R-NCC` y `R` hacia el router.

`/etc/bind/named.conf.options`:

```
options {
    directory "/var/cache/bind";
    listen-on { 127.0.0.1; 10.0.20.10; };
    allow-query { 127.0.0.1; 10.0.10.0/24; 10.0.20.0/24; };
    recursion yes;
    allow-recursion { 127.0.0.1; 10.0.10.0/24; 10.0.20.0/24; };
    forwarders { 8.8.8.8; 1.1.1.1; };
    dnssec-validation no;
};
```

`/etc/bind/named.conf.local`:

```
zone "sitioitb.cat" { type master; file "/etc/bind/db.sitioitb.cat"; };
zone "20.0.10.in-addr.arpa" { type master; file "/etc/bind/db.10.0.20"; };
zone "10.0.10.in-addr.arpa" { type master; file "/etc/bind/db.10.0.10"; };
```

Zona directa `/etc/bind/db.sitioitb.cat`:

```
$TTL 86400
@      IN SOA b-nnc.sitioitb.cat. admin.sitioitb.cat. ( 1 3600 900 604800 86400 )
@      IN NS  b-nnc.sitioitb.cat.
@      IN A   10.0.10.10
r      IN A   10.0.20.1
r-nnc  IN A   10.0.20.1
r-ncc  IN A   10.0.20.1
b-nnc  IN A   10.0.20.10
b-ncc  IN A   10.0.20.10
w-nnc  IN A   10.0.10.10
w-ncc  IN A   10.0.10.10
f-nnc  IN A   10.0.10.20
f-ncc  IN A   10.0.10.20
www    IN CNAME w-nnc
ftp    IN CNAME f-nnc
```

Las zonas inversas (`db.10.0.20` y `db.10.0.10`) tienen los registros PTR de `r-nnc`, `b-nnc`, `w-nnc` y `f-nnc`.

Un comodín tipo `*-nnc` no vale en DNS (el asterisco solo funciona como etiqueta completa), por eso los hosts están puestos uno a uno.

```bash
sudo named-checkconf
sudo named-checkzone sitioitb.cat /etc/bind/db.sitioitb.cat
sudo systemctl enable --now named
sudo systemctl restart named
```

## DHCP (isc-dhcp-server)

`/etc/default/isc-dhcp-server`:

```
INTERFACESv4="enp3s0"
```

`/etc/dhcp/dhcpd.conf`:

```
authoritative;
default-lease-time 600;
max-lease-time 7200;

subnet 10.0.20.0 netmask 255.255.255.0 {
    range 10.0.20.100 10.0.20.200;
    option routers 10.0.20.1;
    option domain-name-servers 10.0.20.10;
    option domain-name "sitioitb.cat";
    option broadcast-address 10.0.20.255;
}
```

```bash
sudo dhcpd -t -cf /etc/dhcp/dhcpd.conf
sudo systemctl enable --now isc-dhcp-server
```

Los clientes reciben IP entre `10.0.20.100` y `10.0.20.200`, puerta de enlace `10.0.20.1` y DNS `10.0.20.10`.

## MySQL y carga del CSV

MySQL 8. Para que la web de `W-NNC` pueda conectar, MySQL escucha en todas las interfaces. En `/etc/mysql/mysql.conf.d/mysqld.cnf`:

```ini
bind-address = 0.0.0.0
```

```bash
sudo systemctl enable --now mysql
sudo systemctl restart mysql
```

Base de datos y usuario (acceso desde local, DMZ e INTRANET):

```sql
SET GLOBAL local_infile = 1;
CREATE DATABASE equipaments CHARACTER SET utf8mb4;
CREATE USER 'bchecker'@'localhost' IDENTIFIED BY 'bchecker121';
CREATE USER 'bchecker'@'10.0.10.%' IDENTIFIED BY 'bchecker121';
CREATE USER 'bchecker'@'10.0.20.%' IDENTIFIED BY 'bchecker121';
GRANT ALL PRIVILEGES ON equipaments.* TO 'bchecker'@'localhost';
GRANT ALL PRIVILEGES ON equipaments.* TO 'bchecker'@'10.0.10.%';
GRANT ALL PRIVILEGES ON equipaments.* TO 'bchecker'@'10.0.20.%';
FLUSH PRIVILEGES;
```

CSV: listado de equipamientos de educación de la ciudad de Barcelona (Open Data BCN).

```bash
curl -L -o /tmp/equip.csv "https://opendata-ajuntament.barcelona.cat/data/dataset/f36b60f2-9541-4d08-b0f9-b0a9313fab3d/resource/29d9ff10-6892-4f16-9012-d5c4997857e7/download"
```

El fichero viene en UTF-16, así que antes de cargarlo hay que pasarlo a UTF-8 y limpiarlo:

```bash
iconv -f UTF-16 -t UTF-8 /tmp/equip.csv -o /tmp/equip_utf8.csv
mv /tmp/equip_utf8.csv /tmp/equip.csv
sed -i '1s/^\xEF\xBB\xBF//; s/\r$//' /tmp/equip.csv
```

La cabecera es `register_id,name,institution_id,institution_name,created,modified,addresses_roadtype_id,...`. Como el CSV tiene muchas columnas, un pequeño script de Python lee la cabecera, crea la tabla con todas las columnas como `TEXT` y genera el `LOAD DATA`:

```bash
python3 - <<'PY'
import csv, re
f = open('/tmp/equip.csv', encoding='utf-8')
hdr = next(csv.reader(f))
cols = []
for h in hdr:
    c = re.sub(r'[^a-z0-9]+', '_', h.strip().lower()).strip('_') or 'col'
    while c in cols:
        c += '_2'
    cols.append(c)
defs = ", ".join("`%s` TEXT" % c for c in cols)
names = ",".join("`%s`" % c for c in cols)
sql = "DROP TABLE IF EXISTS equipaments;\n"
sql += "CREATE TABLE equipaments (id INT AUTO_INCREMENT PRIMARY KEY, %s) CHARACTER SET utf8mb4;\n" % defs
sql += "LOAD DATA LOCAL INFILE '/tmp/equip.csv' INTO TABLE equipaments CHARACTER SET utf8mb4 "
sql += "FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '\"' LINES TERMINATED BY '\\n' IGNORE 1 ROWS (%s);\n" % names
open('/tmp/load.sql', 'w').write(sql)
PY

mysql --local-infile=1 equipaments < /tmp/load.sql
```

## Comprobación de B-NNC

Servicios y puertos:

```bash
systemctl is-active ssh named isc-dhcp-server mysql
ss -tln | grep -E ':22 |:3306 '
ss -uln | grep -E ':53 |:67 '
```

Red:

```bash
ip -br a show enp3s0      # 10.0.20.10/24
ping -c2 10.0.20.1        # router
ping -c2 8.8.8.8          # salida a Internet
ping -c2 google.com       # DNS recursivo
```

DNS:

```bash
dig +short r.sitioitb.cat @10.0.20.10        # 10.0.20.1
dig +short r-ncc.sitioitb.cat @10.0.20.10    # 10.0.20.1
dig +short w-nnc.sitioitb.cat @10.0.20.10    # 10.0.10.10
dig +short -x 10.0.20.1 @10.0.20.10          # r-nnc.sitioitb.cat.
```

DHCP: arrancar `PC-Linux` y `PC-W11` en la INTRANET y comprobar que reciben IP entre `10.0.20.100` y `10.0.20.200`, puerta de enlace `10.0.20.1` y DNS `10.0.20.10` (ver [Clientes de la INTRANET](clientes.md)). Las concesiones se ven en `/var/lib/dhcp/dhcpd.leases`.

MySQL:

```bash
mysql -e "SELECT COUNT(*) FROM equipaments.equipaments;"
mysql -h 10.0.20.10 -u bchecker -pbchecker121 equipaments -e "SELECT name FROM equipaments LIMIT 5;"
```

Desde `W-NNC`, la aplicación web tiene que poder conectar a `10.0.20.10:3306` y mostrar el contenido de la tabla `equipaments`.

---

[Volver al índice](../../README.md)
