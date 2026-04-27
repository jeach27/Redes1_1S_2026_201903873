# Proyecto 2: Red Nacional de Coordinación SE-CONRED
**Universidad San Carlos de Guatemala**  
**Facultad de Ingeniería – Ingeniería en Ciencias y Sistemas**  
**Redes de Computadoras 1**

---

## Datos del estudiante

Joaquin Emmanuel Aldair Coromac Huezo
201903873

| Campo | Valor |
|---|---|
| Últimos dos dígitos del carnet (XX) | 73 |
| Último dígito del carnet (Y) | 3 |

---

## Tabla de VLANs del proyecto

| VLAN | ID (con Y=3) | Occidente | Norte | Oriente | Central |
|---|---|---|---|---|---|
| Operaciones | 13 | 40 equipos | 30 equipos | — | — |
| Administración | 23 | 18 equipos | 12 equipos | 19 equipos | 20 equipos |
| Seguridad | 33 | 8 equipos | 10 equipos | 8 equipos | 9 equipos |
| Inventario | 43 | 55 equipos | 15 equipos | 21 equipos | — |
| Atención Regional | 53 | — | — | 28 equipos | — |
| Monitoreo y Control | 63 | — | — | — | 45 equipos |
| Soporte | 73 | — | — | — | 10 equipos |
| Servicios Críticos | 83 | — | — | — | 16 equipos |

---

## Redes base asignadas por sede

| Sede | Red Base |
|---|---|
| Occidente | 172.16.0.0/16 |
| Norte | 172.17.0.0/16 |
| Oriente | 172.18.0.0/16 |
| Central | 172.19.0.0/16 |
| Backbone | 10.0.0.0/8 |

> **Criterio de subneteo:** Se aplica VLSM (Variable Length Subnet Mask), ordenando las VLANs de mayor a menor cantidad de hosts para aprovechar eficientemente el espacio de direcciones. Se reserva 1 dirección para el gateway (primera usable) y se considera margen de crecimiento al elegir el prefijo.

---

## Paso 1 – Subneteo VLSM

### Metodología VLSM aplicada

Para cada sede se ordenan las VLANs de **mayor a menor** número de hosts requeridos. Luego se asigna la subred más pequeña que contenga todos los hosts + gateway + broadcast. La fórmula es:

```
Hosts utilizables = 2^n - 2   (donde n = bits de host)
Se elige el prefijo tal que: 2^n - 2 >= equipos_requeridos
```

---

### 1.1 Sede Occidente — Red base: 172.16.0.0/16

**VLANs ordenadas de mayor a menor:**

| # | VLAN | ID | Equipos | Hosts necesarios | Prefijo elegido | Hosts utilizables |
|---|---|---|---|---|---|---|
| 1 | Inventario | 43 | 55 | 55+1 gw = 56 | /26 | 62 |
| 2 | Operaciones | 13 | 40 | 40+1 gw = 41 | /26 | 62 |
| 3 | Administración | 23 | 18 | 18+1 gw = 19 | /27 | 30 |
| 4 | Seguridad | 33 | 8 | 8+1 gw = 9 | /28 | 14 |

**Asignación de subredes:**

#### VLAN 43 – Inventario
| Campo | Valor |
|---|---|
| Subred | 172.16.0.0/26 |
| Máscara | 255.255.255.192 |
| Gateway (primera usable) | 172.16.0.1 |
| Rango utilizable | 172.16.0.1 – 172.16.0.62 |
| Broadcast | 172.16.0.63 |
| Hosts disponibles | 62 |

#### VLAN 13 – Operaciones
| Campo | Valor |
|---|---|
| Subred | 172.16.0.64/26 |
| Máscara | 255.255.255.192 |
| Gateway (primera usable) | 172.16.0.65 |
| Rango utilizable | 172.16.0.65 – 172.16.0.126 |
| Broadcast | 172.16.0.127 |
| Hosts disponibles | 62 |

#### VLAN 23 – Administración
| Campo | Valor |
|---|---|
| Subred | 172.16.0.128/27 |
| Máscara | 255.255.255.224 |
| Gateway (primera usable) | 172.16.0.129 |
| Rango utilizable | 172.16.0.129 – 172.16.0.158 |
| Broadcast | 172.16.0.159 |
| Hosts disponibles | 30 |

#### VLAN 33 – Seguridad
| Campo | Valor |
|---|---|
| Subred | 172.16.0.160/28 |
| Máscara | 255.255.255.240 |
| Gateway (primera usable) | 172.16.0.161 |
| Rango utilizable | 172.16.0.161 – 172.16.0.174 |
| Broadcast | 172.16.0.175 |
| Hosts disponibles | 14 |

---

### 1.2 Sede Norte — Red base: 172.17.0.0/16

**VLANs ordenadas de mayor a menor:**

| # | VLAN | ID | Equipos | Hosts necesarios | Prefijo elegido | Hosts utilizables |
|---|---|---|---|---|---|---|
| 1 | Operaciones | 13 | 30 | 30+1 gw = 31 | /27 | 30 ⚠ exacto — usar /26 |
| 2 | Inventario | 43 | 15 | 15+1 gw = 16 | /27 | 30 |
| 3 | Seguridad | 33 | 10 | 10+1 gw = 11 | /28 | 14 |
| 4 | Administración | 23 | 12 | 12+1 gw = 13 | /28 | 14 |

> **Nota sobre Operaciones Norte:** Con 30 equipos + 1 gateway = 31 hosts mínimos. Un /27 da exactamente 30 utilizables, lo cual no alcanza. Se asigna **/26 (62 hosts)** para dejar margen de crecimiento.

**Asignación de subredes:**

#### VLAN 13 – Operaciones
| Campo | Valor |
|---|---|
| Subred | 172.17.0.0/26 |
| Máscara | 255.255.255.192 |
| Gateway (primera usable) | 172.17.0.1 |
| Rango utilizable | 172.17.0.1 – 172.17.0.62 |
| Broadcast | 172.17.0.63 |
| Hosts disponibles | 62 |

