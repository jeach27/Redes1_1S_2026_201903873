# Proyecto 2: Red Nacional de Coordinacion SE-CONRED

**Universidad San Carlos de Guatemala**
**Facultad de Ingenieria - Ingenieria en Ciencias y Sistemas**
**Redes de Computadoras 1**

---

## Datos del estudiante

| Campo | Valor |
|---|---|
| Nombre | Joaquin Emmanuel Aldair Coromac Huezo |
| Carne | 201903873 |
| Ultimos dos digitos del carnet (XX) | 73 |
| Ultimo digito del carnet (Y) | 3 |
| Seccion | A |

---

## Tabla de VLANs del proyecto

| VLAN | ID (con Y=3) | Occidente | Norte | Oriente | Central |
|---|---|---|---|---|---|
| Operaciones | 13 | 40 equipos | 30 equipos | - | - |
| Administracion | 23 | 18 equipos | 12 equipos | 19 equipos | 20 equipos |
| Seguridad | 33 | 8 equipos | 10 equipos | 8 equipos | 9 equipos |
| Inventario | 43 | 55 equipos | 15 equipos | 21 equipos | - |
| Atencion Regional | 53 | - | - | 28 equipos | - |
| Monitoreo y Control | 63 | - | - | - | 45 equipos |
| Soporte | 73 | - | - | - | 10 equipos |
| Servicios Criticos | 83 | - | - | - | 16 equipos |

---

## Redes base asignadas por sede

| Sede | Red Base |
|---|---|
| Occidente | 172.16.0.0/16 |
| Norte | 172.17.0.0/16 |
| Oriente | 172.18.0.0/16 |
| Central | 172.19.0.0/16 |
| Backbone | 10.0.0.0/8 |

Criterio de subneteo: Se aplica VLSM (Variable Length Subnet Mask), ordenando las VLANs de mayor a menor cantidad de hosts para aprovechar eficientemente el espacio de direcciones. Se reserva 1 direccion para el gateway (primera usable) y se considera margen de crecimiento al elegir el prefijo.

---

## Seccion 1 - Subneteo VLSM

### Metodologia VLSM aplicada

Para cada sede se ordenan las VLANs de mayor a menor numero de hosts requeridos. Luego se asigna la subred mas pequena que contenga todos los hosts mas gateway mas broadcast. La formula es:

```
Hosts utilizables = 2^n - 2   (donde n = bits de host)
Se elige el prefijo tal que: 2^n - 2 >= equipos_requeridos
```

---

### 1.1 Sede Occidente - Red base: 172.16.0.0/16

VLANs ordenadas de mayor a menor:

| Orden | VLAN | ID | Equipos | Hosts necesarios | Prefijo elegido | Hosts utilizables |
|---|---|---|---|---|---|---|
| 1 | Inventario | 43 | 55 | 55+1 gw = 56 | /26 | 62 |
| 2 | Operaciones | 13 | 40 | 40+1 gw = 41 | /26 | 62 |
| 3 | Administracion | 23 | 18 | 18+1 gw = 19 | /27 | 30 |
| 4 | Seguridad | 33 | 8 | 8+1 gw = 9 | /28 | 14 |

#### VLAN 43 - Inventario

| Campo | Valor |
|---|---|
| Subred | 172.16.0.0/26 |
| Mascara | 255.255.255.192 |
| Gateway | 172.16.0.1 |
| Rango utilizable | 172.16.0.2 - 172.16.0.62 |
| Broadcast | 172.16.0.63 |
| Hosts disponibles | 62 |

#### VLAN 13 - Operaciones

| Campo | Valor |
|---|---|
| Subred | 172.16.0.64/26 |
| Mascara | 255.255.255.192 |
| Gateway | 172.16.0.65 |
| Rango utilizable | 172.16.0.66 - 172.16.0.126 |
| Broadcast | 172.16.0.127 |
| Hosts disponibles | 62 |

#### VLAN 23 - Administracion

| Campo | Valor |
|---|---|
| Subred | 172.16.0.128/27 |
| Mascara | 255.255.255.224 |
| Gateway | 172.16.0.129 |
| Rango utilizable | 172.16.0.130 - 172.16.0.158 |
| Broadcast | 172.16.0.159 |
| Hosts disponibles | 30 |

#### VLAN 33 - Seguridad

| Campo | Valor |
|---|---|
| Subred | 172.16.0.160/28 |
| Mascara | 255.255.255.240 |
| Gateway | 172.16.0.161 |
| Rango utilizable | 172.16.0.162 - 172.16.0.174 |
| Broadcast | 172.16.0.175 |
| Hosts disponibles | 14 |

---

### 1.2 Sede Norte - Red base: 172.17.0.0/16

VLANs ordenadas de mayor a menor:

| Orden | VLAN | ID | Equipos | Hosts necesarios | Prefijo elegido | Hosts utilizables |
|---|---|---|---|---|---|---|
| 1 | Operaciones | 13 | 30 | 30+1 gw = 31 | /26 | 62 |
| 2 | Inventario | 43 | 15 | 15+1 gw = 16 | /27 | 30 |
| 3 | Seguridad | 33 | 10 | 10+1 gw = 11 | /28 | 14 |
| 4 | Administracion | 23 | 12 | 12+1 gw = 13 | /28 | 14 |

Nota sobre Operaciones Norte: Con 30 equipos mas 1 gateway = 31 hosts minimos. Un /27 da exactamente 30 utilizables, lo cual no alcanza porque uno es el gateway. Se asigna /26 (62 hosts) para dejar margen de crecimiento.

#### VLAN 13 - Operaciones

| Campo | Valor |
|---|---|
| Subred | 172.17.0.0/26 |
| Mascara | 255.255.255.192 |
| Gateway | 172.17.0.1 |
| Rango utilizable | 172.17.0.2 - 172.17.0.62 |
| Broadcast | 172.17.0.63 |
| Hosts disponibles | 62 |

#### VLAN 43 - Inventario

| Campo | Valor |
|---|---|
| Subred | 172.17.0.64/27 |
| Mascara | 255.255.255.224 |
| Gateway | 172.17.0.65 |
| Rango utilizable | 172.17.0.66 - 172.17.0.94 |
| Broadcast | 172.17.0.95 |
| Hosts disponibles | 30 |

#### VLAN 33 - Seguridad

| Campo | Valor |
|---|---|
| Subred | 172.17.0.96/28 |
| Mascara | 255.255.255.240 |
| Gateway | 172.17.0.97 |
| Rango utilizable | 172.17.0.98 - 172.17.0.110 |
| Broadcast | 172.17.0.111 |
| Hosts disponibles | 14 |

#### VLAN 23 - Administracion

| Campo | Valor |
|---|---|
| Subred | 172.17.0.112/28 |
| Mascara | 255.255.255.240 |
| Gateway | 172.17.0.113 |
| Rango utilizable | 172.17.0.114 - 172.17.0.126 |
| Broadcast | 172.17.0.127 |
| Hosts disponibles | 14 |

---

### 1.3 Sede Oriente - Red base: 172.18.0.0/16

VLANs ordenadas de mayor a menor:

| Orden | VLAN | ID | Equipos | Hosts necesarios | Prefijo elegido | Hosts utilizables |
|---|---|---|---|---|---|---|
| 1 | Atencion Regional | 53 | 28 | 28+1 gw = 29 | /27 | 30 |
| 2 | Inventario | 43 | 21 | 21+1 gw = 22 | /27 | 30 |
| 3 | Administracion | 23 | 19 | 19+1 gw = 20 | /27 | 30 |
| 4 | Seguridad | 33 | 8 | 8+1 gw = 9 | /28 | 14 |

Nota sobre Oriente: Los gateways de las VLANs son IPs virtuales HSRP. Las IPs reales de los routers ocupan las primeras direcciones del rango, por lo que los equipos finales inician desde la tercera IP usable.

#### VLAN 53 - Atencion Regional

