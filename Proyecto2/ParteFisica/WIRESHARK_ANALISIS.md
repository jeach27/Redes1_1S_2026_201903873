# Análisis de Tráfico con Wireshark
### Proyecto 2 - Parte Física | Redes de Computadoras 1

 **[← Volver a la documentación principal](README.md)**

---

## Índice
1. [Configuración de Captura](#configuración-de-captura)
2. [Captura 1 — Tráfico ICMP](#captura-1--tráfico-icmp)
3. [Captura 2 — Resolución ARP](#captura-2--resolución-arp)
4. [Captura 3 — Tráfico del Router Kali](#captura-3--tráfico-del-router-kali)
5. [Captura 4 — Tráfico Windows ↔ Linux Cliente](#captura-4--tráfico-windows--linux-cliente)
6. [Análisis Conceptual](#análisis-conceptual)

---

## Configuración de Captura

- **Herramienta:** Wireshark 4.6.5 x64
- **Adaptador de captura:** `Ethernet 3` — VirtualBox Host-Only Ethernet Adapter
- **IP del adaptador:** `192.168.53.1`
- **Motivo de selección:** Este adaptador es el punto de entrada/salida entre Windows y el router Kali, por lo que captura todo el tráfico inter-red que pasa por la topología.

---

## Captura 1 — Tráfico ICMP

### Filtro aplicado
```
icmp
```

### Comando ejecutado durante la captura
```powershell
ping 192.168.103.20
```

### Screenshot
![Captura ICMP](Imagenes/wireshark_icmp.png)

### Descripción de los paquetes capturados

| Campo | Echo Request | Echo Reply |
|---|---|---|
| Source | 192.168.53.1 (Windows) | 192.168.103.20 (Linux Cliente) |
| Destination | 192.168.103.20 (Linux Cliente) | 192.168.53.1 (Windows) |
| Protocol | ICMP | ICMP |
| Info | Echo (ping) request | Echo (ping) reply |

### Observaciones
- Se capturan los mensajes **Echo Request** (Windows → Linux Cliente) y **Echo Reply** (Linux Cliente → Windows).
- El tráfico pasa a través del router Kali en ambas direcciones, aunque en esta captura solo se ve el segmento Host-Only.
- Los paquetes viajan desde `192.168.53.1` hacia `192.168.103.20`, cruzando dos redes distintas gracias al enrutamiento de Kali.

---

## Captura 2 — Resolución ARP

### Filtro aplicado
```
arp
```

### Comandos ejecutados durante la captura
```powershell
# Limpiar caché ARP para forzar nueva resolución
arp -d *

# Generar tráfico que fuerce resolución ARP
ping 192.168.53.10
```

### Screenshot
![Captura ARP](Imagenes/wireshark_arp.png)

### Descripción de los paquetes capturados

| Tipo de mensaje | Significado |
|---|---|
| **ARP Request** — "Who has 192.168.53.10? Tell 192.168.53.1" | Windows pregunta quién tiene la IP del router Kali |
| **ARP Reply** — "192.168.53.10 is at 08:00:27:8a:35:d2" | Kali responde con su dirección MAC |

### Observaciones
- ARP opera en **Capa 2** del modelo OSI y solo funciona dentro de la misma red local.
- Windows necesita la MAC de Kali (`eth0`) para poder enviarle frames Ethernet, incluso cuando el destino final es `192.168.103.20`.
- La caché ARP almacena estas asociaciones IP↔MAC para no repetir la consulta en cada paquete.

---

## Captura 3 — Tráfico del Router Kali

### Filtro aplicado
```
ip.addr == 192.168.53.10
```

### Comando ejecutado durante la captura
```powershell
ping 192.168.103.20
```

### Screenshot
![Captura Kali](Imagenes/wireshark_kali.png)

### Descripción de los paquetes capturados

| Source | Destination | Protocol | Descripción |
|---|---|---|---|
| 192.168.53.1 | 192.168.103.20 | ICMP | Windows envía Echo Request, pasa por Kali |
| 192.168.103.20 | 192.168.53.1 | ICMP | Linux Cliente responde, regresa por Kali |

### Observaciones
- Se puede observar que Kali aparece como **intermediario** — los paquetes con destino `192.168.103.20` llegan a `192.168.53.10` (Kali eth0) y Kali los reenvía por `eth1` hacia la red interna.
- Esto es posible gracias al **IP Forwarding** habilitado en Kali (`net.ipv4.ip_forward=1`).
- En la captura se ven IPs de ambas redes porque Kali es el nexo entre ellas.

---

## Captura 4 — Tráfico Windows ↔ Linux Cliente

### Filtro aplicado
```
ip.addr == 192.168.53.1 && ip.addr == 192.168.103.20
```

### Comando ejecutado durante la captura
```powershell
ping 192.168.103.20
```

### Screenshot
![Captura Win-Linux](Imagenes/wireshark_winlinux.png)

### Descripción de los paquetes capturados

| Source | Destination | TTL | Info |
|---|---|---|---|
| 192.168.53.1 | 192.168.103.20 | 128 | Echo Request de Windows |
| 192.168.103.20 | 192.168.53.1 | 63 | Echo Reply de Linux Cliente |

### Observaciones
- El filtro muestra únicamente el tráfico entre los dos extremos de la comunicación.
- Se confirma que **Windows (192.168.53.1) y Linux Cliente (192.168.103.20) se comunican** a través del router Kali.
- El TTL del Echo Reply es **63** (64 - 1 = 63), evidenciando que pasó por exactamente un router.

---

## Análisis Conceptual

### ¿Qué protocolo utiliza el comando `ping`?

El comando `ping` utiliza el protocolo **ICMP** (Internet Control Message Protocol), definido en el RFC 792. ICMP opera en la **Capa 3 (Red)** del modelo OSI y se encapsula directamente sobre IP.

Específicamente, `ping` usa dos tipos de mensajes ICMP:
- **Type 8 — Echo Request:** enviado por el origen para solicitar respuesta.
- **Type 0 — Echo Reply:** enviado por el destino para confirmar que recibió el request.

En las capturas de Wireshark se puede observar claramente el par Request/Reply con sus respectivos números de secuencia (`icmp_seq`) e identificadores.

---

### ¿Para qué se utiliza ARP?

**ARP** (Address Resolution Protocol) se utiliza para **traducir una dirección IP conocida a su dirección MAC correspondiente** dentro de una misma red local (LAN). Opera en la **Capa 2 (Enlace de Datos)** del modelo OSI.

**¿Por qué es necesario?**
Cuando Windows quiere enviar un paquete a `192.168.53.10` (Kali), necesita encapsularlo en un frame Ethernet. Para construir ese frame, requiere la **dirección MAC destino**. ARP resuelve ese problema:

1. Windows envía un **ARP Request** broadcast: *"¿Quién tiene la IP 192.168.53.10? Dígaselo a 192.168.53.1"*
2. Kali responde con un **ARP Reply** unicast: *"192.168.53.10 está en la MAC 08:00:27:8a:35:d2"*
3. Windows almacena esa asociación en su **caché ARP** y ya puede enviar frames directamente.

---

### ¿Qué dirección MAC se utiliza como destino cuando el tráfico va hacia otra red?

Cuando el tráfico tiene como destino una IP de **otra red** (por ejemplo, Windows `192.168.53.1` enviando a Linux Cliente `192.168.103.20`), la dirección MAC destino en el frame Ethernet **NO es la del destino final**, sino la **MAC del gateway (router)**.

**En este proyecto:**
- Windows quiere llegar a `192.168.103.20` (red diferente).
- Windows detecta que `192.168.103.20` no está en su red local (`192.168.53.0/24`).
- Windows consulta su tabla de rutas y encuentra que el next-hop es `192.168.53.10` (Kali).
- Windows hace ARP para obtener la **MAC de Kali eth0** (`08:00:27:8a:35:d2`).
- El frame Ethernet sale con **MAC destino = MAC de Kali**, aunque la IP destino sea `192.168.103.20`.

Cuando Kali recibe el frame, lo desencapsula, ve que la IP destino es `192.168.103.20`, lo reencapsula con la **MAC de Linux Cliente** y lo envía por `eth1`.

---

### ¿Qué ocurre con el TTL cuando el paquete pasa por el router?

El **TTL** (Time To Live) es un campo de 8 bits en el encabezado IP que se **decrementa en 1 por cada router** que atraviesa el paquete. Su propósito es evitar que los paquetes circulen indefinidamente en la red en caso de bucles de enrutamiento.

**En este proyecto se puede observar:**

| Tramo | TTL observado | Explicación |
|---|---|---|
| Windows → Kali (ping a 192.168.53.10) | TTL=64 en respuesta | Kali responde directamente, sin saltos intermedios |
| Windows → Linux Cliente (ping a 192.168.103.20) | TTL=63 en respuesta | Linux parte con TTL=64, Kali lo decrementa a 63 al reenviarlo |

**Flujo completo del TTL en un ping de Windows a Linux Cliente:**
1. Windows envía Echo Request con TTL=128.
2. Llega a Kali (eth0). Kali decrementa TTL → 127 y reenvía por eth1.
3. Linux Cliente recibe el request con TTL=127.
4. Linux responde con TTL=64 (valor inicial de Linux).
5. Kali decrementa TTL → 63 y reenvía hacia Windows.
6. Windows recibe la respuesta con **TTL=63**, evidenciando 1 salto de router.

Esto es exactamente lo que muestra el `tracert`:
```
1   <1ms   192.168.53.10   ← 1er salto: Kali Router
2    1ms   192.168.103.20  ← 2do salto: Linux Cliente
```

---

*Análisis de Tráfico — Proyecto 2 | Redes de Computadoras 1*
*Universidad San Carlos de Guatemala | Carnet: 201903873 | Mayo 2026*