#### VLAN 43 – Inventario
| Campo | Valor |
|---|---|
| Subred | 172.17.0.64/27 |
| Máscara | 255.255.255.224 |
| Gateway (primera usable) | 172.17.0.65 |
| Rango utilizable | 172.17.0.65 – 172.17.0.94 |
| Broadcast | 172.17.0.95 |
| Hosts disponibles | 30 |

#### VLAN 33 – Seguridad
| Campo | Valor |
|---|---|
| Subred | 172.17.0.96/28 |
| Máscara | 255.255.255.240 |
| Gateway (primera usable) | 172.17.0.97 |
| Rango utilizable | 172.17.0.97 – 172.17.0.110 |
| Broadcast | 172.17.0.111 |
| Hosts disponibles | 14 |

#### VLAN 23 – Administración
| Campo | Valor |
|---|---|
| Subred | 172.17.0.112/28 |
| Máscara | 255.255.255.240 |
| Gateway (primera usable) | 172.17.0.113 |
| Rango utilizable | 172.17.0.113 – 172.17.0.126 |
| Broadcast | 172.17.0.127 |
| Hosts disponibles | 14 |

---

### 1.3 Sede Oriente — Red base: 172.18.0.0/16

**VLANs ordenadas de mayor a menor:**

| # | VLAN | ID | Equipos | Hosts necesarios | Prefijo elegido | Hosts utilizables |
|---|---|---|---|---|---|---|
| 1 | Atención Regional | 53 | 28 | 28+1 gw = 29 | /27 | 30 |
| 2 | Inventario | 43 | 21 | 21+1 gw = 22 | /27 | 30 |
| 3 | Administración | 23 | 19 | 19+1 gw = 20 | /27 | 30 |
| 4 | Seguridad | 33 | 8 | 8+1 gw = 9 | /28 | 14 |

**Asignación de subredes:**

#### VLAN 53 – Atención Regional
| Campo | Valor |
|---|---|
| Subred | 172.18.0.0/27 |
| Máscara | 255.255.255.224 |
| Gateway (primera usable) | 172.18.0.1 |
| Rango utilizable | 172.18.0.1 – 172.18.0.30 |
| Broadcast | 172.18.0.31 |
| Hosts disponibles | 30 |

#### VLAN 43 – Inventario
| Campo | Valor |
|---|---|
| Subred | 172.18.0.32/27 |
| Máscara | 255.255.255.224 |
| Gateway (primera usable) | 172.18.0.33 |
| Rango utilizable | 172.18.0.33 – 172.18.0.62 |
| Broadcast | 172.18.0.63 |
| Hosts disponibles | 30 |

#### VLAN 23 – Administración
| Campo | Valor |
|---|---|
| Subred | 172.18.0.64/27 |
| Máscara | 255.255.255.224 |
| Gateway (primera usable) | 172.18.0.65 |
| Rango utilizable | 172.18.0.65 – 172.18.0.94 |
| Broadcast | 172.18.0.95 |
| Hosts disponibles | 30 |

#### VLAN 33 – Seguridad
| Campo | Valor |
|---|---|
| Subred | 172.18.0.96/28 |
| Máscara | 255.255.255.240 |
| Gateway (primera usable) | 172.18.0.97 |
| Rango utilizable | 172.18.0.97 – 172.18.0.110 |
| Broadcast | 172.18.0.111 |
| Hosts disponibles | 14 |

---

### 1.4 Sede Central — Red base: 172.19.0.0/16

**VLANs ordenadas de mayor a menor:**

| # | VLAN | ID | Equipos | Hosts necesarios | Prefijo elegido | Hosts utilizables |
|---|---|---|---|---|---|---|
| 1 | Monitoreo y Control | 63 | 45 | 45+1 gw = 46 | /26 | 62 |
| 2 | Administración | 23 | 20 | 20+1 gw = 21 | /27 | 30 |
| 3 | Servicios Críticos | 83 | 16 | 16+1 gw = 17 | /27 | 30 |
| 4 | Soporte | 73 | 10 | 10+1 gw = 11 | /28 | 14 |
| 5 | Seguridad | 33 | 9 | 9+1 gw = 10 | /28 | 14 |

**Asignación de subredes:**

#### VLAN 63 – Monitoreo y Control
| Campo | Valor |
|---|---|
| Subred | 172.19.0.0/26 |
| Máscara | 255.255.255.192 |
| Gateway (primera usable) | 172.19.0.1 |
| Rango utilizable | 172.19.0.1 – 172.19.0.62 |
| Broadcast | 172.19.0.63 |
| Hosts disponibles | 62 |

#### VLAN 23 – Administración
| Campo | Valor |
|---|---|
| Subred | 172.19.0.64/27 |
| Máscara | 255.255.255.224 |
| Gateway (primera usable) | 172.19.0.65 |
| Rango utilizable | 172.19.0.65 – 172.19.0.94 |
| Broadcast | 172.19.0.95 |
| Hosts disponibles | 30 |

#### VLAN 83 – Servicios Críticos
| Campo | Valor |
|---|---|
| Subred | 172.19.0.96/27 |
| Máscara | 255.255.255.224 |
| Gateway (primera usable) | 172.19.0.97 |
| Rango utilizable | 172.19.0.97 – 172.19.0.126 |
| Broadcast | 172.19.0.127 |
| Hosts disponibles | 30 |

#### VLAN 73 – Soporte
| Campo | Valor |
|---|---|
| Subred | 172.19.0.128/28 |
| Máscara | 255.255.255.240 |
| Gateway (primera usable) | 172.19.0.129 |
| Rango utilizable | 172.19.0.129 – 172.19.0.142 |
| Broadcast | 172.19.0.143 |
| Hosts disponibles | 14 |

#### VLAN 33 – Seguridad
| Campo | Valor |
|---|---|
| Subred | 172.19.0.144/28 |
| Máscara | 255.255.255.240 |
| Gateway (primera usable) | 172.19.0.145 |
| Rango utilizable | 172.19.0.145 – 172.19.0.158 |
| Broadcast | 172.19.0.159 |
| Hosts disponibles | 14 |

---

