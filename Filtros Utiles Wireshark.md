---
modifiedDate: 2024-11-14T17:37:59-03:00
createdDate: 2024-11-14T17:10:44-03:00
---
Filtros útiles para el segundo parcial de laboratorio con Wireshark.
Notas:
- Todo va en minuscula siempre, no funciona en mayuscula.
- Si la barra está roja, el filtro es invalido y no filtra.
- Para confirmar un filtro, se debe presionar enter.
- Para quitar los filtros, se debe vaciar el campo, y presionar enter.
## Filtros de protocolo
### Un protocolo
```
tcp
```
### Operador OR
```
tcp || udp
```
### Operador NOT
```
!udp
```
### Combinación
```
!(tcp || udp)
```
## Filtros de IP
### IP (en src o des)
```
ip.addr == 192.168.0.11
```
### IP Source
```
ip.src == 192.168.0.11
```
### IP Destination
```
ip.dst == 192.168.0.11
```
## Filtros de MAC
### MAC (en src o des)
```
eth.addr == ec:55:f9:76:20:23
```
### MAC Source
```
eth.src == 192.168.0.11
```
### MAC Destination
```
eth.dst == 192.168.0.11
```