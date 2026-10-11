# CCNA Lab 06 | Redundancia y Failover 🔧

**Laboratorio práctico en Cisco Packet Tracer | CCNA 200-301**

¿Qué pasa si se cae un enlace o deja de funcionar el router que utilizan los usuarios?

En este laboratorio armé una red con redundancia para probar distintos escenarios de falla y comprobar cómo se recupera la conectividad.

## Topología

La red está formada por **3 routers, 3 switches y 3 PCs**.

- **R1 y R2:** realizan el enrutamiento entre VLANs y proporcionan gateways redundantes mediante HSRP.
- **R3:** simula una red remota para realizar pruebas de conectividad.
- **S1, S2 y S3:** conectan los dispositivos mediante VLANs, trunks y enlaces redundantes.

Utilicé tres VLANs:

| VLAN | Red | Gateway virtual |
|---|---|---|
| 15 - ADMIN | 192.168.15.0/24 | 192.168.15.254 |
| 25 - STAFF | 192.168.25.0/24 | 192.168.25.254 |
| 35 - SERVERS | 192.168.35.0/24 | 192.168.35.254 |

La red remota de prueba está representada por la Loopback0 de R3: `172.30.100.1`.

## ⚙️ Configuración realizada

**Switching:** configuré VLANs, VTP, trunks 802.1Q y un EtherChannel con LACP entre S1 y S2. También verifiqué STP para evitar bucles en los enlaces redundantes.

**Routing:** configuré Router-on-a-Stick en R1 y R2, y OSPF en área 0 para intercambiar rutas entre los tres routers.

**Redundancia:** implementé HSRP para que R1 fuera Active en las VLANs 15 y 35, mientras R2 fuera Active en la VLAN 25.

También configuré una ruta estática flotante en R1 como alternativa al camino principal aprendido por OSPF.

**Seguridad:** utilicé una ACL extendida para impedir que los equipos de VLAN 25 accedieran a la red `172.30.100.0/24`, permitiendo el resto del tráfico.

## Pruebas y troubleshooting

Después de configurar la red, simulé varias fallas:

**1. Caída del enlace R1–R3**

Al apagar el enlace directo, R1 utilizó la ruta estática flotante a través de R2. Cuando restauré el enlace, volvió a utilizar OSPF.

**2. Falla de R2**

Apagué la interfaz LAN de R2 y comprobé que R1 asumiera el rol Active para VLAN 25 mediante HSRP. Al restaurar R2, recuperó su rol original.

**3. Verificación de la ACL durante el failover**

Durante las pruebas descubrí que, al pasar el tráfico por R1, la restricción de acceso ya no se cumplía porque todavía no había configurado la ACL en ese router.

Corregí la configuración y repetí los pings para comprobar que la política de seguridad funcionara con cualquiera de los dos routers activos.

También resolví problemas de direccionamiento IP y de aprendizaje de rutas OSPF durante el laboratorio.

## Archivo Packet Tracer

El archivo `.pkt` contiene la topología y las configuraciones de los dispositivos.

Se puede abrir en Cisco Packet Tracer para revisar los protocolos, ejecutar comandos de verificación y reproducir las pruebas de failover.

## Qué aprendí

Este laboratorio me ayudó a entender mejor cómo trabajan juntas las tecnologías de switching, routing, redundancia y seguridad.

Lo más útil fue practicar troubleshooting: encontrar qué estaba fallando, revisar las configuraciones y comprobar que los cambios realmente solucionaran el problema.

Sigo construyendo mis conocimientos y habilidades prácticas para el **CCNA 200-301**, un laboratorio a la vez.