| Campo | Valor |
|---|---|
| Subred | 172.18.0.0/27 |
| Mascara | 255.255.255.224 |
| Gateway (IP virtual HSRP) | 172.18.0.1 |
| IP real R-Oriente1 | 172.18.0.2 |
| IP real R-Oriente2 | 172.18.0.3 |
| Rango utilizable para equipos | 172.18.0.4 - 172.18.0.30 |
| Broadcast | 172.18.0.31 |
| Hosts disponibles | 30 |

#### VLAN 43 - Inventario

| Campo | Valor |
|---|---|
| Subred | 172.18.0.32/27 |
| Mascara | 255.255.255.224 |
| Gateway (IP virtual HSRP) | 172.18.0.33 |
| IP real R-Oriente1 | 172.18.0.34 |
| IP real R-Oriente2 | 172.18.0.35 |
| Rango utilizable para equipos | 172.18.0.36 - 172.18.0.62 |
| Broadcast | 172.18.0.63 |
| Hosts disponibles | 30 |

#### VLAN 23 - Administracion

| Campo | Valor |
|---|---|
| Subred | 172.18.0.64/27 |
| Mascara | 255.255.255.224 |
| Gateway (IP virtual HSRP) | 172.18.0.65 |
| IP real R-Oriente1 | 172.18.0.66 |
| IP real R-Oriente2 | 172.18.0.67 |
| Rango utilizable para equipos | 172.18.0.68 - 172.18.0.94 |
| Broadcast | 172.18.0.95 |
| Hosts disponibles | 30 |

#### VLAN 33 - Seguridad

| Campo | Valor |
|---|---|
| Subred | 172.18.0.96/28 |
| Mascara | 255.255.255.240 |
| Gateway (IP virtual HSRP) | 172.18.0.97 |
| IP real R-Oriente1 | 172.18.0.98 |
| IP real R-Oriente2 | 172.18.0.99 |
| Rango utilizable para equipos | 172.18.0.100 - 172.18.0.110 |
| Broadcast | 172.18.0.111 |
| Hosts disponibles | 14 |

---

### 1.4 Sede Central - Red base: 172.19.0.0/16

VLANs ordenadas de mayor a menor:

| Orden | VLAN | ID | Equipos | Hosts necesarios | Prefijo elegido | Hosts utilizables |
|---|---|---|---|---|---|---|
| 1 | Monitoreo y Control | 63 | 45 | 45+1 gw = 46 | /26 | 62 |
| 2 | Administracion | 23 | 20 | 20+1 gw = 21 | /27 | 30 |
| 3 | Servicios Criticos | 83 | 16 | 16+1 gw = 17 | /27 | 30 |
| 4 | Soporte | 73 | 10 | 10+1 gw = 11 | /28 | 14 |
| 5 | Seguridad | 33 | 9 | 9+1 gw = 10 | /28 | 14 |

#### VLAN 63 - Monitoreo y Control

| Campo | Valor |
|---|---|
| Subred | 172.19.0.0/26 |
| Mascara | 255.255.255.192 |
| Gateway | 172.19.0.1 |
| Rango utilizable | 172.19.0.2 - 172.19.0.62 |
| Broadcast | 172.19.0.63 |
| Hosts disponibles | 62 |

#### VLAN 23 - Administracion

| Campo | Valor |
|---|---|
| Subred | 172.19.0.64/27 |
| Mascara | 255.255.255.224 |
| Gateway | 172.19.0.65 |
| Rango utilizable | 172.19.0.66 - 172.19.0.94 |
| Broadcast | 172.19.0.95 |
| Hosts disponibles | 30 |

#### VLAN 83 - Servicios Criticos

| Campo | Valor |
|---|---|
| Subred | 172.19.0.96/27 |
| Mascara | 255.255.255.224 |
| Gateway | 172.19.0.97 |
| Rango utilizable | 172.19.0.98 - 172.19.0.126 |
| Broadcast | 172.19.0.127 |
| Hosts disponibles | 30 |

#### VLAN 73 - Soporte

| Campo | Valor |
|---|---|
| Subred | 172.19.0.128/28 |
| Mascara | 255.255.255.240 |
| Gateway | 172.19.0.129 |
| Rango utilizable | 172.19.0.130 - 172.19.0.142 |
| Broadcast | 172.19.0.143 |
| Hosts disponibles | 14 |

#### VLAN 33 - Seguridad

| Campo | Valor |
|---|---|
| Subred | 172.19.0.144/28 |
| Mascara | 255.255.255.240 |
| Gateway | 172.19.0.145 |
| Rango utilizable | 172.19.0.146 - 172.19.0.158 |
| Broadcast | 172.19.0.159 |
| Hosts disponibles | 14 |

---

### 1.5 Resumen consolidado de subneteo VLSM

#### Sede Occidente (172.16.0.0/16)

| VLAN | ID | Subred | Mascara | Gateway | Rango Equipos | Broadcast |
|---|---|---|---|---|---|---|
| Inventario | 43 | 172.16.0.0/26 | 255.255.255.192 | 172.16.0.1 | 172.16.0.2 - 172.16.0.62 | 172.16.0.63 |
| Operaciones | 13 | 172.16.0.64/26 | 255.255.255.192 | 172.16.0.65 | 172.16.0.66 - 172.16.0.126 | 172.16.0.127 |
| Administracion | 23 | 172.16.0.128/27 | 255.255.255.224 | 172.16.0.129 | 172.16.0.130 - 172.16.0.158 | 172.16.0.159 |
| Seguridad | 33 | 172.16.0.160/28 | 255.255.255.240 | 172.16.0.161 | 172.16.0.162 - 172.16.0.174 | 172.16.0.175 |

#### Sede Norte (172.17.0.0/16)

| VLAN | ID | Subred | Mascara | Gateway | Rango Equipos | Broadcast |
|---|---|---|---|---|---|---|
| Operaciones | 13 | 172.17.0.0/26 | 255.255.255.192 | 172.17.0.1 | 172.17.0.2 - 172.17.0.62 | 172.17.0.63 |
| Inventario | 43 | 172.17.0.64/27 | 255.255.255.224 | 172.17.0.65 | 172.17.0.66 - 172.17.0.94 | 172.17.0.95 |
| Seguridad | 33 | 172.17.0.96/28 | 255.255.255.240 | 172.17.0.97 | 172.17.0.98 - 172.17.0.110 | 172.17.0.111 |
| Administracion | 23 | 172.17.0.112/28 | 255.255.255.240 | 172.17.0.113 | 172.17.0.114 - 172.17.0.126 | 172.17.0.127 |

#### Sede Oriente (172.18.0.0/16)

| VLAN | ID | Subred | Mascara | Gateway HSRP | Rango Equipos | Broadcast |
|---|---|---|---|---|---|---|
| Atencion Regional | 53 | 172.18.0.0/27 | 255.255.255.224 | 172.18.0.1 | 172.18.0.4 - 172.18.0.30 | 172.18.0.31 |
| Inventario | 43 | 172.18.0.32/27 | 255.255.255.224 | 172.18.0.33 | 172.18.0.36 - 172.18.0.62 | 172.18.0.63 |
| Administracion | 23 | 172.18.0.64/27 | 255.255.255.224 | 172.18.0.65 | 172.18.0.68 - 172.18.0.94 | 172.18.0.95 |
| Seguridad | 33 | 172.18.0.96/28 | 255.255.255.240 | 172.18.0.97 | 172.18.0.100 - 172.18.0.110 | 172.18.0.111 |

#### Sede Central (172.19.0.0/16)

