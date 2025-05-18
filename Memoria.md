# Memoria de Prácticas: Direccionamiento y Enrutamiento de Redes IPv4 📚

## 1. Introducción
Esta memoria documenta la práctica de **Direccionamiento y Enrutamiento de Redes IPv4**, realizada en 2024 por **Pedro Antonio Ruiz Mendoza** (Grupo 2.1, 23958367A) y **Andrés Martínez Lorca** (Grupo 2.2, 49248948Z) para la asignatura *Arquitectura de Redes* en la **Facultad de Informática** de la Universidad de Murcia. 🌟

El objetivo es diseñar, configurar y conectar dos organizaciones (A y B) utilizando **Cisco Packet Tracer**, abordando:
1. Direccionamiento IPv4 para subredes de LANs y enlaces punto a punto (P2P).
2. Enrutamiento intra-dominio con **RIP v2** (Organización A) y **OSPF** (Organización B).
3. Interconexión entre organizaciones mediante redistribución de rutas.
4. Agregación de rutas para optimizar tablas de enrutamiento.
5. Análisis de técnicas de enrutamiento como *split horizon*, *poison reverse* y *triggered updates*.

La práctica simula un escenario profesional, apoyándose en guiones de prácticas del Aula Virtual que guían el uso de Cisco Packet Tracer y la configuración de las topologías. 🖥️

## 2. Direccionamiento

### 2.1 Organización A
- **Rango Asignado**: `172.78.0.0/20`.
- **Subredes**:
  - **LAN 1.1**: 500 hosts, `172.78.0.0/23` (510 direcciones, máscara `255.255.254.0`).
  - **LAN 1.2**: 130 hosts, `172.78.2.0/24` (254 direcciones, máscara `255.255.255.0`).
  - **LAN 1.3**: 58 hosts, `172.78.3.0/26` (62 direcciones, máscara `255.255.255.192`).
  - **P2P (1.1 a 1.9)**: Enlaces punto a punto con `/30` (2 direcciones utilizables por enlace), desde `172.78.4.0/30` hasta `172.78.4.32/30`.
- **Cálculo**:
  - LAN 1.1: 9 bits para hosts (`2^9 = 512`), suficiente para 500 hosts.
  - LAN 1.2: 8 bits (`2^8 = 256`), suficiente para 130 hosts.
  - LAN 1.3: 6 bits (`2^6 = 64`), suficiente para 58 hosts.
  - P2P: 2 bits (`2^2 = 4`, 2 utilizables), ideal para conectar routers.

### 2.2 Organización B
- **Rango Asignado**: `172.78.16.0/20`.
- **Subredes**:
  - **LAN 2.1**: 225 hosts, `172.78.16.0/24` (254 direcciones, máscara `255.255.255.0`).
  - **LAN 2.2**: 210 hosts, `172.78.17.0/24` (254 direcciones, máscara `255.255.255.0`).
  - **LAN 2.3**: 100 hosts, `172.78.18.0/25` (126 direcciones, máscara `255.255.255.128`).
  - **LAN 2.4**: 50 hosts, `172.78.18.128/26` (62 direcciones, máscara `255.255.255.192`).
  - **LAN 2.5**: 35 hosts, `172.78.19.0/26` (62 direcciones, máscara `255.255.255.192`).
  - **LAN 2.6**: 20 hosts, `172.78.19.64/27` (30 direcciones, máscara `255.255.255.224`).
  - **LAN 2.7**: 5 hosts, `172.78.18.192/29` (6 direcciones, máscara `255.255.255.248`).
  - **P2P (2.1 a 2.6)**: Enlaces punto a punto con `/30`, desde `172.78.19.96/30` hasta `172.78.19.116/30`.
- **Cálculo**:
  - LAN 2.1 y 2.2: 8 bits (`2^8 = 256`), suficiente para 225 y 210 hosts.
  - LAN 2.3: 7 bits (`2^7 = 128`), suficiente para 100 hosts.
  - LAN 2.4 y 2.5: 6 bits (`2^6 = 64`), suficiente para 50 y 35 hosts.
  - LAN 2.6: 5 bits (`2^5 = 32`), suficiente para 20 hosts.
  - LAN 2.7: 3 bits (`2^3 = 8`), suficiente para 5 hosts.
  - P2P: 2 bits, ideal para conexiones router-router.

- **Diseño**: Las subredes se asignaron en bloques contiguos para facilitar la agregación futura (ej., LAN 2.3, 2.4 y 2.7 en `172.78.18.0/24`).

## 3. Enrutamiento Intra-dominio

### 3.1 Organización A (RIP v2)
- **Protocolo**: RIP versión 2, un protocolo de enrutamiento de vector-distancia.
- **Configuración**:
  ```bash
  enable
  configure terminal
  router rip
  version 2
  network 172.78.0.0
  network 172.78.2.0
  network 172.78.3.0
  network 172.78.4.0
  ```
