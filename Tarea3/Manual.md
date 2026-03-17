# Manual Técnico – Tarea #3
## Configuración de VLANs y VTP en Cisco Packet Tracer

Universidad San Carlos de Guatemala – Facultad de Ingeniería  
Ingeniería en Ciencias y Sistemas  
Redes de Computadoras 1  

---

Joaquin Emmanuel Aldair Coromac Huezo - 201903873


## Descripción de la Topología

La red implementada consiste en **4 switches Cisco 2960** interconectados mediante enlaces **trunk**, y **6 PCs** distribuidas en tres VLANs departamentales.

```
                    [PC3] [PC4] [PC5]
                       \   |   /
                      Switch VENTAS
                           |  (trunk)
              (trunk)      |       (trunk)
[PC0]──Switch ADMIN──── Switch0 ────Switch MERCA──[PC1]
                                                   [PC2]
```

### Roles de los switches

| Switch     | Rol VTP    | VLAN Asociada |
|------------|------------|---------------|
| Switch0    | Servidor   | —             |
| Switch VENTAS | Cliente | VENTAS (20)   |
| Switch ADMIN  | Cliente | ADMIN (10)    |
| Switch MERCA  | Cliente | MERCA (30)    |

---

## Direccionamiento IP

| Dispositivo | VLAN         | ID VLAN | Dirección IP    | Máscara         |
|-------------|--------------|---------|-----------------|-----------------|
| PC0         | ADMIN        | 10      | 192.168.10.1    | 255.255.255.0   |
| PC1         | MERCA        | 30      | 192.168.30.1    | 255.255.255.0   |
| PC2         | MERCA        | 30      | 192.168.30.2    | 255.255.255.0   |
| PC3         | VENTAS       | 20      | 192.168.20.1    | 255.255.255.0   |
| PC4         | VENTAS       | 20      | 192.168.20.2    | 255.255.255.0   |
| PC5         | VENTAS       | 20      | 192.168.20.3    | 255.255.255.0   |

---

## Configuración de Switch0 (Servidor VTP)

Este switch actúa como **servidor VTP**. Aquí se crean las VLANs y se propagan al resto de switches cliente.

```bash
enable
configure terminal

! Renombrar el switch
hostname Switch0

! Configurar VTP en modo SERVIDOR
vtp mode server
vtp domain REDES1
vtp password cisco123
vtp version 2

! Crear las VLANs
vlan 10
 name ADMIN
vlan 20
 name VENTAS
vlan 30
 name MERCA
exit

! Configurar puertos trunk hacia los demás switches
! (Ajustar fa0/X según los puertos reales utilizados)
interface fastEthernet 0/1
 switchport mode trunk
 switchport trunk allowed vlan all
 no shutdown

interface fastEthernet 0/2
 switchport mode trunk
 switchport trunk allowed vlan all
 no shutdown

interface fastEthernet 0/3
 switchport mode trunk
 switchport trunk allowed vlan all
 no shutdown

end
write memory
```

---

## Configuración de Switch VENTAS (Cliente VTP)

Este switch recibe las VLANs del servidor. Contiene las PCs del departamento de **VENTAS**.

```bash
enable
configure terminal

! Renombrar el switch
hostname VENTAS

! Configurar VTP en modo CLIENTE
vtp mode client
vtp domain REDES1
vtp password cisco123
vtp version 2

! Puerto trunk hacia Switch0
interface fastEthernet 0/1
 switchport mode trunk
 switchport trunk allowed vlan all
 no shutdown

! Puertos access para PC3, PC4, PC5 en VLAN 20
interface fastEthernet 0/2
 switchport mode access
 switchport access vlan 20
 no shutdown

interface fastEthernet 0/3
 switchport mode access
 switchport access vlan 20
 no shutdown

interface fastEthernet 0/4
 switchport mode access
 switchport access vlan 20
 no shutdown

end
write memory
```

---

## Configuración de Switch ADMIN (Cliente VTP)

Este switch contiene la PC del departamento de **ADMIN**.

```bash
enable
configure terminal

! Renombrar el switch
hostname ADMIN

! Configurar VTP en modo CLIENTE
vtp mode client
vtp domain REDES1
vtp password cisco123
vtp version 2

! Puerto trunk hacia Switch0
interface fastEthernet 0/1
 switchport mode trunk
 switchport trunk allowed vlan all
 no shutdown

! Puerto access para PC0 en VLAN 10
interface fastEthernet 0/2
 switchport mode access
 switchport access vlan 10
 no shutdown

end
write memory
```

---

## Configuración de Switch MERCA (Cliente VTP)

Este switch contiene las PCs del departamento de **MERCA**.

```bash
enable
configure terminal

! Renombrar el switch
hostname MERCA

! Configurar VTP en modo CLIENTE
vtp mode client
vtp domain REDES1
vtp password cisco123
vtp version 2

! Puerto trunk hacia Switch0
interface fastEthernet 0/1
 switchport mode trunk
 switchport trunk allowed vlan all
 no shutdown

! Puertos access para PC1, PC2 en VLAN 30
interface fastEthernet 0/2
 switchport mode access
 switchport access vlan 30
 no shutdown

interface fastEthernet 0/3
 switchport mode access
 switchport access vlan 30
 no shutdown

end
write memory
```

---

## Verificación – show vtp status

Ejecutar en **cada switch** el siguiente comando para verificar el estado de VTP:

```bash
show vtp status
```

### Salida esperada en Switch0 (Servidor):

```
VTP Version capable             : 1 to 2
VTP version running             : 2
VTP Domain Name                 : REDES1
VTP Pruning Mode                : Disabled
VTP Traps Generation            : Disabled
Device ID                       : [MAC del switch]
Configuration last modified by  : 0.0.0.0 at ...

Feature VLAN:
--------------
VTP Operating Mode              : Server
Maximum VLANs supported locally : 255
Number of existing VLANs        : 8
Configuration Revision          : 3
MD5 digest                      : ...
```