## Resumen consolidado de subneteo VLSM

### Sede Occidente (172.16.0.0/16)

| VLAN | ID | Subred | Máscara | Gateway | Rango Utilizable | Broadcast |
|---|---|---|---|---|---|---|
| Inventario | 43 | 172.16.0.0/26 | 255.255.255.192 | 172.16.0.1 | 172.16.0.2 – 172.16.0.62 | 172.16.0.63 |
| Operaciones | 13 | 172.16.0.64/26 | 255.255.255.192 | 172.16.0.65 | 172.16.0.66 – 172.16.0.126 | 172.16.0.127 |
| Administración | 23 | 172.16.0.128/27 | 255.255.255.224 | 172.16.0.129 | 172.16.0.130 – 172.16.0.158 | 172.16.0.159 |
| Seguridad | 33 | 172.16.0.160/28 | 255.255.255.240 | 172.16.0.161 | 172.16.0.162 – 172.16.0.174 | 172.16.0.175 |

### Sede Norte (172.17.0.0/16)

| VLAN | ID | Subred | Máscara | Gateway | Rango Utilizable | Broadcast |
|---|---|---|---|---|---|---|
| Operaciones | 13 | 172.17.0.0/26 | 255.255.255.192 | 172.17.0.1 | 172.17.0.2 – 172.17.0.62 | 172.17.0.63 |
| Inventario | 43 | 172.17.0.64/27 | 255.255.255.224 | 172.17.0.65 | 172.17.0.66 – 172.17.0.94 | 172.17.0.95 |
| Seguridad | 33 | 172.17.0.96/28 | 255.255.255.240 | 172.17.0.97 | 172.17.0.98 – 172.17.0.110 | 172.17.0.111 |
| Administración | 23 | 172.17.0.112/28 | 255.255.255.240 | 172.17.0.113 | 172.17.0.114 – 172.17.0.126 | 172.17.0.127 |

### Sede Oriente (172.18.0.0/16)

| VLAN | ID | Subred | Máscara | Gateway | Rango Utilizable | Broadcast |
|---|---|---|---|---|---|---|
| Atención Regional | 53 | 172.18.0.0/27 | 255.255.255.224 | 172.18.0.1 | 172.18.0.2 – 172.18.0.30 | 172.18.0.31 |
| Inventario | 43 | 172.18.0.32/27 | 255.255.255.224 | 172.18.0.33 | 172.18.0.34 – 172.18.0.62 | 172.18.0.63 |
| Administración | 23 | 172.18.0.64/27 | 255.255.255.224 | 172.18.0.65 | 172.18.0.66 – 172.18.0.94 | 172.18.0.95 |
| Seguridad | 33 | 172.18.0.96/28 | 255.255.255.240 | 172.18.0.97 | 172.18.0.98 – 172.18.0.110 | 172.18.0.111 |

### Sede Central (172.19.0.0/16)

| VLAN | ID | Subred | Máscara | Gateway | Rango Utilizable | Broadcast |
|---|---|---|---|---|---|---|
| Monitoreo y Control | 63 | 172.19.0.0/26 | 255.255.255.192 | 172.19.0.1 | 172.19.0.2 – 172.19.0.62 | 172.19.0.63 |
| Administración | 23 | 172.19.0.64/27 | 255.255.255.224 | 172.19.0.65 | 172.19.0.66 – 172.19.0.94 | 172.19.0.95 |
| Servicios Críticos | 83 | 172.19.0.96/27 | 255.255.255.224 | 172.19.0.97 | 172.19.0.98 – 172.19.0.126 | 172.19.0.127 |
| Soporte | 73 | 172.19.0.128/28 | 255.255.255.240 | 172.19.0.129 | 172.19.0.130 – 172.19.0.142 | 172.19.0.143 |
| Seguridad | 33 | 172.19.0.144/28 | 255.255.255.240 | 172.19.0.145 | 172.19.0.146 – 172.19.0.158 | 172.19.0.159 |

---

---

## Paso 2 – Diseño del Backbone Nacional

### 2.1 Justificación del backbone

El backbone es la infraestructura de capa 3 que interconecta las cuatro sedes regionales de SE-CONRED. Opera como núcleo de enrutamiento central, garantizando que el tráfico entre sedes se enrute de forma eficiente, redundante y con tolerancia a fallos.

Se diseñó con **7 routers** distribuidos en roles específicos para cumplir todos los requerimientos del enunciado.

---

### 2.2 Dispositivos del backbone y sus roles

| Dispositivo | Rol | Protocolo(s) | Sede vinculada |
|---|---|---|---|
| Core-1 | Núcleo principal — punto central de redistribución | OSPF + redistribuye EIGRP y RIP | — |
| Core-2 | Núcleo redundante — segundo punto de redistribución | OSPF + redistribuye EIGRP y RIP | — |
| R-Occidente | Router de borde de sede | EIGRP (hacia Core) | Sede Occidente |
| R-Norte | Router de borde de sede | RIP (hacia Core) | Sede Norte |
| R-Oriente1 | Router de borde activo (HSRP activo) | OSPF (hacia Core) | Sede Oriente |
| R-Oriente2 | Router de borde standby (HSRP standby) | OSPF (hacia Core) | Sede Oriente |
| R-Central | Router de borde de sede | Ruta estática hacia Core-1 | Sede Central |

---

### 2.3 Topología lógica del backbone

```
        ┌─────────────────────────────────────────┐
        │         NÚCLEO PRINCIPAL (Core)          │
        │                                          │
        │   [Core-1] ══════════════ [Core-2]       │
        │     │    EtherChannel (fibra óptica)  │  │
        │     │    (redundancia de enlace)      │  │
        └─────┼────────────────────────────────┼──┘
              │                                │
    ┌─────────┴──┐                    ┌────────┴────────┐
    │            │                    │                 │
[R-Occ]     [R-Norte]          [R-Ori1]           [R-Ori2]
 EIGRP         RIP               OSPF               OSPF
  │             │               (HSRP activo)   (HSRP standby)
  │             │                    └──────┬──────┘
Sede        Sede                          Sede
Occidente   Norte                        Oriente

                        [R-Central] ──── Core-1
                        (ruta estática)
                             │
                          Sede
                          Central
```

