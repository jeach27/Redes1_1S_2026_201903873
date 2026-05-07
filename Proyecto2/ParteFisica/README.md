# Proyecto 2 - Parte Física
### Redes de Computadoras 1 — Sección A
**Universidad San Carlos de Guatemala | Facultad de Ingeniería**
**Estudiante:** Joaquin Coromac | **Carnet:** 201903873

---

## Índice
1. [Descripción General](#descripción-general)
2. [Tabla de Direccionamiento IP](#tabla-de-direccionamiento-ip)
3. [Topología de Red](#topología-de-red)
4. [Configuración de VirtualBox](#configuración-de-virtualbox)
5. [Configuración Kali Router](#configuración-kali-router)
6. [Configuración Linux Cliente](#configuración-linux-cliente)
7. [Configuración Windows Host](#configuración-windows-host)
8. [Pruebas de Conectividad](#pruebas-de-conectividad)
9. [Análisis de Tráfico con Wireshark](#análisis-de-tráfico-con-wireshark)

---

## Descripción General

Este proyecto implementa una red física simulada mediante virtualización, utilizando:

- **Kali Linux** como router Linux con dos interfaces de red
- **Debian (LinuxCliente)** como equipo cliente en red interna
- **Windows** como computadora física host

El objetivo es demostrar conectividad real entre redes diferentes mediante enrutamiento, IPs estáticas y rutas estáticas, replicando el comportamiento de un router de capa 3.

---

## Tabla de Direccionamiento IP

| Dispositivo | Interfaz | Tipo de Red | Dirección IP | Máscara | Gateway |
|---|---|---|---|---|---|
| Windows (Host) | Ethernet 3 (VirtualBox Host-Only) | Host-Only | 192.168.53.1 | 255.255.255.0 | — |
| Kali Router | eth0 | Host-Only | 192.168.53.10 | 255.255.255.0 | — |
| Kali Router | eth1 | Red Interna (LAN_COROMAC) | 192.168.103.1 | 255.255.255.0 | — |
| Kali Router | eth2 | NAT | 10.0.4.15 | 255.255.255.0 | 10.0.4.2 |
| Linux Cliente | enp0s3 | Red Interna (LAN_COROMAC) | 192.168.103.20 | 255.255.255.0 | 192.168.103.1 |

> **X = 3** (último dígito del carnet 201903873)
> Red Host-Only: `192.168.5X.0/24` → `192.168.53.0/24`
> Red Interna: `192.168.10X.0/24` → `192.168.103.0/24`

---

## Topología de Red

```
┌─────────────────────────────────────────────────────────────────┐
│                        Windows Host                             │
│                    IP: 192.168.53.1                             │
│                 (VirtualBox Host-Only - Ethernet 3)             │
└───────────────────────────┬─────────────────────────────────────┘
                            │ Red Host-Only
                            │ 192.168.53.0/24
                            │
              ┌─────────────▼──────────────┐
              │         KALI ROUTER        │
              │   eth0: 192.168.53.10      │ ◄── Host-Only
              │   eth1: 192.168.103.1      │ ◄── Red Interna
              │   eth2: 10.0.4.15 (NAT)   │
              │   ip_forward = 1           │
              └─────────────┬──────────────┘
                            │ Red Interna LAN_COROMAC
                            │ 192.168.103.0/24
                            │
              ┌─────────────▼──────────────┐
              │       LINUX CLIENTE        │
              │   enp0s3: 192.168.103.20   │
              │   Gateway: 192.168.103.1   │
              └────────────────────────────┘
```

---

## Configuración de VirtualBox

### Red Host-Only (Administrador de Red)

| Parámetro | Valor |
|---|---|
| Nombre | VirtualBox Host-Only Ethernet Adapter |
| Dirección IPv4 | 192.168.53.1 |
| Máscara | 255.255.255.0 |
| Servidor DHCP | **Inhabilitado** |

### Adaptadores VM Kali Router

| Adaptador | Tipo | Nombre/Red | Propósito |
|---|---|---|---|
| Adaptador 1 | Adaptador sólo-anfitrión | VirtualBox Host-Only Ethernet Adapter | Comunicación con Windows |
| Adaptador 2 | Red interna | LAN_COROMAC | Comunicación con Linux Cliente |
| Adaptador 3 | NAT | — | Acceso a internet (instalación de paquetes) |

### Adaptadores VM Linux Cliente

| Adaptador | Tipo | Nombre/Red | Propósito |
|---|---|---|---|
| Adaptador 1 | Red interna | LAN_COROMAC | Comunicación con Kali Router |

> El nombre `LAN_COROMAC` debe ser **exactamente igual** en ambas VMs para que pertenezcan a la misma red interna.

---

## Configuración Kali Router

### Especificaciones de la VM

| Recurso | Valor |
|---|---|
| Sistema Operativo | Kali Linux 2026.1 (amd64) |
| RAM | 2048 MB |
| CPU | 2 núcleos |
| Disco | 80.09 GB |
| Adaptadores de red | 3 |

### 1. Verificación de interfaces

```bash
ip addr show
```

**Resultado:**
```
2: eth0 → Host-Only  (MAC: 08:00:27:8a:35:d2)
3: eth1 → Red Interna (MAC: 08:00:27:70:46:1d)
4: eth2 → NAT        (IP: 10.0.4.15 - asignada por DHCP)
```

### 2. Verificación de conexiones nmcli

```bash
nmcli connection show
```

**Resultado:**
```
NAME                UUID                                  TYPE      DEVICE
Wired connection 3  ce9ea26c-35ad-3321-98cc-66f687cb110b  ethernet  eth2
lo                  a0070c52-5ebd-4b7a-a8bb-33d76d77e7d9  loopback  lo
Wired connection 1  d7d7365f-c2d9-3cb3-ab4f-5d63bd7fe7e9  ethernet  --
Wired connection 2  5ddffe32-8a4a-35d9-8fb8-7659097d45bb  ethernet  --
```

### 3. Configuración de IP estática en eth0 (Host-Only)

```bash
sudo nmcli connection modify "Wired connection 1" \
  ipv4.method manual \
  ipv4.addresses 192.168.53.10/24 \
  ipv4.gateway "" \
  connection.autoconnect yes
```

### 4. Configuración de IP estática en eth1 (Red Interna)

```bash
sudo nmcli connection modify "Wired connection 2" \
  ipv4.method manual \
  ipv4.addresses 192.168.103.1/24 \
  ipv4.gateway "" \
  connection.autoconnect yes
```

### 5. Activación de conexiones

```bash
sudo nmcli connection up "Wired connection 1"
sudo nmcli connection up "Wired connection 2"
```

**Resultado esperado:**
```
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/13)
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/14)
```

### 6. Habilitación de IP Forwarding

```bash
# Temporal
sudo sysctl -w net.ipv4.ip_forward=1

# Permanente
echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

### 7. Verificación final de Kali

```bash
ip addr show
```

**Resultado confirmado:**
```
eth0: inet 192.168.53.10/24   
eth1: inet 192.168.103.1/24   
eth2: inet 10.0.4.15/24        (NAT)
```

```bash
cat /proc/sys/net/ipv4/ip_forward
# Resultado: 1  
```

---

## Configuración Linux Cliente

### Especificaciones de la VM

| Recurso | Valor |
|---|---|
| Sistema Operativo | Debian 13 Trixie (64-bit) |
| RAM | 2048 MB |
| CPU | 1 núcleo |
| Disco | 20 GB |
| Adaptadores de red | 1 |

### 1. Verificación de interfaz

```bash
ip addr show
```

**Resultado inicial:**
```
2: enp0s3 → Sin IPv4 (solo IPv6 link-local)
```

### 2. Verificación de conexión nmcli

```bash
nmcli connection show
```

**Resultado:**
```
NAME               UUID                                  TYPE      DEVICE
lo                 9b6e8bfa-6fcc-414f-8af5-1115ff347ebd  loopback  lo
Wired connection 1 fc22ebbc-5e3c-4cee-9d65-5ffd09d2bee2  ethernet  --
```

### 3. Configuración de IP estática y gateway

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.method manual ipv4.addresses 192.168.103.20/24 ipv4.gateway 192.168.103.1 connection.autoconnect yes
```

### 4. Activación de la conexión

```bash
sudo nmcli connection up "Wired connection 1"
```

**Resultado:**
```
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/11)
```

### 5. Verificación final de Linux Cliente

```bash
ip addr show
```

**Resultado confirmado:**
```
enp0s3: inet 192.168.103.20/24   
Gateway: 192.168.103.1           
```

---

## Configuración Windows Host

### 1. Verificación del adaptador Host-Only

```powershell
ipconfig /all
```

**Adaptador identificado:** `Ethernet 3`
```
Descripción: VirtualBox Host-Only Ethernet Adapter
Dirección IPv4: 192.168.53.1
Máscara: 255.255.255.0
DHCP: No
```

### 2. Ruta estática hacia la red de Linux Cliente

```powershell
# Ruta permanente
route -p add 192.168.103.0 mask 255.255.255.0 192.168.53.10
```

**Resultado:** `Correcto`

### 3. Habilitación de ICMP en el firewall

```powershell
netsh advfirewall firewall add rule name="Permitir ICMPv4 VirtualBox" protocol=icmpv4:8,any dir=in action=allow
```

**Resultado:** `Aceptar`

### 4. Verificación de rutas

```powershell
route print
```

**Rutas relevantes confirmadas:**
```
192.168.53.0   255.255.255.0   En vínculo      192.168.53.1    330  
192.168.103.0  255.255.255.0   192.168.53.10   192.168.53.1     75  
```

**Ruta persistente:**
```
192.168.103.0  255.255.255.0   192.168.53.10   1  
```

---

## Pruebas de Conectividad

### Desde Linux Cliente

| Destino | Comando | Resultado |
|---|---|---|
| Kali Router (eth1) | `ping -c 4 192.168.103.1` |  0% packet loss, TTL=64 |
| Windows Host | `ping -c 4 192.168.53.1` |  0% packet loss, TTL=127 |

### Desde Windows Host

| Destino | Comando | Resultado |
|---|---|---|
| Kali Router (eth0) | `ping 192.168.53.10` |  0% perdidos, TTL=64 |
| Linux Cliente | `ping 192.168.103.20` |  0% perdidos, TTL=63 |

> **Nota sobre TTL=63:** El ping hacia Linux Cliente parte con TTL=64 desde Kali. Windows lo recibe con TTL=63 porque decrementó en 1 al pasar por el router Kali — evidencia del salto de Capa 3.

### Tracert desde Windows

```powershell
tracert 192.168.103.20
```

**Resultado:**
```
Traza a 192.168.103.20 sobre caminos de 30 saltos como máximo.

  1    <1 ms    <1 ms    <1 ms  192.168.53.10   ← Kali Router
  2     1 ms     1 ms     1 ms  192.168.103.20  ← Linux Cliente

Traza completa.
```

 El tráfico pasa obligatoriamente por Kali antes de llegar a Linux Cliente.

---

## Análisis de Tráfico con Wireshark

 **[Ver análisis completo de Wireshark y respuestas conceptuales →](WIRESHARK_ANALISIS.md)**

---

*Proyecto 2 — Redes de Computadoras 1 | Universidad San Carlos de Guatemala*
*Carnet: 201903873 | Fecha: Mayo 2026*
