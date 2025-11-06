# Plan de Traducción al Español
## How To Secure A Linux Server

**Fecha de inicio:** 2025-11-06
**Estado:** En Progreso
**Estrategia:** Archivos paralelos (.es.md) en rama master

---

## Objetivos

1. ✅ Crear versión completa en español de toda la documentación
2. ✅ Mantener términos técnicos/slang sin traducir (ver glosario)
3. ✅ Preservar todos los comandos, código y ejemplos en inglés
4. ✅ Adaptar explicaciones al contexto hispanohablante cuando sea necesario
5. ✅ Preparar PR para el repositorio original

---

## Estrategia: Archivos Paralelos

```
master/
├── README.md                              (inglés - original)
├── README.es.md                           (español - nueva traducción)
├── linux-kernel-sysctl-hardening.md       (inglés - original)
├── linux-kernel-sysctl-hardening.es.md    (español - nueva traducción)
├── nginx.md                               (inglés - original)
├── nginx.es.md                            (español - nueva traducción)
├── CLAUDE.md                              (ya en español)
└── TRANSLATION_PLAN.md                    (este archivo)
```

---

## Glosario de Términos NO Traducibles

### Conceptos Técnicos Core
- **Server** → server (NO servidor)
- **Linux** → Linux
- **SSH** → SSH
- **Firewall** → firewall
- **Hardening** → hardening
- **Kernel** → kernel
- **Root** → root (usuario/permisos)
- **Shell** → shell
- **Script** → script
- **Rootkit** → rootkit
- **Malware** → malware
- **Backdoor** → backdoor
- **Log/Logs** → log/logs
- **Debugging** → debugging
- **Bug** → bug

### Herramientas y Software
- **UFW** → UFW
- **PSAD** → PSAD
- **Fail2Ban** → Fail2Ban
- **CrowdSec** → CrowdSec
- **AIDE** → AIDE
- **ClamAV** → ClamAV
- **Rkhunter** → rkhunter
- **Lynis** → Lynis
- **OSSEC** → OSSEC
- **FireJail** → FireJail
- **NTP** → NTP

### Comandos y Sintaxis
- Todos los comandos: `sudo`, `apt`, `yum`, `systemctl`, `grep`, `cat`, `sed`, `awk`, etc.
- Todos los flags: `-a`, `--help`, `-v`, etc.
- Paths: `/etc/ssh/sshd_config`, `/var/log/`, etc.
- Variables: `$USER`, `$HOME`, etc.

### Términos de Red
- **Router** → router
- **Proxy** → proxy
- **Endpoint** → endpoint
- **Gateway** → gateway
- **IP/IPv4/IPv6** → IP/IPv4/IPv6
- **NAT** → NAT
- **DNS** → DNS
- **Port** → port
- **Packet** → packet

### Conceptos de Seguridad
- **Threat model** → threat model
- **Attack vector** → vector de ataque (ESTA traducción está OK)
- **Brute force** → brute force
- **DoS/DDoS** → DoS/DDoS
- **Man-in-the-middle** → man-in-the-middle
- **Zero-day** → zero-day
- **Exploit** → exploit
- **Payload** → payload

### Términos que SÍ se traducen
- **File** → archivo
- **Folder/Directory** → carpeta/directorio
- **User** → usuario
- **Password** → contraseña
- **Configuration** → configuración
- **Installation** → instalación
- **Update** → actualización
- **Backup** → respaldo/copia de seguridad
- **Permission** → permiso
- **Access** → acceso

---

## Fases del Trabajo

### Fase 1: Preparación ✅
- [x] Crear TRANSLATION_PLAN.md
- [x] Definir glosario de términos
- [x] Configurar estructura de archivos
- [ ] Crear plantilla de encabezado para archivos .es.md

### Fase 2: Traducción de README.es.md (Archivo Principal)
**Estimado:** ~4000 líneas, dividido en secciones

