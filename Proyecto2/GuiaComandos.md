# Guia de Estudio y Explicacion de Comandos
## Proyecto 2 - Red Nacional de Coordinacion SE-CONRED
**Redes de Computadoras 1 - Universidad San Carlos de Guatemala**

Este archivo no forma parte de la documentacion tecnica oficial del proyecto. Es una guia personal de estudio que explica el porque de cada decision de diseno y el funcionamiento detallado de cada comando utilizado durante la implementacion.

---

## Parte 1 - Por que cada tecnologia del proyecto

### 1.1 Por que VLANs

Sin VLANs, todos los dispositivos de una sede estarian en el mismo dominio de broadcast. Esto significa que cuando una PC manda un mensaje broadcast (como una solicitud ARP), ese mensaje llega a todos los dispositivos de la red al mismo tiempo. En una red con 121 equipos como Occidente, esto genera una cantidad enorme de trafico innecesario que degrada el rendimiento de toda la red.

Una VLAN es una red local virtual que segmenta logicamente el trafico. Aunque todos los switches esten conectados fisicamente, las VLANs crean barreras logicas. Un broadcast en VLAN 13 (Operaciones) no llega a los dispositivos de VLAN 43 (Inventario). Esto reduce el trafico innecesario y mejora la seguridad porque los departamentos quedan aislados entre si.

En este proyecto cada VLAN representa un departamento: Operaciones, Administracion, Seguridad, Inventario, etc. Los equipos de Seguridad no necesitan ver el trafico de Inventario, y separandolos en VLANs se logra ese aislamiento sin necesitar hardware fisico separado.

### 1.2 Por que Trunking 802.1Q

Un enlace de acceso solo puede pertenecer a una VLAN. Pero el switch principal necesita transportar trafico de las 4 VLANs al mismo tiempo hacia el router de borde. Para esto existe el trunking.

Un trunk es un enlace que transporta trafico de multiples VLANs simultaneamente. Lo logra agregando una etiqueta de 4 bytes al frame Ethernet que identifica a que VLAN pertenece ese frame. Ese proceso se llama encapsulacion 802.1Q, que es el estandar de la industria.

Cuando el frame llega al destino, el switch lee la etiqueta, sabe a que VLAN pertenece, y lo procesa en consecuencia. Los puertos trunk se configuran entre switches y entre switch y router. Los puertos hacia PCs son siempre de acceso porque una PC normal no entiende etiquetas VLAN.

### 1.3 Por que VTP

VTP (VLAN Trunking Protocol) es un protocolo Cisco que sincroniza la base de datos de VLANs entre switches del mismo dominio. Sin VTP, si agregas una nueva VLAN tendrias que configurarla manualmente en cada switch de la sede. Con VTP, la configuras una sola vez en el VTP Server y se propaga automaticamente a todos los VTP Clients del mismo dominio.

El VTP Server es el switch principal de cada sede porque es el punto central de administracion. Los switches de acceso son VTP Clients porque solo necesitan recibir la informacion de VLANs, no crearlas.

El VTP password existe para evitar que switches no autorizados se conecten a la red y modifiquen la base de datos de VLANs. Si un switch tiene una contrasena diferente, no puede participar en VTP.

Una advertencia importante sobre VTP: si conectas un switch externo con un VTP revision number mas alto que el Server, puede sobreescribir toda la base de datos de VLANs. Por eso es importante controlar quien se conecta a la red.

### 1.4 Por que Spanning Tree Protocol (STP) y Rapid PVST+

Cuando hay mas de un camino fisico entre switches (redundancia), los frames Ethernet pueden circular en bucle infinito. Esto se llama broadcast storm y puede colapsar completamente la red en segundos porque los switches reenvian los frames de manera indefinida y el trafico crece exponencialmente.

STP soluciona esto eligiendo un switch como Root Bridge y calculando el camino mas corto desde cada switch hacia el Root Bridge. Los puertos que generarian bucles se bloquean (estado BLK). Si el camino activo falla, STP desbloquea el puerto alternativo.

Rapid PVST+ (Rapid Per-VLAN Spanning Tree Plus) es la version moderna de STP. La diferencia clave es la velocidad de convergencia: el STP clasico tarda entre 30 y 50 segundos en recalcular los caminos cuando algo falla. Rapid PVST+ lo hace en menos de 2 segundos usando un mecanismo de negociacion rapida llamado proposal/agreement.

