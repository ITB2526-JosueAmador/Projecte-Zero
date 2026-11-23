# Router R-NNC

## Interfaces de red

Fichero `/etc/network/interfaces`:

```
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface (INTERNET)
auto enp1s0
iface enp1s0 inet dhcp

#Conexión VPN Wireguard enp2s0 no hace falta

#Conexión DMZ

auto enp3s0
iface enp3s0 inet static
    address 10.0.10.1/24

#Conexión INTRANET

auto enp4s0
iface enp4s0 inet static
    address 10.0.20.1/24
```

Una vez escrita la configuración con `sudo`, se aplica con `sudo systemctl restart networking`.

## Reenvío de paquetes

```bash
echo "net.ipv4.ip_forward=1" | sudo tee /etc/sysctl.d/99-router.conf
sudo sysctl --system
```

## Cortafuegos y NAT con nftables

Fichero `/etc/nftables.conf`:

```
#!/usr/sbin/nft -f
flush ruleset

define WAN   = "enp1s0"
define DMZ   = "enp3s0"
define INTRA = "enp4s0"

define WEB  = 10.0.10.10   # W-NNC
define FTP  = 10.0.10.20   # F-NNC
define BNNC = 10.0.20.10   # B-NNC

table inet filter {
    chain input {
        type filter hook input priority filter; policy drop;
        ct state established,related accept
        ct state invalid drop
        iif lo accept
        ip protocol icmp accept
        ip6 nexthdr icmpv6 accept
        iifname $WAN udp sport 67 udp dport 68 accept
        # Administración del router solo desde la intranet
        iifname $INTRA tcp dport 22 accept
    }

    chain forward {
        type filter hook forward priority filter; policy drop;
        ct state established,related accept
        ct state invalid drop

        # Internet -> DMZ: solo los servicios publicados
        iifname $WAN oifname $DMZ ip daddr $WEB tcp dport { 80, 443 } accept
        iifname $WAN oifname $DMZ ip daddr $FTP tcp dport { 21, 40000-40100 } accept

        # Intranet -> DMZ e Internet (incluye SSH a los servidores)
        iifname $INTRA oifname { $WAN, $DMZ } accept

        # DMZ -> BBDD en B-NNC: solo el servidor web y solo ese puerto
        iifname $DMZ oifname $INTRA ip saddr $WEB ip daddr $BNNC tcp dport 3306 accept

        # DMZ -> Internet: solo lo necesario (DNS, NTP, actualizaciones, ping)
        iifname $DMZ oifname $WAN tcp dport { 53, 80, 443 } accept
        iifname $DMZ oifname $WAN udp dport { 53, 123 } accept
        iifname $DMZ oifname $WAN icmp type echo-request accept
    }
}

table ip nat {
    chain prerouting {
        type nat hook prerouting priority dstnat;
        iifname $WAN tcp dport { 80, 443 } dnat to $WEB
        iifname $WAN tcp dport { 21, 40000-40100 } dnat to $FTP
        # SSH desde fuera (opcional, descomenta si lo necesitas):
        # iifname $WAN tcp dport 2210 dnat to $WEB:22
        # iifname $WAN tcp dport 2220 dnat to $FTP:22
    }
    chain postrouting {
        type nat hook postrouting priority srcnat;
        oifname $WAN masquerade
    }
}
```

Se comprueba la sintaxis, se carga la configuración y se habilita el servicio para que las reglas se apliquen también al arrancar:

```bash
sudo nft -c -f /etc/nftables.conf
sudo systemctl restart nftables
sudo systemctl enable nftables
```

## Comprobación de R-NNC

```bash
ip -br a
sysctl net.ipv4.ip_forward
sudo nft list ruleset
systemctl is-enabled nftables
```

| Comprobación | Resultado esperado |
| :--- | :--- |
| Interfaces | `enp3s0` con `10.0.10.1/24` y `enp4s0` con `10.0.20.1/24` |
| Reenvío | `net.ipv4.ip_forward = 1` |
| Reglas | Aparecen las tablas `inet filter` e `ip nat` |
| Arranque | `enabled` |

---

[Volver al índice](../../README.md)
