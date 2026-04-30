# Seccion 5 - Capturas de implementacion

**Proyecto 2: Red Nacional de Coordinacion SE-CONRED**
**Joaquin Emmanuel Aldair Coromac Huezo - Carne 201903873**

---

## 5.1 Topologia general

### Topologia completa

![Topologia Completa](Images/TopologiaCompleta.png)

### Topologia Backbone

![Topologia Backbone](Images/TopologiaBackbone.png)

### Topologia Sede Occidente

![Topologia Occidente](Images/TopologiaOccidente.png)

### Topologia Sede Norte

![Topologia Norte](Images/TopologiaNorte.png)

### Topologia Sede Oriente

![Topologia Oriente](Images/TopologiaOriente.png)

### Topologia Sede Central

![Topologia Central](Images/TopologiaCentral.png)

---

## 5.2 Capturas de verificacion de backbone

### show ip route - Core-1

![Route Core-1](Images/RouteCore1.png)

### show ip route - Core-2

![Route Core-2](Images/RouteCore2.png)

### show ip eigrp neighbors

![EIGRP Neighbors](Images/EigrpNeighbors.png)

### show ip ospf neighbor

![OSPF Neighbors](Images/OspfNeighbors.png)

### show ip rip database - R-Norte

![RIP Database](Images/RipDatabase.png)

### show ip route - R-Occidente

![Route R-Occidente](Images/RouteROccidente.png)

### show ip route - R-Oriente1

![Route R-Oriente1](Images/RouteROriente1.png)

---

## 5.3 Capturas de verificacion de VLANs y VTP

### show vlan brief - SW-OCC-Principal

![VLAN Occidente](Images/VlanOccidente.png)

### show vlan brief - SW-NOR-Core

![VLAN Norte](Images/VlanNorte.png)

### show vlan brief - SW-ORI-Principal

![VLAN Oriente](Images/VlanOriente.png)

### show vlan brief - SW-CEN-Dist1

![VLAN Central](Images/VlanCentral.png)

### show interfaces trunk - SW-OCC-Principal

![Trunk Occidente](Images/TrunkOccidente.png)

### show interfaces trunk - SW-NOR-Core

![Trunk Norte](Images/TrunkNorte.png)

### show vtp status - SW-OCC-Principal

![VTP Occidente](Images/VtpOccidente.png)

---

## 5.4 Capturas de redundancia

### show standby brief - R-Oriente1 (HSRP)

![HSRP Oriente](Images/HsrpOriente.png)

### show spanning-tree vlan 13 - SW-NOR-Core

![STP Norte Core](Images/StpNorte.png)

### show spanning-tree vlan 13 - SW-NOR-Dist1

![STP Norte Dist1](Images/StpNorteDist1.png)

### show spanning-tree vlan 63 - SW-CEN-Dist1

![STP Central Dist1](Images/StpCentral.png)

### show spanning-tree vlan 63 - SW-CEN-Acc-Mon

![STP Central Mon](Images/StpCentralMon.png)

---

## 5.5 Capturas de pruebas de conectividad

### Ping intra-sede Occidente (PC6 VLAN 23 hacia PC2 VLAN 43)

![Ping Intra Occidente](Images/PingIntraOccidente.png)

### Ping intra-sede Norte (PC8 VLAN 13 hacia PC14 VLAN 43)

![Ping Intra Norte](Images/PingIntraNorte.png)

### Ping intra-sede Oriente (PC16 VLAN 53 hacia PC22 VLAN 43)

![Ping Intra Oriente](Images/PingIntraOriente.png)

### Ping intra-sede Central (PC28 VLAN 63 hacia PC24 VLAN 83)

![Ping Intra Central](Images/PingIntraCentral.png)

### Ping inter-sede Occidente hacia Central

![Ping Occidente Central](Images/PingOccidenteCentral.png)

### Ping inter-sede Occidente hacia Norte

![Ping Occidente Norte](Images/PingOccidenteNorte.png)

### Ping inter-sede Occidente hacia Oriente

![Ping Occidente Oriente](Images/PingOccidenteOriente.png)

### Ping inter-sede Norte hacia Central

![Ping Norte Central](Images/PingNorteCentral.png)

---

*Capturas de implementacion - Proyecto 2 - Red Nacional de Coordinacion SE-CONRED*
*Redes de Computadoras 1 - Universidad San Carlos de Guatemala*
