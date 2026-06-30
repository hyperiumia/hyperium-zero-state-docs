# HYPERIUM ZERO-STATE v1.0.0

## Informe Tecnico de Version

**Fecha:** Junio 2026
**Autor:** Patricio Tirado — CEO, Hyperiumia
**Email:** hyperiumia@protonmail.com
**Licencia:** Proprietary — Hyperiumia
**Repositorio:** https://github.com/hyperiumia/hyperium-zero-state-source
**Binario:** 2.1 MB (release, optimizado)

---

## 1. RESUMEN EJECUTIVO

Hyperium ZERO-STATE v1.0.0 es un validador de integridad pre-engagement que certifica el estado inicial de un sistema antes de cualquier operacion ofensiva. Produce un acta notariada digital inmutable, sellada con SHA-256, conforme a ISO/IEC 27037:2012.

Esta version representa un salto cualitativo y cuantitativo respecto a v0.1.0: se paso de 46 tests y 4 modulos a **217 tests y 7 modulos de auditoria reales**, con 3 formatos de exportacion, API REST, dashboard web embebido, y despliegue Docker completo.

**Metricas clave:**
- Codigo fuente: 6,971 lineas Rust en 28 archivos
- Tests: 217 (de 46 en v0.1.0 → **+372%**)
- Modulos de auditoria: 7 (de 4 en v0.1.0 → **+75%**)
- Checks de seguridad totales: 73 (de 14 en v0.1.0 → **+421%**)
- Reglas YARA: 20 builtin
- Tiempo de scan completo: ~15 segundos
- Tamano binario: 2.1 MB
- Commits: 9 (de v0.1.0 a v1.0.0)

---

## 2. ARQUITECTURA DEL SISTEMA

### 2.1 Estructura de Directorios

src/
├── api/
│ └── mod.rs (338 lineas) REST API server
├── audit/
│ ├── mod.rs ( 17 lineas) Module declarations
│ ├── persistence.rs (1048 lineas) Linux persistence audit
│ ├── network.rs ( 446 lineas) Network state audit
│ ├── integrity.rs ( 410 lineas) Binary integrity check
│ ├── memory.rs ( 294 lineas) Memory/process scan
│ ├── yara.rs ( 760 lineas) YARA pattern engine
│ ├── hardware.rs ( 576 lineas) Hardware audit
│ └── user_session.rs ( 682 lineas) User session audit
├── dashboard/
│ └── mod.rs ( 137 lineas) Embedded HTML dashboard
├── dictamen/
│ ├── mod.rs ( 3 lineas) Module declaration
│ └── acta.rs ( 543 lineas) Digital notarized act
├── export/
│ ├── mod.rs ( 7 lineas) Module declarations
│ ├── json_export.rs ( 6 lineas) JSON export
│ ├── csv_export.rs ( 24 lineas) CSV export
│ └── stix_export.rs ( 164 lineas) STIX 2.0 export
├── platform/
│ ├── mod.rs ( 4 lineas) Module declarations
│ ├── detect.rs ( 84 lineas) Platform detection
│ └── linux.rs ( 1 linea) Linux specifics
├── rules/
│ └── mod.rs ( 162 lineas) Custom YARA rule loader
├── verdict/
│ ├── mod.rs ( 4 lineas) Module declarations
│ ├── engine.rs ( 108 lineas) Verdict calculation
│ └── rules.rs ( 116 lineas) Verdict rules config
├── evidence.rs ( 347 lineas) SHA-256 evidence chain
├── types.rs ( 101 lineas) Core types
├── error.rs ( 91 lineas) Error handling
├── lib.rs ( 27 lineas) Library API surface
└── main.rs ( 471 lineas) CLI entry point


Archivos adicionales:
├── Dockerfile Multi-stage production build
├── docker-compose.yml Full stack deployment
├── Makefile Build automation
├── .dockerignore Docker build exclusions
└── Cargo.toml Dependencies and metadata

text

