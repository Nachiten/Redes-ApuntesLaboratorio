# Tutorial Packet Tracer

## Utilidades
| Comando             | Función                                            |
|---------------------|----------------------------------------------------|
| Control + Shift + 6 | Parar proceso corriendo                            |
| Control + z         | Volver atrás                                       |
| Shift + Insert      | Pegar texto copiado                                |
| Tab                 | Autocompletar entrada                              |
| Flecha Arriba       | Reinsertar comandos anteriores                     |
| ?                   | Obtener documentación sobre un comando o parámetro |
| exit                | Salir del modo actual, volver al anterior          |

## Modos
*Nota: Los modos (en general) van de menor a mayor permisos, y de menor a mayor especificidad.*

| Modo                                     | Función                     | Para ingresar                |
|------------------------------------------|-----------------------------|------------------------------|
| [U] \> Usuario                           | A penas entras, default     | -                            |
| [P] # Privilegiado                       | Modo privilegiado           | en                           |
| [CG] (config) Configuración Global       | Configuración general       | conf t                       |
| [CE] (config-X) Configuración Especifica | Configuraciones especificas | in \<interfaz\>, entre otros |
*Nota: Uso los prefijos entre [X] para identificar el modo donde va cada comando.*

## Configuraciones

### Nombre
```
[P] hostname <nombre>
```

### Contraseña de [P]
```
[CG] enable password <contra>
```

### Mostrar info
```
[P] show <cosa>
show r             | Running config
show in            | Interfaces
show in <interfaz> | Interfaz especifica
show v             | VLANs
show ac            | Access lists
show ip ro         | Tabla de rutas
show ip p          | Protocolo de enrutamiento
```

### Interfaces
```
[P] interface f 0/0         | Interfaz especifica
[P] interface range f0/4-24 | Rango de interfaces
```

#### Output de "show interfaces"
*Nota: show interface <interfaz> para ver solo una interfaz.*

| Output                             | Explicación                                                        |
|------------------------------------|--------------------------------------------------------------------|
| Interface is up                    | Está habilitado y tiene algo conectado                             |
| Interface is down                  | Está habilitado y no tiene algo conectado                          |
| Interface is administratively down | Deshabilitado. No se puede usar hasta que se habilite manualmente. |

### Seguridad de interfaz
| Comando                                      | Explicación                                                  |
|----------------------------------------------|--------------------------------------------------------------|
| [Interfaz] switchport mode access            | Configurar puerto en modo acceso                             |
| switchport port-security                     | Activar la seguridad del puerto                              |
| switchport port-security maximum 1           | Numero máximo de MAC                                         |
| switchport port-security mac-address \<mac\> | Asignar de MAC permitida                                     |
| switchport port-security violation shutdown  | Establecer acción ante violación. En este caso apagar puerto |

*Nota: Al hacer shutdown por una violation se pasa el puerto al estado administratively down.*

### IP administrativa
```
[CG] interface vlan 1     | Es la vlan por defecto
ip address <ip> <mascara> | Configurar IP y mascara
no shutdown               | Encender interfaz
```

### Interfaz serial
| Comando                   | Explicación                                              |
|---------------------------|----------------------------------------------------------|
| [CG] interface serial 0/0 | Ingresar a interfaz                                      |
| encapsulation ppp         | Configurar protocolo de encapsulación                    |
| ip address <IP> <máscara> | Configurar IP y mascara                                  |
| clock rate 2000000        | Velocidad de reloj (SOLO en extremo DCE, icono relojito) |
| no shutdown               | Encender interfaz                                        |

### VLANs

*Notas:*

- Todos los switches involucrados deben tener configuradas las VLANs para poder direccionarlas correctamente.
- Por default todo está en la VLAN 1.

#### Crear VLANs
```
[CG] vlan <numero>
name <nombre>
```

#### Asignar un puerto a una VLAN
```
[Interfaz] switchport mode access
switchport access vlan <numVlan>
```

#### Trunk
```
[Interfaz] switchport mode trunk
```

### Acceso remoto SSH
*Notas: Existen 16 VTYs (Virtual Terminal). Desde 0 hasta 15.*

Primero, activar acceso SSH en VTY 0.

| Comando                                               | Explicación                                           |
|-------------------------------------------------------|-------------------------------------------------------|
| [CG] ip domain-name dominio.com                       | Configurar nombre de dominio                          |
| crypto key generate rsa                               | Generar claves RSA (Se debe usar 1024, no el default) |
| ip ssh version 2                                      | Configurar versión de SSH                             |
| line vty 0                                            | Configurar terminal virtual (VTY) 0                   |
| transport input ssh                                   | Configurar acceso SSH                                 |
| login local                                           | Configurar autenticación                              |
| [CG] username \<user\> privilege 15 password \<pass\> | Especificar user y pass                               |