#### Sección 1: Introducción y Overview
- [ ] Title y badges
- [ ] Table of Contents (adaptar enlaces)
- [ ] Introduction (Guide Objective, Why Secure, Why Another Guide, Other Guides)
- [ ] Guide Overview (About, Use-Case, Editing Files, Contributing)
- [ ] Before You Start (Principles, Distribution, Installing, Requirements)

#### Sección 2: The SSH Server
- [ ] Important Note Before Changes
- [ ] SSH Public/Private Keys
- [ ] Create SSH Group
- [ ] Secure sshd_config
- [ ] Remove Short Diffie-Hellman Keys
- [ ] 2FA/MFA for SSH

#### Sección 3: The Basics
- [ ] Limit sudo/su
- [ ] FireJail
- [ ] NTP Client
- [ ] Securing /proc
- [ ] Force Secure Passwords
- [ ] Automatic Security Updates
- [ ] Random Entropy Pool
- [ ] Panic/Secondary Password

#### Sección 4: The Network
- [ ] UFW (Uncomplicated Firewall)
- [ ] PSAD (iptables IDS/IPS)
- [ ] Fail2Ban
- [ ] CrowdSec

#### Sección 5: The Auditing
- [ ] AIDE (Integrity Monitoring)
- [ ] ClamAV (Anti-Virus)
- [ ] Rkhunter (Rootkit Detection)
- [ ] chrootkit
- [ ] logwatch
- [ ] ss command
- [ ] Lynis
- [ ] OSSEC

#### Sección 6: The Danger Zone & Miscellaneous
- [ ] The Danger Zone
- [ ] MSMTP
- [ ] Gmail and Exim4
- [ ] Separate iptables Log

#### Sección 7: Left Over
- [ ] Contacting Me
- [ ] Helpful Links
- [ ] Acknowledgments
- [ ] License and Copyright

### Fase 3: Traducción de Archivos Secundarios
- [ ] linux-kernel-sysctl-hardening.es.md
- [ ] nginx.es.md

### Fase 4: Revisión y Calidad
- [ ] Revisar consistencia de términos (usar glosario)
- [ ] Verificar todos los enlaces internos
- [ ] Verificar que TOC funcione correctamente
- [ ] Revisar que code blocks mantengan formato
- [ ] Spellcheck en español
- [ ] Peer review (opcional pero recomendado)

### Fase 5: Integración
- [ ] Crear badge en README.md original apuntando a README.es.md
- [ ] Actualizar CLAUDE.md con información de traducción
- [ ] Commit y push al fork
- [ ] Testing: verificar rendering en GitHub

### Fase 6: Upstream Contribution
- [ ] Crear issue en repo original proponiendo traducción
- [ ] Esperar feedback del maintainer
- [ ] Ajustar según feedback
- [ ] Crear Pull Request
- [ ] Responder a code review

---

## Guías de Estilo

### 1. Formato de Comandos
```markdown
Instala el paquete con:

\`\`\`bash
sudo apt install package-name
\`\`\`
```
**Traducir:** Las explicaciones antes/después del código
**NO traducir:** El código mismo

### 2. Notas y Warnings
```markdown
**Nota**: Siempre haz un backup antes de editar archivos de configuración.

**Advertencia**: Este comando puede dejarte sin acceso SSH.
```
**Usar:** Negritas para enfatizar, mantener tono serio de seguridad

### 3. Enlaces
```markdown
[Tabla de Contenidos](#tabla-de-contenidos)
```
**Traducir:** El texto del enlace
**Traducir:** Los anchors/IDs si contienen palabras (table-of-contents → tabla-de-contenidos)

### 4. Tono
- **Formal pero accesible**: Tutear al lector (tú/tu, no usted)
- **Educativo**: Mantener explicaciones del "por qué"
- **Práctico**: Instrucciones claras paso a paso
- **Inclusivo**: Evitar asumir género o nivel de experiencia

### 5. Adaptaciones Culturales
- **Ejemplos de ISP**: Mantener genéricos o adaptar si es necesario
- **Referencias legales**: Mantener originales (US-centric está OK)
- **Fechas**: Usar formato ISO (YYYY-MM-DD) o "6 de noviembre de 2025"