### 2.2 Dependencias

| Crate | Version | Proposito |
|-------|---------|-----------|
| clap | 4.x | CLI argument parsing con derive macros |
| serde | 1.x | Serializacion/deserializacion |
| serde_json | 1.x | JSON encoding/decoding |
| chrono | 0.4 | Timestamps y formato de fecha |
| sha2 | 0.10 | SHA-256 hashing para evidence chain |
| hex | 0.4 | Hex encoding para hashes |
| anyhow | 1.x | Error handling ergonomico |
| thiserror | 1.x | Custom error types derivados |
| colored | 2.x | Terminal output con colores |
| walkdir | 2.x | Recursive directory traversal |
| sysinfo | 0.30 | System information (CPU, RAM, processes) |
| csv | 1.x | CSV export formatting |

### 2.3 Compilacion

ZERO-STATE se compila como:
- **Binario** (`zero-state`): CLI con 11 subcomandos
- **Libreria** (`hyperium_zero_state`): API publica para integracion con RED-CORE y otros modulos Hyperium

```toml
[lib]
name = "hyperium_zero_state"
path = "src/lib.rs"

[[bin]]
name = "zero-state"
path = "src/main.rs"

### 2.2 Dependencias

| Crate | Version | Proposito |
|-------|---------|-----------|
| clap | 4.x | CLI argument parsing con derive macros |
| serde | 1.x | Serializacion/deserializacion |
| serde_json | 1.x | JSON encoding/decoding |
| chrono | 0.4 | Timestamps y formato de fecha |
| sha2 | 0.10 | SHA-256 hashing para evidence chain |
| hex | 0.4 | Hex encoding para hashes |
| anyhow | 1.x | Error handling ergonomico |
| thiserror | 1.x | Custom error types derivados |
| colored | 2.x | Terminal output con colores |
| walkdir | 2.x | Recursive directory traversal |
| sysinfo | 0.30 | System information (CPU, RAM, processes) |
| csv | 1.x | CSV export formatting |

### 2.3 Compilacion

ZERO-STATE se compila como:
- **Binario** (`zero-state`): CLI con 11 subcomandos
- **Libreria** (`hyperium_zero_state`): API publica para integracion con RED-CORE y otros modulos Hyperium

```toml
[lib]
name = "hyperium_zero_state"
path = "src/lib.rs"

[[bin]]
name = "zero-state"
path = "src/main.rs"


3. MODULOS DE AUDITORIA

3.1 Persistence Audit (src/audit/persistence.rs — 1,048 lineas)

El modulo mas grande del sistema. Audita vectores de persistencia comunes en sistemas Linux.


Checks implementados (20):

1.Crontab entries (usuario y sistema)
2.Systemd services (servicios habilitados)
3.Systemd timers
4.Init.d scripts
5.RC.local entries
6.Bashrc/Profile modifications
7.SSH authorized_keys
8.LD_PRELOAD environment
9.MOTD/Profile scripts
10.AT jobs
11.XDG autostart entries
12.Kernel modules loaded
13.PAM configuration
14.SUID/SGID binaries
15.World-writable scripts
16.File capabilities
17.Rpm/Vera verification
18.Package integrity
19.Systemd user services
20.Wtmp/btmp log entries

Reporte producido: PersistenceReport con findings categorizados por severidad.


3.2 Network State Audit (src/audit/network.rs — 446 lineas)

Audita el estado de red del sistema al momento del scan.


Checks implementados (7):

1.Active TCP/UDP connections
2.Listening ports analysis
3.Suspicious port detection (4444, 5555, 6666, 7777, 8888, 9999, 1234, 31337)
4.Promiscuous mode interfaces
5.DNS configuration
6.Firewall rules (iptables/nftables)
7.Routing table anomalies

Deteccion de C2: Puertos comand-and-control conocidos, conexiones a 0.0.0.0, patrones de beacon.


3.3 Integrity Check (src/audit/integrity.rs — 410 lineas)