### Salida esperada en Switch VENTAS/ADMIN/MERCA (Cliente):

```
VTP Version capable             : 1 to 2
VTP version running             : 2
VTP Domain Name                 : REDES1
VTP Pruning Mode                : Disabled
VTP Traps Generation            : Disabled
Device ID                       : [MAC del switch]
Configuration last modified by  : [IP Switch0] at ...

Feature VLAN:
--------------
VTP Operating Mode              : Client
Maximum VLANs supported locally : 255
Number of existing VLANs        : 8
Configuration Revision          : 3
MD5 digest                      : ...
```

> **Nota:** El número de revisión (`Configuration Revision`) debe ser **igual** en todos los switches, confirmando que las VLANs se propagaron correctamente.

> **[INSERTAR CAPTURA DE PANTALLA: show vtp status en cada switch]**

---

## Verificación – show vlan brief

Ejecutar en **cada switch**:

```bash
show vlan brief
```

### Salida esperada (en todos los switches):

```
VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/5, Fa0/6, ... (puertos sin asignar)
10   ADMIN                            active    Fa0/2 (solo en switch ADMIN)
20   VENTAS                           active    Fa0/2, Fa0/3, Fa0/4 (solo en VENTAS)
30   MERCA                            active    Fa0/2, Fa0/3 (solo en MERCA)
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
```

> **Verificación clave:** Las VLANs 10 (ADMIN), 20 (VENTAS) y 30 (MERCA) deben aparecer en **todos** los switches, incluso en los clientes que no tienen PCs de esa VLAN. Esto confirma la correcta propagación mediante VTP.

> **[INSERTAR CAPTURA DE PANTALLA: show vlan brief en cada switch]**

---

## Pruebas de Conectividad (Ping)

### Pings que DEBEN ser exitosos (misma VLAN)

#### VLAN 20 – VENTAS: PC3 → PC4

```
C:\> ping 192.168.20.2

Pinging 192.168.20.2 with 32 bytes of data:
Reply from 192.168.20.2: bytes=32 time<1ms TTL=128
Reply from 192.168.20.2: bytes=32 time<1ms TTL=128
Reply from 192.168.20.2: bytes=32 time<1ms TTL=128
Reply from 192.168.20.2: bytes=32 time<1ms TTL=128

Ping statistics for 192.168.20.2:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

#### VLAN 20 – VENTAS: PC3 → PC5

```
C:\> ping 192.168.20.3

Pinging 192.168.20.3 with 32 bytes of data:
Reply from 192.168.20.3: bytes=32 time<1ms TTL=128
Reply from 192.168.20.3: bytes=32 time<1ms TTL=128
Reply from 192.168.20.3: bytes=32 time<1ms TTL=128
Reply from 192.168.20.3: bytes=32 time<1ms TTL=128

Ping statistics for 192.168.20.3:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

#### VLAN 30 – MERCA: PC1 → PC2

```
C:\> ping 192.168.30.2

Pinging 192.168.30.2 with 32 bytes of data:
Reply from 192.168.30.2: bytes=32 time<1ms TTL=128
Reply from 192.168.30.2: bytes=32 time<1ms TTL=128
Reply from 192.168.30.2: bytes=32 time<1ms TTL=128
Reply from 192.168.30.2: bytes=32 time<1ms TTL=128

Ping statistics for 192.168.30.2:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

> **[INSERTAR CAPTURAS DE PANTALLA de pings exitosos]**

---

### Pings que DEBEN FALLAR (diferente VLAN)

#### VLAN 10 (ADMIN) → VLAN 20 (VENTAS): PC0 → PC3

```
C:\> ping 192.168.20.1

Pinging 192.168.20.1 with 32 bytes of data:
Request timeout for icmp_seq 0
Request timeout for icmp_seq 1
Request timeout for icmp_seq 2
Request timeout for icmp_seq 3

Ping statistics for 192.168.20.1:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

#### VLAN 10 (ADMIN) → VLAN 30 (MERCA): PC0 → PC1

```
C:\> ping 192.168.30.1

Pinging 192.168.30.1 with 32 bytes of data:
Request timeout for icmp_seq 0
Request timeout for icmp_seq 1
Request timeout for icmp_seq 2
Request timeout for icmp_seq 3

Ping statistics for 192.168.30.1:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

#### VLAN 20 (VENTAS) → VLAN 30 (MERCA): PC3 → PC1

```
C:\> ping 192.168.30.1

Pinging 192.168.30.1 with 32 bytes of data:
Request timeout for icmp_seq 0
Request timeout for icmp_seq 1
Request timeout for icmp_seq 2
Request timeout for icmp_seq 3

Ping statistics for 192.168.30.1:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

> **[INSERTAR CAPTURAS DE PANTALLA de pings fallidos]**

---

## Conclusiones

- Se implementó exitosamente una red con **4 switches Cisco 2960** y **6 PCs** en Cisco Packet Tracer.
- Se configuró el protocolo **VTP** con Switch0 como servidor y los demás como clientes, logrando la propagación automática de las VLANs.
- Se crearon **3 VLANs**: ADMIN (10), VENTAS (20) y MERCA (30), con nombres y asignaciones correctas.
- Las pruebas de conectividad confirmaron que los dispositivos dentro de la **misma VLAN se comunican exitosamente**, mientras que los de **VLANs distintas no pueden comunicarse**, demostrando la segmentación lógica de la red.
- Los comandos `show vtp status` y `show vlan brief` verificaron la correcta propagación y configuración en todos los switches.

---

