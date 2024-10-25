---
modifiedDate: 2024-10-25T11:15:22-03:00
createdDate: 2024-10-14T16:46:12-03:00
---
Una orientación macro de todos los objetivos que se deben cumplir para completar el examen de laboratorio.
## Checkpoint 1: VLANs
Resolver:
- Conectar cada Terminal a los switches, y los switches entre si.
- Crear *TODAS* las VLANs en cada switch.
- Asignar las VLANs a cada puerto.
- Configurar los puertos trunk entre los switches.
- Asignar las IPs a cada Terminal.

Testear:
- Conexiones entre misma VLAN. Debe funcionar.
- Conexiones entre diferente VLAN. Debe NO funcionar.
## Checkpoint 2a: AP Bridge
Resolver:
- Conectar el AP al switch que corresponda.
- Verificar que el switch tenga este puerto asignado a la VLAN correcta.
- Configurar el AP en modo Bridge.

Testear:
- Conexiones entre dispositivos del AP y misma VLAN. Debe funcionar.
- Conexiones entre dispositivos del AP y diferente VLAN. Debe NO funcionar.
## Checkpoint 2b: AP Router
Resolver:
- Conectar el AP al router que corresponda.
- Asignarle al AP la IP que corresponda en el router al que se conecta.
- Configurar el AP en modo Router.

Testear:
- Conexión de los dispositivos del AP a donde diga la consigna. Debe funcionar.
## Checkpoint 3: Subinterfaces Dot1Q
- Configurar protocolo Dot1Q para la interfaz del router de salida a WAN que conecta hacia la LAN.
- Asegurarse que cada dispositivo tenga la default gateway que le corresponda según la VLAN donde está.
- Configurar access list, para evitar acceso entre VLANs.

Testear:
- Conexiones entre diferente VLAN. Debe NO funcionar.
- Conexiones entre las VLANs y el router de salida a WAN. Debe funcionar.
## Checkpoint 4: WAN
Resolver:
- Configurar protocolo de enrutamiento en cada router de WAN donde corresponda.
- Red de acceso de Regional a WAN.
- Redistribuir rutas entre protocolos en el router donde estos se junten.

Testear:
- Acceso de dispositivos de LAN a la WAN. Debe funcionar.
## Checkpoint 5: Tunel IPSec
Resolver:
- Configurar el túnel IPSec en los dos router del extremo.
- Asegurarse que la configuración sea IDÉNTICA en ambos routers. La única diferencia es que el extremo X referencia al Y, y viceversa.

Testear:
- Debuggear paquete para ver que ingrese lo que deba al túnel, y no ingrese lo que no.