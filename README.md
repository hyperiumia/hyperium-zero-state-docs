# Hyperium ZERO-STATE v1.0.0

**Pre-Engagement Integrity Validator**

Certifica el estado inicial de un sistema antes de cualquier operacion ofensiva. Produce un acta notariada digital inmutable, sellada con SHA-256, conforme a ISO/IEC 27037:2012.

## Metricas

| Dato | Valor |
|------|-------|
| Version | 1.0.0 |
| Lenguaje | Rust |
| Lineas de codigo | 6,971 |
| Tests automatizados | 217 |
| Modulos de auditoria | 7 |
| Checks de seguridad | 73 |
| Reglas YARA | 20 |
| Tiempo de scan | ~15s |
| Tamano binario | 2.1 MB |
| Compliance | ISO/IEC 27037:2012 |

## Modulos

- **Persistence Audit** — 20 vectores Linux (crontab, systemd, SUID, PAM, kernel modules...)
- **Network State** — Conexiones, listeners, deteccion C2
- **Integrity Check** — Hash SHA-256 de binarios y configs criticos
- **Memory Scan** — Procesos ocultos, inyeccion, LOLBIN abuse
- **YARA Engine** — 20 reglas de deteccion (shells, ransomware, miners, backdoors...)
- **Hardware Audit** — USB malicioso, TPM, Secure Boot, BIOS
- **User Session** — Cuentas root, passwords vacios, sudo NOPASSWD, SSH

## Caracteristicas

- Acta notariada digital con hash SHA-256
- Veredicto automatico: CLEAN / SUSPICIOUS / COMPROMISED
- Exportacion: JSON, CSV, STIX 2.0
- REST API con 5 endpoints
- Dashboard HTML embebido
- Docker + docker-compose ready
- Libreria para integracion con RED-CORE

## Autor

**Patricio Tirado** — CEO, Hyperiumia
hyperiumia@protonmail.com

## Documentacion

- [Informe Tecnico v1.0.0](./INFORME_v1.0.0.md)
- [Post LinkedIn](./POST_LINKEDIN.md)

## Contacto

El codigo fuente es proprietario. Para licencia, integracion o demo:

**Email:** hyperiumia@protonmail.com

---
*Hyperiumia — Plataforma integral para operaciones ofensivas*
