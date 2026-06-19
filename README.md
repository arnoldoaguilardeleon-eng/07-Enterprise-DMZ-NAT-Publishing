# 07 - Enterprise DMZ and Web Server Publishing

Simulación de red empresarial con Firewall perimetral, red interna y DMZ para publicación segura de servidor web hacia Internet.
Implementada en Cisco Packet Tracer.

## Escenario
Empresa con tres zonas de red conectadas mediante un Firewall perimetral:

- **LAN** → Usuarios internos de la empresa
- **DMZ** → Servidor web accesible públicamente desde Internet
- **Internet** → Representada mediante un ISP simulado

El objetivo es que los usuarios internos naveguen hacia Internet, que el servidor web sea accesible desde afuera, y que nadie externo pueda llegar directamente a la red interna.

## Topología
![Topologia](topologia.png)

```
                 INTERNET
                     |
                    ISP
                     |
              Router Externo
                     |
              Firewall Empresa
              /              \
            LAN              DMZ
             |                |
        Usuarios          Servidor Web
     192.168.10.0/24    192.168.30.10
```

## Direccionamiento
| Zona | Red | Gateway | Uso |
|---|---|---|---|
| LAN | 192.168.10.0/24 | 192.168.10.1 | Equipos internos |
| DMZ | 192.168.30.0/24 | 192.168.30.1 | Servidor público |
| Red externa Firewall | 200.1.1.0/24 | 200.1.1.2 | Comunicación con Internet |
| ISP | 203.0.113.0/24 | 203.0.113.1 | Proveedor simulado |
| Internet | 8.8.8.0/24 | 8.8.8.1 | Red externa de prueba |

## Tecnologías implementadas
- Firewall perimetral como punto de control entre LAN, DMZ e Internet
- NAT dinámico con PAT para salida de usuarios internos
- NAT estático para publicación del servidor web
- DMZ para aislar servicios públicos de la red interna

## Comandos clave

### NAT/PAT para salida a Internet
```cisco
ip nat inside source list 1 interface GigabitEthernet0/1 overload
```

Permite que múltiples equipos internos (`192.168.10.0/24`) salgan a Internet usando la IP pública `200.1.1.2` del Firewall.

### NAT estático para publicar el servidor web
```cisco
ip nat inside source static 192.168.30.10 200.1.1.10
```

Traduce la IP pública `200.1.1.10` hacia el servidor interno `192.168.30.10`, permitiendo acceso desde Internet vía `http://200.1.1.10`.

## Flujo de tráfico

| Origen | Destino | Resultado |
|---|---|---|
| LAN (192.168.10.2) | Internet | ✅ Permitido vía NAT/PAT |
| Internet (8.8.8.10) | Servidor Web (200.1.1.10 → 192.168.30.10) | ✅ Permitido vía NAT estático |
| Internet | LAN interna | ❌ Bloqueado |

## Concepto adicional descubierto
Una conexión realizada **desde dentro de la empresa hacia la propia IP pública del servidor** no funciona con la configuración NAT estándar. Esto requiere una técnica adicional llamada **NAT Hairpin** (o NAT Loopback), donde el Firewall debe permitir que el tráfico "regrese" hacia la misma red interna desde la que salió.

## Conclusión
Una red empresarial no solo consiste en conectar dispositivos, sino en diseñar una arquitectura segura donde cada zona cumple una función específica. La combinación de Firewall, NAT y DMZ permite ofrecer servicios públicos sin comprometer los recursos internos de la organización.

## Archivo Packet Tracer
[Descargar proyecto (.pkt)](Enterprise-DMZ-NAT-Publishing.pkt)