---

### 2.4 Segmentos de enrutamiento y medios físicos

| Segmento | Protocolo | Medio físico | Justificación del medio |
|---|---|---|---|
| Core-1 ↔ Core-2 | EtherChannel (agregación) | Fibra óptica | Alta velocidad y redundancia entre núcleos |
| Core-1 ↔ R-Occidente | EIGRP | Ethernet | Enlace de área local de alta velocidad |
| Core-1 ↔ R-Norte | RIP | Serial | Simula enlace WAN de área extendida |
| Core-2 ↔ R-Oriente1 | OSPF | Ethernet | Enlace principal hacia borde de Oriente |
| Core-2 ↔ R-Oriente2 | OSPF | Ethernet | Enlace redundante hacia borde de Oriente |
| Core-1 ↔ R-Central | Estático | Ethernet | Ruta fija hacia sede principal |

**Cumplimiento de requerimientos del enunciado:**
- Al menos 2 dispositivos de capa 3 en el núcleo → Core-1 y Core-2
- Redundancia de enlace entre núcleos → EtherChannel con fibra óptica
- Segmento OSPF → Core-2 ↔ R-Oriente1/2
- Segmento EIGRP → Core-1 ↔ R-Occidente
- Segmento RIP → Core-1 ↔ R-Norte
- Segmento con rutas estáticas → Core-1 ↔ R-Central
- Al menos 2 puntos de redistribución → Core-1 y Core-2
- Fibra óptica + Ethernet + Serial como medios físicos distintos

---

### 2.5 Justificación de protocolos por segmento

**EIGRP hacia Occidente:** Occidente es el centro logístico regional con mayor volumen de tráfico operativo (55+40 equipos). EIGRP es el protocolo Cisco más eficiente en convergencia y uso de ancho de banda, apropiado para el segmento de mayor demanda.

**RIP hacia Norte:** Norte opera como centro de monitoreo remoto con menor volumen de tráfico. RIP es suficiente para este segmento de menor escala, y cumple el requerimiento sin sobredimensionar el protocolo.

**OSPF hacia Oriente:** Oriente requiere dos routers de borde (HSRP activo/standby). OSPF maneja eficientemente topologías con múltiples routers en el mismo dominio y converge rápido ante la caída de un equipo, complementando la redundancia HSRP.

**Estático hacia Central:** Central es la sede principal con rutas predecibles y controladas. Una ruta estática hacia el core garantiza control total del tráfico saliente sin depender de convergencia dinámica para el segmento más crítico.

**Redistribución en Core-1 y Core-2:** Al correr diferentes protocolos en cada segmento, los routers del núcleo deben redistribuir rutas entre dominios. Core-1 redistribuye EIGRP ↔ OSPF ↔ RIP ↔ Estático. Core-2 hace lo mismo como respaldo, garantizando que si Core-1 falla, la redistribución no se interrumpe.

---

### 2.6 Subneteo del backbone — Red base: 10.0.0.0/8

Los enlaces punto a punto entre routers solo necesitan **2 hosts utilizables** (un extremo de cada router). El prefijo más eficiente para esto es **/30**, que provee exactamente 2 hosts utilizables.

```
2^2 - 2 = 2 hosts utilizables  →  prefijo /30  →  máscara 255.255.255.252
```

Se asignan subredes /30 secuenciales desde 10.0.0.0, una por cada enlace punto a punto.

#### Enlace 1: Core-1 ↔ Core-2 (EtherChannel — fibra óptica)

> Nota: EtherChannel agrupa múltiples interfaces físicas en un canal lógico. La IP se asigna sobre la interfaz lógica (Port-Channel), no sobre las físicas individuales. Se asigna una sola subred /30 para el canal lógico.

| Campo | Valor |
|---|---|
| Subred | 10.0.0.0/30 |
| Máscara | 255.255.255.252 |
| Core-1 (Port-Channel) | 10.0.0.1 |
| Core-2 (Port-Channel) | 10.0.0.2 |
| Broadcast | 10.0.0.3 |

#### Enlace 2: Core-1 ↔ R-Occidente (Ethernet — EIGRP)

| Campo | Valor |
|---|---|
| Subred | 10.0.0.4/30 |
| Máscara | 255.255.255.252 |
| Core-1 | 10.0.0.5 |
| R-Occidente | 10.0.0.6 |
| Broadcast | 10.0.0.7 |

#### Enlace 3: Core-1 ↔ R-Norte (Serial — RIP)

| Campo | Valor |
|---|---|
| Subred | 10.0.0.8/30 |
| Máscara | 255.255.255.252 |
| Core-1 (DCE) | 10.0.0.9 |
| R-Norte (DTE) | 10.0.0.10 |
| Broadcast | 10.0.0.11 |

> **Nota sobre enlaces Serial:** En Packet Tracer, el extremo DCE debe configurar el clock rate. Se asignará Core-1 como DCE.

#### Enlace 4: Core-2 ↔ R-Oriente1 (Ethernet — OSPF)

| Campo | Valor |
|---|---|
| Subred | 10.0.0.12/30 |
| Máscara | 255.255.255.252 |
| Core-2 | 10.0.0.13 |
| R-Oriente1 | 10.0.0.14 |
| Broadcast | 10.0.0.15 |

#### Enlace 5: Core-2 ↔ R-Oriente2 (Ethernet — OSPF)

| Campo | Valor |
|---|---|
| Subred | 10.0.0.16/30 |
| Máscara | 255.255.255.252 |
| Core-2 | 10.0.0.17 |
| R-Oriente2 | 10.0.0.18 |
| Broadcast | 10.0.0.19 |

#### Enlace 6: Core-1 ↔ R-Central (Ethernet — Estático)

| Campo | Valor |
|---|---|
| Subred | 10.0.0.20/30 |
| Máscara | 255.255.255.252 |
| Core-1 | 10.0.0.21 |
| R-Central | 10.0.0.22 |
| Broadcast | 10.0.0.23 |