Verifica la integridad de archivos criticos del sistema.


Checks implementados (7):

1.System binaries hash verification (/bin, /usr/bin, /sbin, /usr/sbin)
2.Kernel module integrity
3.Critical configuration files (/etc/passwd, /etc/shadow, /etc/sudoers, etc.)
4.Package manager verification (dpkg -V, rpm -V)
5.Unsigned driver detection
6.Boot chain integrity
7.SELinux/AppArmor status

Metodo: SHA-256 hashing de archivos criticos, comparacion con baseline, deteccion de archivos sin firma.


3.4 Memory Scan (src/audit/memory.rs — 294 lineas)

Escanea procesos en memoria en busca de anomalias.


Checks implementados (6):

1.Hidden process detection (comparando /proc con ps output)
2.Process injection indicators
3.Suspicious memory regions (/tmp, /dev/shm, /var/tmp)
4.Office process spawning shell processes (WINWORD → cmd.exe)
5.Process masquerading (nombre vs ubicacion)
6.Anomalous CPU/memory usage

Patron de deteccion: Procesos ejecutandose desde rutas sospechosas, procesos padre-hijo inusuales, regiones de memoria ejecutables en rutas no estandar.


3.5 YARA Pattern Engine (src/audit/yara.rs — 760 lineas)

Motor de deteccion basado en patrones con 20 reglas builtin.


Reglas YARA (YARA-001 a YARA-020):


ID	Nombre	Categoria	Severidad
YARA-001	C2 Beacon Patterns	C2 Detection	CRITICAL
YARA-002	Reverse Shell Patterns	Shell	CRITICAL
YARA-003	Credential Dumping	Credential	HIGH
YARA-004	Privilege Escalation	Privesc	HIGH
YARA-005	Data Exfiltration	Exfiltration	HIGH
YARA-006	Persistence Mechanisms	Persistence	MEDIUM
YARA-007	Anti-Debug/Evasion	Evasion	HIGH
YARA-008	Webshell Patterns	Webshell	HIGH
YARA-009	Cryptominer Patterns	Miner	MEDIUM
YARA-010	Keylogger Patterns	Keylogger	CRITICAL
YARA-011	LOLBIN Abuse	LOLBIN	MEDIUM
YARA-012	Ransomware Indicators	Ransomware	CRITICAL
YARA-013	Dropper/Downloader	Delivery	HIGH
YARA-014	Tunneling Tools	Tunneling	MEDIUM
YARA-015	Backdoor Patterns	Backdoor	CRITICAL
YARA-016	VM/Sandbox Escape	Escape	HIGH
YARA-017	Anti-Forensics	AntiForensics	MEDIUM
YARA-018	Code Injection	Injection	HIGH
YARA-019	Script Obfuscation	Scripting	MEDIUM
YARA-020	Exploit Kit Patterns	ExploitKit	CRITICAL

Extensiones escaneadas: .sh, .py, .pl, .rb, .php, .js, .c, .cpp, .h, .asm, .exe, .dll, .sys, .bat, .cmd, .ps1, .vbs, .jar, .elf, .so


Caracteristicas:

Carga de reglas custom desde directorio JSON
Scaneo recursivo con profundidad configurable
Filtrado por extension de archivo
Hash SHA-256 de cada archivo matcheado

3.6 Hardware Audit (src/audit/hardware.rs — 576 lineas)

Audita el hardware del sistema en busca de anomalias fisicas y firmware.


Checks implementados (6):

1.CPU model, cores, architecture
2.RAM total (deteccion de VMs con poca memoria)
3.Disk devices enumeration
4.USB device detection (BadUSB, Rubber Ducky)
5.Secure Boot status (via mokutil/EFI)
6.TPM presence detection

Deteccion de USB maliciosos: Analisis de VID/PID sospechosos, dispositivos HID con multiples interfaces, dispositivos sin fabricante conocido.


Estructuras de datos:

HardwareReport: Resultado completo del audit
HardwareInfo: CPU, RAM, discos, USB, BIOS, SecureBoot, TPM
UsbDevice: Vendor ID, Product ID, fabricante, serial

3.7 User Session Audit (src/audit/user_session.rs — 682 lineas)

Audita cuentas de usuario y sesiones activas.


Checks implementados (7):

1.User enumeration from /etc/passwd
2.Root equivalent accounts (UID 0)
3.Empty password detection (/etc/shadow)
4.World-writable home directories
5.NOPASSWD sudo rules
6.Active SSH sessions
7.Sudo group membership

Reporte producido:

UserSessionReport con:
Total users, users with shell
Root equivalent users count
Active sessions, SSH sessions
Checks passed/failed
Findings detallados
Duration en milisegundos


4. CADENA DE EVIDENCIA

4.1 Diseno (src/evidence.rs — 347 lineas)

La cadena de evidencia es el nucleo de ZERO-STATE. Cada accion del sistema se sella inmutabilmente en una cadena blockchain-like.


Estructura:

rust
pub struct EvidenceChain {
    entries: Vec<EvidenceEntry>,
    next_id: i64,
}

pub struct EvidenceEntry {
    pub id: i64,
    pub category: String,
    pub check_name: String,
    pub target: String,
    pub command: String,
    pub output_hash: String,
    pub result_summary: String,
    pub finding_count: usize,
    pub severity: String,
    pub prev_hash: String,
    pub chain_hash: String,
    pub timestamp: String,
    pub duration_ms: u64,
}
pub struct EvidenceChain {
    entries: Vec<EvidenceEntry>,
    next_id: i64,
}

pub struct EvidenceEntry {
    pub id: i64,
    pub category: String,
    pub check_name: String,
    pub target: String,
    pub command: String,
    pub output_hash: String,
    pub result_summary: String,
    pub finding_count: usize,
    pub severity: String,
    pub prev_hash: String,
    pub chain_hash: String,
    pub timestamp: String,
    pub duration_ms: u64,
}

Propiedades de seguridad:

Cada entrada referencia el hash de la entrada anterior (prev_hash)
El chain_hash es SHA-256 del contenido concatenado con el hash previo
La verificacion reconstruye la cadena y detecta cualquier manipulacion
Los IDs son autoincrementales
Los timestamps son UTC con precision de segundo

API publica:

new(): Crea cadena vacia
seal(): Agrega entrada sellada
entries(): Acceso read-only a entradas
last_hash(): Hash de la ultima entrada
verify(): Verificacion completa de la cadena → (bool, usize, usize)
total_duration_ms(): Duracion acumulada
total_findings(): Total de hallazgos


5. MOTOR DE VEREDICTO

5.1 Calculo de Score (src/verdict/engine.rs — 108 lineas)

El motor de veredicto calcula un score de 0-100 basado en la severidad de los hallazgos.


Pesos de severidad (configurables en src/verdict/rules.rs):


Tipo	Peso	Impacto
Critical	25	-25 puntos por finding
High	10	-10 puntos por finding
Medium	3	-3 puntos por finding
YARA Match	15	-15 puntos por match
Hidden Process	20	-20 puntos
Process Injection	15	-15 puntos
C2 Connection	20	-20 puntos

Umbrales:

Score ≥ 80 → CLEAN (Limpio)
Score 50-79 → SUSPICIOUS (Sospechoso)
Score < 50 → COMPROMISED (Comprometido)

Invariantes:

Score nunca baja de 0
Score nunca sube de 100
Sin hallazgos → score 100

5.2 Configuracion (src/verdict/rules.rs — 116 lineas)

Reglas de veredicto configurables con Default implementado. Todos los pesos y umbrales son campos publicos para permitir personalizacion.



6. ACTA NOTARIADA DIGITAL

6.1 Generacion (src/dictamen/acta.rs — 543 lineas)

El acta es un documento formal conforme a ISO/IEC 27037:2012 que contiene:


Secciones del acta:

