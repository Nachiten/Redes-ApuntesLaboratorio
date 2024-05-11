# Tutorial Packet Tracer

## Utilidades
| Comando | Funcion |
|---------|---------|
Control + Shift + 6 | Parar proceso corriendo 
Shift + Insert      | Pegar texto copiado
Tab                 | Autocompletar entrada
Flecha Arriba       | Reinsertar comando anteriores
?                   | Obtener documentacion sobre un comando o parametro
exit                | Salir del modo actual, volver al anterior

## Modos
*Nota: Los modos (en general) van de menor a mayor permisos, y de menor a mayor especificidad*

|Modo | Funcion | Comando para ingresar |
|-----|---------|-----------------------|
[U] Usuario                     | A penas entras, default | -
[P] Privilegiado                | Modo privilegiado | enable
[CG] Configuracion Global       | Configuracion general  | configure terminal
[CE] Configuracion Especifica   | Configuraciones especificas como interfaces | interface \<interfaz\>

## Entendiendo el output de "show interfaces"
|Output | Explicacion |
|--|--|
Interface is up | Está habilitado y tiene algo conectado
Interface is down | Está habilitado y no tiene algo conectado
Interface is administratively down |  Deshabilitado. No se puede usar hasta que se habilite manualmente.

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
show <cosa>
show interface
show running-config

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
switchport mode access                       | Configurar puerto en modo acceso 
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
encapsulation ppp | Configurar protocolo de encapsulacion
ip address <IP> <máscara> | Configurar IP y mascara
clock rate 2000000 | Velocidad de reloj (SOLO en extremo DCE, icono relojito)
no shutdown | Encender interfaz

### VLANs
*Nota: Todos los switches involucrados deben tener configuradas las vlans para poder direccionarlas correctamente.*

#### Crear VLANs
```
[CG] vlan <numero>
name <nombre>
```

#### Asignar un puerto a una VLAN
```
[CG] interface <interfaz>
switchport access vlan <numVlan>
```

#### Trunk
```
[CG] interface <interfaz>
switchport mode trunk
```

### Acceso remoto SSH
Primero, activar acceso SSH en VTY 0

| Comando | Explicacion |
|---------|-------------|
ip domain-name dominio.com                    | Configurar nombre de dominio
crypto key generate rsa                       | Generar claves RSA (Se debe usar 1024, no el default)
ip ssh version 2                              | Configurar versión de SSH
line vty 0                                    | Configurar terminal virtual (VTY) 0
transport input ssh                           | Configurar acceso SSH
login local                                   | Configurar autenticacion
username redes privilege 15 password cisco    | Especificar user y pass

Ultimo, desactivar acceso en el resto de VTY
| Comando | Explicacion |
|---------|-------------|
line vty 1 15                                 | Configurar VTY 1 a 15
transport input none                          | Deshabilitar acceso remoto

### Acceso remoto TELNET
| Comando | Explicación |
|---------|-------------|
| line vty 0 1                 | Configurar VTY 0 y 1
| login                        | Configurar autenticación
| password \<contra\>          | Establecer contraseña
| exec-timeout \<minutos\>     | Timeout antes de terminar sesion

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


### TODO - Protocolo RIP
### TODO - Revisar TP 3 desde pagina 6 inclusive y TP4