| VLAN | ID | Subred | Mascara | Gateway | Rango Equipos | Broadcast |
|---|---|---|---|---|---|---|
| Monitoreo y Control | 63 | 172.19.0.0/26 | 255.255.255.192 | 172.19.0.1 | 172.19.0.2 - 172.19.0.62 | 172.19.0.63 |
| Administracion | 23 | 172.19.0.64/27 | 255.255.255.224 | 172.19.0.65 | 172.19.0.66 - 172.19.0.94 | 172.19.0.95 |
| Servicios Criticos | 83 | 172.19.0.96/27 | 255.255.255.224 | 172.19.0.97 | 172.19.0.98 - 172.19.0.126 | 172.19.0.127 |
| Soporte | 73 | 172.19.0.128/28 | 255.255.255.240 | 172.19.0.129 | 172.19.0.130 - 172.19.0.142 | 172.19.0.143 |
| Seguridad | 33 | 172.19.0.144/28 | 255.255.255.240 | 172.19.0.145 | 172.19.0.146 - 172.19.0.158 | 172.19.0.159 |

---

### 1.6 IPs asignadas a dispositivos finales en simulacion

#### Sede Occidente

| PC | Switch de acceso | VLAN | IP asignada | Mascara | Gateway |
|---|---|---|---|---|---|
| PC2 | SW-OCC-Acc-Inv | 43 - Inventario | 172.16.0.2 | 255.255.255.192 | 172.16.0.1 |
| PC3 | SW-OCC-Acc-Inv | 43 - Inventario | 172.16.0.3 | 255.255.255.192 | 172.16.0.1 |
| PC4 | SW-OCC-Acc-Ops | 13 - Operaciones | 172.16.0.66 | 255.255.255.192 | 172.16.0.65 |
| PC5 | SW-OCC-Acc-Ops | 13 - Operaciones | 172.16.0.67 | 255.255.255.192 | 172.16.0.65 |
| PC6 | SW-OCC-Acc-Adm | 23 - Administracion | 172.16.0.130 | 255.255.255.224 | 172.16.0.129 |
| PC7 | SW-OCC-Acc-Adm | 23 - Administracion | 172.16.0.131 | 255.255.255.224 | 172.16.0.129 |
| PC0 | SW-OCC-Acc-Seg | 33 - Seguridad | 172.16.0.162 | 255.255.255.240 | 172.16.0.161 |
| PC1 | SW-OCC-Acc-Seg | 33 - Seguridad | 172.16.0.163 | 255.255.255.240 | 172.16.0.161 |

#### Sede Norte

| PC | Switch de acceso | VLAN | IP asignada | Mascara | Gateway |
|---|---|---|---|---|---|
| PC8 | SW-NOR-Acc-Ops | 13 - Operaciones | 172.17.0.2 | 255.255.255.192 | 172.17.0.1 |
| PC9 | SW-NOR-Acc-Ops | 13 - Operaciones | 172.17.0.3 | 255.255.255.192 | 172.17.0.1 |
| PC10 | SW-NOR-Acc-Adm | 23 - Administracion | 172.17.0.114 | 255.255.255.240 | 172.17.0.113 |
| PC11 | SW-NOR-Acc-Adm | 23 - Administracion | 172.17.0.115 | 255.255.255.240 | 172.17.0.113 |
| PC12 | SW-NOR-Acc-Seg | 33 - Seguridad | 172.17.0.98 | 255.255.255.240 | 172.17.0.97 |
| PC13 | SW-NOR-Acc-Seg | 33 - Seguridad | 172.17.0.99 | 255.255.255.240 | 172.17.0.97 |
| PC14 | SW-NOR-Acc-Inv | 43 - Inventario | 172.17.0.66 | 255.255.255.224 | 172.17.0.65 |
| PC15 | SW-NOR-Acc-Inv | 43 - Inventario | 172.17.0.67 | 255.255.255.224 | 172.17.0.65 |

#### Sede Oriente

| PC | Switch de acceso | VLAN | IP asignada | Mascara | Gateway HSRP |
|---|---|---|---|---|---|
| PC16 | SW-ORI-Acc-Ate | 53 - Atencion Regional | 172.18.0.4 | 255.255.255.224 | 172.18.0.1 |
| PC17 | SW-ORI-Acc-Ate | 53 - Atencion Regional | 172.18.0.5 | 255.255.255.224 | 172.18.0.1 |
| PC18 | SW-ORI-Acc-Adm | 23 - Administracion | 172.18.0.68 | 255.255.255.224 | 172.18.0.65 |
| PC19 | SW-ORI-Acc-Adm | 23 - Administracion | 172.18.0.69 | 255.255.255.224 | 172.18.0.65 |
| PC20 | SW-ORI-Acc-Seg | 33 - Seguridad | 172.18.0.100 | 255.255.255.240 | 172.18.0.97 |
| PC21 | SW-ORI-Acc-Seg | 33 - Seguridad | 172.18.0.101 | 255.255.255.240 | 172.18.0.97 |
| PC22 | SW-ORI-Acc-Inv | 43 - Inventario | 172.18.0.36 | 255.255.255.224 | 172.18.0.33 |
| PC23 | SW-ORI-Acc-Inv | 43 - Inventario | 172.18.0.37 | 255.255.255.224 | 172.18.0.33 |

#### Sede Central

| PC | Switch de acceso | VLAN | IP asignada | Mascara | Gateway |
|---|---|---|---|---|---|
| PC28 | SW-CEN-Acc-Mon | 63 - Monitoreo y Control | 172.19.0.2 | 255.255.255.192 | 172.19.0.1 |
| PC29 | SW-CEN-Acc-Mon | 63 - Monitoreo y Control | 172.19.0.3 | 255.255.255.192 | 172.19.0.1 |
| PC32 | SW-CEN-Acc-Adm | 23 - Administracion | 172.19.0.66 | 255.255.255.224 | 172.19.0.65 |
| PC33 | SW-CEN-Acc-Adm | 23 - Administracion | 172.19.0.67 | 255.255.255.224 | 172.19.0.65 |
| PC24 | SW-CEN-Acc-SC | 83 - Servicios Criticos | 172.19.0.98 | 255.255.255.224 | 172.19.0.97 |
| PC25 | SW-CEN-Acc-SC | 83 - Servicios Criticos | 172.19.0.99 | 255.255.255.224 | 172.19.0.97 |
| PC26 | SW-CEN-Acc-Sop | 73 - Soporte | 172.19.0.130 | 255.255.255.240 | 172.19.0.129 |
| PC27 | SW-CEN-Acc-Sop | 73 - Soporte | 172.19.0.131 | 255.255.255.240 | 172.19.0.129 |
| PC30 | SW-CEN-Acc-Seg | 33 - Seguridad | 172.19.0.146 | 255.255.255.240 | 172.19.0.145 |
| PC31 | SW-CEN-Acc-Seg | 33 - Seguridad | 172.19.0.147 | 255.255.255.240 | 172.19.0.145 |

---

## Seccion 2 - Diseno del Backbone Nacional

### 2.1 Justificacion del backbone

El backbone es la infraestructura de capa 3 que interconecta las cuatro sedes regionales de SE-CONRED. Opera como nucleo de enrutamiento central, garantizando que el trafico entre sedes se enrute de forma eficiente, redundante y con tolerancia a fallos.

Se diseno con 7 routers distribuidos en roles especificos para cumplir todos los requerimientos del enunciado.

### 2.2 Dispositivos del backbone y sus roles

| Dispositivo | Rol | Protocolo | Sede vinculada |
|---|---|---|---|
| Core-1 | Nucleo principal, punto central de redistribucion | OSPF + redistribuye EIGRP, RIP y estatico | - |
| Core-2 | Nucleo redundante, segundo punto de redistribucion | OSPF + redistribuye conectadas | - |
| R-Occidente | Router de borde | EIGRP hacia Core-1 | Sede Occidente |
| R-Norte | Router de borde | RIP hacia Core-1 | Sede Norte |
| R-Oriente1 | Router de borde activo HSRP | OSPF hacia Core-2 | Sede Oriente |
| R-Oriente2 | Router de borde standby HSRP | OSPF hacia Core-2 | Sede Oriente |
| R-Central | Router de borde | Ruta estatica hacia Core-1 | Sede Central |

### 2.3 Segmentos de enrutamiento y medios fisicos