1.Encabezado: ID unico, version, fecha/hora UTC, plataforma
2.Informacion del cliente: Nombre, autorizacion, target
3.Resumen ejecutivo: Veredicto, score, total de hallazgos
4.Resumen por modulo: Persistence, Network, Integrity, Memory
5.Hallazgos detallados: Cada finding con severidad, evidencia, remediacion
6.IOCs extraidos: Indicators of Compromise
7.Hash de integridad: SHA-256 del documento completo
8.Disclaimer legal: Limitaciones, responsabilidades, validez

Formatos de salida:

Texto plano (terminal)
JSON estructurado

Generacion de ID:ZS-{timestamp}-{hash} — Unico por instancia



7. EXPORTACION

7.1 JSON (src/export/json_export.rs — 6 lineas)

Serializacion JSON pretty-printed de cualquier estructura serializable.


7.2 CSV (src/export/csv_export.rs — 24 lineas)

Exportacion de findings a formato CSV con headers. Compatible con Excel, Google Sheets, y herramientas de analisis.


7.3 STIX 2.0 (src/export/stix_export.rs — 164 lineas)

Exportacion en formato STIX 2.0 (Structured Threat Information Expression), estandar de la industria para compartir inteligencia de amenazas.


Estructuras STIX:

StixBundle: Contenedor principal
StixIndicator: Indicadores de amenaza (IOCs)
StixObservedData: Datos observados
StixReport: Reporte de amenazas
StixIdentity: Organizacion

Generacion automatica:

Cada finding se convierte en un STIX Indicator
Patrones STIX generados segun categoria (IPv4, file hash, file name)
Reporte STIX con object_refs a todos los indicadores


8. API REST

8.1 Servidor (src/api/mod.rs — 338 lineas)

Servidor HTTP basico embebido en el binario.


Endpoints:


Metodo	Ruta	Descripcion
GET	/health	Health check con version y modulos
GET	/version	Informacion de version
GET	/modules	Lista de modulos y checks
POST	/scan	Iniciar scan (requiere JSON body)
GET	/	Dashboard HTML

POST /scan Body:

json
{
    "client": "Nombre del cliente",
    "authorized_by": "Autorizado por",
    "target": "localhost"
}
{
    "client": "Nombre del cliente",
    "authorized_by": "Autorizado por",
    "target": "localhost"
}

Headers: CORS habilitado (Access-Control-Allow-Origin: *), Content-Type JSON.


8.2 Dashboard (src/dashboard/mod.rs — 137 lineas)

Dashboard HTML embebido directamente en el binario (sin archivos externos).


Caracteristicas:

Dark theme profesional (JetBrains Mono)
Formulario de scan interactivo
Visualizacion de modulos disponibles
Resultados demo con datos simulados
Tabs: Modulos, Hallazgos, Evidencia
Badge de severidad con colores
Barra de score visual
Health check automatico cada 30 segundos
Responsive (mobile-ready)
Exportacion (JSON, CSV, STIX desde browser)


9. INTERFAZ DE LINEA DE COMANDOS

9.1 Comandos Disponibles (11)

Comando	Descripcion
scan	Scan completo con todos los modulos
persistence	Solo audit de persistencia
network	Solo audit de red
integrity	Solo verificacion de integridad
memory	Solo scan de memoria
yara	Solo scan YARA
hardware	Solo audit de hardware
user-session	Solo audit de sesiones
api	Iniciar servidor REST API
export	Exportar resultados
info	Informacion del sistema

Opciones comunes:

--json: Output en formato JSON
--client: Nombre del cliente
--authorized-by: Persona que autoriza
--target: Descripcion del target
--host / --port: Para el comando API
--dir: Directorio para scan YARA
--format: Formato de exportacion (json, csv, stix)

9.2 Ejemplo de Uso

bash
# Scan completo
zero-state scan --client "Banco Nacional" --authorized-by "CISO Juan Perez"

# Solo YARA en /tmp
zero-state yara --dir /tmp