El sufijo PVST significa que corre una instancia de STP por VLAN. Esto permite que diferentes VLANs usen diferentes caminos como Root Bridge, distribuyendo la carga del trafico entre multiples enlaces en lugar de tener un solo camino activo para todo.

Se implementa en Norte y Central porque son las unicas sedes con multiples caminos entre switches. Occidente tiene topologia estrella sin redundancia entre switches, por lo que no hay bucles posibles y STP no es necesario.

### 1.5 Por que Router on a Stick para inter-VLAN routing

Las VLANs aíslan el trafico tan eficientemente que ni siquiera pueden comunicarse entre ellas sin ayuda de un dispositivo de capa 3. Un switch de capa 2 no puede enrutar entre VLANs porque opera solo en capa 2 (MAC addresses). Se necesita un router.

Router on a Stick es la tecnica de usar una sola interfaz fisica del router dividida en multiples subinterfaces virtuales, una por VLAN. El switch envia todo el trafico inter-VLAN al router por un trunk. El router recibe el frame, ve la etiqueta VLAN, identifica la VLAN destino, y reenvía el frame de vuelta por la subinterfaz correcta con la etiqueta de la VLAN destino.

Se llama Router on a Stick porque el router esta conectado al switch por un solo cable (como un palito) pero maneja el trafico de multiples VLANs. La alternativa seria un switch Layer 3 que puede enrutar internamente, pero en este proyecto los routers de borde ya existen y tienen puertos disponibles, haciendo innecesario agregar equipos extra.

### 1.6 Por que HSRP en Sede Oriente

HSRP (Hot Standby Router Protocol) resuelve el problema del gateway unico. Cuando una PC tiene configurado un gateway estatico (la IP del router), si ese router cae la PC pierde toda conectividad fuera de su subred. No importa que haya otro router disponible, la PC seguira mandando trafico al gateway que ya no existe.

HSRP crea una IP virtual compartida entre dos routers. Los equipos finales configuran esa IP virtual como gateway. Internamente, uno de los routers es el Activo (el que realmente responde al trafico) y el otro es el Standby (el que monitorea al activo). Si el Activo cae, el Standby asume la IP virtual y responde al trafico en segundos, sin que los equipos finales necesiten cambiar su configuracion.

La prioridad determina quien es el Activo: el router con mayor prioridad gana. R-Oriente1 tiene prioridad 110 y R-Oriente2 tiene 100, por lo que R-Oriente1 siempre sera el Activo. El comando preempt garantiza que si R-Oriente1 se recupera despues de una falla, retoma automaticamente el rol Activo en lugar de quedarse como Standby.

### 1.7 Por que EIGRP, OSPF y RIP en el backbone

Los protocolos de enrutamiento dinamico permiten que los routers descubran automaticamente las rutas hacia otras redes, sin necesidad de configurarlas manualmente. Si una red cambia o un enlace cae, el protocolo recalcula las rutas automaticamente.

EIGRP (Enhanced Interior Gateway Routing Protocol) es un protocolo Cisco propietario avanzado. Calcula las rutas usando una metrica compuesta que considera el ancho de banda y el retardo del enlace. Converge muy rapido cuando hay cambios en la topologia. Se asigno al segmento de Occidente porque es el segmento con mayor volumen de trafico.

OSPF (Open Shortest Path First) es un protocolo estandar abierto que todos los fabricantes implementan. Cada router construye un mapa completo de la topologia y calcula el camino mas corto usando el algoritmo de Dijkstra. Es muy eficiente en redes grandes y converge rapidamente. Se asigno a los segmentos de Oriente porque OSPF funciona bien con topologias donde hay multiples routers en el mismo segmento (como los dos routers de borde de Oriente).

RIP (Routing Information Protocol) es el protocolo mas antiguo y simple. Solo cuenta el numero de saltos para determinar la mejor ruta, sin considerar velocidad del enlace. Tiene un maximo de 15 saltos y converge lentamente. Se usa en el segmento de Norte porque es una sede de menor escala y RIP es suficiente para ese volumen de trafico.

### 1.8 Por que redistribucion de rutas

Cada protocolo de enrutamiento habla su propio idioma. Un router que aprende rutas por EIGRP no comparte automaticamente esas rutas con un vecino que usa OSPF. Son protocolos completamente independientes.

