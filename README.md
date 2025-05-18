# Direccionamiento y Enrutamiento de Redes IPv4 🖧🚀

## Descripción
¡Bienvenidos al proyecto de **Direccionamiento y Enrutamiento de Redes IPv4**! 🌐 Este proyecto, desarrollado en **2024** por **Pedro Antonio Ruiz Mendoza** y **Andrés Martínez Lorca** como parte de la asignatura *Arquitectura de Redes* en la **Facultad de Informática** de la Universidad de Murcia, utiliza **Cisco Packet Tracer** para diseñar, configurar y conectar dos organizaciones (A y B) con direccionamiento IPv4, enrutamiento intra-dominio e interconexión. 🛠️

El objetivo es crear una red funcional que simule un escenario profesional, configurando subredes, enrutamiento con **RIP** (Organización A) y **OSPF** (Organización B), y logrando la interconexión mediante redistribución de rutas. Incluye agregación de rutas y análisis detallado de técnicas como *split horizon*, *poison reverse* y *triggered updates*. ¡Una experiencia práctica que combina teoría y aplicación real! 🎓

## Características Principales 🌟
- **Direccionamiento IPv4** 📍:
  - Organización A: Rango `172.78.0.0/20`, con subredes para LANs (500, 130, 58 hosts) y enlaces P2P.
  - Organización B: Rango `172.78.16.0/20`, con subredes para LANs (225, 210, 100, 50, 35, 20, 5 hosts) y enlaces P2P.
- **Enrutamiento Intra-dominio** 🔄:
  - Organización A: Protocolo **RIP v2** para enrutamiento dinámico.
  - Organización B: Protocolo **OSPF** con áreas (*stub* y *totally stub*) para optimizar tablas de rutas.
- **Interconexión** 🔗: Redistribución de rutas entre RIP y OSPF a través del router frontera (R7).
- **Agregación de Rutas** 📉: Simplificación de tablas de enrutamiento mediante resumen de subredes.
- **Análisis Avanzado** 🕵️‍♂️:
  - Uso de comandos como `show ip route`, `debug ip rip`, y `tracert` para analizar rutas y convergencia.
  - Estudio de técnicas anti-bucles (*split horizon*, *poison reverse*, *triggered updates*).

## Estructura del Proyecto 📂
- **Archivos Clave**:
  - `MemoriaPracticas.pdf`: Documento original con la descripción detallada de la práctica.
  - Archivos de configuración de Cisco Packet Tracer (no incluidos en el repositorio, pero referenciados en la memoria).
- **Documentación Adicional**: Consulta `Memoria.md` en este repositorio para un resumen detallado de la práctica.
- **Topologías**: Diseñadas en Cisco Packet Tracer, con routers, switches y hosts configurados para ambas organizaciones.

## Requisitos e Instalación ⚙️
1. **Requisitos Previos**:
   - **Cisco Packet Tracer** (versión compatible con el curso 2024/2025).
   - Sistema operativo compatible (Windows, macOS, Linux).
   - Conocimientos básicos de redes y protocolos RIP/OSPF.
2. **Clonar el Repositorio**:
   ```bash
   git clone https://github.com/andreesmrtnz/ArquitecturaRedes.git
   cd ipv4-networking
   ```
3. **Ejecutar la Topología**:
   - Abre los archivos `.pkt` de Cisco Packet Tracer (si están disponibles).
   - Sigue las instrucciones de configuración en `Memoria.md` para replicar las topologías.

## Ejemplo de Uso 🎮
1. **Configurar un Router con RIP (Organización A)**:
   ```bash
   enable
   configure terminal
   router rip
   version 2
   network 172.78.0.0
   network 172.78.4.0
   ```
2. **Configurar un Router con OSPF (Organización B)**:
   ```bash
   enable
   configure terminal
   router ospf 100
   network 172.78.16.0 0.0.0.255 area 0
   area 1 stub
   ```
3. **Verificar Rutas**:
   ```bash
   show ip route
   ```
   Analiza las tablas de rutas para confirmar la conectividad entre subredes.

## Ejemplo de Análisis 📊
- **Tabla de Rutas (R4, Organización A)**:
  ```plaintext
  R 172.78.4.32/30 [120/2] via 172.78.4.6, 00:00:06, Serial0/1/1
  R 172.78.4.32/30 [120/2] via 172.78.4.14, 00:00:01, Serial0/0/0
  R 172.78.4.32/30 [120/2] via 172.78.4.22, 00:00:05, Serial0/0/1
  ```
  Tres rutas equivalentes hacia `172.78.4.32/30` (interfaz de R2 que conecta con Organización B), mostrando balanceo de carga en RIP.
- **Traceroute (Host2 a Host8)**:
  Traza el camino entre organizaciones, pasando por el router frontera R7, que redistribuye rutas entre RIP y OSPF.

## Contribuciones y Agradecimientos 🙌
Desarrollado por **Pedro Antonio Ruiz Mendoza** (Grupo 2.1, 23958367A) y **Andrés Martínez Lorca** (Grupo 2.2, 49248948Z) durante el curso 2024/2025. Agradecemos a la **Facultad de Informática** y a los profesores de *Arquitectura de Redes* por proporcionar los guiones de prácticas y el soporte necesario. 🙏

Si te ha gustado este proyecto, ¡déjanos una ⭐ en GitHub! 😊 ¿Tienes sugerencias? ¡Abre un *issue* o un *pull request*! 

## Licencia 📜
Este proyecto está licenciado bajo la **Licencia MIT**. Consulta el archivo `LICENSE` para más detalles.