Nota: El diseno original contemplaba EtherChannel entre Core-1 y Core-2. Durante la implementacion se determino que Packet Tracer no soporta EtherChannel en routers 2911, por lo que el enlace entre nucleos se implemento como un unico enlace Ethernet. La redundancia del backbone se mantiene gracias a la existencia de dos dispositivos de nucleo distintos con rutas alternativas via los diferentes protocolos.

| Segmento | Protocolo | Medio fisico | Puertos reales |
|---|---|---|---|
| Core-1 G0/0 a Core-2 G0/0 | OSPF | Ethernet (Copper Cross-Over) | Core-1 G0/0 - Core-2 G0/0 |
| Core-1 G0/2 a R-Occidente G0/0 | EIGRP | Ethernet (Copper Cross-Over) | Core-1 G0/2 - R-Occ G0/0 |
| Core-1 Se0/3/0 a R-Norte Se0/3/0 | RIP | Serial (DCE en Core-1) | Core-1 Se0/3/0 - R-Norte Se0/3/0 |
| Core-2 G0/2 a R-Oriente1 G0/0 | OSPF | Ethernet (Copper Cross-Over) | Core-2 G0/2 - R-Ori1 G0/0 |
| Core-2 Se0/3/0 a R-Oriente2 Se0/3/0 | OSPF | Serial (DCE en Core-2) | Core-2 Se0/3/0 - R-Ori2 Se0/3/0 |
| Core-1 Se0/3/1 a R-Central Se0/3/0 | Estatico | Serial (DCE en Core-1) | Core-1 Se0/3/1 - R-Cen Se0/3/0 |

Cumplimiento de requerimientos del enunciado:

| Requerimiento | Estado | Implementacion |
|---|---|---|
| Al menos 2 dispositivos de capa 3 en el nucleo | Cumplido | Core-1 y Core-2 |
| Redundancia de enlace entre nucleos | Cumplido | Enlace Ethernet entre Core-1 y Core-2 |
| Segmento OSPF | Cumplido | Core-1/2 hacia R-Oriente1/2 |
| Segmento EIGRP | Cumplido | Core-1 hacia R-Occidente |
| Segmento RIP | Cumplido | Core-1 hacia R-Norte via Serial |
| Segmento con rutas estaticas | Cumplido | Core-1 hacia R-Central via Serial |
| Al menos 2 puntos de redistribucion | Cumplido | Core-1 y Core-2 |
| Distintos medios fisicos | Cumplido | Ethernet + Serial |

### 2.4 Subneteo del backbone - Red base: 10.0.0.0/8

Los enlaces punto a punto entre routers requieren exactamente 2 hosts utilizables. El prefijo /30 provee exactamente 2 hosts utilizables con la formula 2^2 - 2 = 2.

#### Tabla de enlaces del backbone

| Enlace | Protocolo | Subred | Mascara | IP extremo A | IP extremo B | Broadcast |
|---|---|---|---|---|---|---|
| Core-1 a Core-2 | OSPF | 10.0.0.0/30 | 255.255.255.252 | 10.0.0.1 (Core-1 G0/0) | 10.0.0.2 (Core-2 G0/0) | 10.0.0.3 |
| Core-1 a R-Occidente | EIGRP | 10.0.0.4/30 | 255.255.255.252 | 10.0.0.5 (Core-1 G0/2) | 10.0.0.6 (R-Occ G0/0) | 10.0.0.7 |
| Core-1 a R-Norte | RIP | 10.0.0.8/30 | 255.255.255.252 | 10.0.0.9 (Core-1 Se0/3/0 DCE) | 10.0.0.10 (R-Norte Se0/3/0 DTE) | 10.0.0.11 |
| Core-2 a R-Oriente1 | OSPF | 10.0.0.12/30 | 255.255.255.252 | 10.0.0.13 (Core-2 G0/2) | 10.0.0.14 (R-Ori1 G0/0) | 10.0.0.15 |
| Core-2 a R-Oriente2 | OSPF | 10.0.0.16/30 | 255.255.255.252 | 10.0.0.17 (Core-2 Se0/3/0 DCE) | 10.0.0.18 (R-Ori2 Se0/3/0 DTE) | 10.0.0.19 |
| Core-1 a R-Central | Estatico | 10.0.0.20/30 | 255.255.255.252 | 10.0.0.21 (Core-1 Se0/3/1 DCE) | 10.0.0.22 (R-Central Se0/3/0 DTE) | 10.0.0.23 |

### 2.5 Justificacion de protocolos por segmento

EIGRP hacia Occidente: Occidente es el centro logistico regional con mayor volumen de trafico (55+40 equipos). EIGRP es el protocolo Cisco mas eficiente en convergencia y uso de ancho de banda, apropiado para el segmento de mayor demanda.

RIP hacia Norte: Norte opera como centro de monitoreo remoto con menor volumen de trafico. RIP es suficiente para este segmento de menor escala y cumple el requerimiento sin sobredimensionar el protocolo. El enlace serial refuerza la simulacion de un enlace WAN hacia una sede remota.

OSPF hacia Oriente: Oriente requiere dos routers de borde con HSRP. OSPF maneja eficientemente topologias con multiples routers en el mismo dominio y converge rapido ante la caida de un equipo, complementando la redundancia HSRP.

Estatico hacia Central: Central es la sede principal con rutas predecibles y controladas. Una ruta estatica garantiza control total del trafico saliente sin depender de convergencia dinamica.

Redistribucion en Core-1 y Core-2: Al correr diferentes protocolos en cada segmento, los routers del nucleo redistribuyen rutas entre dominios para que todas las sedes puedan comunicarse entre si.

---

## Seccion 3 - Diseno interno de cada sede

### 3.1 Parametros VTP globales del proyecto

| Parametro | Valor |
|---|---|
| VTP Version | 2 |
| VTP Mode switch principal | Server |
| VTP Mode switches secundarios | Client |
| VTP Password | conred2026 |

Cada sede tiene su propio dominio VTP independiente para aislar la propagacion de VLANs entre sedes.

### 3.2 Metodo de inter-VLAN routing

Todas las sedes utilizan Router on a Stick como metodo de enrutamiento inter-VLAN. El router de borde de cada sede tiene una interfaz fisica conectada al switch principal mediante trunk. Sobre esa interfaz se crean subinterfaces virtuales, una por cada VLAN, y cada subinterfaz actua como el gateway de esa VLAN.

La excepcion es Sede Oriente donde se tienen dos routers de borde con HSRP, por lo que ambos routers tienen subinterfaces pero los equipos usan la IP virtual HSRP como gateway.

---

### 3.3 Sede Occidente

Contexto: centro logistico regional que coordina inventarios, personal operativo, seguridad y administracion regional.

Topologia elegida: arbol jerarquico de dos niveles. Un switch principal concentra todo el trafico y conecta al router de borde. Cuatro switches de acceso, uno por VLAN, conectan los equipos finales. Esta topologia responde a la necesidad de administracion centralizada, organizacion clara por departamentos y facilidad de mantenimiento.

#### Dispositivos de Sede Occidente

| Dispositivo | Tipo | Rol | VTP Mode |
|---|---|---|---|
| SW-OCC-Principal | Switch 2960 | Distribucion, VTP Server, trunk hacia router | Server |
| SW-OCC-Acc-Inv | Switch 2960 | Acceso VLAN 43 | Client |
| SW-OCC-Acc-Ops | Switch 2960 | Acceso VLAN 13 | Client |
| SW-OCC-Acc-Adm | Switch 2960 | Acceso VLAN 23 | Client |
| SW-OCC-Acc-Seg | Switch 2960 | Acceso VLAN 33 | Client |
| R-Occidente | Router 2911 | Borde, Router on a Stick, EIGRP hacia Core-1 | - |

#### Tabla de enlaces Sede Occidente