La redistribucion es el proceso de tomar las rutas aprendidas por un protocolo e inyectarlas en otro protocolo. Core-1 y Core-2 son los unicos routers que participan en todos los protocolos simultaneamente, por eso son los puntos de redistribucion.

Sin redistribucion, R-Occidente (EIGRP) conoceria las redes de Occidente pero no sabria como llegar a Norte o Central. Con redistribucion, Core-1 toma las rutas EIGRP que aprendio de R-Occidente y las inyecta en OSPF y RIP, haciendo que todos los routers del backbone conozcan las redes de Occidente.

### 1.9 Por que rutas estaticas hacia Central

Las rutas estaticas son rutas que se configuran manualmente. No se aprenden automaticamente ni se redistribuyen solos, el administrador define exactamente el camino.

R-Central usa una ruta estatica default (0.0.0.0/0) hacia Core-1 en lugar de un protocolo dinamico. Esto es una decision de diseno deliberada: Central es la sede principal y su trafico hacia el backbone debe ser completamente predecible y controlado. No se quiere que un protocolo dinamico cambie el camino del trafico de Central automaticamente.

Del lado de Core-1, se configuraron rutas estaticas especificas hacia cada subred de Central (las 5 subredes /26, /27 y /28). Esto permite que Core-1 las conozca y las redistribuya hacia EIGRP, OSPF y RIP para que todos los demas routers sepan como llegar a Central.

---

## Parte 2 - Explicacion detallada de comandos

### 2.1 Comandos de configuracion basica