---

### 2.7 Resumen consolidado — Subneteo del backbone

| Enlace | Protocolo | Medio | Subred | Máscara | IP extremo A | IP extremo B | Broadcast |
|---|---|---|---|---|---|---|---|
| Core-1 ↔ Core-2 | EtherChannel | Fibra óptica | 10.0.0.0/30 | 255.255.255.252 | 10.0.0.1 (Core-1) | 10.0.0.2 (Core-2) | 10.0.0.3 |
| Core-1 ↔ R-Occidente | EIGRP | Ethernet | 10.0.0.4/30 | 255.255.255.252 | 10.0.0.5 (Core-1) | 10.0.0.6 (R-Occ) | 10.0.0.7 |
| Core-1 ↔ R-Norte | RIP | Serial | 10.0.0.8/30 | 255.255.255.252 | 10.0.0.9 (Core-1) | 10.0.0.10 (R-Norte) | 10.0.0.11 |
| Core-2 ↔ R-Oriente1 | OSPF | Ethernet | 10.0.0.12/30 | 255.255.255.252 | 10.0.0.13 (Core-2) | 10.0.0.14 (R-Ori1) | 10.0.0.15 |
| Core-2 ↔ R-Oriente2 | OSPF | Ethernet | 10.0.0.16/30 | 255.255.255.252 | 10.0.0.17 (Core-2) | 10.0.0.18 (R-Ori2) | 10.0.0.19 |
| Core-1 ↔ R-Central | Estático | Ethernet | 10.0.0.20/30 | 255.255.255.252 | 10.0.0.21 (Core-1) | 10.0.0.22 (R-Cen) | 10.0.0.23 |

---

## Paso 3 – Diseño interno de cada sede

---

### Parámetros VTP globales del proyecto

Todas las sedes comparten el mismo dominio VTP para que las VLANs se propaguen correctamente entre switches del mismo dominio. Cada sede tiene su propio dominio independiente porque las VLANs no deben propagarse entre sedes — cada sede administra sus propias VLANs localmente.

| Parametro | Valor |
|---|---|
| VTP Version | 2 |
| VTP Mode switch principal | Server |
| VTP Mode switches secundarios | Client |
| VTP Password | conred2026 |

El dominio VTP es unico por sede para aislar la propagacion de VLANs. Los nombres se definen en cada seccion.

---

### Metodo de inter-VLAN routing

Todas las sedes utilizan **Router on a Stick** como metodo de enrutamiento inter-VLAN. Esto significa que el router de borde de cada sede tiene una unica interfaz fisica conectada al switch principal mediante un enlace trunk. Sobre esa interfaz se crean subinterfaces virtuales — una por cada VLAN — y cada subinterfaz actua como el gateway de esa VLAN.

Ventajas de este metodo en este proyecto:
- No requiere switches Layer 3 adicionales, reduciendo costo y complejidad
- El router de borde ya existe como parte del backbone, por lo que no se agrega hardware extra
- Es el metodo estandar para redes de este tamano en Cisco IOS

La unica excepcion es **Sede Oriente**, donde se tienen dos routers de borde con HSRP, por lo que el inter-VLAN se distribuye entre ambos routers para garantizar disponibilidad del gateway.

---

### 3.1 Sede Occidente

#### Contexto y justificacion de topologia

Occidente es un centro logistico regional. Sus necesidades son administracion centralizada, organizacion clara por departamentos y facilidad de mantenimiento. Estas caracteristicas se resuelven con una **topologia en arbol jerarquico de dos niveles**:

- **Nivel 1 (distribucion):** un switch principal que concentra todo el trafico y conecta al router de borde
- **Nivel 2 (acceso):** un switch de acceso por cada VLAN, conectando los equipos finales

Esta topologia es ideal para una sede logistica porque:
- El switch principal es el unico punto de administracion de VLANs y VTP Server
- Cada switch de acceso es independiente — si uno falla, solo afecta a esa VLAN
- El crecimiento futuro se hace agregando switches de acceso sin redisenar la red

No se implementa redundancia entre switches porque el enunciado no la requiere para esta sede y la topologia jerarquica simple responde mejor a la necesidad de mantenimiento facil.

#### Dispositivos de la sede

| Dispositivo | Tipo | Rol | VTP Mode |
|---|---|---|---|
| SW-OCC-Principal | Switch 2960 | Switch de distribucion, VTP Server, trunk hacia router | Server |
| SW-OCC-Acc-Inv | Switch 2960 | Switch de acceso VLAN 43 (Inventario) | Client |
| SW-OCC-Acc-Ops | Switch 2960 | Switch de acceso VLAN 13 (Operaciones) | Client |
| SW-OCC-Acc-Adm | Switch 2960 | Switch de acceso VLAN 23 (Administracion) | Client |
| SW-OCC-Acc-Seg | Switch 2960 | Switch de acceso VLAN 33 (Seguridad) | Client |
| R-Occidente | Router 2911 | Router de borde, Router on a Stick, EIGRP hacia Core | — |

**Total: 5 switches + 1 router de borde**

#### Topologia logica

```
R-Occidente (Router de borde)
    |
    | (trunk 802.1Q — subinterfaces por VLAN)
    |
SW-OCC-Principal (Switch principal — VTP Server)
    |─────────────────────────────────────────
    |              |               |          |
SW-OCC-Acc-Inv  SW-OCC-Acc-Ops  SW-OCC-Acc-Adm  SW-OCC-Acc-Seg
 VLAN 43          VLAN 13          VLAN 23          VLAN 33
 (55 equipos)    (40 equipos)     (18 equipos)     (8 equipos)
```

#### Tipos de enlace

| Enlace | Tipo | Razon |
|---|---|---|
| R-Occidente → SW-OCC-Principal | Trunk 802.1Q | Transporta trafico de todas las VLANs hacia el router para inter-VLAN |
| SW-OCC-Principal → SW-OCC-Acc-Inv | Trunk 802.1Q | El switch principal debe poder propagar VLANs hacia los switches de acceso |
| SW-OCC-Principal → SW-OCC-Acc-Ops | Trunk 802.1Q | Idem |
| SW-OCC-Principal → SW-OCC-Acc-Adm | Trunk 802.1Q | Idem |
| SW-OCC-Principal → SW-OCC-Acc-Seg | Trunk 802.1Q | Idem |
| SW-OCC-Acc-XXX → PCs | Access | Los puertos hacia equipos finales son siempre de acceso, asignados a su VLAN |