# API server
zero-state api --host 0.0.0.0 --port 8080

# Exportar a STIX
zero-state export --scan-id ZS-1234567890 --format stix
# Scan completo
zero-state scan --client "Banco Nacional" --authorized-by "CISO Juan Perez"

# Solo YARA en /tmp
zero-state yara --dir /tmp

# API server
zero-state api --host 0.0.0.0 --port 8080

# Exportar a STIX
zero-state export --scan-id ZS-1234567890 --format stix


10. TESTING

10.1 Resumen de Tests

Total: 217 tests, 0 fallos, ~15 segundos de ejecucion


10.2 Tests por Modulo

Modulo	Tests	Cobertura
audit/yara.rs	30	Reglas builtin, scan, matching, categorias
audit/user_session.rs	26	Usuarios, sudo, SSH, passwords, home dirs
audit/hardware.rs	25	CPU, RAM, USB, TPM, SecureBoot, BIOS
api/mod.rs	22	Endpoints, parseo, responses, CORS, serializacion
evidence.rs	22	Seal, verify, chain integrity, IDs, timestamps
verdict/rules.rs	14	Pesos, umbrales, ordenamiento, validacion
audit/persistence.rs	12	Crontab, systemd, bashrc, SSH keys, SUID
error.rs	10	Todos los variantes, IO conversion, Result type
rules/mod.rs	10	Carga custom, validacion, parseo JSON
audit/memory.rs	8	Procesos ocultos, inyeccion, paths sospechosos
dictamen/acta.rs	8	Generacion, renderizado, IOCs, severidades
platform/detect.rs	7	Deteccion, clone, equality, debug
audit/network.rs	7	Puertos C2, listeners, parseo, audit completo
audit/integrity.rs	7	Hashing, binarios, anomalias, audit completo
verdict/engine.rs	5	Score, veredictos, limites, mezcla severidades

10.3 Evolucion de Tests

Version	Tests	Delta
v0.1.0	46	—
+types	51	+5
+evidence/platform	78	+27
+modules reales	132	+54
+expanded	185	+53
+API/rules/modules	217	+32
Total	217	+372%


11. DESPLIEGUE

11.1 Docker

Dockerfile multi-stage:

1.Builder: rust:1.77-bookworm con compilacion release
2.Runtime: debian:bookworm-slim con herramientas de sistema

Herramientas incluidas en el contenedor:

procps, net-tools, iproute2
pciutils, usbutils, dmidecode
mokutil, dpkg

Puerto: 8080


Healthcheck: Cada 30 segundos


11.2 Docker Compose

yaml
Servicios:
  zero-state:    API + Dashboard en puerto 8080
  redis:         Cache/session store en puerto 6379

Volumes:
  ./reports:/app/reports   Resultados persistentes
  redis-data               Datos Redis

Networks:
  zero-state-net           Red aislada

Capabilities:
  SYS_PTRACE               Para lectura de /proc
  seccomp:unconfined       Para acceso a dispositivos
Servicios:
  zero-state:    API + Dashboard en puerto 8080
  redis:         Cache/session store en puerto 6379

Volumes:
  ./reports:/app/reports   Resultados persistentes
  redis-data               Datos Redis

Networks:
  zero-state-net           Red aislada

Capabilities:
  SYS_PTRACE               Para lectura de /proc
  seccomp:unconfined       Para acceso a dispositivos

11.3 Makefile

Target	Comando
make build	Compilacion debug
make test	Ejecutar tests
make release	Compilacion release
make docker	Build Docker image
make docker-run	docker-compose up -d
make docker-stop	docker-compose down
make run	Scan de ejemplo
make api	Iniciar API en localhost:8080
make clean	Limpiar build artifacts
make lint	Clippy linting
make format	Rustfmt


12. COMPLIANCE

12.1 ISO/IEC 27037:2012

ZERO-STATE cumple con los requisitos de la norma ISO/IEC 27037:2012 para identificacion, recoleccion, adquisicion y preservacion de evidencia digital:


1.Chain of Custody: La cadena de evidencia SHA-256 garantiza la integridad de la secuencia de acciones.
2.Timestamping: Cada entrada tiene timestamp UTC preciso.
3.Immutability: La verificacion de la cadena detecta cualquier alteracion.
4.Documentation: El acta notariada digital documenta el procedimiento completo.
5.Hashing: SHA-256 para cada componente de evidencia.
6.Attribution: Cada hallazgo incluye autorizacion, target, y operador.

12.2 Alcance Legal

El acta es un documento pericial digital
Valido como evidencia en procedimientos legales
Cumple con estandares de forensic readiness
Compatible con marcos regulatorios internacionales


13. ROADMAP FUTURO

v1.1 (Corto plazo)
Expansion a 250 tests
Soporte macOS (launchd, kext, plist)
Soporte Windows (registry, services, WMI)
Integracion nativa con RED-CORE

v1.2 (Mediano plazo)
Reportes HTML/PDF exportables
Firma digital del acta (GPG/X.509)
WebSocket para dashboard en tiempo real
Redis session store para historico

v2.0 (Largo plazo)
Plugin system para modulos custom
API GraphQL
Cluster mode (multi-host scan)
Compliance mapping (MITRE ATT&CK, NIST 800-86)
Machine learning para anomaly scoring


14. INTEGRACION CON RED-CORE

14.1 Como Libreria

rust
use hyperium_zero_state::audit::*;
use hyperium_zero_state::evidence::EvidenceChain;
use hyperium_zero_state::verdict::VerdictEngine;
use hyperium_zero_state::dictamen::DictamenEngine;

// Crear cadena de evidencia
let mut chain = EvidenceChain::new();

// Ejecutar modulos
let persistence = PersistenceAuditor::new().audit(&mut chain);
let network = NetworkAuditor::new().audit(&mut chain);
let memory = MemoryScanner::new().scan(&mut chain);

// Generar veredicto
let engine = VerdictEngine::new();
let score = engine.score();
let verdict = engine.verdict();

// Generar acta
let dictamen = DictamenEngine::new().generate(/* params */);
use hyperium_zero_state::audit::*;
use hyperium_zero_state::evidence::EvidenceChain;
use hyperium_zero_state::verdict::VerdictEngine;
use hyperium_zero_state::dictamen::DictamenEngine;

// Crear cadena de evidencia
let mut chain = EvidenceChain::new();

// Ejecutar modulos
let persistence = PersistenceAuditor::new().audit(&mut chain);
let network = NetworkAuditor::new().audit(&mut chain);
let memory = MemoryScanner::new().scan(&mut chain);

// Generar veredicto
let engine = VerdictEngine::new();
let score = engine.score();
let verdict = engine.verdict();

// Generar acta
let dictamen = DictamenEngine::new().generate(/* params */);

14.2 Como Servicio

bash
# Iniciar como servicio
zero-state api --host 0.0.0.0 --port 8080

# RED-CORE hace POST a /scan
curl -X POST http://localhost:8080/scan \
  -H "Content-Type: application/json" \
  -d '{"client":"Target","authorized_by":"Operator","target":"localhost"}'
# Iniciar como servicio
zero-state api --host 0.0.0.0 --port 8080

# RED-CORE hace POST a /scan
curl -X POST http://localhost:8080/scan \
  -H "Content-Type: application/json" \
  -d '{"client":"Target","authorized_by":"Operator","target":"localhost"}'


15. CREDENCIALES

Desarrollador: Patricio Tirado
Organizacion: Hyperiumia
Cargo: CEO / Lead Security Researcher
Email:
hyperiumia@protonmail.com
GitHub:
https://github.com/hyperiumia


Stack tecnologico: Rust 1.77+, Cargo, Docker, Git



Informe generado para Hyperiumia — Todos los derechos reservadosISO/IEC 27037:2012 Compliant