**enable**
Pasa del modo EXEC usuario (Router>) al modo EXEC privilegiado (Router#). El modo usuario solo permite consultas basicas. El modo privilegiado permite ver toda la configuracion y entrar a configuracion global. Es el primer comando que se escribe siempre al abrir el CLI.

**configure terminal**
Entra al modo de configuracion global (Router(config)#). Todos los cambios de configuracion se hacen desde este modo. Se puede abreviar como conf t.

**hostname [nombre]**
Cambia el nombre del dispositivo. Ademas de ser identificacion visual, el hostname aparece en los logs y en los comandos show, lo que facilita identificar que dispositivo se esta configurando cuando se trabaja con muchos equipos simultaneamente.

**no ip domain-lookup**
Deshabilita la resolucion DNS en el router. Sin este comando, cada vez que escribes mal un comando el router intenta resolverlo como nombre de dominio DNS y espera 30 segundos antes de devolver un error. Con este comando el error es inmediato, ahorrando tiempo durante la configuracion.

**write memory**
Guarda la configuracion activa en la memoria no volatil (NVRAM). Sin este comando, toda la configuracion se pierde cuando el router se reinicia. Se puede abreviar como wr. Equivalente a copy running-config startup-config.

**end**
Regresa directamente al modo EXEC privilegiado desde cualquier nivel de configuracion. Es mas rapido que escribir exit multiples veces.

**exit**
Retrocede un nivel en el modo de configuracion. Desde config-if va a config. Desde config va a modo privilegiado.

### 2.2 Comandos de configuracion de interfaces

**interface GigabitEthernet0/0**
Entra al modo de configuracion de la interfaz GigabitEthernet 0/0. El formato es tipo/slot/puerto. GigabitEthernet es una interfaz de 1 Gbps. Se puede abreviar como int g0/0.

**interface Serial0/3/0**
Entra a la configuracion de la interfaz serial en el slot 3, puerto 0. Las interfaces seriales requieren el modulo HWIC-2T instalado fisicamente en el router. Se usan para simular enlaces WAN de baja velocidad como lineas dedicadas o conexiones entre ciudades.

**ip address [IP] [mascara]**
Asigna la direccion IP y mascara de subred a una interfaz. La IP y la mascara determinan a que subred pertenece esa interfaz y que rango de IPs puede alcanzar directamente.

**no shutdown**
Activa la interfaz. Por defecto todas las interfaces de un router estan administrativamente apagadas (administratively down). Este comando las enciende. Sin el no shutdown, la interfaz no transmite ni recibe trafico aunque tenga IP configurada.

**clock rate 64000**
Configura la velocidad del reloj en el extremo DCE de un enlace serial. En un enlace serial real, uno de los extremos es el DCE (Data Communications Equipment) que proporciona la senal de reloj, y el otro es el DTE (Data Terminal Equipment) que sigue ese reloj. En Packet Tracer, el extremo DCE se identifica por el icono de reloj en el cable. Si no se configura clock rate en el DCE, el enlace serial no sube. El valor 64000 significa 64 Kbps de velocidad.

**no interface [nombre]**
Intenta eliminar una interfaz de la configuracion. En routers 2911, las interfaces fisicas no se pueden eliminar con este comando (da error "Removal of physical interfaces is not permitted"). Solo funciona para subinterfaces y Port-channels.

### 2.3 Comandos de subinterfaces (Router on a Stick)

**interface GigabitEthernet0/1.13**
Crea y entra a la configuracion de la subinterfaz 13 dentro de la interfaz fisica G0/1. El numero 13 es arbitrario pero por convencion se usa el mismo numero que el ID de la VLAN para facilitar la identificacion. Se pueden crear tantas subinterfaces como VLANs necesiten enrutamiento.

**encapsulation dot1Q 13**
Este comando le dice a la subinterfaz que use encapsulacion 802.1Q y que pertenece a la VLAN 13. Cuando llega un frame con la etiqueta VLAN 13, esta subinterfaz lo procesa. Cuando esta subinterfaz envia un frame, le pone la etiqueta VLAN 13. Sin este comando, la subinterfaz no puede distinguir el trafico de diferentes VLANs.

La interfaz fisica padre (G0/1 en el ejemplo) no debe tener IP asignada. Solo las subinterfaces tienen IP. La interfaz fisica debe estar encendida con no shutdown pero sin IP.

### 2.4 Comandos de EIGRP

**router eigrp 100**
Activa el proceso EIGRP con el numero de sistema autonomo 100. El numero de AS debe ser identico en todos los routers que participan en el mismo dominio EIGRP. Si dos routers tienen numeros de AS diferentes, no forman vecindad aunque esten conectados directamente.

**network [red] [wildcard]**
Le indica a EIGRP que interfaces participan en el proceso. El wildcard es el inverso de la mascara de subred. Por ejemplo, para una /30 (mascara 255.255.255.252) el wildcard es 0.0.0.3 porque 255-252=3. EIGRP anunciara las redes conectadas a las interfaces que coincidan con este comando y buscara vecinos EIGRP en esas interfaces.

**no auto-summary**
Deshabilita la sumarizacion automatica de rutas. Sin este comando, EIGRP resume las rutas a sus classful boundaries (por ejemplo, sumariza 172.16.0.64/26 a 172.16.0.0/16). Esto causa problemas en redes con VLSM porque se pierden los prefijos especificos. Con no auto-summary, EIGRP anuncia cada subred con su mascara exacta.

**redistribute ospf 1 metric 10000 100 255 1 1500**
Redistribuye las rutas aprendidas por OSPF proceso 1 hacia EIGRP. Los 5 numeros son la metrica EIGRP compuesta: bandwidth (10000 Kbps), delay (100 microsegundos), reliability (255 = 100% confiable), load (1 = minima carga), MTU (1500 bytes). Sin estos 5 valores, EIGRP no puede calcular su metrica y rechaza las rutas redistribuidas.

### 2.5 Comandos de OSPF

**router ospf 1**
Activa el proceso OSPF con el ID de proceso 1. A diferencia de EIGRP, el ID de proceso OSPF es local al router y no necesita coincidir entre routers. Dos routers pueden tener IDs de proceso diferentes y aun asi formar vecindad OSPF.

**network [red] [wildcard] area [numero]**
Indica que interfaces participan en OSPF y en que area. El area 0 es el area backbone de OSPF y es obligatoria en todo diseno OSPF. Todas las areas deben conectarse al area 0 directamente o a traves de un virtual link. En este proyecto todo opera en area 0 porque el backbone es relativamente simple.

**redistribute eigrp 100 subnets**
Redistribuye las rutas EIGRP hacia OSPF. La palabra subnets es obligatoria en OSPF: sin ella, OSPF solo redistribuye redes classful y descarta las subredes con mascaras variables. Esto haria que la mayoria de las rutas del proyecto no se redistribuyeran correctamente.

### 2.6 Comandos de RIP

**router rip**
Activa el proceso RIP en el router.

**version 2**
Especifica RIP version 2. La version 1 es classful (no soporta VLSM) y no envia la mascara de subred en sus actualizaciones. La version 2 es classless, soporta VLSM y envia la mascara en cada actualizacion. Siempre se usa version 2 en redes modernas.

**network [red]**
En RIP no se usa wildcard. Se especifica la red classful (sin mascara). RIP activara el proceso en todas las interfaces que pertenezcan a esa red classful. Por ejemplo, network 172.17.0.0 activa RIP en todas las interfaces con IPs en el rango 172.17.x.x.

**redistribute ospf 1 metric 5**
Redistribuye rutas OSPF hacia RIP con una metrica de 5 saltos. RIP usa el numero de saltos como metrica. Usar un valor de 5 es conservador, deja margen para que las rutas no sean descartadas por el limite de 15 saltos de RIP.

### 2.7 Comandos de rutas estaticas

**ip route [red destino] [mascara] [siguiente salto]**
Configura una ruta estatica. Le dice al router: para llegar a [red destino] con [mascara], envia el trafico hacia [siguiente salto]. El siguiente salto es la IP del router vecino al que se debe enviar el trafico, no la IP propia.

**ip route 0.0.0.0 0.0.0.0 10.0.0.21**
Esta es la ruta estatica default o ruta de ultimo recurso. La red 0.0.0.0 con mascara 0.0.0.0 coincide con cualquier destino. Cuando el router no sabe como llegar a un destino especifico, usa esta ruta y envia el trafico hacia 10.0.0.21 (Core-1). Es como decir "todo lo que no sepa como manejar, mandalo a Core-1".

### 2.8 Comandos de HSRP

**standby 1 ip 172.18.0.1**
Configura la IP virtual HSRP para el grupo 1 en esta subinterfaz. El numero 1 es el numero de grupo HSRP y debe ser identico en ambos routers que participan en el mismo grupo. La IP 172.18.0.1 es la IP virtual que los equipos finales usan como gateway.

**standby 1 priority 110**
Establece la prioridad HSRP de este router en el grupo 1. El router con mayor prioridad se convierte en el Activo. El valor por defecto es 100. R-Oriente1 tiene 110 y R-Oriente2 tiene 100, por lo que R-Oriente1 siempre gana la eleccion y se convierte en Activo.

**standby 1 preempt**
Habilita el preempt (reemplazo proactivo). Sin este comando, si el router Activo cae y el Standby asume el rol, cuando el Activo original se recupera NO retoma el rol Activo automaticamente, quedandose como Standby. Con preempt, el router con mayor prioridad siempre retoma el rol Activo cuando se recupera. Solo se configura en el router que debe ser Activo.

### 2.9 Comandos de VTP en switches

**vtp mode server**
Configura el switch como VTP Server. El Server puede crear, modificar y eliminar VLANs. Los cambios se propagan automaticamente a todos los Clients del mismo dominio.

**vtp mode client**
Configura el switch como VTP Client. El Client recibe la base de datos de VLANs del Server y la aplica automaticamente. Un Client no puede crear o modificar VLANs manualmente.

**vtp domain CONRED-OCCIDENTE**
Define el nombre del dominio VTP. Solo los switches con el mismo dominio y contrasena se sincronizan entre si. Dominios diferentes estan completamente aislados entre si.

**vtp password conred2026**
Define la contrasena VTP. Actua como mecanismo de seguridad: un switch con contrasena diferente no puede unirse al dominio ni recibir actualizaciones VTP.

**vtp version 2**
Especifica la version de VTP. La version 2 soporta Token Ring VLANs y tiene mejor manejo de mensajes inconsistentes. Se usa version 2 como estandar en este proyecto.

### 2.10 Comandos de VLANs en switches

**vlan 13**
Entra al modo de configuracion de VLAN 13 y la crea si no existe. Si el switch es VTP Server, esta VLAN se propagara a todos los Clients del dominio. Si el switch es VTP Client, no puede crear VLANs manualmente.

**name Operaciones-OCC**
Asigna un nombre descriptivo a la VLAN. El nombre es importante para la documentacion y para identificar las VLANs rapidamente en el comando show vlan brief. El IOS no acepta espacios en el nombre de VLAN.

### 2.11 Comandos de puertos en switches

**switchport mode trunk**
Configura el puerto en modo trunk. En modo trunk el puerto transporta trafico de multiples VLANs usando etiquetas 802.1Q. Se usa entre switches y entre switch y router.

**switchport mode access**
Configura el puerto en modo access. En modo access el puerto pertenece a una sola VLAN y no usa etiquetas 802.1Q. Se usa para conectar PCs, impresoras y otros dispositivos finales que no entienden 802.1Q.

**switchport access vlan 43**
Asigna el puerto de acceso a la VLAN 43. Todo el trafico que entre por este puerto se considera de VLAN 43. Todo el trafico que salga por este puerto hacia la PC no lleva etiqueta VLAN.

**switchport trunk encapsulation dot1q**
En algunos modelos de switch Cisco este comando es necesario antes de poder configurar switchport mode trunk. Especifica que el trunk usa encapsulacion 802.1Q. En los switches 2960 de Packet Tracer generalmente no es necesario porque 802.1Q es el unico protocolo soportado.

### 2.12 Comandos de Spanning Tree

**spanning-tree mode rapid-pvst**
Activa Rapid PVST+ como el modo de Spanning Tree en el switch. Debe configurarse en todos los switches de la sede para que funcione correctamente. Si un switch usa STP clasico y otro usa Rapid PVST+, el switch clasico forzara al rapido a operar en modo lento en ese enlace.

**spanning-tree vlan 13,23,33,43 priority 4096**
Establece la prioridad STP de este switch para las VLANs especificadas. Un valor menor significa mayor prioridad y mayor probabilidad de ser elegido Root Bridge. El valor default es 32768. Con 4096, este switch casi garantiza ganar la eleccion de Root Bridge. La prioridad debe ser multiplo de 4096 segun el estandar IEEE.

El Root Bridge es importante porque es el punto de referencia desde el cual STP calcula todos los caminos. Conviene que sea el switch mas central y confiable de la topologia, que en este proyecto es el switch principal de cada sede.

### 2.13 Comandos de verificacion

**show ip interface brief**
Muestra un resumen de todas las interfaces del dispositivo con su IP, estado fisico (Status) y estado del protocolo (Protocol). Status up/Protocol up significa que el enlace funciona completamente. Status up/Protocol down significa que el cable esta conectado pero hay un problema de configuracion en el otro extremo. Status administratively down significa que la interfaz esta apagada con shutdown.

**show ip route**
Muestra la tabla de enrutamiento completa. Cada entrada indica como el router llegara a una red especifica. Las letras al inicio indican el origen de la ruta: C=connected (directamente conectada), S=static (estatica), D=EIGRP, O=OSPF, R=RIP, O E2=OSPF external tipo 2 (redistribuida). El numero entre corchetes como [110/2] indica [distancia administrativa / metrica].

**show vlan brief**
Muestra todas las VLANs existentes en el switch y los puertos asignados a cada una. Los puertos trunk no aparecen en esta lista porque no pertenecen a una VLAN especifica. Si un switch Client muestra las VLANs del Server, confirma que VTP esta funcionando correctamente.

**show interfaces trunk**
Muestra todos los puertos en modo trunk con detalles sobre que VLANs permiten y cuales estan activas. La columna "Vlans allowed and active in management domain" muestra las VLANs que realmente estan propagandose por ese trunk.

**show vtp status**
Muestra la configuracion VTP del switch: modo (Server/Client/Transparent), dominio, version, numero de revision y cantidad de VLANs. El VTP configuration revision number es importante: cada vez que se crea o modifica una VLAN en el Server, el numero incrementa. Los Clients aceptan actualizaciones solo si el numero de revision es mayor al que tienen.

**show ip eigrp neighbors**
Muestra los vecinos EIGRP que este router ha descubierto. Si aparece un vecino, significa que EIGRP esta funcionando entre esos dos routers y estan intercambiando informacion de rutas. Si la lista esta vacia, hay un problema de configuracion: AS numbers diferentes, redes incorrectas en el comando network, o interfaz apagada.

**show ip ospf neighbor**
Muestra los vecinos OSPF y su estado. El estado FULL significa que la adyacencia OSPF esta completamente establecida y los routers han intercambiado toda su informacion de topologia. El estado 2WAY significa que se ven pero no han intercambiado toda la informacion. DR es Designated Router y BDR es Backup Designated Router, roles que OSPF asigna en redes con multiples routers.

**show ip rip database**
Muestra las rutas que RIP ha aprendido. Incluye las redes directamente conectadas y las aprendidas de vecinos con el tiempo en que se recibio la ultima actualizacion y la interfaz por donde se recibio.

**show standby brief**
Muestra el estado HSRP en el router. Indica el grupo, prioridad, estado (Active/Standby/Init), la IP del router Activo, la IP del Standby y la IP virtual. Confirmar que el estado es Active en R-Oriente1 y Standby en R-Oriente2 verifica que HSRP esta funcionando correctamente.

**show spanning-tree vlan [numero]**
Muestra el estado de Spanning Tree para una VLAN especifica. Indica si este switch es el Root Bridge, cual es el Root Bridge si no lo es, el costo hacia el Root Bridge, y el estado de cada puerto (FWD=forwarding/activo, BLK=blocking/bloqueado, ALT=alternativo/bloqueado, ROOT=puerto hacia el Root Bridge, DESG=designated/activo hacia abajo).

**show etherchannel summary**
Muestra el estado del EtherChannel. La letra SU en la columna flags indica que el Port-Channel esta activo (S=Layer2 o U=Up). Las letras P en los puertos miembros indican que estan en bundle (agrupados). Este comando no se uso en el proyecto porque Packet Tracer no soporta EtherChannel en routers 2911.

**ping [IP destino]**
Envia 4 paquetes ICMP Echo Request hacia el destino y espera respuestas. Cada punto (.) es un timeout y cada signo de exclamacion (!) es una respuesta exitosa. Se usa para verificar conectividad entre dispositivos. El primer ping frecuentemente pierde el primer paquete por el tiempo que toma poblar la tabla ARP.

---

## Parte 3 - Problemas encontrados durante la implementacion y sus soluciones

### 3.1 EtherChannel no funciona en routers 2911

Problema: el comando channel-group 1 mode active daba error "Invalid input detected" en las interfaces del router 2911.

Causa: Packet Tracer no implementa EtherChannel en routers 2911. EtherChannel es una funcion que el simulador solo soporta en switches.

Solucion: se reemplazo el EtherChannel por un unico enlace Ethernet entre Core-1 y Core-2. La redundancia del backbone se mantiene porque existen dos routers de nucleo con rutas alternativas a traves de los diferentes protocolos de enrutamiento.

### 3.2 Conflicto de interfaces en R-Occidente

Problema: R-Occidente tenia el cable del backbone conectado a G0/0 y las subinterfaces del Router on a Stick tambien en G0/0, causando que una sola interfaz tuviera la IP del backbone y las subinterfaces de las VLANs al mismo tiempo.

Causa: al planificar la topologia no se considero que G0/0 estaba siendo usada para dos propositos distintos simultaneamente.

Solucion: se movio el cable del backbone a G0/0 (con la IP del backbone 10.0.0.6) y se reubicaron las subinterfaces del Router on a Stick a G0/1. Esto separa correctamente el enlace al backbone del enlace hacia la sede.

### 3.3 IPs de PCs en conflicto con IPs de routers en Sede Oriente

Problema: al intentar asignar 172.18.0.2 a una PC de Oriente, Packet Tracer mostraba "This address is already used in the network".

Causa: en Oriente, las IPs reales de los routers de borde ocupan las primeras IPs del rango de cada VLAN (172.18.0.2 para R-Oriente1 y 172.18.0.3 para R-Oriente2). Las PCs deben empezar desde la cuarta IP (172.18.0.4 en adelante).

Solucion: se corrigieron las IPs de todas las PCs de Oriente para empezar desde la cuarta IP usable de cada subred, dejando las primeras tres para la IP virtual HSRP y las IPs reales de ambos routers.

### 3.4 Redes de Central no aparecian en la tabla de rutas de Core-1

Problema: despues de configurar la redistribucion, las redes 172.19.x.x de Central no aparecian en show ip route de Core-1.

Causa: R-Central usa ruta estatica default (0.0.0.0/0) en lugar de un protocolo dinamico. Esto significa que R-Central no anuncia sus redes hacia Core-1, Core-1 no las aprende automaticamente y por tanto no puede redistribuirlas.

Solucion: se configuraron rutas estaticas especificas en Core-1 para cada subred de Central, apuntando hacia R-Central (10.0.0.22). Esto le permite a Core-1 conocer las redes de Central y redistribuirlas hacia EIGRP, OSPF y RIP.

---

*Guia de estudio personal - Proyecto 2 SE-CONRED*
*Redes de Computadoras 1 - Universidad San Carlos de Guatemala*