#### Configuracion VTP

| Parametro | Valor |
|---|---|
| VTP Domain | CONRED-OCCIDENTE |
| VTP Password | conred2026 |
| VTP Server | SW-OCC-Principal |
| VTP Clients | SW-OCC-Acc-Inv, SW-OCC-Acc-Ops, SW-OCC-Acc-Adm, SW-OCC-Acc-Seg |

#### Subinterfaces del Router on a Stick (R-Occidente)

La interfaz fisica conectada al switch principal es GigabitEthernet0/0. Sobre ella se crean las siguientes subinterfaces:

| Subinterfaz | VLAN | IP del Gateway | Descripcion |
|---|---|---|---|
| G0/0.13 | 13 — Operaciones | 172.16.0.65 | Gateway de la VLAN Operaciones |
| G0/0.23 | 23 — Administracion | 172.16.0.129 | Gateway de la VLAN Administracion |
| G0/0.33 | 33 — Seguridad | 172.16.0.161 | Gateway de la VLAN Seguridad |
| G0/0.43 | 43 — Inventario | 172.16.0.1 | Gateway de la VLAN Inventario |

La interfaz G0/1 de R-Occidente se conecta al backbone (Core-1) con IP 10.0.0.6/30.

#### Minimo de dispositivos finales por VLAN (requerimiento del enunciado: 7 minimo por area)

El enunciado exige al menos 7 dispositivos finales por area. Occidente tiene 4 VLANs con 40+55+18+8 = 121 equipos en total. Se colocaran PCs genericas en Packet Tracer para representar los equipos. Para efectos de simulacion se colocaran al menos 2 PCs por VLAN en Packet Tracer (la cantidad real en produccion es la indicada en las tablas de subneteo).

---

### 3.2 Sede Norte

#### Contexto y justificacion de topologia

Norte es un centro de monitoreo y coordinacion remota con operaciones sensibles. Su necesidad critica es **continuidad de servicio y tolerancia a fallos internos**. Esto exige redundancia fisica entre switches, lo que genera multiples caminos de capa 2 y obliga a implementar **Rapid PVST+** para evitar bucles.

Se utiliza una **topologia en anillo entre el switch principal y dos switches de distribucion**, con switches de acceso en el nivel inferior. Esto garantiza que si un enlace entre switches falla, el trafico puede tomar el camino alternativo.

Rapid PVST+ se implementa porque:
- Hay mas de un camino posible entre switches (redundancia real)
- Sin STP, los frames circularian en bucle infinito y colapsarian la red
- Rapid PVST+ mantiene un camino activo y bloquea los redundantes, activandolos en segundos si el primario falla

#### Dispositivos de la sede

| Dispositivo | Tipo | Rol | VTP Mode | STP Role |
|---|---|---|---|---|
| SW-NOR-Core | Switch 2960 | Switch principal, VTP Server, Root Bridge | Server | Root Bridge (prioridad 4096) |
| SW-NOR-Dist1 | Switch 2960 | Switch de distribucion 1, enlace redundante | Client | Designated |
| SW-NOR-Dist2 | Switch 2960 | Switch de distribucion 2, enlace redundante | Client | Designated |
| SW-NOR-Acc-Ops | Switch 2960 | Switch de acceso VLAN 13 | Client | — |
| SW-NOR-Acc-Adm | Switch 2960 | Switch de acceso VLAN 23 | Client | — |
| SW-NOR-Acc-Seg | Switch 2960 | Switch de acceso VLAN 33 | Client | — |
| SW-NOR-Acc-Inv | Switch 2960 | Switch de acceso VLAN 43 | Client | — |
| R-Norte | Router 2911 | Router de borde, Router on a Stick, RIP hacia Core | — | — |

**Total: 7 switches + 1 router de borde**

#### Topologia logica

```
R-Norte (Router de borde)
    |
    | (trunk 802.1Q)
    |
SW-NOR-Core (Switch principal — VTP Server — Root Bridge)
    |──────────────────────────────────────
    |                                      |
SW-NOR-Dist1 ──────────────────── SW-NOR-Dist2
(distribucion 1)  (enlace redundante)  (distribucion 2)
    |──────────────                ──────────────|
    |             |                |             |
SW-NOR-Acc-Ops  SW-NOR-Acc-Adm  SW-NOR-Acc-Seg  SW-NOR-Acc-Inv
  VLAN 13         VLAN 23          VLAN 33          VLAN 43
 (30 equipos)   (12 equipos)     (10 equipos)     (15 equipos)
```

El enlace entre SW-NOR-Dist1 y SW-NOR-Dist2 es el enlace redundante que Rapid PVST+ bloqueara en condiciones normales y activara si cae cualquier enlace hacia SW-NOR-Core.

#### Tipos de enlace

| Enlace | Tipo | Razon |
|---|---|---|
| R-Norte → SW-NOR-Core | Trunk 802.1Q | Inter-VLAN routing hacia el router |
| SW-NOR-Core → SW-NOR-Dist1 | Trunk 802.1Q | Propaga VLANs hacia nivel de distribucion |
| SW-NOR-Core → SW-NOR-Dist2 | Trunk 802.1Q | Idem, camino redundante |
| SW-NOR-Dist1 → SW-NOR-Dist2 | Trunk 802.1Q | Enlace redundante entre distribuciones (bloqueado por STP en operacion normal) |
| SW-NOR-Dist1/2 → SW-NOR-Acc-XXX | Trunk 802.1Q | Propagacion de VLANs hacia acceso |
| SW-NOR-Acc-XXX → PCs | Access | Puertos de acceso hacia equipos finales |

#### Configuracion VTP y STP