*TODO: No me dejo poner username en VTY 0, pero si en CG

Por último, desactivar acceso en el resto de VTY.

| Comando              | Explicación                |
|----------------------|----------------------------|
| [CG] line vty 1 15   | Configurar VTY 1 a 15      |
| transport input none | Deshabilitar acceso remoto |

### Acceso remoto TELNET

| Comando                  | Explicación                      |
|--------------------------|----------------------------------|
| [CG] line vty 0 1        | Configurar VTY 0 y               |
| login                    | Configurar autenticación         |
| password \<contra\>      | Establecer contraseña            |
| exec-timeout \<minutos\> | Timeout antes de terminar sesión |

### Spanning tree
Configurar este switch como root.
```
[CG] spanning-tree vlan <vlan1,vlanN> root primary
```

### Enlace LACP
Este es un enlace que une dos puertos físicos lógicamente como si fueran el mismo. Esto se usa para evitar que spanning tree corte un bucle. Esto permite duplicar el ancho de banda de una conexion utilizando dos puertos a la vez.
```
port-channel load-balance {dst-mac | src-mac}
interface gigabitethernet 1/1
switchport mode trunk
channel-protocol LACP
```

### Protocolo RIP
| Comando         | Descripción                                        |
|-----------------|----------------------------------------------------|
| [CG] router rip | Entra al modo de configuración del router para RIP |
| version 2       | Version de RIP                                     |
| network w.x.y.z | Especifica una red para incluirla en la tabla      |

*Nota: Las network deben estar en ip **CLASSFULL**. Es decir, la dirección de red (no subred) con la mascara default.*

#### Evitar la publicación de RIP en una interfaz
```
[Router rip] passive-interface <interfaz>
```

#### Establecer rutas sumarizadas CIDR
```
[CG] ip route <prefijo> <mascara> <interfaz>
```
Publico este prefijo, donde si una IP tiene el mismo prefijo, que me la manden porque ya la manejo.

#### Redistribuir rutas estáticas en el protocolo de ip dinámica
```
[Router rip] redistribute static
```

### Lista de acceso

### Crear la acess list (Entrenar el patoba)
```
[CG] access-list <numAccessList> <accion> <origen> | Estandar
[CG] access-list <numAccessList> <accion> <protocolo> <origen> <destino> | Extendida
```

numAccessList: Identificador de la access list 
- 1-99 Estandar
- 100-199 Extendida

accion: Acción realizada si se matchea
- permit | Permitir
- deny | Denegar

protocolo: Protocolo a filtrar
- ip (siempre se usa ese, en primer parcial)

origen y destino: IPs a filtrar
- host <ip> | Una ip especifica
- any | Cualquier ip
- \<ip> \<wildcard> | Rango de IPs

wildcard: Los bits que estén en las mismas posiciones a los 0 de la wildcard, deben coincidir con esa misma posición de la ip.

#### Activar la access list en una interfaz (Poner al patoba a laburar)
```
[Interfaz] ip access-group <numAccessList> <direccion>
```

direccion:
- in: Paquetes que entran
- out: Paquetes que salen


### Túnel IPsec

#### Configuración de IKE (Internet Key Exchange)

| Comando                         | Descripción                                           |
|---------------------------------|-------------------------------------------------------|
| [CG] crypto isakmp policy \<id> | Configurar política de encriptación                   |
| encr AES                        | Algoritmo de encriptación                             |
| authentication pre-share        | Método de autenticación. Clave pre-compartida         |
| group 5                         | Grupo de Diffie-Helulman. Grupo 5, clave de 1536 bits |
| lifetime 900                    | Tiempo de vida de la clave (segundos)                 |

#### Definición de clave simétrica con el otro extremo
```
[CG] crypto isakmp key <clave> address <ip>
```

- clave: Clave pre-compartida
- ip: IP del otro extremo

#### Configuración de IPSec modo túnel
```
[CG] crypto ipsec transform-set <id> ah-sha-hmac esp-3des
```

- transform-set: Crea un mapa de transformación
- id: Identificador del transform-set. Debe ser único
- ah-sha-hmac: Algoritmo de autenticación
- esp-3des: Algoritmo de encriptación