| Enlace | Puerto origen | Puerto destino | Tipo |
|---|---|---|---|
| R-Occidente a SW-OCC-Principal | G0/1 | Fa0/5 | Trunk 802.1Q |
| SW-OCC-Principal a SW-OCC-Acc-Inv | Fa0/1 | Fa0/1 | Trunk 802.1Q |
| SW-OCC-Principal a SW-OCC-Acc-Ops | Fa0/2 | Fa0/1 | Trunk 802.1Q |
| SW-OCC-Principal a SW-OCC-Acc-Adm | Fa0/3 | Fa0/1 | Trunk 802.1Q |
| SW-OCC-Principal a SW-OCC-Acc-Seg | Fa0/4 | Fa0/1 | Trunk 802.1Q |
| SW-OCC-Acc-Inv a PCs | Fa0/2, Fa0/3 | Fa0 PCs | Access VLAN 43 |
| SW-OCC-Acc-Ops a PCs | Fa0/2, Fa0/3 | Fa0 PCs | Access VLAN 13 |
| SW-OCC-Acc-Adm a PCs | Fa0/2, Fa0/3 | Fa0 PCs | Access VLAN 23 |
| SW-OCC-Acc-Seg a PCs | Fa0/2, Fa0/3 | Fa0 PCs | Access VLAN 33 |

#### Configuracion VTP Occidente

| Parametro | Valor |
|---|---|
| VTP Domain | CONRED-OCCIDENTE |
| VTP Password | conred2026 |
| VTP Server | SW-OCC-Principal |

#### Subinterfaces Router on a Stick R-Occidente

| Subinterfaz | VLAN | IP Gateway | Mascara |
|---|---|---|---|
| G0/1.13 | 13 - Operaciones | 172.16.0.65 | 255.255.255.192 |
| G0/1.23 | 23 - Administracion | 172.16.0.129 | 255.255.255.224 |
| G0/1.33 | 33 - Seguridad | 172.16.0.161 | 255.255.255.240 |
| G0/1.43 | 43 - Inventario | 172.16.0.1 | 255.255.255.192 |

Interfaz hacia backbone: G0/0 con IP 10.0.0.6/30 conectada a Core-1 G0/2.

---

### 3.4 Sede Norte

Contexto: centro regional de monitoreo y coordinacion remota con operaciones sensibles que requieren continuidad de servicio.

Topologia elegida: jerarquica con redundancia interna. SW-NOR-Core como switch raiz conectado a dos switches de distribucion. Los switches de distribucion estan interconectados entre si formando el enlace redundante. Cuatro switches de acceso cuelgan de los switches de distribucion. Se implementa Rapid PVST+ para gestionar los bucles que genera la redundancia.

#### Dispositivos de Sede Norte

| Dispositivo | Tipo | Rol | VTP Mode | STP Role |
|---|---|---|---|---|
| SW-NOR-Core | Switch 2960 | Switch principal, VTP Server, Root Bridge | Server | Root Bridge (priority 4096) |
| SW-NOR-Dist1 | Switch 2960 | Distribucion 1 | Client | Designated |
| SW-NOR-Dist2 | Switch 2960 | Distribucion 2 | Client | Designated |
| SW-NOR-Acc-Ops | Switch 2960 | Acceso VLAN 13 | Client | - |
| SW-NOR-Acc-Adm | Switch 2960 | Acceso VLAN 23 | Client | - |
| SW-NOR-Acc-Seg | Switch 2960 | Acceso VLAN 33 | Client | - |
| SW-NOR-Acc-Inv | Switch 2960 | Acceso VLAN 43 | Client | - |
| R-Norte | Router 2911 | Borde, Router on a Stick, RIP hacia Core-1 via Serial | - | - |

#### Tabla de enlaces Sede Norte

| Enlace | Puerto origen | Puerto destino | Tipo |
|---|---|---|---|
| R-Norte a SW-NOR-Core | G0/0 | Fa0/1 | Trunk 802.1Q |
| SW-NOR-Core a SW-NOR-Dist1 | Fa0/2 | Fa0/1 | Trunk 802.1Q |
| SW-NOR-Core a SW-NOR-Dist2 | Fa0/3 | Fa0/1 | Trunk 802.1Q |
| SW-NOR-Dist1 a SW-NOR-Dist2 | Fa0/2 | Fa0/2 | Trunk 802.1Q (bloqueado por STP) |
| SW-NOR-Dist1 a SW-NOR-Acc-Ops | Fa0/3 | Fa0/1 | Trunk 802.1Q |
| SW-NOR-Dist1 a SW-NOR-Acc-Adm | Fa0/4 | Fa0/1 | Trunk 802.1Q |
| SW-NOR-Dist2 a SW-NOR-Acc-Seg | Fa0/3 | Fa0/1 | Trunk 802.1Q |
| SW-NOR-Dist2 a SW-NOR-Acc-Inv | Fa0/4 | Fa0/1 | Trunk 802.1Q |

#### Configuracion VTP y STP Norte

| Parametro | Valor |
|---|---|
| VTP Domain | CONRED-NORTE |
| VTP Password | conred2026 |
| VTP Server | SW-NOR-Core |
| STP Mode | Rapid PVST+ |
| Root Bridge | SW-NOR-Core (priority 4096 para VLANs 13,23,33,43) |
| Puerto bloqueado por STP | SW-NOR-Dist1 Fa0/2 (enlace hacia Dist2) |

#### Subinterfaces Router on a Stick R-Norte

| Subinterfaz | VLAN | IP Gateway | Mascara |
|---|---|---|---|
| G0/0.13 | 13 - Operaciones | 172.17.0.1 | 255.255.255.192 |
| G0/0.23 | 23 - Administracion | 172.17.0.113 | 255.255.255.240 |
| G0/0.33 | 33 - Seguridad | 172.17.0.97 | 255.255.255.240 |
| G0/0.43 | 43 - Inventario | 172.17.0.65 | 255.255.255.224 |

Interfaz hacia backbone: Se0/3/0 con IP 10.0.0.10/30 conectada a Core-1 Se0/3/0. R-Norte es extremo DTE, no configura clock rate.

---

### 3.5 Sede Oriente

Contexto: sede de coordinacion operativa con procesos sensibles que no pueden perder acceso al gateway ante la caida de un router de borde.

Topologia elegida: jerarquica con dos routers de borde. SW-ORI-Principal conectado a ambos routers mediante trunks. Cuatro switches de acceso conectados al switch principal. Se implementa HSRP para garantizar disponibilidad del gateway ante la caida de cualquiera de los routers de borde.

#### Dispositivos de Sede Oriente

| Dispositivo | Tipo | Rol | VTP Mode | HSRP Role |
|---|---|---|---|---|
| SW-ORI-Principal | Switch 2960 | Switch principal, VTP Server | Server | - |
| SW-ORI-Acc-Ate | Switch 2960 | Acceso VLAN 53 | Client | - |
| SW-ORI-Acc-Adm | Switch 2960 | Acceso VLAN 23 | Client | - |
| SW-ORI-Acc-Seg | Switch 2960 | Acceso VLAN 33 | Client | - |
| SW-ORI-Acc-Inv | Switch 2960 | Acceso VLAN 43 | Client | - |
| R-Oriente1 | Router 2911 | Borde activo, HSRP Active priority 110, OSPF | - | Active |
| R-Oriente2 | Router 2911 | Borde standby, HSRP Standby priority 100, OSPF | - | Standby |

#### Tabla de enlaces Sede Oriente

| Enlace | Puerto origen | Puerto destino | Tipo |
|---|---|---|---|
| R-Oriente1 a SW-ORI-Principal | G0/1 | Fa0/1 | Trunk 802.1Q |
| R-Oriente2 a SW-ORI-Principal | G0/0 | Fa0/2 | Trunk 802.1Q |
| SW-ORI-Principal a SW-ORI-Acc-Ate | Fa0/3 | Fa0/1 | Trunk 802.1Q |
| SW-ORI-Principal a SW-ORI-Acc-Adm | Fa0/4 | Fa0/1 | Trunk 802.1Q |
| SW-ORI-Principal a SW-ORI-Acc-Seg | Fa0/5 | Fa0/1 | Trunk 802.1Q |
| SW-ORI-Principal a SW-ORI-Acc-Inv | Fa0/6 | Fa0/1 | Trunk 802.1Q |