| Parametro | Valor |
|---|---|
| VTP Domain | CONRED-NORTE |
| VTP Password | conred2026 |
| VTP Server | SW-NOR-Core |
| STP Mode | Rapid PVST+ |
| Root Bridge | SW-NOR-Core (spanning-tree vlan 13,23,33,43 priority 4096) |

#### Subinterfaces del Router on a Stick (R-Norte)

| Subinterfaz | VLAN | IP del Gateway | Descripcion |
|---|---|---|---|
| G0/0.13 | 13 — Operaciones | 172.17.0.1 | Gateway de la VLAN Operaciones |
| G0/0.23 | 23 — Administracion | 172.17.0.113 | Gateway de la VLAN Administracion |
| G0/0.33 | 33 — Seguridad | 172.17.0.97 | Gateway de la VLAN Seguridad |
| G0/0.43 | 43 — Inventario | 172.17.0.65 | Gateway de la VLAN Inventario |

La interfaz G0/1 de R-Norte se conecta al backbone (Core-1) con IP 10.0.0.10/30. Al ser el extremo DTE del enlace serial, no configura clock rate.

---

### 3.3 Sede Oriente

#### Contexto y justificacion de topologia

Oriente es una sede de coordinacion operativa con procesos sensibles que **no pueden perder su gateway ante la caida de un router**. El enunciado exige dos routers de borde y la implementacion de HSRP o VRRP.

**HSRP (Hot Standby Router Protocol)** es un protocolo Cisco que permite que dos routers compartan una IP virtual. Los equipos de la red configuran esa IP virtual como su gateway. En todo momento, uno de los routers es el **activo** (responde al trafico) y el otro es el **standby** (monitorea al activo). Si el activo cae, el standby asume la IP virtual en segundos sin que los equipos finales noten el cambio.

Se utiliza una **topologia jerarquica con dos uplinks desde el switch principal**, uno hacia cada router de borde, garantizando que siempre haya camino hacia el gateway activo.

#### Dispositivos de la sede

| Dispositivo | Tipo | Rol | VTP Mode | HSRP Role |
|---|---|---|---|---|
| SW-ORI-Principal | Switch 2960 | Switch principal, VTP Server | Server | — |
| SW-ORI-Acc-Ate | Switch 2960 | Acceso VLAN 53 | Client | — |
| SW-ORI-Acc-Adm | Switch 2960 | Acceso VLAN 23 | Client | — |
| SW-ORI-Acc-Seg | Switch 2960 | Acceso VLAN 33 | Client | — |
| SW-ORI-Acc-Inv | Switch 2960 | Acceso VLAN 43 | Client | — |
| R-Oriente1 | Router 2911 | Router de borde activo, HSRP Active, OSPF hacia Core | — | Active |
| R-Oriente2 | Router 2911 | Router de borde standby, HSRP Standby, OSPF hacia Core | — | Standby |

**Total: 5 switches + 2 routers de borde**

#### Topologia logica

```
Core-2 (Backbone)
    |──────────────────────────────────
    |                                  |
R-Oriente1 (HSRP Active)      R-Oriente2 (HSRP Standby)
IP real: 172.18.X.X/27        IP real: 172.18.X.X/27
IP virtual HSRP: 172.18.X.X   IP virtual HSRP: (misma IP virtual)
    |──────────────────────────────────|
                    |
          SW-ORI-Principal (VTP Server)
              |──────────────────────────────────
              |            |           |          |
        SW-ORI-Acc-Ate  SW-ORI-Acc-Adm  SW-ORI-Acc-Seg  SW-ORI-Acc-Inv
          VLAN 53          VLAN 23        VLAN 33          VLAN 43
         (28 equipos)    (19 equipos)    (8 equipos)      (21 equipos)
```

#### IPs virtuales HSRP por VLAN

En Oriente, el inter-VLAN routing funciona diferente al Router on a Stick clasico. Ambos routers tienen subinterfaces para cada VLAN, pero la IP de gateway que los equipos usan es la **IP virtual HSRP**, no la IP real del router.

| VLAN | IP R-Oriente1 (real) | IP R-Oriente2 (real) | IP Virtual HSRP (gateway) |
|---|---|---|---|
| 53 — Atencion Regional | 172.18.0.2 | 172.18.0.3 | 172.18.0.1 |
| 43 — Inventario | 172.18.0.34 | 172.18.0.35 | 172.18.0.33 |
| 23 — Administracion | 172.18.0.66 | 172.18.0.67 | 172.18.0.65 |
| 33 — Seguridad | 172.18.0.98 | 172.18.0.99 | 172.18.0.97 |

R-Oriente1 tiene prioridad HSRP 110 (activo por defecto). R-Oriente2 tiene prioridad 100 (standby). Si R-Oriente1 cae, R-Oriente2 asume la IP virtual y los equipos finales no pierden su gateway.

#### Conexiones al backbone

| Router | Interfaz hacia Core | IP | Subred backbone |
|---|---|---|---|
| R-Oriente1 | G0/1 | 10.0.0.14/30 | 10.0.0.12/30 |
| R-Oriente2 | G0/1 | 10.0.0.18/30 | 10.0.0.16/30 |

#### Configuracion VTP

| Parametro | Valor |
|---|---|
| VTP Domain | CONRED-ORIENTE |
| VTP Password | conred2026 |
| VTP Server | SW-ORI-Principal |

---

### 3.4 Sede Central

#### Contexto y justificacion de topologia

Central es la sede principal de servicios nacionales con los departamentos mas criticos del proyecto (Monitoreo, Servicios Criticos, Administracion superior). Sus necesidades son alta disponibilidad, multiples caminos de comunicacion y separacion clara entre departamentos.

Se utiliza una **topologia jerarquica redundante de tres niveles** con malla parcial entre los switches de distribucion:

- **Nivel 1 (core de sede):** el router R-Central conecta al backbone
- **Nivel 2 (distribucion):** dos switches de distribucion interconectados entre si, formando redundancia
- **Nivel 3 (acceso):** switches de acceso por VLAN conectados a ambos switches de distribucion cuando es posible

