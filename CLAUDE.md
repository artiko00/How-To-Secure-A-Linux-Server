# CLAUDE.md

Este archivo proporciona orientación a Claude Code (claude.ai/code) al trabajar con código en este repositorio.

## Descripción del Proyecto

Este es un repositorio de documentación educativa que proporciona una guía completa para asegurar servidores Linux. No es un proyecto de código activo, sino una colección de guías en formato Markdown con instrucciones detalladas de hardening.

## Estructura del Repositorio

El repositorio contiene tres archivos principales de documentación:

- **README.md** - Guía principal exhaustiva (166KB+) que cubre todos los aspectos de securización de un servidor Linux, desde SSH hasta auditoría del sistema
- **linux-kernel-sysctl-hardening.md** - Lista de referencia de configuraciones sysctl para hardening del kernel Linux
- **nginx.md** - Guía específica para configurar headers de seguridad en nginx

## Arquitectura y Contenido

### README.md - Guía Principal

La guía principal está organizada en las siguientes secciones principales:

1. **The SSH Server** - Configuración segura de SSH, autenticación 2FA/MFA, gestión de claves
2. **The Basics** - Control de acceso sudo/su, FireJail, NTP, hardening de /proc, políticas de contraseñas, actualizaciones automáticas
3. **The Network** - Firewall (UFW), prevención de intrusiones (PSAD, Fail2Ban, CrowdSec)
4. **The Auditing** - Monitoreo de integridad (AIDE), antivirus (ClamAV), detección de rootkits (rkhunter, chrootkit), análisis de logs (logwatch), auditoría de seguridad (Lynis, OSSEC)
5. **The Miscellaneous** - Configuración de MSMTP/Exim4 para alertas por correo, logging de iptables

### Temas Pendientes (To Do)

El README incluye una sección de tareas pendientes que incluye:
- Custom Jails para Fail2ban
- MAC/LSMs (SELinux, AppArmor)
- Cifrado de disco
- Backup de logs
- Herramientas adicionales (CIS-CAT, debsums)

## Enfoque de la Guía

- **Distribución agnóstica**: Aplicable a cualquier distribución Linux (Debian, Ubuntu, CentOS, etc.)
- **Orientada al home lab**: Enfocada en servidores caseros pero los conceptos aplican a entornos empresariales
- **Educativa**: Explica el "por qué" además del "cómo"
- **Práctica**: Incluye comandos copy-paste para facilitar la implementación
- **Secuencial**: Diseñada para seguirse en orden aunque no es estrictamente necesario

## Caso de Uso de Referencia

La guía está orientada a un escenario específico:
- Computadora de escritorio como servidor
- Una sola NIC
- Router de grado consumidor
- IP WAN dinámica del ISP
- WAN+LAN en IPv4
- LAN usando NAT
- Acceso SSH remoto desde ubicaciones desconocidas

## Editando Archivos de Documentación

Al trabajar con estos archivos:

1. **Mantén el formato Markdown**: Usa GitHub-flavored Markdown
2. **Preserva la Tabla de Contenidos**: Si añades secciones, actualiza el TOC
3. **Sigue la estructura existente**: Usa el mismo estilo de encabezados y formato
4. **Incluye enlaces de navegación**: Cada sección debe terminar con `([Table of Contents](#table-of-contents))`
5. **Proporciona comandos prácticos**: Los usuarios esperan código copy-paste cuando sea posible
6. **Explica el contexto**: No solo digas qué hacer, explica por qué es importante para la seguridad

## Licencia

El proyecto está bajo licencia CC-BY-SA (Creative Commons Attribution-ShareAlike 4.0).

## Contribuciones

Este es un proyecto comunitario que acepta contribuciones vía:
- Pull requests (fork y PR)
- Issues en GitHub para reportar errores o sugerir mejoras

## Recursos Relacionados

- Playbooks de Ansible disponibles en: https://github.com/moltenbit/How-To-Secure-A-Linux-Server-With-Ansible
- Benchmarks CIS para validación de seguridad
- Documentación específica de cada distribución Linux