#### Configuracion HSRP por VLAN

| VLAN | IP R-Oriente1 real | IP R-Oriente2 real | IP Virtual HSRP (gateway) | Prioridad Ori1 | Prioridad Ori2 |
|---|---|---|---|---|---|
| 53 - Atencion Regional | 172.18.0.2 | 172.18.0.3 | 172.18.0.1 | 110 | 100 |
| 43 - Inventario | 172.18.0.34 | 172.18.0.35 | 172.18.0.33 | 110 | 100 |
| 23 - Administracion | 172.18.0.66 | 172.18.0.67 | 172.18.0.65 | 110 | 100 |
| 33 - Seguridad | 172.18.0.98 | 172.18.0.99 | 172.18.0.97 | 110 | 100 |

#### Configuracion VTP Oriente

| Parametro | Valor |
|---|---|
| VTP Domain | CONRED-ORIENTE |
| VTP Password | conred2026 |
| VTP Server | SW-ORI-Principal |

Interfaces hacia backbone: R-Oriente1 G0/0 con IP 10.0.0.14/30 hacia Core-2 G0/2. R-Oriente2 Se0/3/0 con IP 10.0.0.18/30 hacia Core-2 Se0/3/0 (Core-2 es DCE).

---

### 3.6 Sede Central

Contexto: sede principal de servicios nacionales con administracion superior, monitoreo institucional, soporte y servicios criticos.

Topologia elegida: jerarquica redundante con malla parcial entre switches de distribucion. Dos switches de distribucion interconectados entre si. Cinco switches de acceso, uno por VLAN. Las VLANs criticas (63 y 83) tienen doble uplink hacia ambos switches de distribucion. Se implementa Rapid PVST+ para gestionar los bucles generados por la redundancia.

#### Dispositivos de Sede Central

| Dispositivo | Tipo | Rol | VTP Mode | STP Role |
|---|---|---|---|---|
| SW-CEN-Dist1 | Switch 2960 | Distribucion principal, VTP Server, Root Bridge | Server | Root Bridge (priority 4096) |
| SW-CEN-Dist2 | Switch 2960 | Distribucion secundaria, Backup Root | Client | Backup Root (priority 8192) |
| SW-CEN-Acc-Adm | Switch 2960 | Acceso VLAN 23 | Client | - |
| SW-CEN-Acc-Seg | Switch 2960 | Acceso VLAN 33 | Client | - |
| SW-CEN-Acc-Mon | Switch 2960 | Acceso VLAN 63, doble uplink | Client | - |
| SW-CEN-Acc-Sop | Switch 2960 | Acceso VLAN 73 | Client | - |
| SW-CEN-Acc-SC | Switch 2960 | Acceso VLAN 83, doble uplink | Client | - |
| R-Central | Router 2911 | Borde, Router on a Stick, ruta estatica hacia Core-1 | - | - |

#### Tabla de enlaces Sede Central

| Enlace | Puerto origen | Puerto destino | Tipo |
|---|---|---|---|
| R-Central a SW-CEN-Dist1 | G0/0 | Fa0/1 | Trunk 802.1Q |
| SW-CEN-Dist1 a SW-CEN-Dist2 | Fa0/2 | Fa0/1 | Trunk 802.1Q (STP gestiona) |
| SW-CEN-Dist1 a SW-CEN-Acc-Adm | Fa0/3 | Fa0/1 | Trunk 802.1Q |
| SW-CEN-Dist1 a SW-CEN-Acc-Seg | Fa0/4 | Fa0/1 | Trunk 802.1Q |
| SW-CEN-Dist1 a SW-CEN-Acc-Mon | Fa0/5 | Fa0/1 | Trunk 802.1Q (uplink principal) |
| SW-CEN-Dist1 a SW-CEN-Acc-Sop | Fa0/6 | Fa0/1 | Trunk 802.1Q |
| SW-CEN-Dist1 a SW-CEN-Acc-SC | Fa0/7 | Fa0/1 | Trunk 802.1Q (uplink principal) |
| SW-CEN-Dist2 a SW-CEN-Acc-Mon | Fa0/2 | Fa0/2 | Trunk 802.1Q (uplink redundante) |
| SW-CEN-Dist2 a SW-CEN-Acc-SC | Fa0/3 | Fa0/2 | Trunk 802.1Q (uplink redundante) |

#### Configuracion VTP y STP Central

| Parametro | Valor |
|---|---|
| VTP Domain | CONRED-CENTRAL |
| VTP Password | conred2026 |
| VTP Server | SW-CEN-Dist1 |
| STP Mode | Rapid PVST+ |
| Root Bridge principal | SW-CEN-Dist1 (priority 4096 para VLANs 23,33,63,73,83) |
| Root Bridge secundario | SW-CEN-Dist2 (priority 8192) |
| Gateway interno de VLANs | R-Central via subinterfaces en G0/0 |

#### Subinterfaces Router on a Stick R-Central

| Subinterfaz | VLAN | IP Gateway | Mascara |
|---|---|---|---|
| G0/0.23 | 23 - Administracion | 172.19.0.65 | 255.255.255.224 |
| G0/0.33 | 33 - Seguridad | 172.19.0.145 | 255.255.255.240 |
| G0/0.63 | 63 - Monitoreo y Control | 172.19.0.1 | 255.255.255.192 |
| G0/0.73 | 73 - Soporte | 172.19.0.129 | 255.255.255.240 |
| G0/0.83 | 83 - Servicios Criticos | 172.19.0.97 | 255.255.255.224 |

Interfaz hacia backbone: Se0/3/0 con IP 10.0.0.22/30 hacia Core-1 Se0/3/1. R-Central es extremo DTE, no configura clock rate.

---

### 3.7 Resumen general de dispositivos del proyecto

| Sede | Switches | Routers de borde | STP | VTP Domain |
|---|---|---|---|---|
| Occidente | 5 (1 principal + 4 acceso) | 1 (R-Occidente) | No requerido | CONRED-OCCIDENTE |
| Norte | 7 (1 core + 2 dist + 4 acceso) | 1 (R-Norte) | Rapid PVST+ | CONRED-NORTE |
| Oriente | 5 (1 principal + 4 acceso) | 2 (R-Ori1 + R-Ori2 con HSRP) | No requerido | CONRED-ORIENTE |
| Central | 7 (2 dist + 5 acceso) | 1 (R-Central) | Rapid PVST+ | CONRED-CENTRAL |
| Backbone | - | 7 (Core-1, Core-2, R-Occ, R-Nor, R-Ori1, R-Ori2, R-Cen) | - | - |
| Total | 24 switches | 7 routers | - | - |

---

## Seccion 4 - Configuraciones CLI

### 4.1 Core-1

```
hostname Core-1
no ip domain-lookup

interface GigabitEthernet0/0
 ip address 10.0.0.1 255.255.255.252
 no shutdown

interface GigabitEthernet0/2
 ip address 10.0.0.5 255.255.255.252
 no shutdown

interface Serial0/3/0
 ip address 10.0.0.9 255.255.255.252
 clock rate 64000
 no shutdown

interface Serial0/3/1
 ip address 10.0.0.21 255.255.255.252
 clock rate 64000
 no shutdown

router eigrp 100
 network 10.0.0.4 0.0.0.3
 network 10.0.0.0 0.0.0.3
 no auto-summary

router rip
 version 2
 network 10.0.0.8
 network 10.0.0.0
 no auto-summary

router ospf 1
 network 10.0.0.0 0.0.0.3 area 0
 redistribute eigrp 100 subnets
 redistribute rip subnets
 redistribute static subnets
 redistribute connected subnets

router eigrp 100
 redistribute ospf 1 metric 10000 100 255 1 1500
 redistribute rip metric 10000 100 255 1 1500
 redistribute static metric 10000 100 255 1 1500
 redistribute connected metric 10000 100 255 1 1500

router rip
 redistribute ospf 1 metric 5
 redistribute eigrp 100 metric 5
 redistribute static metric 5
 redistribute connected metric 5

ip route 172.19.0.0 255.255.255.192 10.0.0.22
ip route 172.19.0.64 255.255.255.224 10.0.0.22
ip route 172.19.0.96 255.255.255.224 10.0.0.22
ip route 172.19.0.128 255.255.255.240 10.0.0.22
ip route 172.19.0.144 255.255.255.240 10.0.0.22
```