Esta topologia elimina puntos unicos de falla entre los equipos de distribucion porque si un switch de distribucion falla, el trafico puede llegar por el otro. Se implementa **Rapid PVST+** para gestionar los bucles que genera esta redundancia.

#### Root Bridge

El switch SW-CEN-Dist1 funcionara como Root Bridge principal para todas las VLANs porque esta mas cerca del router de borde (R-Central) y tiene mayor capacidad de procesamiento al ser el punto central de distribucion. Se configura con prioridad 4096 para garantizar que siempre gane la eleccion de Root Bridge. SW-CEN-Dist2 tendra prioridad 8192 como Root Bridge secundario (Backup Root).

#### Dispositivos de la sede

| Dispositivo | Tipo | Rol | VTP Mode | STP Role |
|---|---|---|---|---|
| SW-CEN-Dist1 | Switch 2960 | Distribucion principal, VTP Server, Root Bridge | Server | Root Bridge (prioridad 4096) |
| SW-CEN-Dist2 | Switch 2960 | Distribucion secundaria, Backup Root | Client | Backup Root (prioridad 8192) |
| SW-CEN-Acc-Adm | Switch 2960 | Acceso VLAN 23 | Client | — |
| SW-CEN-Acc-Seg | Switch 2960 | Acceso VLAN 33 | Client | — |
| SW-CEN-Acc-Mon | Switch 2960 | Acceso VLAN 63 | Client | — |
| SW-CEN-Acc-Sop | Switch 2960 | Acceso VLAN 73 | Client | — |
| SW-CEN-Acc-SC | Switch 2960 | Acceso VLAN 83 | Client | — |
| R-Central | Router 2911 | Router de borde, Router on a Stick, ruta estatica hacia Core-1 | — | — |

**Total: 7 switches + 1 router de borde**

#### Topologia logica

```
R-Central (Router de borde — ruta estatica hacia Core-1)
    |
    | (trunk 802.1Q)
    |
SW-CEN-Dist1 ─────────────────── SW-CEN-Dist2
(Root Bridge — VTP Server)        (Backup Root)
    |─────────────────────────────────────|
    |          |         |        |        |
SW-CEN-    SW-CEN-  SW-CEN-  SW-CEN-  SW-CEN-
Acc-Adm    Acc-Seg  Acc-Mon  Acc-Sop  Acc-SC
 VLAN 23   VLAN 33  VLAN 63  VLAN 73  VLAN 83
(20 eq.)  (9 eq.)  (45 eq.) (10 eq.) (16 eq.)
```

Los switches de acceso de VLANs criticas (VLAN 63 y VLAN 83) tendran doble uplink: uno hacia SW-CEN-Dist1 y otro hacia SW-CEN-Dist2, garantizando que nunca pierdan conectividad aunque caiga un switch de distribucion. Rapid PVST+ bloqueara el enlace redundante en condiciones normales.

#### Tipos de enlace

| Enlace | Tipo | Razon |
|---|---|---|
| R-Central → SW-CEN-Dist1 | Trunk 802.1Q | Inter-VLAN routing hacia el router |
| SW-CEN-Dist1 → SW-CEN-Dist2 | Trunk 802.1Q | Enlace redundante entre distribuciones (Rapid PVST+ gestiona) |
| SW-CEN-Dist1/2 → SW-CEN-Acc-XXX | Trunk 802.1Q | Propagacion de VLANs hacia acceso |
| SW-CEN-Acc-Mon → Dist1 y Dist2 | Trunk 802.1Q | Doble uplink para VLAN critica de Monitoreo |
| SW-CEN-Acc-SC → Dist1 y Dist2 | Trunk 802.1Q | Doble uplink para VLAN critica de Servicios Criticos |
| SW-CEN-Acc-XXX → PCs | Access | Puertos de acceso hacia equipos finales |

#### Configuracion VTP y STP

| Parametro | Valor |
|---|---|
| VTP Domain | CONRED-CENTRAL |
| VTP Password | conred2026 |
| VTP Server | SW-CEN-Dist1 |
| STP Mode | Rapid PVST+ |
| Root Bridge principal | SW-CEN-Dist1 (priority 4096 para VLANs 23,33,63,73,83) |
| Root Bridge secundario | SW-CEN-Dist2 (priority 8192) |

#### Subinterfaces del Router on a Stick (R-Central)

| Subinterfaz | VLAN | IP del Gateway | Descripcion |
|---|---|---|---|
| G0/0.23 | 23 — Administracion | 172.19.0.65 | Gateway de la VLAN Administracion |
| G0/0.33 | 33 — Seguridad | 172.19.0.145 | Gateway de la VLAN Seguridad |
| G0/0.63 | 63 — Monitoreo y Control | 172.19.0.1 | Gateway de la VLAN Monitoreo y Control |
| G0/0.73 | 73 — Soporte | 172.19.0.129 | Gateway de la VLAN Soporte |
| G0/0.83 | 83 — Servicios Criticos | 172.19.0.97 | Gateway de la VLAN Servicios Criticos |

La interfaz G0/1 de R-Central se conecta al backbone (Core-1) con IP 10.0.0.22/30.

---

### 3.5 Resumen general de dispositivos del proyecto

| Sede | Switches | Routers de borde | STP | VTP Domain |
|---|---|---|---|---|
| Occidente | 5 (1 principal + 4 acceso) | 1 (R-Occidente) | No requerido | CONRED-OCCIDENTE |
| Norte | 7 (1 core + 2 dist + 4 acceso) | 1 (R-Norte) | Rapid PVST+ | CONRED-NORTE |
| Oriente | 5 (1 principal + 4 acceso) | 2 (R-Ori1 + R-Ori2 con HSRP) | No requerido | CONRED-ORIENTE |
| Central | 7 (2 dist + 5 acceso) | 1 (R-Central) | Rapid PVST+ | CONRED-CENTRAL |
| Backbone | — | 7 (Core-1, Core-2, R-Occ, R-Nor, R-Ori1, R-Ori2, R-Cen) | — | — |
| **Total** | **24 switches** | **7 routers** | — | — |

---



