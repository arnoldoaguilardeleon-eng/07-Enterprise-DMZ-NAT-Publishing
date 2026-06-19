# 07 - Enterprise DMZ and Web Server Publishing

Simulación de una red empresarial donde se implementa una arquitectura con Firewall, red interna, zona DMZ y publicación de un servidor web hacia Internet utilizando Cisco Packet Tracer.

El objetivo de este laboratorio es comprender cómo una empresa puede permitir que sus usuarios tengan acceso a Internet, mientras mantiene separados sus servidores públicos de la red interna.

---

# Escenario

Una empresa cuenta con diferentes zonas dentro de su infraestructura:

* **Red LAN (usuarios internos):** donde trabajan los empleados y equipos de la empresa.
* **DMZ (zona desmilitarizada):** una red separada donde se colocan servicios que necesitan ser accesibles desde Internet.
* **Internet:** representada mediante un ISP simulado.

La idea principal es que:

* Los usuarios internos puedan navegar hacia Internet.
* Un servidor web pueda ser accesible desde Internet.
* Los usuarios externos no puedan acceder directamente a la red interna.

---

# Diseño de la red

```
                 INTERNET
                     |
                     |
                    ISP
                     |
                     |
              Router Externo
                     |
                     |
              Firewall Empresa
              /              \
             /                \
            LAN              DMZ
             |                |
        Usuarios          Servidor Web
     192.168.10.0/24    192.168.30.10
```

---

# Direccionamiento IP

| Zona                 | Red             | Gateway      | Uso                       |
| -------------------- | --------------- | ------------ | ------------------------- |
| LAN                  | 192.168.10.0/24 | 192.168.10.1 | Equipos internos          |
| DMZ                  | 192.168.30.0/24 | 192.168.30.1 | Servidor público          |
| Red externa Firewall | 200.1.1.0/24    | 200.1.1.2    | Comunicación con Internet |
| ISP                  | 203.0.113.0/24  | 203.0.113.1  | Proveedor simulado        |
| Internet             | 8.8.8.0/24      | 8.8.8.1      | Red externa de prueba     |

---

# Tecnologías implementadas

## Firewall perimetral

El Firewall funciona como punto de control entre la red empresarial e Internet.

Su función es:

* Separar la red interna de Internet.
* Controlar el tráfico que entra y sale.
* Aplicar traducciones de direcciones IP.

---

## NAT/PAT para salida a Internet

Dentro de una empresa, los dispositivos normalmente utilizan direcciones privadas.

Ejemplo:

```
PC interna:

192.168.10.2
```

Estas direcciones no pueden salir directamente a Internet.

Por eso se utiliza NAT/PAT:

```
192.168.10.2

        ↓

200.1.1.2

        ↓

Internet
```

El Firewall traduce múltiples equipos internos utilizando una sola dirección pública.

Configuración utilizada:

```cisco
ip nat inside source list 1 interface GigabitEthernet0/1 overload
```

---

# Publicación del servidor Web

Los servidores públicos no se colocan directamente dentro de la red interna.

Por seguridad se utiliza una DMZ.

Ejemplo:

```
Servidor Web:

IP privada:
192.168.30.10
```

Pero los usuarios de Internet no conocen esa dirección.

Por eso se crea una traducción:

```
IP pública:

200.1.1.10

        ↓

IP privada:

192.168.30.10
```

Configuración:

```cisco
ip nat inside source static 192.168.30.10 200.1.1.10
```

Ahora un usuario externo puede ingresar mediante:

```
http://200.1.1.10
```

y el Firewall redirige la petición al servidor interno.

---

# Funcionamiento del tráfico

## Usuario interno hacia Internet

Ejemplo:

```
PC LAN
192.168.10.2

       ↓

Firewall

       ↓

NAT/PAT

       ↓

Internet
```

Resultado:

✅ Permitido

---

## Usuario externo hacia servidor Web

Ejemplo:

```
Internet

8.8.8.10

       ↓

200.1.1.10

       ↓

Firewall

       ↓

192.168.30.10

Servidor Web
```

Resultado:

✅ Permitido

---

## Usuario externo hacia red interna

Ejemplo:

```
Internet

       ↓

Firewall

       ↓

LAN usuarios
```

Resultado:

❌ Bloqueado

La red interna no debe estar expuesta públicamente.

---

# Pruebas realizadas

## Conectividad LAN

Se comprobó que los usuarios internos tienen comunicación con el Firewall.

Resultado:

✅ Correcto

---

## Salida a Internet

Se realizó una prueba desde la LAN hacia la red externa.

Resultado:

✅ Correcto

---

## Traducciones NAT

Se verificaron las traducciones creadas por el Firewall.

Ejemplo:

```
192.168.10.2

        ↓

200.1.1.2
```

Resultado:

✅ Correcto

---

## Acceso externo al servidor Web

Desde una red externa se accedió al servidor mediante su dirección pública:

```
http://200.1.1.10
```

Resultado:

✅ Correcto

---

# Aprendizaje del laboratorio

Este laboratorio permitió comprender cómo funciona una arquitectura empresarial básica:

* La LAN protege los equipos internos.
* La DMZ permite publicar servicios sin exponer la red completa.
* NAT permite comunicación entre redes privadas e Internet.
* El Firewall funciona como punto de seguridad entre diferentes zonas.

También se comprendió que una conexión desde dentro de la empresa hacia la propia IP pública del servidor requiere una configuración adicional llamada NAT Hairpin o NAT Loopback.

---

# Conclusión

Una red empresarial no solamente consiste en conectar dispositivos, sino en diseñar una arquitectura segura donde cada zona tenga una función específica.

La implementación de Firewall, NAT y DMZ permite ofrecer servicios públicos manteniendo protegidos los recursos internos de la organización.

---

# Archivo Packet Tracer

`Enterprise-DMZ-NAT-Publishing.pkt`
