# Tutorial Packet Tracer

## Utilidades
| Comando | Funcion |
|---------|---------|
Control + Shift + 6 | Parar proceso corriendo 
Control + z         | Volver atras
Shift + Insert      | Pegar texto copiado
Tab                 | Autocompletar entrada
Flecha Arriba       | Reinsertar comando anteriores
?                   | Obtener documentacion sobre un comando o parametro
exit                | Salir del modo actual, volver al anterior

## Modos
*Nota: Los modos (en general) van de menor a mayor permisos, y de menor a mayor especificidad*

|Modo | Funcion | Comando para ingresar |
|-----|---------|-----------------------|
[U] \> Usuario                  | A penas entras, default | -
[P] # Privilegiado                | Modo privilegiado | enable
[CG] (config) Configuracion Global       | Configuracion general  | configure terminal
[CE] (config-X) Configuracion Especifica   | Configuraciones especificas | interface \<interfaz\>, entre otros

## Output de "show interfaces"
|Output | Explicacion |
|-------|-------------|
Interface is up                    | Está habilitado y tiene algo conectado
Interface is down                  | Está habilitado y no tiene algo conectado
Interface is administratively down | Deshabilitado. No se puede usar hasta que se habilite manualmente. Se pasa a este estado ante una violacion de seguridad donde se indique shutdown

## Configuraciones

### Nombre
```
[P] hostname <nombre>
```

### Contraseña de [P]
```
[CG] enable password <contra>
```

### 

### Mostrar info
```
show <cosa>
show running-config
show interface
show vlan
show access-lists
show ip route | Tabla de IPs
show ip protocol | Protocolo de enrutamiento
```

### Interfaces
#### Interfaz especifica
```
[P] interface f 0/0
```

#### Rango de interfaces
```
interface range f0/4-24
```

### Seguridad de interfaz
| Comando  | Explicación |
|----------|-------------|
[Interfaz] switchport mode access  | Configurar puerto en modo acceso 
switchport port-security                     | Activar la seguridad del puerto 
switchport port-security maximum 1           | Numero maximo de MAC 
switchport port-security mac-address \<mac\> | Asignar de MAC permitida 
switchport port-security violation shutdown  | Establecer accion ante violacion. En este caso apagar puerto 

### IP administrativa
```
[CG] interface vlan 1
ip address <ip> <mascara>
no shutdown
```

### Interfaz serial
| Comando  | Explicación |
|----------|-------------|
[CG] interface serial 0/0 | Ingresar a interfaz
encapsulation ppp         | Configurar protocolo de encapsulacion
ip address <IP> <máscara> | Configurar IP y mascara
clock rate 2000000        | Velocidad de reloj (SOLO en extremo DCE, icono relojito)
no shutdown               | Encender interfaz

### VLANs
*Notas:*
- Todos los switches involucrados deben tener configuradas las vlans para poder direccionarlas correctamente.
- Por default todo está en la VLAN 1.


#### Crear VLANs
```
[CG] vlan <numero>
name <nombre>
```

#### Asignar un puerto a una VLAN
```
[Interfaz] switchport access vlan <numVlan>
```

#### Trunk
```
[Interfaz] switchport mode trunk
```

### Acceso remoto SSH
*Notas:*
- Existen 16 VTYs (Virtual Terminal). Desde 0 hasta 15.

Primero activar acceso SSH en VTY 0.

| Comando | Explicacion |
|---------|-------------|
[CG] ip domain-name dominio.com                     | Configurar nombre de dominio
crypto key generate rsa                             | Generar claves RSA (Se debe usar 1024, no el default)
ip ssh version 2                                    | Configurar versión de SSH
line vty 0                                          | Configurar terminal virtual (VTY) 0
transport input ssh                                 | Configurar acceso SSH
login local                                         | Configurar autenticacion
username \<user\> privilege 15 password \<pass\>    | Especificar user y pass

Por ultimo desactivar acceso en el resto de VTY.
| Comando | Explicacion |
|---------|-------------|
line vty 1 15         | Configurar VTY 1 a 15
transport input none  | Deshabilitar acceso remoto

### Acceso remoto TELNET
| Comando | Explicación |
|---------|-------------|
line vty 0 1                 | Configurar VTY 0 y 1
login                        | Configurar autenticación
password \<contra\>          | Establecer contraseña
exec-timeout \<minutos\>     | Timeout antes de terminar sesion

### Spanning tree
Configurar este switch como root.
```
[CG] spanning-tree vlan <vlan1,vlanN> root primary
```

### Enlace LACP
Este es un enlace que une dos puertos fisicos logicamente como si fueran el mismo. Esto se usa para evitar que spanning tree corte un bucle. Esto permite duplicar el ancho de banda de una conexion utilizando dos puertos a la vez.
```
port-channel load-balance {dst-mac | src-mac}
interface gigabitethernet 1/1
switchport mode trunk
channel-protocol LACP
```

### Protocolo RIP
| Comando         | Descripción                                      |
|-----------------|--------------------------------------------------|
router rip        | Entra al modo de configuración del router para RIP      
version 2         | Establece la versión de RIP en 2 
network w.x.y.z   | Especifica una red para incluirla en la tabla 

*Nota: Las network deben estar en ip CLASSFULL. Es decir, la direccion de red (no subred) con la mascara default.*

#### Evitar la publicacion de RIP en una interfaz
```
[Router rip] passive-interface <interfaz>
```

#### Establecer rutas sumarizadas CIDR
```
ip route <prefijo> <mascara> <interfaz>
```
Publico este prefijo, donde si una IP tiene el mismo prefijo, que me la manden porque ya la manejo.

#### Redistribuir rutas estaticas en el procotolo de ip dinamica
```
redistribute static
```

### Lista de acceso

### Entrenar el patoba (crear la acess list)
```
[CG] access-list <numAccessList> <action> <ip> <wildcard> | Estandar
[CG] access-list <numAccessList> <action> <ipOrigen> <wildcardOrigen> <ipDestino> <wildcardDestino> | Extendida
```

TODO: googlear en que orden se establecen las access list al poner varias

numAccessList: Identificador de la access list 
- 1-99 Estandar
- 100-199 Extendida

action:
- permit
- deny

ip: La ip a matchear con el wildcard
wildcard: Los bits que esten en las mismas posiones a los 0 de la wildcard, deben coincidir con esa misma posicion de la ip.

#### Poner el patoba a laburar (Activar la access list en una interfaz)
```
[Interfaz] ip access-group <numAccessList> <direccion>
```

direccion:
- in: Paquetes que entran
- out: Paquetes que salen



### TODO - Revisar TP 3 desde pagina 6 inclusive y TP4

## TODO
- IpSec
- 
