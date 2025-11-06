# Cómo Asegurar Un Servidor Linux

> **Nota del Traductor:** Esta es una traducción al español de la guía original "[How To Secure A Linux Server](README.md)". Se mantienen términos técnicos en inglés siguiendo convenciones de la industria. Para la versión original en inglés, consulta [README.md](README.md).
>
> **Translator's Note:** This is a Spanish translation of the original guide "[How To Secure A Linux Server](README.md)". Technical terms are kept in English following industry conventions. For the original English version, see [README.md](README.md).

Una guía en constante evolución para asegurar un servidor Linux que, con suerte, también te enseña un poco sobre seguridad y por qué es importante.

[![CC-BY-SA](https://i.creativecommons.org/l/by-sa/4.0/88x31.png)](#licencia)

## Tabla de Contenidos

- [Introducción](#introducción)
  - [Objetivo de la Guía](#objetivo-de-la-guía)
  - [Por Qué Asegurar Tu Servidor](#por-qué-asegurar-tu-servidor)
  - [Por Qué Otra Guía Más](#por-qué-otra-guía-más)
  - [Otras Guías](#otras-guías)
  - [Por Hacer / Por Agregar](#por-hacer--por-agregar)
- [Visión General de la Guía](#visión-general-de-la-guía)
  - [Acerca de Esta Guía](#acerca-de-esta-guía)
  - [Mi Caso de Uso](#mi-caso-de-uso)
  - [Editando Archivos de Configuración - Para Los Perezosos](#editando-archivos-de-configuración---para-los-perezosos)
  - [Contribuyendo](#contribuyendo)
- [Antes de Empezar](#antes-de-empezar)
  - [Identifica Tus Principios](#identifica-tus-principios)
  - [Eligiendo Una Distribución Linux](#eligiendo-una-distribución-linux)
  - [Instalando Linux](#instalando-linux)
  - [Requisitos Pre/Post Instalación](#requisitos-prepost-instalación)
  - [Otras Notas Importantes](#otras-notas-importantes)
  - [Usando Playbooks de Ansible para asegurar tu Servidor Linux](#usando-playbooks-de-ansible-para-asegurar-tu-servidor-linux)
- [El Servidor SSH](#el-servidor-ssh)
  - [Nota Importante Antes de Hacer Cambios en SSH](#nota-importante-antes-de-hacer-cambios-en-ssh)
  - [Claves Públicas/Privadas SSH](#claves-públicasprivadas-ssh)
  - [Crear Grupo SSH Para AllowGroups](#crear-grupo-ssh-para-allowgroups)
  - [Asegurar `/etc/ssh/sshd_config`](#asegurar-etcsshsshd_config)
  - [Remover Claves Diffie-Hellman Cortas](#remover-claves-diffie-hellman-cortas)
  - [2FA/MFA para SSH](#2famfa-para-ssh)
- [Lo Básico](#lo-básico)
  - [Limitar Quién Puede Usar sudo](#limitar-quién-puede-usar-sudo)
  - [Limitar Quién Puede Usar su](#limitar-quién-puede-usar-su)
  - [Ejecutar aplicaciones en un sandbox con FireJail](#ejecutar-aplicaciones-en-un-sandbox-con-firejail)
  - [Cliente NTP](#cliente-ntp)
  - [Asegurando /proc](#asegurando-proc)
  - [Forzar Cuentas a Usar Contraseñas Seguras](#forzar-cuentas-a-usar-contraseñas-seguras)
  - [Actualizaciones y Alertas de Seguridad Automáticas](#actualizaciones-y-alertas-de-seguridad-automáticas)
  - [Pool de Entropía Aleatoria Más Seguro (WIP)](#pool-de-entropía-aleatoria-más-seguro-wip)
  - [Agregar Sistema de Seguridad de Login con Contraseña de Pánico/Secundaria/Falsa](#agregar-sistema-de-seguridad-de-login-con-contraseña-de-pánicosecundariafalsa)
- [La Red](#la-red)
  - [Firewall Con UFW (Uncomplicated Firewall)](#firewall-con-ufw-uncomplicated-firewall)
  - [Detección y Prevención de Intrusiones en iptables con PSAD](#detección-y-prevención-de-intrusiones-en-iptables-con-psad)
  - [Detección y Prevención de Intrusiones en Aplicaciones Con Fail2Ban](#detección-y-prevención-de-intrusiones-en-aplicaciones-con-fail2ban)
  - [Detección y Prevención de Intrusiones en Aplicaciones Con CrowdSec](#detección-y-prevención-de-intrusiones-en-aplicaciones-con-crowdsec)
- [La Auditoría](#la-auditoría)
  - [Monitoreo de Integridad de Archivos/Carpetas Con AIDE (WIP)](#monitoreo-de-integridad-de-archivoscarpetas-con-aide-wip)
  - [Escaneo Anti-Virus Con ClamAV (WIP)](#escaneo-anti-virus-con-clamav-wip)
  - [Detección de Rootkit Con Rkhunter (WIP)](#detección-de-rootkit-con-rkhunter-wip)
  - [Detección de Rootkit Con chrootkit (WIP)](#detección-de-rootkit-con-chrootkit-wip)
  - [logwatch - analizador y reporteador de logs del sistema](#logwatch---analizador-y-reporteador-de-logs-del-sistema)
  - [ss - Ver Ports En Los Que Tu Servidor Está Escuchando](#ss---ver-ports-en-los-que-tu-servidor-está-escuchando)
  - [Lynis - Auditoría de Seguridad Linux](#lynis---auditoría-de-seguridad-linux)
  - [OSSEC - Detección de Intrusiones en Host](#ossec---detección-de-intrusiones-en-host)
- [La Zona de Peligro](#la-zona-de-peligro)
- [Lo Misceláneo](#lo-misceláneo)
  - [MSMTP (Simple Sendmail) con Google](#msmtp-alternativa)
  - [Gmail y Exim4 Como MTA Con TLS Implícito](#gmail-y-exim4-como-mta-con-tls-implícito)
  - [Archivo de Log Separado para iptables](#archivo-de-log-separado-para-iptables)
- [Lo Que Quedó](#lo-que-quedó)
  - [Contactándome](#contactándome)
  - [Enlaces Útiles](#enlaces-útiles)
  - [Agradecimientos](#agradecimientos)
  - [Licencia y Derechos de Autor](#licencia-y-derechos-de-autor)

(Tabla de contenidos hecha con [nGitHubTOC](https://imthenachoman.github.io/nGitHubTOC/))

## Introducción

### Objetivo de la Guía

El propósito de esta guía es enseñarte cómo asegurar un servidor Linux.

Hay muchas cosas que puedes hacer para asegurar un servidor Linux y esta guía intentará cubrir tantas como sea posible. Se agregarán más temas/material a medida que aprenda, o a medida que la gente [contribuya](#contribuyendo).

Los playbooks de Ansible de esta guía están disponibles en [How To Secure A Linux Server With Ansible](https://github.com/moltenbit/How-To-Secure-A-Linux-Server-With-Ansible) por [moltenbit](https://github.com/moltenbit).

([Tabla de Contenidos](#tabla-de-contenidos))

### Por Qué Asegurar Tu Servidor

Asumo que estás usando esta guía porque, con suerte, ya entiendes por qué la buena seguridad es importante. Ese es un tema pesado en sí mismo y desglosarlo está fuera del alcance de esta guía. Si no conoces la respuesta a esa pregunta, te aconsejo que primero la investigues.

A alto nivel, en el momento en que un dispositivo, como un server, está en el dominio público -- es decir, visible al mundo exterior -- se convierte en un objetivo para actores maliciosos. Un dispositivo sin asegurar es un patio de recreo para actores maliciosos que quieren acceso a tus datos, o usar tu server como otro nodo para sus ataques DDoS a gran escala.

Lo que es peor, sin buena seguridad, puede que nunca sepas si tu server ha sido comprometido. Un actor malicioso puede haber obtenido acceso no autorizado a tu server y copiado tus datos sin cambiar nada, así que nunca lo sabrías. O tu server puede haber sido parte de un ataque DDoS, y no lo sabrías. Mira muchas de las grandes filtraciones de datos en las noticias -- las compañías a menudo no descubrieron la filtración de datos o intrusión hasta mucho después de que los actores maliciosos se fueran.

Contrario a la creencia popular, los actores maliciosos no siempre quieren cambiar algo o [bloquearte el acceso a tus datos por dinero](https://en.wikipedia.org/wiki/Ransomware). A veces solo quieren los datos en tu server para sus almacenes de datos (hay mucho dinero en big data) o usar tu server de manera encubierta para sus propósitos nefastos.

([Tabla de Contenidos](#tabla-de-contenidos))

### Por Qué Otra Guía Más

Esta guía puede parecer duplicada/innecesaria porque hay innumerables artículos en línea que te dicen [cómo asegurar Linux](https://duckduckgo.com/?q=how+to+secure+linux&t=ffab&atb=v151-7&ia=web), pero la información está dispersa en diferentes artículos, que cubren diferentes cosas, y de diferentes maneras. ¿Quién tiene tiempo para examinar cientos de artículos?

Mientras revisaba la investigación para mi build de Debian, tomé notas. Al final me di cuenta de que, junto con lo que ya sabía y lo que estaba aprendiendo, tenía los elementos de una guía práctica. Pensé que la pondría en línea para, con suerte, ayudar a otros a **aprender** y **ahorrar tiempo**.

Nunca he encontrado una guía que cubra todo -- esta guía es mi intento.

Muchas de las cosas cubiertas en esta guía pueden ser bastante básicas/triviales, pero la mayoría de nosotros no instalamos Linux todos los días, y es fácil olvidar esas cosas básicas.

([Tabla de Contenidos](#tabla-de-contenidos))

### Otras Guías

Hay muchas guías proporcionadas por expertos, líderes de la industria y las propias distribuciones. No es práctico, y a veces va contra los derechos de autor, incluir todo de esas guías. Te recomiendo que las revises antes de comenzar con esta guía.

- El [Center for Internet Security (CIS)](https://www.cisecurity.org/) proporciona [benchmarks](https://www.cisecurity.org/cis-benchmarks/) que son exhaustivos, confiables para la industria, instrucciones paso a paso para asegurar muchos sabores de Linux. Revisa su página [About Us](https://www.cisecurity.org/about-us/) para más detalles. Mi recomendación es revisar esta guía (la que estás leyendo aquí) primero y LUEGO la guía de CIS. De esa manera sus recomendaciones superarán cualquier cosa en esta guía.
- Para guías de hardening/seguridad específicas de la distribución, consulta la documentación de tu distribución.
- https://security.utexas.edu/os-hardening-checklist/linux-7 - Red Hat Enterprise Linux 7 Hardening Checklist
- https://cloudpro.zone/index.php/2018/01/18/debian-9-3-server-setup-guide-part-1/ - Guía de configuración de servidor Debian 9.3
- https://blog.vigilcode.com/2011/04/ubuntu-server-initial-security-quick-secure-setup-part-i/ - Guía de seguridad inicial de Ubuntu Server
- https://www.tldp.org/LDP/sag/html/index.html
- https://seifried.org/lasg/
- https://news.ycombinator.com/item?id=19178964
- https://wiki.archlinux.org/index.php/Security - muchas personas también han recomendado esta
- https://securecompliance.co/linux-server-hardening-checklist/

([Tabla de Contenidos](#tabla-de-contenidos))

### Por Hacer / Por Agregar

- [ ] [Custom Jails para Fail2ban](#custom-jails)
- [ ] MAC (Mandatory Access Control) y Linux Security Modules (LSMs)
   - https://wiki.archlinux.org/index.php/security#Mandatory_access_control
   - Security-Enhanced Linux / SELinux
       - https://en.wikipedia.org/wiki/Security-Enhanced_Linux
       - https://linuxtechlab.com/beginners-guide-to-selinux/
       - https://linuxtechlab.com/replicate-selinux-policies-among-linux-machines/
       - https://teamignition.us/how-to-stop-being-a-scrub-and-learn-to-use-selinux.html
   - AppArmor
       - https://wiki.archlinux.org/index.php/AppArmor
       - https://security.stackexchange.com/questions/29378/comparison-between-apparmor-and-selinux
        - http://www.insanitybit.com/2012/06/01/why-i-like-apparmor-more-than-selinux-5/
- [ ] Cifrado de disco
- [ ] Rkhunter y chrootkit
    - http://www.chkrootkit.org/
    - http://rkhunter.sourceforge.net/
    - https://www.cyberciti.biz/faq/howto-check-linux-rootkist-with-detectors-software/
    - https://www.tecmint.com/install-rootkit-hunter-scan-for-rootkits-backdoors-in-linux/
- [ ] Respaldo/backup de logs - https://news.ycombinator.com/item?id=19178681
- [ ] CIS-CAT - https://learn.cisecurity.org/cis-cat-landing-page
- [ ] debsums - https://blog.sleeplessbeastie.eu/2015/03/02/how-to-verify-installed-packages/

([Tabla de Contenidos](#tabla-de-contenidos))

## Visión General de la Guía

### Acerca de Esta Guía

Esta guía...

- ...**es** un trabajo en progreso.
- ...**está** enfocada en servidores Linux **caseros**. Todos los conceptos/recomendaciones aquí aplican a entornos más grandes/profesionales pero esos casos de uso requieren configuraciones más avanzadas y especializadas que están fuera del alcance de esta guía.
- ...**no** te enseña sobre Linux, cómo [instalar Linux](#instalando-linux), o cómo usarlo. Revisa https://linuxjourney.com/ si eres nuevo en Linux.
- ...**está** diseñada para ser [agnóstica de distribución Linux](#eligiendo-una-distribución-linux).
- ...**no** te enseña todo lo que necesitas saber sobre seguridad ni entra en todos los aspectos de seguridad del sistema/servidor. Por ejemplo, la seguridad física está fuera del alcance de esta guía.
- ...**no** habla sobre cómo funcionan los programas/herramientas, ni profundiza en sus rincones y grietas. La mayoría de los programas/herramientas a los que esta guía hace referencia son muy potentes y altamente configurables. El objetivo es cubrir lo estrictamente necesario -- lo suficiente para abrir tu apetito y hacerte lo suficientemente hambriento como para querer ir y aprender más.
- ...**busca** hacerlo fácil al proporcionar código que puedes copiar y pegar. Puede que necesites modificar los comandos antes de pegarlos, así que ten a mano tu [editor de texto](https://notepad-plus-plus.org/) favorito.
- ...**está** organizada en un orden que tiene sentido lógico para mí -- es decir, asegurar SSH antes de instalar un firewall. Como tal, esta guía está destinada a seguirse en el orden en que se presenta, pero no es necesario hacerlo. Solo ten cuidado si haces las cosas en un orden diferente -- algunas secciones requieren que las secciones anteriores estén completadas.

([Tabla de Contenidos](#tabla-de-contenidos))

### Mi Caso de Uso

Hay muchos tipos de servidores y diferentes casos de uso. Si bien quiero que esta guía sea lo más genérica posible, habrá algunas cosas que pueden no aplicar a todos/otros casos de uso. Usa tu mejor juicio al revisar esta guía.

Para ayudar a poner en contexto muchos de los temas cubiertos en esta guía, mi caso de uso/configuración es:

- Una computadora clase desktop...
- Con una sola NIC...
- Conectada a un router de grado consumidor...
- Obteniendo una IP WAN dinámica proporcionada por el ISP...
- Con WAN+LAN en IPv4...
- Y LAN usando [NAT](https://en.wikipedia.org/wiki/Network_address_translation)...
- A la que quiero poder hacer SSH remotamente desde computadoras desconocidas y ubicaciones desconocidas (es decir, la casa de un amigo).

([Tabla de Contenidos](#tabla-de-contenidos))

### Editando Archivos de Configuración - Para Los Perezosos

Soy muy perezoso y no me gusta editar archivos a mano si no es necesario. También asumo que todos los demás son como yo. :)

Así que, cuando y donde sea posible, he proporcionado snippets de `código` para hacer rápidamente lo que se necesita, como agregar o cambiar una línea en un archivo de configuración.

Los snippets de `código` usan comandos básicos como `echo`, `cat`, `sed`, `awk` y `grep`. Cómo funcionan los snippets de `código`, como qué hace cada comando/parte, está fuera del alcance de esta guía -- las páginas `man` son tus amigas.

**Nota**: Los snippets de `código` no validan/verifican que el cambio se haya realizado -- es decir, que la línea realmente se agregó o cambió. Dejaré la parte de verificación en tus capaces manos. Los pasos en esta guía incluyen tomar respaldos de todos los archivos que se cambiarán.

No todos los cambios se pueden automatizar con snippets de `código`. Esos cambios necesitan buena edición manual, a la antigua. Por ejemplo, no puedes simplemente agregar una línea a un archivo tipo [INI](https://en.wikipedia.org/wiki/INI_file). Usa tu editor de texto Linux [favorito](https://en.wikipedia.org/wiki/Vi).

([Tabla de Contenidos](#tabla-de-contenidos))

### Contribuyendo

Quise poner esta guía en [GitHub](http://www.github.com) para facilitar la colaboración. Cuanta más gente contribuya, mejor y más completa se volverá esta guía.

Para contribuir puedes hacer fork y enviar un pull request o enviar un [nuevo issue](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/new).

([Tabla de Contenidos](#tabla-de-contenidos))

## Antes de Empezar

### Identifica Tus Principios

Antes de empezar, querrás identificar cuáles son tus Principios. ¿Cuál es tu [threat model](https://en.wikipedia.org/wiki/Threat_model)? Algunas cosas en las que pensar:

- ¿Por qué quieres asegurar tu server?
- ¿Cuánta seguridad quieres o no quieres?
- ¿Cuánta conveniencia estás dispuesto a comprometer por seguridad y viceversa?
- ¿Cuáles son las amenazas contra las que quieres protegerte? ¿Cuáles son los detalles específicos de tu situación? Por ejemplo:
  - ¿Es el acceso físico a tu server/red un posible vector de ataque?
  - ¿Abrirás ports en tu router para poder acceder a tu server desde fuera de tu hogar?
  - ¿Estarás alojando un compartido de archivos en tu server que se montará en una máquina clase desktop? ¿Cuál es la posibilidad de que la máquina desktop se infecte y, a su vez, infecte el server?
 - ¿Tienes un medio de recuperación si tu implementación de seguridad te bloquea el acceso a tu propio server? Por ejemplo, [deshabilitaste el login root](#deshabilitar-login-root) o [protegiste con contraseña GRUB](#proteger-grub-con-contraseña).

Estas son solo **algunas cosas** en las que pensar. Antes de comenzar a asegurar tu server, querrás entender contra qué estás tratando de protegerte y por qué, para que sepas qué necesitas hacer.

([Tabla de Contenidos](#tabla-de-contenidos))

### Eligiendo Una Distribución Linux

Esta guía está destinada a ser agnóstica de distribución para que los usuarios puedan usar [cualquier distribución](https://distrowatch.com/) que quieran. Dicho esto, hay algunas cosas a tener en cuenta:

Quieres una distribución que...

- ...**sea estable**. A menos que te guste hacer debugging de problemas a las 2 AM, no quieres que una [actualización desatendida](#actualizaciones-y-alertas-de-seguridad-automáticas), o una actualización manual de paquete/sistema, deje tu server inoperable. Pero esto también significa que estás de acuerdo con no ejecutar el software más reciente, más grande y de vanguardia.
- ...**se mantenga actualizada con parches de seguridad**. Puedes asegurar todo en tu server, pero si el sistema operativo central o las aplicaciones que estás ejecutando tienen vulnerabilidades conocidas, nunca estarás seguro.
- ...**con la que estés familiarizado.** Si no conoces Linux, te aconsejaría que juegues con uno antes de intentar asegurarlo. Deberías sentirte cómodo con él y conocer tu camino, como cómo instalar software, dónde están los archivos de configuración, etc...
- ...**tenga buen soporte.** Incluso el administrador más experimentado necesita ayuda de vez en cuando. Tener un lugar a dónde ir por ayuda salvará tu cordura.

([Tabla de Contenidos](#tabla-de-contenidos))

### Instalando Linux

Instalar Linux está fuera del alcance de esta guía porque cada distribución lo hace de manera diferente y las instrucciones de instalación generalmente están bien documentadas. Si necesitas ayuda, comienza con la documentación de tu distribución. Independientemente de la distribución, el proceso de alto nivel generalmente es así:

1. descargar el ISO
1. grabar/copiar/transferir a tu medio de instalación (por ejemplo, un CD o memoria USB)
1. arrancar tu server desde tu medio de instalación
1. seguir las instrucciones para instalar

Cuando sea aplicable, usa la opción de instalación experta para tener un control más estricto de lo que se está ejecutando en tu server. **Solo instala lo que absolutamente necesites.** Yo, personalmente, no instalo nada más que SSH. Además, marca la opción de Cifrado de Disco.

([Tabla de Contenidos](#tabla-de-contenidos))

### Requisitos Pre/Post Instalación

- Si estás abriendo ports en tu router para poder acceder a tu server desde el exterior, deshabilita el reenvío de ports hasta que tu sistema esté configurado y asegurado.
- A menos que estés haciendo todo conectado físicamente a tu server, necesitarás acceso remoto, así que asegúrate de que SSH funcione.
- Mantén tu sistema actualizado (es decir, `sudo apt update && sudo apt upgrade` en sistemas basados en Debian).
- Asegúrate de realizar cualquier tarea específica de tu configuración como:
  - Configurar red
  - Configurar puntos de montaje en `/etc/fstab`
  - Crear las cuentas de usuario iniciales
  - Instalar software central que querrás como `man`
  - Etc...
- Tu server necesitará poder enviar correos electrónicos para que puedas recibir alertas de seguridad importantes. Si no estás configurando un servidor de correo, revisa [Gmail y Exim4 Como MTA Con TLS Implícito](#gmail-y-exim4-como-mta-con-tls-implícito).
- También recomendaría que **leas** los [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks/) antes de comenzar con esta guía solo para digerir/entender lo que tienen que decir. Mi recomendación es revisar esta guía (la que estás leyendo aquí) primero y LUEGO la guía de CIS. De esa manera sus recomendaciones superarán cualquier cosa en esta guía.

([Tabla de Contenidos](#tabla-de-contenidos))

### Otras Notas Importantes

- Esta guía está siendo escrita y probada en Debian. La mayoría de las cosas a continuación deberían funcionar en otras distribuciones. Si encuentras algo que no funciona, por favor [contáctame](#contactándome). Lo principal que separa cada distribución será su sistema de gestión de paquetes. Como uso Debian, proporcionaré los comandos `apt` apropiados que deberían funcionar en todas las [distribuciones basadas en Debian](https://www.debian.org/derivatives/). Si alguien está dispuesto a [proporcionar](#contribuyendo) los comandos respectivos para otras distribuciones, los agregaré.
- Las rutas de archivos y configuraciones también pueden diferir ligeramente -- consulta con la documentación de tu distribución si tienes problemas.
- Lee toda la guía antes de empezar. Tu caso de uso y/o principios pueden requerir no hacer algo o cambiar el orden.
- No copies y pegues **ciegamente** sin entender lo que estás pegando. Algunos comandos necesitarán ser modificados para tus necesidades antes de que funcionen -- nombres de usuario por ejemplo.

([Tabla de Contenidos](#tabla-de-contenidos))

### Usando Playbooks de Ansible para asegurar tu Servidor Linux

Los playbooks de Ansible de esta guía están disponibles en [How To Secure A Linux Server With Ansible](https://github.com/moltenbit/How-To-Secure-A-Linux-Server-With-Ansible).

Asegúrate de editar las variables según tus necesidades y lee todas las tareas de antemano para confirmar que no rompe tu sistema. Después de ejecutar los playbooks, ¡asegúrate de que todas las configuraciones estén configuradas según tus necesidades!

1. Instala [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
2. git clone [How To Secure A Linux Server With Ansible](https://github.com/moltenbit/How-To-Secure-A-Linux-Server-With-Ansible)
3. [Crea Claves SSH Públicas/Privadas](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server#ssh-publicprivate-keys)
  ```
  ssh-keygen -t ed25519
  ```

5. Cambia todas las variables en *group_vars/variables.yml* según tus necesidades.
6. Habilita el acceso root SSH antes de ejecutar los playbooks:

  ```
  nano /etc/ssh/sshd_config
  [...]
  PermitRootLogin yes
  [...]
  ```

7. Recomendado: configura dirección IP estática en tu sistema.
8. Agrega la dirección IP de tu sistema a *hosts.yml*.

&nbsp;