#### Configurar lista de acceso
Nota: Esta lista de acceso determina que trafico se va a encriptar.
```
[CG] access-list <id> permit ip 10.10.0.0 0.0.255.255 10.4.0.0 0.0.0.255
```
En este ejemplo, las ips cuyo origen matchee 10.10.X.X y su destino matchee 10.4.0.X, ingresará al túnel. (Será encriptado)

#### Configurar el mapa
Este determina la IP del otro extremo y el tráfico de interés que será encapsulado.

| Comando                                        | Descripción                                             |
|------------------------------------------------|---------------------------------------------------------|
| crypto map mymap 10 ipsec-isakmp               | Crea un mapa criptográfico                              |
| set peer \<ip\>                                | IP del otro extremo                                     |
| set security-association lifetime seconds 1800 | Tiempo de establecimiento de la asociación de seguridad |
| set transform-set \<idTransformSet>            | Vincula el transform-set 50 creado anteriormente        |
| match address \<idAccessList>                  | Vincula la lista de acceso 101 creada anteriormente     |

#### Activar el túnel
```
[Interface] crypto map mymap
```

### Sub-interfaces
Se utilizan para generar N interfaces virtuales en una interfaz física. Se utilizan para separar tráfico de diferentes VLANs.
```
[CG] interface f0/0.XX
encapsulation dot1Q XX
ip address <ip> <mascara>
```
XX = Número de VLAN

### Wireless

#### Modos del AP
- Router: Es un Router inalámbrico. En este modo se crea una red independiente de la red cableada, y se realizan todas las funciones de un Router a la vez que de un AP.
- Bridge: Funciona como un puente inalámbrico. En este modo no se crea una red, se extiende la red existente. El AP reenvía los paquetes entre la red cableada y la inalámbrica, sin realizar funciones de router.

#### Modo Router
Para conectar el AP en este modo, se debe usar su puerto WAN (Internet). En este modo, el AP genera su propia WLAN, y además se conecta con el otro router para tener acceso al resto de redes.

Ejemplo:
- RouterA usa su interfaz f0/0 para conectarse con el AP1, y el AP1 usa su interfaz Internet para conectarse con RouterA.
- Entre RouterA y AP1 usan la red 192.168.170.0/24 para conectarse entre sí. 
- Entonces, el RouterA tiene configurada la IP 192.168.170.254/24.
- Ademas, el AP creará la red 192.168.180.0/24 para su WLAN

Para configurar el AP, ingresar a la GUI:
```
**Pesataña Setup:**
En Internet Setup:
Connection Type -> Static IP
IP Address: IP de este router en la red con el otro router. En este ejemplo 192.168.170.253
Subnet Mask: Mascara de subred. En este ejemplo 255.255.255.0
Default Gateway: IP del otro router. En este ejemplo 192.168.170.254

Mas abajo en Network Setup:
IP Adress: IP de este router en la red de la WLAN. En este ejemplo 192.168.180.1
Subnet Mask: Mascara de subred. En este ejemplo 255.255.255.0
Activar DHCP Server
Elegir la IP inicial y la cantidad de usuarios(IPs) maxima

**Pestaña Wireless:**
Configurar lo que se pida sobre la red en Basic Wireless Setttings y Wireless Security
```

Luego ir a los dispositivos terminales de la WLAN y configurar la IP en DHCP y las credenciales de la red wireless.

#### Modo Bridge
Para conectar el AP en este modo, se debe usar alguno de sus puertos LAN. En este modo, el AP extiende la red LAN existente, creando su red WLAN. Actúa como un puente que conecta ambas.

Ejemplo:
- AP1 se conecta a SwitchA por su puerto LAN.
- SwitchA es parte de una red X.
- Entonces, el AP1 se conecta a la red X.
- El AP1 crea su red WLAN 192.168.168.64/27

Para configurar el AP, ingresar a la GUI:
```
**Pesataña Setup:**
En Internet Setup:
Connection Type -> Automatic Configuration - DHCP

Mas abajo en Network Setup:
IP Address: IP de este router en la red de la WLAN. En este ejemplo 192.168.168.65
Subnet Mask: Mascara de subred. En este ejemplo 255.255.255.224 (/27)
Activar DHCP Server
Elegir la IP inicial y la cantidad de usuarios(IPs) maxima

**Pestaña Wireless:**
Configurar lo que se pida sobre la red en Basic Wireless Setttings y Wireless Security
```

Luego ir a los dispositivos terminales de la WLAN y configurar la IP en DHCP y las credenciales de la red wireless.