---

## Checklist Pre-Commit

Antes de hacer commit de cada archivo traducido:

- [ ] ¿Todos los términos del glosario se respetaron?
- [ ] ¿Los code blocks mantienen formato correcto?
- [ ] ¿Todos los enlaces funcionan?
- [ ] ¿La Tabla de Contenidos está actualizada?
- [ ] ¿Se agregó nota de traducción al inicio del archivo?
- [ ] ¿Spellcheck pasó sin errores?

---

## Nota de Traducción (Template)

Agregar al inicio de cada archivo .es.md:

```markdown
> **Nota del Traductor:** Esta es una traducción al español de la guía original "[How To Secure A Linux Server](README.md)". Se mantienen términos técnicos en inglés siguiendo convenciones de la industria. Para la versión original en inglés, consulta [README.md](README.md).
>
> **Translator's Note:** This is a Spanish translation of the original guide "[How To Secure A Linux Server](README.md)". Technical terms are kept in English following industry conventions. For the original English version, see [README.md](README.md).
```

---

## Métricas de Progreso

### README.es.md
- **Líneas totales:** ~3893
- **Líneas traducidas:** 0
- **Progreso:** 0%

### linux-kernel-sysctl-hardening.es.md
- **Líneas totales:** ~152
- **Líneas traducidas:** 0
- **Progreso:** 0%

### nginx.es.md
- **Líneas totales:** ~45
- **Líneas traducidas:** 0
- **Progreso:** 0%

### **Total General: 0% (0/4090 líneas)**

---

## Estrategia de Commits

### Commits Incrementales
```bash
git add README.es.md
git commit -m "traducción: README.es.md - Sección Introduction completa"

git add README.es.md
git commit -m "traducción: README.es.md - Sección SSH Server completa"
```

### Mensaje de Commit Format
```
traducción: [archivo] - [sección/descripción]

Ejemplos:
- traducción: README.es.md - Sección Introduction completa
- traducción: nginx.es.md - Traducción completa
- fix: README.es.md - Corregir enlaces rotos en TOC
```

---

## Issue Template para Repo Original

Cuando esté lista la traducción, usar este template:

```markdown
### Propuesta: Traducción al Español

Hola @imthenachoman,

Me gustaría contribuir una traducción completa al español de esta excelente guía de seguridad para Linux.

#### Detalles:
- ✅ Traducción completa de README.md → README.es.md
- ✅ Traducción de archivos secundarios (.es.md)
- ✅ Respeto de términos técnicos (sin traducir slang/comandos)
- ✅ Todos los comandos y code blocks intactos
- ✅ Enlaces y TOC funcionando correctamente

#### Beneficios:
- Accesibilidad para ~500M de hispanohablantes
- GitHub renderiza automáticamente README.es.md
- Mantiene estructura de archivos paralelos (fácil mantenimiento)
- No afecta documentación original en inglés

#### Mi fork: [link]

¿Estarías interesado en aceptar esta contribución? Puedo preparar un PR si te parece bien.

¡Gracias por esta guía increíble!
```

---

## Recursos y Referencias

### Guías de Traducción de Proyectos Similares
- [Arch Linux - Español](https://wiki.archlinux.org/title/Main_page_(Espa%C3%B1ol))
- [Ubuntu Documentation - Spanish](https://help.ubuntu.com/community/es)
- [Debian Wiki - Spanish](https://wiki.debian.org/es/)

### Herramientas Útiles
- **Spellcheck:** `aspell -l es`
- **Markdown Linting:** `markdownlint`
- **Link Checker:** `markdown-link-check`

---

## Notas Finales

- **Prioridad:** Calidad sobre velocidad
- **Consistencia:** Usar el glosario religiosamente
- **Contexto:** Mantener el espíritu educativo del original
- **Comunidad:** Esta traducción ayudará a miles de administradores hispanohablantes

**¡Vamos a hacer esto! 🚀**