### 4.2 Core-2

```
hostname Core-2
no ip domain-lookup

interface GigabitEthernet0/0
 ip address 10.0.0.2 255.255.255.252
 no shutdown

interface GigabitEthernet0/2
 ip address 10.0.0.13 255.255.255.252
 no shutdown

interface Serial0/3/0
 ip address 10.0.0.17 255.255.255.252
 clock rate 64000
 no shutdown

router ospf 1
 network 10.0.0.0 0.0.0.3 area 0
 network 10.0.0.12 0.0.0.3 area 0
 network 10.0.0.16 0.0.0.3 area 0
 redistribute connected subnets
 redistribute static subnets
```

### 4.3 R-Occidente

```
hostname R-Occidente
no ip domain-lookup

interface GigabitEthernet0/0
 ip address 10.0.0.6 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 no shutdown

interface GigabitEthernet0/1.13
 encapsulation dot1Q 13
 ip address 172.16.0.65 255.255.255.192
 no shutdown

interface GigabitEthernet0/1.23
 encapsulation dot1Q 23
 ip address 172.16.0.129 255.255.255.224
 no shutdown

interface GigabitEthernet0/1.33
 encapsulation dot1Q 33
 ip address 172.16.0.161 255.255.255.240
 no shutdown

interface GigabitEthernet0/1.43
 encapsulation dot1Q 43
 ip address 172.16.0.1 255.255.255.192
 no shutdown

router eigrp 100
 network 10.0.0.4 0.0.0.3
 network 172.16.0.0 0.0.0.63
 network 172.16.0.64 0.0.0.63
 network 172.16.0.128 0.0.0.31
 network 172.16.0.160 0.0.0.15
 no auto-summary
```

### 4.4 R-Norte

```
hostname R-Norte
no ip domain-lookup

interface Serial0/3/0
 ip address 10.0.0.10 255.255.255.252
 no shutdown

interface GigabitEthernet0/0
 no shutdown

interface GigabitEthernet0/0.13
 encapsulation dot1Q 13
 ip address 172.17.0.1 255.255.255.192
 no shutdown

interface GigabitEthernet0/0.23
 encapsulation dot1Q 23
 ip address 172.17.0.113 255.255.255.240
 no shutdown

interface GigabitEthernet0/0.33
 encapsulation dot1Q 33
 ip address 172.17.0.97 255.255.255.240
 no shutdown

interface GigabitEthernet0/0.43
 encapsulation dot1Q 43
 ip address 172.17.0.65 255.255.255.224
 no shutdown

router rip
 version 2
 network 10.0.0.8
 network 172.17.0.0
 no auto-summary
```

### 4.5 R-Oriente1

```
hostname R-Oriente1
no ip domain-lookup

interface GigabitEthernet0/0
 ip address 10.0.0.14 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 no shutdown

interface GigabitEthernet0/1.53
 encapsulation dot1Q 53
 ip address 172.18.0.2 255.255.255.224
 standby 1 ip 172.18.0.1
 standby 1 priority 110
 standby 1 preempt
 no shutdown

interface GigabitEthernet0/1.43
 encapsulation dot1Q 43
 ip address 172.18.0.34 255.255.255.224
 standby 1 ip 172.18.0.33
 standby 1 priority 110
 standby 1 preempt
 no shutdown

interface GigabitEthernet0/1.23
 encapsulation dot1Q 23
 ip address 172.18.0.66 255.255.255.224
 standby 1 ip 172.18.0.65
 standby 1 priority 110
 standby 1 preempt
 no shutdown

interface GigabitEthernet0/1.33
 encapsulation dot1Q 33
 ip address 172.18.0.98 255.255.255.240
 standby 1 ip 172.18.0.97
 standby 1 priority 110
 standby 1 preempt
 no shutdown

router ospf 1
 network 10.0.0.12 0.0.0.3 area 0
 network 172.18.0.0 0.0.0.31 area 0
 network 172.18.0.32 0.0.0.31 area 0
 network 172.18.0.64 0.0.0.31 area 0
 network 172.18.0.96 0.0.0.15 area 0
```

### 4.6 R-Oriente2

```
hostname R-Oriente2
no ip domain-lookup

interface Serial0/3/0
 ip address 10.0.0.18 255.255.255.252
 no shutdown

interface GigabitEthernet0/0
 no shutdown

interface GigabitEthernet0/0.53
 encapsulation dot1Q 53
 ip address 172.18.0.3 255.255.255.224
 standby 1 ip 172.18.0.1
 standby 1 priority 100
 no shutdown

interface GigabitEthernet0/0.43
 encapsulation dot1Q 43
 ip address 172.18.0.35 255.255.255.224
 standby 1 ip 172.18.0.33
 standby 1 priority 100
 no shutdown

interface GigabitEthernet0/0.23
 encapsulation dot1Q 23
 ip address 172.18.0.67 255.255.255.224
 standby 1 ip 172.18.0.65
 standby 1 priority 100
 no shutdown

interface GigabitEthernet0/0.33
 encapsulation dot1Q 33
 ip address 172.18.0.99 255.255.255.240
 standby 1 ip 172.18.0.97
 standby 1 priority 100
 no shutdown

router ospf 1
 network 10.0.0.16 0.0.0.3 area 0
 network 172.18.0.0 0.0.0.31 area 0
 network 172.18.0.32 0.0.0.31 area 0
 network 172.18.0.64 0.0.0.31 area 0
 network 172.18.0.96 0.0.0.15 area 0
```

### 4.7 R-Central

```
hostname R-Central
no ip domain-lookup

interface Serial0/3/0
 ip address 10.0.0.22 255.255.255.252
 no shutdown

ip route 0.0.0.0 0.0.0.0 10.0.0.21

interface GigabitEthernet0/0
 no shutdown

interface GigabitEthernet0/0.23
 encapsulation dot1Q 23
 ip address 172.19.0.65 255.255.255.224
 no shutdown

interface GigabitEthernet0/0.33
 encapsulation dot1Q 33
 ip address 172.19.0.145 255.255.255.240
 no shutdown

interface GigabitEthernet0/0.63
 encapsulation dot1Q 63
 ip address 172.19.0.1 255.255.255.192
 no shutdown

interface GigabitEthernet0/0.73
 encapsulation dot1Q 73
 ip address 172.19.0.129 255.255.255.240
 no shutdown

interface GigabitEthernet0/0.83
 encapsulation dot1Q 83
 ip address 172.19.0.97 255.255.255.224
 no shutdown
```

### 4.8 SW-OCC-Principal

```
hostname SW-OCC-Principal
no ip domain-lookup
vtp mode server
vtp domain CONRED-OCCIDENTE
vtp password conred2026
vtp version 2
vlan 13
 name Operaciones-OCC
vlan 23
 name Administracion-OCC
vlan 33
 name Seguridad-OCC
vlan 43
 name Inventario-OCC
interface FastEthernet0/5
 switchport mode trunk
interface FastEthernet0/1
 switchport mode trunk
interface FastEthernet0/2
 switchport mode trunk
interface FastEthernet0/3
 switchport mode trunk
interface FastEthernet0/4
 switchport mode trunk
```

### 4.9 Switches de acceso Occidente (patron por switch)

