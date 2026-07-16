# 🌐 miip — Utilidad de diagnóstico de red para Linux

**miip** es una pequeña suite de scripts en Bash que recopila, en un único vistazo, toda la información esencial de red y sistema de una máquina Linux: interfaces, IP local, IP pública, puerta de enlace, tabla ARP, DNS, carga del sistema, memoria y discos.

Está pensada para administradores de sistemas, técnicos de soporte y pentesters que necesitan un diagnóstico rápido de "¿dónde estoy y cómo está conectada esta máquina?" sin tener que teclear media docena de comandos distintos.

> Proyecto de [hackingyseguridad.com](http://www.hackingyseguridad.com) · [www.hackingyseguridad.com/miip.htm](http://www.hackingyseguridad.com/miip.htm)

---

## 📑 Tabla de contenidos

1. [Características](#-características)
2. [Scripts incluidos](#-scripts-incluidos)
3. [Requisitos](#-requisitos)
4. [Instalación](#-instalación)
5. [Uso](#-uso)
6. [Ejemplo de salida](#-ejemplo-de-salida)
7. [Comparativa miip vs miip2](#-comparativa-miip-vs-miip2)
8. [Aviso legal y buenas prácticas](#-aviso-legal-y-buenas-prácticas)
9. [Licencia](#-licencia)

---

## ✨ Características

- 🔍 Detección automática de interfaces de red, IP local e IP pública.
- 🧭 Muestra la puerta de enlace (gateway) y la tabla de rutas.
- 📡 Vuelca la tabla ARP (direcciones MAC visibles en la red local).
- 🧾 Lista los servidores DNS configurados.
- 🖥️ Información del sistema: distribución, kernel, arquitectura, hostname.
- 📊 Estado de recursos: carga media, tiempo de actividad (*uptime*), memoria y espacio en disco.
- ⚡ Ejecutable sin instalación previa, directamente vía `curl` o `wget`.
- 🪶 Cero dependencias externas más allá de utilidades estándar de Linux.

---

## 🧩 Scripts incluidos

| Script | Propósito | Compatibilidad | Salida en color |
|---|---|---|---|
| `miip` | Versión original y ligera, pensada para kernels antiguos | Linux Kernel 3.x | ✅ |
| `miip2` | Versión ampliada, con banner ASCII y más datos de usuario/sesión | Kali Linux / Kernel 4.x+ | ➖ |
| `instalar.sh` | Instalador que copia `miip` y `miip2` a `/sbin/` para poder invocarlos como comandos globales | Cualquiera | — |
| `vertoodo.sh` | Script de enumeración post-explotación (recolección extendida de información del sistema: usuarios, procesos, servicios, claves SSH, historial, etc.) | Linux con herramientas de auditoría (`lscpu`, `dmidecode`, `netstat`...) | ✅ |

> ⚠️ `vertoodo.sh` está pensado como herramienta de *enumeración* en auditorías de seguridad **autorizadas** (post-explotación / hardening review), ya que también inspecciona credenciales, historial de comandos y ficheros con permisos SUID/SGID. Revisa el [aviso legal](#-aviso-legal-y-buenas-prácticas) antes de usarlo.

### ¿Qué datos recopila cada uno?

| Dato | `miip` | `miip2` |
|---|:---:|:---:|
| Interfaces de red (`ifconfig`) | ✅ | ✅ |
| IP interna | ✅ | ✅ |
| Máscara de red | ➖ | ✅ |
| Gateway / rutas | ✅ | ✅ |
| Tabla ARP | ✅ | ✅ |
| Servidores DNS | ✅ | ✅ |
| Sistema operativo | ✅ | ✅ |
| Distribución y versión | ➖ | ✅ |
| Arquitectura | ✅ | ✅ |
| Versión del kernel | ✅ | ✅ |
| Hostname | ➖ | ✅ |
| Usuario actual / usuarios conectados | ➖ | ✅ |
| Carga media (`loadavg`) | ✅ | ✅ |
| Tiempo de actividad (*uptime*) | ✅ | ✅ |
| Memoria (RAM) | ✅ | ✅ |
| Espacio en disco | ✅ | ✅ |
| IP pública | ✅ | ✅ |

---

## ⚙️ Requisitos

- Sistema Linux (probado en distribuciones basadas en Debian/Kali).
- Bash instalado.
- Utilidades estándar disponibles: `ifconfig`/`ip`, `route`, `arp`, `uname`, `free`, `df`, `wget`.
- Conexión a Internet (para consultar la IP pública vía `ipinfo.io`).
- Permisos de escritura en `/sbin/` si se usa `instalar.sh` (normalmente requiere `sudo`).

---

## 📥 Instalación

### Opción 1 — Clonar e instalar

```bash
git clone https://github.com/hackingyseguridad/miip.git
cd miip
sh instalar.sh
```

Esto copia `miip` y `miip2` a `/sbin/`, dejándolos disponibles como comandos del sistema.

### Opción 2 — Ejecutar directamente sin instalar (con conexión a Internet)

No hace falta clonar el repositorio: los scripts pueden lanzarse al vuelo con `curl` o `wget`.

```bash
# Con curl
curl -k https://raw.githubusercontent.com/hackingyseguridad/miip/master/miip | sh
curl -k https://raw.githubusercontent.com/hackingyseguridad/miip/master/miip2 | sh

# Con wget
wget https://raw.githubusercontent.com/hackingyseguridad/miip/master/miip -q -O - | bash
wget https://raw.githubusercontent.com/hackingyseguridad/miip/master/miip2 -q -O - | bash
wget https://raw.githubusercontent.com/hackingyseguridad/miip/master/vertoodo.sh -q -O - | bash
```

> 💡 Antes de ejecutar cualquier script directamente desde Internet en un sistema de producción, es buena práctica descargarlo primero y revisarlo (`cat`/`less`) para saber exactamente qué va a hacer.

---

## ▶️ Uso

Una vez instalado con `instalar.sh`, basta con invocar el comando desde cualquier ruta:

```bash
miip     # Versión simple (Kernel 3.x)
miip2    # Versión Kali Linux (Kernel 4.x)
```

---

## 🧪 Ejemplo de salida

```text
IP interfaces:
eth0: flags=... mtu 1500
        inet 192.168.1.35  netmask 255.255.255.0

Gateway:
Kernel IP routing table
Destination   Gateway       Genmask       Flags  Iface
0.0.0.0       192.168.1.1   0.0.0.0       UG     eth0

DNS:
8.8.8.8

Sistema:
GNU/Linux

Arquitectura:
x86_64

IP publica:
203.0.113.42
```

*(salida ilustrativa; el contenido real depende de la configuración de cada equipo)*

---

## 🆚 Comparativa miip vs miip2

```
┌─────────────────────────────┬───────────────┬────────────────┐
│ Aspecto                     │     miip      │     miip2      │
├─────────────────────────────┼───────────────┼────────────────┤
│ Kernel objetivo             │     3.x       │  4.x (Kali)    │
│ Banner ASCII                │      No       │       Sí       │
│ Detalle de usuarios/sesión  │     Básico    │    Ampliado    │
│ Máscara de red explícita    │      No       │       Sí       │
│ Distribución / versión OS   │      No       │       Sí       │
│ Verbosidad general          │     Media     │     Alta       │
└─────────────────────────────┴───────────────┴────────────────┘
```

En resumen: usa **`miip`** para un chequeo rápido y ligero en cualquier sistema Linux, y **`miip2`** cuando trabajes en Kali Linux y quieras un informe más completo de red y sesión de usuario.

---

## 🔐 Aviso legal y buenas prácticas

- Estos scripts consultan un servicio externo (`ipinfo.io`) para resolver la IP pública; ten en cuenta la privacidad si los ejecutas en entornos sensibles.
- `vertoodo.sh` recopila información extendida del sistema (usuarios, procesos, servicios, historial de comandos, claves SSH y ficheros con permisos especiales) y debe usarse **únicamente en sistemas propios o con autorización explícita** del propietario, como parte de una auditoría de seguridad legítima.
- El acceso no autorizado a sistemas de terceros está prohibido y puede constituir un delito según la legislación vigente. Usa estas herramientas de forma responsable.

---

## 📄 Licencia

Este proyecto se distribuye bajo licencia **GPL-3.0**. Consulta el fichero [`LICENSE`](./LICENSE) para más detalles.

---

<p align="center">
  <a href="http://www.hackingyseguridad.com">www.hackingyseguridad.com</a>
</p>