- **Análisis**:
  - **Tabla de Rutas (R4)**: Muestra múltiples rutas hacia `172.78.4.32/30` (interfaz de R2 que conecta con Organización B), con métrica `[120/2]` a través de tres interfaces (`Serial0/1/1`, `Serial0/0/0`, `Serial0/0/1`). Esto indica balanceo de carga, ya que RIP considera las rutas equivalentes.
  - **Split Horizon**: Observado en `debug ip rip` (Router 6). Las rutas recibidas por `Serial0/0/0` (ej., `172.78.0.0/23`) no se reenvían por la misma interfaz, evitando bucles.
  - **Convergencia tras Fallo**: Al desactivar la interfaz de R4 hacia R2, RIP utiliza *poison reverse* (anunciando métrica 16 para rutas inválidas) y *triggered updates* para converger rápidamente a una nueva ruta. Por ejemplo, R4 recibe actualizaciones con métrica 16 desde `172.78.4.9`, indicando que rutas como `172.78.2.0/24` son inalcanzables por ese camino.

### 3.2 Organización B (OSPF)
- **Protocolo**: OSPF, un protocolo de estado de enlace con soporte para áreas.
- **Configuración**:
  ```bash
  enable
  configure terminal
  router ospf 100
  network 172.78.16.0 0.0.0.255 area 0
  network 172.78.17.0 0.0.0.255 area 0
  network 172.78.18.0 0.0.0.255 area 1
  network 172.78.19.0 0.0.0.255 area 1
  area 1 stub
  ```
- **Análisis**:
  - **Áreas**: Organización B usa área 0 (backbone) y área 1 (stub), reduciendo las entradas en las tablas de rutas al evitar LSAs de tipo 5.
  - **Tabla de Rutas (R7)**: Incluye rutas OSPF inter-área (`O IA`) para redes de otras áreas, rutas conectadas (`C`) y rutas RIP redistribuidas desde Organización A.
  - **Ventajas**: OSPF genera menos entradas en las tablas de rutas que RIP, gracias a la jerarquía de áreas y la agregación.

## 4. Interconexión y Redistribución de Rutas
- **Router Frontera (R7)**: Configurado para redistribuir rutas entre RIP (Organización A) y OSPF (Organización B).
- **Configuración**:
  ```bash
  router ospf 100
  redistribute rip subnets
  router rip
  redistribute ospf 100 metric 2
  ```
- **Análisis**:
  - Un *traceroute* de Host2 (Organización A) a Host8 (Organización B) muestra el camino a través de R7, que actúa como punto de unión.
  - R3 (RIP) obtiene información de Organización B (OSPF) gracias a la redistribución en R7, permitiendo comunicación entre dominios con protocolos diferentes.

## 5. Agregación de Rutas
- **Objetivo**: Simplificar tablas de enrutamiento resumiendo subredes contiguas.
- **Ejemplo**:
  - En Organización B, las subredes `172.78.16.0/24` y `172.78.17.0/24` se agregan como `172.78.16.0/23`.
- **Configuración**:
  ```bash
  router ospf 100
  area 0 range 172.78.16.0 255.255.254.0
  ```
- **Beneficios**:
  - Reduce el tamaño de las tablas de rutas.
  - Disminuye el tráfico de actualizaciones.
  - Mejora la escalabilidad y facilidad de administración.

## 6. Conclusión
La práctica logró una topología funcional con dos organizaciones interconectadas:
- **Organización A**: Usa RIP v2, con tablas de rutas más extensas debido a su naturaleza de vector-distancia.
- **Organización B**: Usa OSPF con áreas, optimizando las tablas de rutas mediante *stub areas* y agregación.
- **Interconexión**: R7 redistribuye rutas, permitiendo comunicación fluida entre dominios.

**Lecciones Aprendidas**:
- RIP es más sencillo pero genera más entradas y es menos eficiente en redes complejas.
- OSPF ofrece mayor escalabilidad y flexibilidad gracias a las áreas y la agregación.
- La redistribución de rutas es clave para conectar redes heterogéneas.

## 7. Valoraciones Personales
- **Pedro Antonio Ruiz Mendoza**: "Esta práctica ha sido una oportunidad única para aplicar conceptos teóricos en un entorno simulado. Configurar RIP y OSPF, y ver cómo convergen las rutas tras un fallo, me ha ayudado a entender la importancia de los protocolos de enrutamiento en redes reales. Los guiones de prácticas fueron muy útiles, y la teoría de la asignatura complementó perfectamente la práctica." 😊
- **Andrés Martínez Lorca**: "Me ha sorprendido lo práctico que es Cisco Packet Tracer para simular redes complejas. Trabajar con áreas en OSPF y analizar técnicas como *split horizon* me ha dado una visión clara de cómo optimizar redes. La práctica no se sintió larga, gracias a la claridad de los guiones y la relación con la teoría." 🚀

La práctica reforzó nuestra comprensión de la *Arquitectura de Redes* y nos preparó para escenarios profesionales, desde el diseño de subredes hasta la gestión de enrutamiento dinámico.

## 8. Agradecimientos
Agradecemos a la **Facultad de Informática** de la Universidad de Murcia y al equipo docente de *Arquitectura de Redes* por proporcionar los recursos y guías necesarios. Este proyecto no habría sido posible sin su apoyo. 🙌