```
-- SW-OCC-Acc-Inv (reemplazar nombre y VLAN segun switch) --
hostname SW-OCC-Acc-Inv
no ip domain-lookup
vtp mode client
vtp domain CONRED-OCCIDENTE
vtp password conred2026
vtp version 2
interface FastEthernet0/1
 switchport mode trunk
interface FastEthernet0/2
 switchport mode access
 switchport access vlan 43
interface FastEthernet0/3
 switchport mode access
 switchport access vlan 43

-- Misma estructura para SW-OCC-Acc-Ops (vlan 13) --
-- Misma estructura para SW-OCC-Acc-Adm (vlan 23) --
-- Misma estructura para SW-OCC-Acc-Seg (vlan 33) --
```

### 4.10 SW-NOR-Core

```
hostname SW-NOR-Core
no ip domain-lookup
vtp mode server
vtp domain CONRED-NORTE
vtp password conred2026
vtp version 2
vlan 13
 name Operaciones-NOR
vlan 23
 name Administracion-NOR
vlan 33
 name Seguridad-NOR
vlan 43
 name Inventario-NOR
spanning-tree mode rapid-pvst
spanning-tree vlan 13,23,33,43 priority 4096
interface FastEthernet0/1
 switchport mode trunk
interface FastEthernet0/2
 switchport mode trunk
interface FastEthernet0/3
 switchport mode trunk
```

### 4.11 SW-CEN-Dist1

```
hostname SW-CEN-Dist1
no ip domain-lookup
vtp mode server
vtp domain CONRED-CENTRAL
vtp password conred2026
vtp version 2
vlan 23
 name Administracion-CEN
vlan 33
 name Seguridad-CEN
vlan 63
 name Monitoreo-Control-CEN
vlan 73
 name Soporte-CEN
vlan 83
 name Servicios-Criticos-CEN
spanning-tree mode rapid-pvst
spanning-tree vlan 23,33,63,73,83 priority 4096
interface FastEthernet0/1
 switchport mode trunk
interface FastEthernet0/2
 switchport mode trunk
interface FastEthernet0/3
 switchport mode trunk
interface FastEthernet0/4
 switchport mode trunk
interface FastEthernet0/5
 switchport mode trunk
interface FastEthernet0/6
 switchport mode trunk
interface FastEthernet0/7
 switchport mode trunk
```

---

## Seccion 5 - Capturas de implementacion

Instrucciones: agregar las capturas en la carpeta Images del repositorio con los nombres indicados a continuacion.

### 5.1 Topologia general

| Nombre de archivo | Contenido de la captura |
|---|---|
| Images/TopologiaCompleta.png | Vista completa del area de trabajo en Packet Tracer mostrando backbone y las cuatro sedes |
| Images/TopologiaBackbone.png | Zoom al backbone mostrando los 7 routers y sus conexiones |
| Images/TopologiaOccidente.png | Zoom a Sede Occidente mostrando switches y PCs |
| Images/TopologiaNorte.png | Zoom a Sede Norte mostrando switches con redundancia |
| Images/TopologiaOriente.png | Zoom a Sede Oriente mostrando los dos routers de borde |
| Images/TopologiaCentral.png | Zoom a Sede Central mostrando switches de distribucion y acceso |

### 5.2 Capturas de verificacion de backbone

| Nombre de archivo | Comando ejecutado | Dispositivo |
|---|---|---|
| Images/RouteCore1.png | show ip route | Core-1 |
| Images/RouteCore2.png | show ip route | Core-2 |
| Images/EigrpNeighbors.png | show ip eigrp neighbors | Core-1 o R-Occidente |
| Images/OspfNeighbors.png | show ip ospf neighbor | Core-2 o R-Oriente1 |
| Images/RipDatabase.png | show ip rip database | R-Norte |
| Images/RouteROccidente.png | show ip route | R-Occidente |
| Images/RouteROriente1.png | show ip route | R-Oriente1 |

### 5.3 Capturas de verificacion de VLANs y VTP

| Nombre de archivo | Comando ejecutado | Dispositivo |
|---|---|---|
| Images/VlanOccidente.png | show vlan brief | SW-OCC-Principal |
| Images/VlanNorte.png | show vlan brief | SW-NOR-Core |
| Images/VlanOriente.png | show vlan brief | SW-ORI-Principal |
| Images/VlanCentral.png | show vlan brief | SW-CEN-Dist1 |
| Images/TrunkOccidente.png | show interfaces trunk | SW-OCC-Principal |
| Images/TrunkNorte.png | show interfaces trunk | SW-NOR-Core |
| Images/VtpOccidente.png | show vtp status | SW-OCC-Principal |

### 5.4 Capturas de redundancia

| Nombre de archivo | Comando ejecutado | Dispositivo |
|---|---|---|
| Images/HsrpOriente.png | show standby brief | R-Oriente1 |
| Images/StpNorte.png | show spanning-tree vlan 13 | SW-NOR-Core |
| Images/StpNorteDist1.png | show spanning-tree vlan 13 | SW-NOR-Dist1 |
| Images/StpCentral.png | show spanning-tree vlan 63 | SW-CEN-Dist1 |
| Images/StpCentralMon.png | show spanning-tree vlan 63 | SW-CEN-Acc-Mon |

### 5.5 Capturas de pruebas de conectividad

| Nombre de archivo | Descripcion | Desde | Hacia |
|---|---|---|---|
| Images/PingIntraOccidente.png | Ping entre VLANs dentro de Occidente | PC6 (VLAN 23) | PC2 (VLAN 43) |
| Images/PingIntraNorte.png | Ping entre VLANs dentro de Norte | PC8 (VLAN 13) | PC14 (VLAN 43) |
| Images/PingIntraOriente.png | Ping entre VLANs dentro de Oriente | PC16 (VLAN 53) | PC22 (VLAN 43) |
| Images/PingIntraCentral.png | Ping entre VLANs dentro de Central | PC28 (VLAN 63) | PC24 (VLAN 83) |
| Images/PingOccidenteCentral.png | Ping entre sedes Occidente hacia Central | PC6 | 172.19.0.2 |
| Images/PingOccidenteNorte.png | Ping entre sedes Occidente hacia Norte | PC6 | 172.17.0.2 |
| Images/PingOccidenteOriente.png | Ping entre sedes Occidente hacia Oriente | PC6 | 172.18.0.4 |
| Images/PingNorteCentral.png | Ping entre sedes Norte hacia Central | PC8 | 172.19.0.2 |

---

## Seccion 6 - Pruebas de conectividad realizadas

### 6.1 Pruebas intra-sede (inter-VLAN)

| Prueba | Origen | Destino | Resultado |
|---|---|---|---|
| Inter-VLAN Occidente | PC6 172.16.0.130 (VLAN 23) | PC2 172.16.0.2 (VLAN 43) | Insertar resultado |
| Inter-VLAN Norte | PC8 172.17.0.2 (VLAN 13) | PC14 172.17.0.66 (VLAN 43) | Exitoso 4/4 |
| Inter-VLAN Oriente | PC16 172.18.0.4 (VLAN 53) | PC22 172.18.0.36 (VLAN 43) | Exitoso 4/4 |
| Inter-VLAN Central | PC28 172.19.0.2 (VLAN 63) | PC24 172.19.0.98 (VLAN 83) | Exitoso 4/4 |

### 6.2 Pruebas inter-sede (a traves del backbone)

| Prueba | Origen | Destino | Resultado |
|---|---|---|---|
| Occidente hacia Central | PC6 172.16.0.130 | 172.19.0.2 | Exitoso 4/4 |
| Occidente hacia Norte | PC6 172.16.0.130 | 172.17.0.2 | Exitoso 3/4 (primer timeout normal) |
| Occidente hacia Oriente | PC6 172.16.0.130 | 172.18.0.4 | Exitoso 4/4 |

---

*Documento tecnico del Proyecto 2 - Red Nacional de Coordinacion SE-CONRED*
*Redes de Computadoras 1 - Universidad San Carlos de Guatemala*