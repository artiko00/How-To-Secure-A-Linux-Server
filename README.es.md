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

Ejecuta el playbook de requisitos usando la contraseña de root que especificaste durante la instalación del servidor:

    ansible-playbook --inventory hosts.yml --ask-pass requirements-playbook.yml

&nbsp;

Ejecuta el playbook principal con la contraseña del nuevo usuario que especificaste en el archivo *variables.yml*:

    ansible-playbook --inventory hosts.yml --ask-pass main-playbook.yml

&nbsp;

Si necesitas ejecutar los playbooks varias veces, recuerda usar la clave SSH y el nuevo puerto SSH:

    ansible-playbook --inventory hosts.yml -e ansible_ssh_port=SSH_PORT --key-file /PATH/TO/SSH/KEY main-playbook.yml


([Tabla de Contenidos](#tabla-de-contenidos))

## El Servidor SSH

### Nota Importante Antes de Hacer Cambios en SSH

Es altamente recomendable que mantengas una 2da terminal abierta a tu servidor **antes de hacer y aplicar cambios en la configuración de SSH**. De esta manera, si te bloqueas fuera de tu 1ra sesión de terminal, aún tendrás una sesión conectada para poder arreglarlo.

Gracias a [Sonnenbrand](https://github.com/Sonnenbrand) por esta [idea](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/56).

### Claves Públicas/Privadas SSH

#### Por Qué

Usar claves públicas/privadas SSH es más seguro que usar una contraseña. También hace que sea más fácil y rápido conectarse a nuestro servidor porque no tienes que ingresar una contraseña.

#### Cómo Funciona

Consulta las referencias a continuación para más detalles pero, a alto nivel, las claves públicas/privadas funcionan usando un par de claves para verificar identidad.

1. Una clave, la clave **pública**, **solo puede encriptar datos**, no desencriptarlos
1. La otra clave, la clave **privada**, puede desencriptar los datos

Para SSH, se crea una clave pública y privada en el cliente. Debes mantener ambas claves seguras, especialmente la clave privada. Aunque la clave pública está destinada a ser pública, es prudente asegurarse de que ninguna de las claves caiga en manos equivocadas.

Cuando te conectas a un servidor SSH, SSH buscará una clave pública que coincida con el cliente desde el que te estás conectando en el archivo \`~/.ssh/authorized_keys\` en el servidor al que te estás conectando. Nota que el archivo está en la **carpeta home** del ID al que estás intentando conectarte. Entonces, después de crear la clave pública, necesitas agregarla a \`~/.ssh/authorized_keys\`. Un enfoque es copiarla a una memoria USB y transferirla físicamente al servidor. Otro enfoque es usar [\`ssh-copy-id\`](https://www.ssh.com/ssh/copy-id) para transferir y agregar la clave pública.

Después de que las claves han sido creadas y la clave pública ha sido agregada a `~/.ssh/authorized_keys` en el host, SSH usa las claves pública y privada para verificar identidad y luego establecer una conexión segura. Cómo se verifica la identidad es un proceso complicado pero [Digital Ocean](https://www.digitalocean.com/community/tutorials/understanding-the-ssh-encryption-and-connection-process) tiene un artículo muy bueno de cómo funciona. A alto nivel, la identidad se verifica cuando el servidor encripta un mensaje de desafío con la clave pública, luego lo envía al cliente. Si el cliente no puede desencriptar el mensaje de desafío con la clave privada, la identidad no puede ser verificada y no se establecerá una conexión.

Se consideran más seguras porque necesitas la clave privada para establecer una conexión SSH. Si estableces [`PasswordAuthentication no` en `/etc/ssh/sshd_config`](#PasswordAuthentication), entonces SSH no te dejará conectar sin la clave privada.

También puedes establecer una frase de contraseña (pass-phrase) para las claves, lo que requeriría que ingreses la frase de contraseña de la clave al conectarte usando claves públicas/privadas. Ten en cuenta que hacer esto significa que no puedes usar la clave para automatización porque no tendrás forma de enviar la frase de contraseña en tus scripts. `ssh-agent` es un programa que viene con muchas distribuciones Linux (y generalmente ya está corriendo) que te permitirá mantener tu clave privada sin encriptar en memoria por una duración configurable. Simplemente ejecuta `ssh-add` y te pedirá tu frase de contraseña. No se te pedirá tu frase de contraseña nuevamente hasta que la duración configurable haya pasado.

Usaremos claves Ed25519 que, según [https://linux-audit.com/](https://linux-audit.com/using-ed25519-openssh-keys-instead-of-dsa-rsa-ecdsa/):

> Está usando un esquema de firma de curva elíptica, que ofrece mejor seguridad que ECDSA y DSA. Al mismo tiempo, también tiene buen rendimiento.

#### Objetivos

- Claves SSH públicas/privadas Ed25519:
  - clave privada en tu cliente
  - clave pública en tu servidor

#### Notas

- Necesitarás hacer este paso para cada computadora y cuenta desde/como la que te conectarás a tu servidor.

#### Referencias

- https://www.ssh.com/ssh/public-key-authentication
- https://help.ubuntu.com/community/SSH/OpenSSH/Keys
- https://linux-audit.com/using-ed25519-openssh-keys-instead-of-dsa-rsa-ecdsa/
- https://www.digitalocean.com/community/tutorials/understanding-the-ssh-encryption-and-connection-process
- https://wiki.archlinux.org/index.php/SSH_Keys
- https://www.ssh.com/ssh/copy-id
- `man ssh-keygen`
- `man ssh-copy-id`
- `man ssh-add`

#### Pasos

1. Desde la computadora que vas a usar para conectarte a tu servidor, **el cliente**, no el servidor mismo, crea una clave [Ed25519](https://linux-audit.com/using-ed25519-openssh-keys-instead-of-dsa-rsa-ecdsa/) con `ssh-keygen`:

    ``` bash
    ssh-keygen -t ed25519
    ```

    > ```
    > Generating public/private ed25519 key pair.
    > Enter file in which to save the key (/home/user/.ssh/id_ed25519):
    > Created directory '/home/user/.ssh'.
    > Enter passphrase (empty for no passphrase):
    > Enter same passphrase again:
    > Your identification has been saved in /home/user/.ssh/id_ed25519.
    > Your public key has been saved in /home/user/.ssh/id_ed25519.pub.
    > The key fingerprint is:
    > SHA256:F44D4dr2zoHqgj0i2iVIHQ32uk/Lx4P+raayEAQjlcs user@client
    > The key's randomart image is:
    > +--[ED25519 256]--+
    > |xxxx  x          |
    > |o.o +. .         |
    > | o o oo   .      |
    > |. E oo . o .     |
    > | o o. o S o      |
    > |... .. o o       |
    > |.+....+ o        |
    > |+.=++o.B..       |
    > |+..=**=o=.       |
    > +----[SHA256]-----+
    > ```

    **Nota**: Si estableces una frase de contraseña, necesitarás ingresarla cada vez que te conectes a tu servidor usando esta clave, a menos que estés usando `ssh-agent`.

1. Ahora necesitas **agregar** la clave pública `~/.ssh/id_ed25519.pub` de tu cliente al archivo `~/.ssh/authorized_keys` en tu servidor. Como presumiblemente aún estamos en casa en la LAN, probablemente estemos a salvo de ataques [MIM](https://en.wikipedia.org/wiki/Man-in-the-middle_attack), así que usaremos `ssh-copy-id` para transferir y agregar la clave pública:

    ``` bash
    ssh-copy-id user@server
    ```

    > ```
    > /usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/user/.ssh/id_ed25519.pub"
    > The authenticity of host 'host (192.168.1.96)' can't be established.
    > ECDSA key fingerprint is SHA256:QaDQb/X0XyVlogh87sDXE7MR8YIK7ko4wS5hXjRySJE.
    > Are you sure you want to continue connecting (yes/no)? yes
    > /usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
    > /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
    > user@host's password:
    > 
    > Number of key(s) added: 1
    > 
    > Now try logging into the machine, with:   "ssh 'user@host'"
    > and check to make sure that only the key(s) you wanted were added.
    > ```

Ahora sería un buen momento para [realizar cualquier tarea específica de tu configuración](#requisitos-prepost-instalación).

([Tabla de Contenidos](#tabla-de-contenidos))

### Crear Grupo SSH Para AllowGroups

#### Por Qué

Para facilitar el control de quién puede hacer SSH al servidor. Al usar un grupo, podemos agregar/remover cuentas del grupo rápidamente para permitir o no permitir acceso SSH al servidor.

#### Cómo Funciona

Usaremos la [opción AllowGroups](#AllowGroups) en el archivo de configuración de SSH [`/etc/ssh/sshd_config`](#asegurar-etcsshsshd_config) para decirle al servidor SSH que solo permita a los usuarios hacer SSH si son miembros de un cierto grupo UNIX. Cualquiera que no esté en el grupo no podrá hacer SSH.

#### Objetivos

- un grupo UNIX que usaremos en [Asegurar `/etc/ssh/sshd_config`](#asegurar-etcsshsshd_config) para limitar quién puede hacer SSH al servidor

#### Notas

- Este es un paso de requisito previo para soportar la configuración `AllowGroup` establecida en [Asegurar `/etc/ssh/sshd_config`](#asegurar-etcsshsshd_config).

#### Referencias

- `man groupadd`
- `man usermod`

#### Pasos

1. Crea un grupo:

    ``` bash
    sudo groupadd sshusers
    ```

1. Agrega cuenta(s) al grupo:

    ``` bash
    sudo usermod -a -G sshusers user1
    sudo usermod -a -G sshusers user2
    sudo usermod -a -G sshusers ...
    ```

    Necesitarás hacer esto para cada cuenta en tu servidor que necesite acceso SSH.

([Tabla de Contenidos](#tabla-de-contenidos))

### Asegurar `/etc/ssh/sshd_config`

#### Por Qué

SSH es una puerta a tu servidor. Esto es especialmente cierto si estás abriendo ports en tu router para que puedas hacer SSH a tu servidor desde fuera de tu red doméstica. Si no está asegurado apropiadamente, un actor malicioso podría usarlo para obtener acceso no autorizado a tu sistema.

#### Cómo Funciona

`/etc/ssh/sshd_config` es el archivo de configuración predeterminado que usa el servidor SSH. Usaremos este archivo para indicar qué opciones debe usar el servidor SSH.

#### Objetivos

- una configuración SSH segura

#### Notas

- Asegúrate de haber completado [Crear Grupo SSH Para AllowGroups](#crear-grupo-ssh-para-allowgroups) primero.

#### Referencias

- Guías de OpenSSH de Mozilla para OpenSSH 6.7+ en https://infosec.mozilla.org/guidelines/openssh#modern-openssh-67
- https://linux-audit.com/audit-and-harden-your-ssh-configuration/
- https://www.ssh.com/ssh/sshd_config/
- https://www.techbrown.com/harden-ssh-secure-linux-vps-server/ (roto; prueba http://web.archive.org/web/20200413100933/https://www.techbrown.com/harden-ssh-secure-linux-vps-server/)
- https://serverfault.com/questions/660160/openssh-difference-between-internal-sftp-and-sftp-server/660325
- `man sshd_config`
- Gracias a [than0s](https://github.com/than0s) por [cómo encontrar configuraciones duplicadas](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/38).

#### Pasos

1. Haz un respaldo del archivo de configuración del servidor OpenSSH `/etc/ssh/sshd_config` y remueve los comentarios para que sea más fácil de leer:

    ``` bash
    sudo cp --archive /etc/ssh/sshd_config /etc/ssh/sshd_config-COPY-$(date +"%Y%m%d%H%M%S")
    sudo sed -i -r -e '/^#|^$/ d' /etc/ssh/sshd_config
    ```

1. Edita `/etc/ssh/sshd_config` luego encuentra y edita o agrega estas configuraciones que deberían aplicarse independientemente de tu configuración/setup:

    **Nota**: SSH no le gustan las configuraciones duplicadas que se contradicen. Por ejemplo, si tienes `ChallengeResponseAuthentication no` y luego `ChallengeResponseAuthentication yes`, SSH respetará la primera e ignorará la segunda. Tu archivo `/etc/ssh/sshd_config` puede que ya tenga algunas de las configuraciones/líneas a continuación. Para evitar problemas necesitarás revisar manualmente tu archivo `/etc/ssh/sshd_config` y abordar cualquier configuración duplicada que se contradiga.

    ```
    ########################################################################################################
    # inicio de configuraciones de https://infosec.mozilla.org/guidelines/openssh#modern-openssh-67 al 2019-01-01
    ########################################################################################################

    # Algoritmos HostKey soportados por orden de preferencia.
    HostKey /etc/ssh/ssh_host_ed25519_key
    HostKey /etc/ssh/ssh_host_rsa_key
    HostKey /etc/ssh/ssh_host_ecdsa_key

    KexAlgorithms curve25519-sha256@libssh.org,ecdh-sha2-nistp521,ecdh-sha2-nistp384,ecdh-sha2-nistp256,diffie-hellman-group-exchange-sha256

    Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,aes128-gcm@openssh.com,aes256-ctr,aes192-ctr,aes128-ctr

    MACs hmac-sha2-512-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512,hmac-sha2-256,umac-128@openssh.com

    # LogLevel VERBOSE registra la huella digital de la clave del usuario al iniciar sesión. Necesario para tener una pista de auditoría clara de qué clave se usó para iniciar sesión.
    LogLevel VERBOSE

    # Usa mecanismos de sandbox del kernel donde sea posible en procesos sin privilegios
    # Systrace en OpenBSD, Seccomp en Linux, seatbelt en MacOSX/Darwin, rlimit en otros lados.
    # Nota: Esta configuración está deprecada en OpenSSH 7.5 (https://www.openssh.com/txt/release-7.5)
    # UsePrivilegeSeparation sandbox

    ########################################################################################################
    # fin de configuraciones de https://infosec.mozilla.org/guidelines/openssh#modern-openssh-67 al 2019-01-01
    ########################################################################################################

    # no dejar que los usuarios establezcan variables de entorno
    PermitUserEnvironment no

    # Registrar acceso a archivos a nivel sftp (lectura/escritura/etc.) que de otra manera no se registraría fácilmente.
    Subsystem sftp  internal-sftp -f AUTHPRIV -l INFO

    # solo usar el protocolo más nuevo y más seguro
    Protocol 2

    # deshabilitar reenvío X11 ya que X11 es muy inseguro
    # realmente no deberías estar ejecutando X en un servidor de todas formas
    X11Forwarding no

    # deshabilitar reenvío de ports
    AllowTcpForwarding no
    AllowStreamLocalForwarding no
    GatewayPorts no
    PermitTunnel no

    # no permitir login si la cuenta tiene una contraseña vacía
    PermitEmptyPasswords no

    # ignorar .rhosts y .shosts
    IgnoreRhosts yes

    # verificar que el hostname coincida con la IP
    UseDNS yes

    Compression no
    TCPKeepAlive no
    AllowAgentForwarding no
    PermitRootLogin no

    # no permitir .rhosts o /etc/hosts.equiv
    HostbasedAuthentication no

    # https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/115
    HashKnownHosts yes
    ```

1. Luego **encuentra y edita o agrega** estas configuraciones, y establece valores según tus requerimientos:

    |Configuración|Valores Válidos|Ejemplo|Descripción|Notas|
    |--|--|--|--|--|
    |<a name="AllowGroups"></a>**AllowGroups**|nombre de grupo UNIX local|`AllowGroups sshusers`|grupo al que permitir acceso SSH||
    |**ClientAliveCountMax**|número|`ClientAliveCountMax 0`|número máximo de mensajes client alive enviados sin respuesta||
    |**ClientAliveInterval**|número de segundos|`ClientAliveInterval 300`|timeout en segundos antes de una solicitud de respuesta||
    |**ListenAddress**|lista separada por espacios de direcciones locales|<ul><li>`ListenAddress 0.0.0.0`</li><li>`ListenAddress 192.168.1.100`</li></ul>|direcciones locales en las que `sshd` debe escuchar|Ve [Issue #1](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/1) para detalles importantes.|
    |**LoginGraceTime**|número de segundos|`LoginGraceTime 30`|tiempo en segundos antes de que el login expire||
    |**MaxAuthTries**|número|`MaxAuthTries 2`|máximo de intentos permitidos para iniciar sesión||
    |**MaxSessions**|número|`MaxSessions 2`|número máximo de sesiones abiertas||
    |**MaxStartups**|número|`MaxStartups 2`|número máximo de sesiones de login||
    |<a name="PasswordAuthentication"></a>**PasswordAuthentication**|`yes` o `no`|`PasswordAuthentication no`|si el login con contraseña está permitido||
    |**Port**|cualquier número de port abierto/disponible|`Port 22`|port en el que `sshd` debe escuchar||

    Revisa `man sshd_config` para más detalles sobre qué significan estas configuraciones.

1. Asegúrate de que no haya configuraciones duplicadas que se contradigan. El comando a continuación no debería tener ninguna salida.

    ```bash
    awk 'NF && $1!~/^(#|HostKey)/{print $1}' /etc/ssh/sshd_config | sort | uniq -c | grep -v ' 1 '
    ```

1. Reinicia ssh:

    ``` bash
    sudo service sshd restart
    ```

1. Puedes verificar que las configuraciones funcionaron con `sshd -T` y verificar la salida:

    ``` bash
    sudo sshd -T
    ```

    > ```
    > port 22
    > addressfamily any
    > listenaddress [::]:22
    > listenaddress 0.0.0.0:22
    > usepam yes
    > logingracetime 30
    > x11displayoffset 10
    > maxauthtries 2
    > maxsessions 2
    > clientaliveinterval 300
    > clientalivecountmax 0
    > streamlocalbindmask 0177
    > permitrootlogin no
    > ignorerhosts yes
    > ignoreuserknownhosts no
    > hostbasedauthentication no
    > ...
    > subsystem sftp internal-sftp -f AUTHPRIV -l INFO
    > maxstartups 2:30:2
    > permittunnel no
    > ipqos lowdelay throughput
    > rekeylimit 0 0
    > permitopen any
    > ```

([Tabla de Contenidos](#tabla-de-contenidos))

### Remover Claves Diffie-Hellman Cortas

#### Por Qué

Según las [guías de OpenSSH de Mozilla para OpenSSH 6.7+](https://infosec.mozilla.org/guidelines/openssh#modern-openssh-67), "todos los módulos Diffie-Hellman en uso deberían tener al menos 3072 bits de largo".

El algoritmo Diffie-Hellman es usado por SSH para establecer una conexión segura. Entre más grande sea el módulo (tamaño de la clave) más fuerte es el cifrado.

#### Objetivos

- remover todas las claves Diffie-Hellman que tengan menos de 3072 bits de largo

#### Referencias

- Guías de OpenSSH de Mozilla para OpenSSH 6.7+ en https://infosec.mozilla.org/guidelines/openssh#modern-openssh-67
- https://infosec.mozilla.org/guidelines/key_management
- `man moduli`

#### Pasos

1. Haz un respaldo del archivo de módulos de SSH `/etc/ssh/moduli`:

    ``` bash
    sudo cp --archive /etc/ssh/moduli /etc/ssh/moduli-COPY-$(date +"%Y%m%d%H%M%S")
    ```

1. Remueve módulos cortos:

    ``` bash
    sudo awk '$5 >= 3071' /etc/ssh/moduli | sudo tee /etc/ssh/moduli.tmp
    sudo mv /etc/ssh/moduli.tmp /etc/ssh/moduli
    ````

([Tabla de Contenidos](#tabla-de-contenidos))

### 2FA/MFA para SSH

#### Por Qué

Aunque SSH es un guardián de seguridad bastante bueno para tus puertas y ventanas, sigue siendo una puerta visible que los actores maliciosos pueden ver e intentar entrar por fuerza bruta. [Fail2ban](#detección-y-prevención-de-intrusiones-en-aplicaciones-con-fail2ban) monitoreará estos intentos de fuerza bruta, pero no existe tal cosa como estar demasiado seguro. Requerir dos factores agrega una capa extra de seguridad.

Usar Autenticación de Dos Factores (2FA) / Autenticación Multi-Factor (MFA) requiere que cualquiera que entre tenga **dos** claves para entrar, lo que lo hace más difícil para los actores maliciosos. Las dos claves son:

1. Su contraseña
1. Un token de 6 dígitos que cambia cada 30 segundos

Sin ambas claves, no podrán entrar.

#### Por Qué No

Muchas personas pueden encontrar la experiencia engorrosa o molesta. Y, el acceso a tu sistema depende de la aplicación autenticadora que acompaña y que genera el código.

#### Cómo Funciona

En Linux, PAM es responsable de la autenticación. Hay cuatro tareas para PAM sobre las que puedes leer en https://en.wikipedia.org/wiki/Linux_PAM. Esta sección habla sobre la tarea de autenticación.

Cuando inicias sesión en un servidor, ya sea directamente desde la consola o vía SSH, la puerta por la que entraste enviará la solicitud a la tarea de autenticación de PAM y PAM pedirá y verificará tu contraseña. Puedes personalizar las reglas que cada puerta usa. Por ejemplo, podrías tener un conjunto de reglas cuando inicias sesión directamente desde la consola y otro conjunto de reglas cuando inicias sesión vía SSH.

Esta sección alterará las reglas de autenticación para cuando se inicia sesión vía SSH para requerir tanto una contraseña como un código de 6 dígitos.

Usaremos el módulo PAM libpam-google-authenticator de Google para crear y verificar una clave [TOTP](https://en.wikipedia.org/wiki/Time-based_One-time_Password_algorithm). https://fastmail.blog/2016/07/22/how-totp-authenticator-apps-work/ y https://jemurai.com/2018/10/11/how-it-works-totp-based-mfa/ tienen muy buenos artículos sobre cómo funciona TOTP.

Lo que haremos es decirle a la configuración PAM de SSH del servidor que le pida al usuario su contraseña y luego su token numérico. PAM entonces verificará la contraseña del usuario y, si es correcta, entonces enrutará la solicitud de autenticación a libpam-google-authenticator, que pedirá y verificará tu token de 6 dígitos. Si, y solo si, todo está bien, la autenticación tendrá éxito y se le permitirá al usuario iniciar sesión.

#### Objetivos

- 2FA/MFA habilitado para todas las conexiones SSH

#### Notas

- Antes de hacer esto, deberías tener una idea de cómo funciona 2FA/MFA y necesitarás una aplicación autenticadora en tu teléfono para continuar.
- Usaremos [google-authenticator-libpam](https://github.com/google/google-authenticator-libpam).
- Con la configuración a continuación, un usuario solo necesitará ingresar su código 2FA/MFA si está iniciando sesión con su contraseña pero **no** si está usando [claves públicas/privadas SSH](#claves-públicasprivadas-ssh). Revisa la documentación sobre cómo cambiar este comportamiento para adaptarlo a tus requerimientos.

#### Referencias

- https://github.com/google/google-authenticator-libpam
- https://en.wikipedia.org/wiki/Linux_PAM
- https://en.wikipedia.org/wiki/Time-based_One-time_Password_algorithm
- https://fastmail.blog/2016/07/22/how-totp-authenticator-apps-work/
- https://jemurai.com/2018/10/11/how-it-works-totp-based-mfa/

#### Pasos

1. Instala libpam-google-authenticator.

    En sistemas basados en Debian:

    ``` bash
    sudo apt install libpam-google-authenticator
    ```

1. **Asegúrate de haber iniciado sesión con el ID para el que quieres habilitar 2FA/MFA** y **ejecuta** `google-authenticator` para crear los datos de token necesarios:

    ``` bash
    google-authenticator
    ```

    > ```
    > Do you want authentication tokens to be time-based (y/n) y
    > https://www.google.com/chart?chs=200x200&chld=M|0&cht=qr&chl=otpauth://totp/user@host%3Fsecret%3DR4ZWX34FQKZROVX7AGLJ64684Y%26issuer%3Dhost
    > 
    > ...
    > 
    > Your new secret key is: R3NVX3FFQKZROVX7AGLJUGGESY
    > Your verification code is 751419
    > Your emergency scratch codes are:
    >   12345678
    >   90123456
    >   78901234
    >   56789012
    >   34567890
    > 
    > Do you want me to update your "/home/user/.google_authenticator" file (y/n) y
    > 
    > Do you want to disallow multiple uses of the same authentication
    > token? This restricts you to one login about every 30s, but it increases
    > your chances to notice or even prevent man-in-the-middle attacks (y/n) Do you want to disallow multiple uses of the same authentication
    > token? This restricts you to one login about every 30s, but it increases
    > your chances to notice or even prevent man-in-the-middle attacks (y/n) y
    > 
    > By default, tokens are good for 30 seconds. In order to compensate for
    > possible time-skew between the client and the server, we allow an extra
    > token before and after the current time. If you experience problems with
    > poor time synchronization, you can increase the window from its default
    > size of +-1min (window size of 3) to about +-4min (window size of
    > 17 acceptable tokens).
    > Do you want to do so? (y/n) y
    > 
    > If the computer that you are logging into isn't hardened against brute-force
    > login attempts, you can enable rate-limiting for the authentication module.
    > By default, this limits attackers to no more than 3 login attempts every 30s.
    > Do you want to enable rate-limiting (y/n) y
    > ```

    Nota que esto **no se ejecuta como root**.

    Selecciona la opción predeterminada (y en la mayoría de los casos) para todas las preguntas que hace y recuerda guardar los códigos de emergencia.

1. Haz un respaldo del archivo de configuración SSH de PAM `/etc/pam.d/sshd`:

    ``` bash
    sudo cp --archive /etc/pam.d/sshd /etc/pam.d/sshd-COPY-$(date +"%Y%m%d%H%M%S")
    ```

1. Ahora necesitamos habilitarlo como un método de autenticación para SSH agregando esta línea a `/etc/pam.d/sshd`:

    ```
    auth       required     pam_google_authenticator.so nullok
    ```

    **Nota**: Revisa [aquí](https://github.com/google/google-authenticator-libpam/blob/master/README.md#nullok) para qué significa `nullok`.

    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):

    ``` bash
    echo -e "\nauth       required     pam_google_authenticator.so nullok         # added by $(whoami) on $(date +"%Y-%m-%d @ %H:%M:%S")" | sudo tee -a /etc/pam.d/sshd
    ```

1. Dile a SSH que lo aproveche agregando o editando esta línea en `/etc/ssh/sshd_config`:

    ```
    ChallengeResponseAuthentication yes
    ```

    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):

    ``` bash
    sudo sed -i -r -e "s/^(challengeresponseauthentication .*)$/# \1         # commented by $(whoami) on $(date +"%Y-%m-%d @ %H:%M:%S")/I" /etc/ssh/sshd_config
    echo -e "\nChallengeResponseAuthentication yes         # added by $(whoami) on $(date +"%Y-%m-%d @ %H:%M:%S")" | sudo tee -a /etc/ssh/sshd_config
    ```

1. Reinicia ssh:

    ``` bash
    sudo service sshd restart
    ```

([Tabla de Contenidos](#tabla-de-contenidos))

## Lo Básico

### Limitar Quién Puede Usar sudo

#### Por Qué

sudo permite que las cuentas ejecuten comandos como otras cuentas, incluyendo **root**. Queremos asegurarnos de que solo las cuentas que queremos puedan usar sudo.

#### Objetivos

- privilegios de sudo limitados a aquellos que están en un grupo que especificamos

#### Notas

- Tu instalación puede que ya haya hecho esto, o puede que ya tenga un grupo especial destinado para este propósito, así que verifica primero.
  - Debian crea el grupo sudo. Para ver usuarios que son parte de este grupo (y por lo tanto tienen privilegios sudo):
	  
	  ```
	  cat /etc/group | grep "sudo"
	  ```
  - RedHat crea el grupo wheel
- Ve [https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/39](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/39) para una nota sobre algunas distribuciones que hacen que `sudo` no requiera contraseña. Gracias a [sbrl](https://github.com/sbrl) por compartir.

#### Pasos

1. Crea un grupo:

    ``` bash
    sudo groupadd sudousers
    ```

1. Agrega cuenta(s) al grupo:

    ``` bash
    sudo usermod -a -G sudousers user1
    sudo usermod -a -G sudousers user2
    sudo usermod -a -G sudousers  ...
    ```

    Necesitarás hacer esto para cada cuenta en tu servidor que necesite privilegios sudo.

1. Haz un respaldo del archivo de configuración de sudo `/etc/sudoers`:

    ``` bash
    sudo cp --archive /etc/sudoers /etc/sudoers-COPY-$(date +"%Y%m%d%H%M%S")
    ```

1. Edita el archivo de configuración de sudo `/etc/sudoers`:

    ``` bash
    sudo visudo
    ```

1. Dile a sudo que solo permita a usuarios en el grupo `sudousers` usar sudo agregando esta línea si no está ya ahí:

    ```
    %sudousers   ALL=(ALL:ALL) ALL
    ```

([Tabla de Contenidos](#tabla-de-contenidos))

### Limitar Quién Puede Usar su

#### Por Qué

su también permite que las cuentas ejecuten comandos como otras cuentas, incluyendo **root**. Queremos asegurarnos de que solo las cuentas que queremos puedan usar su.

#### Objetivos

- privilegios de su limitados a aquellos que están en un grupo que especificamos

#### Referencias

- Gracias a [olavim](https://github.com/olavim) por compartir [esta idea](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/41)

#### Pasos

1. Crea un grupo:

    ``` bash
    sudo groupadd suusers
    ```

1. Agrega cuenta(s) al grupo:

    ``` bash
    sudo usermod -a -G suusers user1
    sudo usermod -a -G suusers user2
    sudo usermod -a -G suusers  ...
    ```

    Necesitarás hacer esto para cada cuenta en tu servidor que necesite privilegios sudo.

1. Haz que solo usuarios en este grupo puedan ejecutar `/bin/su`:

    ``` bash
    sudo dpkg-statoverride --update --add root suusers 4750 /bin/su
    ```

([Tabla de Contenidos](#tabla-de-contenidos))

### Ejecutar aplicaciones en un sandbox con FireJail

#### Por Qué

Es absolutamente mejor, para muchas aplicaciones, ejecutarlas en un sandbox.

Los navegadores (incluso más los de código cerrado) y clientes de correo electrónico son altamente sugeridos.

#### Objetivos

- confinar aplicaciones en una jaula (jail) (pocas carpetas seguras) y bloquear acceso al resto del sistema

#### Referencias

- Gracias a [FireJail](https://firejail.wordpress.com/)

#### Pasos

1. Instala el software:

    ``` bash
    sudo apt install firejail firejail-profiles
    ```
    
    Nota: para Debian 10 Stable, se sugiere el Backport oficial:

    ``` bash
    sudo apt install -t buster-backports firejail firejail-profiles
    ```

2. Permite que una aplicación (instalada en `/usr/bin` o `/bin`) se ejecute solo en un sandbox (ve algunos ejemplos a continuación):

    ``` bash
    sudo ln -s /usr/bin/firejail /usr/local/bin/google-chrome-stable
    sudo ln -s /usr/bin/firejail /usr/local/bin/firefox
    sudo ln -s /usr/bin/firejail /usr/local/bin/chromium
    sudo ln -s /usr/bin/firejail /usr/local/bin/evolution
    sudo ln -s /usr/bin/firejail /usr/local/bin/thunderbird
    ```

3. Ejecuta la aplicación como usual (vía terminal o lanzador) y verifica si está ejecutándose en una jaula:

    ``` bash
    firejail --list
    ```

4. Permite que una aplicación en sandbox se ejecute de nuevo como era antes (ejemplo: firefox)

    ``` bash
    sudo rm /usr/local/bin/firefox
    ```

([Tabla de Contenidos](#tabla-de-contenidos))

### Cliente NTP

#### Por Qué

Muchos protocolos de seguridad aprovechan el tiempo. Si el tiempo de tu sistema es incorrecto, podría tener impactos negativos en tu servidor. Un cliente NTP puede resolver ese problema manteniendo el tiempo de tu sistema sincronizado con [servidores NTP globales](https://en.wikipedia.org/wiki/Network_Time_Protocol)

#### Cómo Funciona

NTP significa Network Time Protocol (Protocolo de Tiempo de Red). En el contexto de esta guía, un cliente NTP en el servidor se usa para actualizar el tiempo del servidor con el tiempo oficial extraído de servidores oficiales. Revisa https://www.pool.ntp.org/en/ para todos los servidores NTP públicos.

#### Objetivos

- Cliente NTP instalado y manteniendo el tiempo del servidor sincronizado

#### Referencias

- https://cloudpro.zone/index.php/2018/01/27/debian-9-3-server-setup-guide-part-4/
- https://en.wikipedia.org/wiki/Network_Time_Protocol
- https://www.pool.ntp.org/en/
- https://serverfault.com/questions/957302/securing-hardening-ntp-client-on-linux-servers-config-file/957450#957450
- https://tf.nist.gov/tf-cgi/servers.cgi

#### Pasos

1. Instala ntp.

    En sistemas basados en Debian:

    ``` bash
    sudo apt install ntp
    ```
    
1. Haz un respaldo del archivo de configuración del cliente NTP `/etc/ntp.conf`:

    ``` bash
    sudo cp --archive /etc/ntpsec/ntp.conf /etc/ntpsec/ntp.conf-COPY-$(date +"%Y%m%d%H%M%S")
    ```

1. La configuración predeterminada, al menos en Debian, ya es bastante segura. Lo único que querremos asegurarnos es que estamos usando la directiva `pool` y no ninguna directiva `server`. La directiva `pool` permite que el cliente NTP deje de usar un servidor si no responde o está sirviendo tiempo incorrecto. Haz esto comentando todas las directivas `server` y agregando lo siguiente a `/etc/ntp.conf`.
	
    ```
    pool pool.ntp.org iburst
    ```
    
    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):
    
    ``` bash
    sudo sed -i -r -e "s/^((server|pool).*)/# \1         # commented by $(whoami) on $(date +"%Y-%m-%d @ %H:%M:%S")/" /etc/ntp.conf
    echo -e "\npool pool.ntp.org iburst         # added by $(whoami) on $(date +"%Y-%m-%d @ %H:%M:%S")" | sudo tee -a /etc/ntp.conf
    ```

    **Ejemplo de `/etc/ntp.conf`**:
    
    > ```
    > driftfile /var/lib/ntp/ntp.drift
    > statistics loopstats peerstats clockstats
    > filegen loopstats file loopstats type day enable
    > filegen peerstats file peerstats type day enable
    > filegen clockstats file clockstats type day enable
    > restrict -4 default kod notrap nomodify nopeer noquery limited
    > restrict -6 default kod notrap nomodify nopeer noquery limited
    > restrict 127.0.0.1
    > restrict ::1
    > restrict source notrap nomodify noquery
    > pool pool.ntp.org iburst         # added by user on 2019-03-09 @ 10:23:35
    > ```
    
1. Reinicia ntp:

    ``` bash
    sudo service ntp restart
    ```

1. Verifica el estado del servicio ntp:

    ``` bash
    sudo systemctl status ntp
    ```

    > ```
    > ● ntp.service - LSB: Start NTP daemon
    >    Loaded: loaded (/etc/init.d/ntp; generated; vendor preset: enabled)
    >    Active: active (running) since Sat 2019-03-09 15:19:46 EST; 4s ago
    >      Docs: man:systemd-sysv-generator(8)
    >   Process: 1016 ExecStop=/etc/init.d/ntp stop (code=exited, status=0/SUCCESS)
    >   Process: 1028 ExecStart=/etc/init.d/ntp start (code=exited, status=0/SUCCESS)
    >     Tasks: 2 (limit: 4915)
    >    CGroup: /system.slice/ntp.service
    >            └─1038 /usr/sbin/ntpd -p /var/run/ntpd.pid -g -u 108:113
    > 
    > Mar 09 15:19:46 host ntpd[1038]: Listen and drop on 0 v6wildcard [::]:123
    > Mar 09 15:19:46 host ntpd[1038]: Listen and drop on 1 v4wildcard 0.0.0.0:123
    > Mar 09 15:19:46 host ntpd[1038]: Listen normally on 2 lo 127.0.0.1:123
    > Mar 09 15:19:46 host ntpd[1038]: Listen normally on 3 enp0s3 10.10.20.96:123
    > Mar 09 15:19:46 host ntpd[1038]: Listen normally on 4 lo [::1]:123
    > Mar 09 15:19:46 host ntpd[1038]: Listen normally on 5 enp0s3 [fe80::a00:27ff:feb6:ed8e%2]:123
    > Mar 09 15:19:46 host ntpd[1038]: Listening on routing socket on fd #22 for interface updates
    > Mar 09 15:19:47 host ntpd[1038]: Soliciting pool server 108.61.56.35
    > Mar 09 15:19:48 host ntpd[1038]: Soliciting pool server 69.89.207.199
    > Mar 09 15:19:49 host ntpd[1038]: Soliciting pool server 45.79.111.114
    > ```

1. Verifica el estado de ntp:

    ``` bash
    sudo ntpq -p
    ```

    > ```
    >      remote           refid      st t when poll reach   delay   offset  jitter
    > ==============================================================================
    >  pool.ntp.org    .POOL.          16 p    -   64    0    0.000    0.000   0.000
    > *lithium.constan 198.30.92.2      2 u    -   64    1   19.900    4.894   3.951
    >  ntp2.wiktel.com 212.215.1.157    2 u    2   64    1   48.061   -0.431   0.104
    > ```

([Tabla de Contenidos](#tabla-de-contenidos))

### Asegurando /proc

#### Por Qué

Para citar https://linux-audit.com/linux-system-hardening-adding-hidepid-to-proc/:

> Cuando miras en `/proc` descubrirás muchos archivos y directorios. Muchos de ellos son solo números, que representan la información sobre un ID de proceso particular (PID). Por defecto, los sistemas Linux están configurados para permitir que todos los usuarios locales vean toda esta información. Esto incluye información de procesos de otros usuarios. Esto podría incluir detalles sensibles que tal vez no quieras compartir con otros usuarios. Al aplicar algunos ajustes de configuración del sistema de archivos, podemos cambiar este comportamiento y mejorar la seguridad del sistema.

**Nota**: Esto puede fallar en algunos sistemas `systemd`. Por favor ve [https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/37](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/37) para más información. Gracias a [nlgranger](https://github.com/nlgranger) por compartir.

#### Objetivos

- `/proc` montado con `hidepid=2` para que los usuarios solo puedan ver información sobre sus procesos

#### Referencias

- https://linux-audit.com/linux-system-hardening-adding-hidepid-to-proc/
- https://likegeeks.com/secure-linux-server-hardening-best-practices/#Hardening-proc-Directory
- https://www.cyberciti.biz/faq/linux-hide-processes-from-other-users/

#### Pasos

1. Haz un respaldo de `/etc/fstab`:

    ``` bash
    sudo cp --archive /etc/fstab /etc/fstab-COPY-$(date +"%Y%m%d%H%M%S")
    ```

1. Agrega esta línea a `/etc/fstab` para tener `/proc` montado con `hidepid=2`:

    ```
    proc     /proc     proc     defaults,hidepid=2     0     0
    ```
    
    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):
    
    ``` bash
    echo -e "\nproc     /proc     proc     defaults,hidepid=2     0     0         # added by $(whoami) on $(date +"%Y-%m-%d @ %H:%M:%S")" | sudo tee -a /etc/fstab
    ```

1. Reinicia el sistema:

    ``` bash
    sudo reboot now
    ```
    
    **Nota**: Alternativamente, puedes remontar `/proc` sin reiniciar con `sudo mount -o remount,hidepid=2 /proc`

([Tabla de Contenidos](#tabla-de-contenidos))

### Forzar Cuentas a Usar Contraseñas Seguras

#### Por Qué

Por defecto, las cuentas pueden usar cualquier contraseña que quieran, incluyendo malas. [pwquality](https://linux.die.net/man/5/pwquality.conf)/[pam_pwquality](https://linux.die.net/man/8/pam_pwquality) aborda esta brecha de seguridad al proporcionar "una forma de configurar los requerimientos de calidad de contraseña predeterminados para las contraseñas del sistema" y verificando "su fortaleza contra un diccionario del sistema y un conjunto de reglas para identificar malas elecciones."

#### Cómo Funciona

En Linux, PAM es responsable de la autenticación. Hay cuatro tareas de PAM sobre las que puedes leer en https://en.wikipedia.org/wiki/Linux_PAM. Esta sección habla sobre la tarea de contraseña.

Cuando hay necesidad de establecer o cambiar una contraseña de cuenta, la tarea de contraseña de PAM maneja la solicitud. En esta sección le diremos a la tarea de contraseña de PAM que pase la nueva contraseña solicitada a libpam-pwquality para asegurarse de que cumpla con nuestros requerimientos. Si los requerimientos se cumplen, se usa/establece; si no cumple con los requerimientos, genera un error y le hace saber al usuario.

#### Objetivos

- contraseñas seguras forzadas

#### Pasos

1. Instala libpam-pwquality.

    En sistemas basados en Debian:

    ``` bash
    sudo apt install libpam-pwquality
    ```

1. Haz un respaldo del archivo de configuración de contraseña de PAM `/etc/pam.d/common-password`:

    ``` bash
    sudo cp --archive /etc/pam.d/common-password /etc/pam.d/common-password-COPY-$(date +"%Y%m%d%H%M%S")
    ```

1. Dile a PAM que use libpam-pwquality para forzar contraseñas seguras editando el archivo `/etc/pam.d/common-password` y cambia la línea que comienza así:

    ```
    password        requisite                       pam_pwquality.so
    ```

    a esto:

    ```
    password        requisite                       pam_pwquality.so retry=3 minlen=10 difok=3 ucredit=-1 lcredit=-1 dcredit=-1 ocredit=-1 maxrepeat=3 gecoschec
    ```

   Las opciones anteriores son:

     - `retry=3` = preguntar al usuario 3 veces antes de retornar con error.
     - `minlen=10` = la longitud mínima de la contraseña, considerando cualquier crédito (o débito) de estos:
       - `dcredit=-1` = debe tener al menos **un dígito**
       - `ucredit=-1` = debe tener al menos **una letra mayúscula**
       - `lcredit=-1` = debe tener al menos **una letra minúscula**
       - `ocredit=-1` = debe tener al menos **un carácter no alfanumérico**
     - `difok=3` = al menos 3 caracteres de la nueva contraseña no pueden haber estado en la contraseña antigua
     - `maxrepeat=3` = permitir un máximo de 3 caracteres repetidos
     - `gecoschec` = no permitir contraseñas con el nombre de la cuenta


    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):
    
    ``` bash
    sudo sed -i -r -e "s/^(password\s+requisite\s+pam_pwquality.so)(.*)$/# \1\2         # commented by $(whoami) on $(date +"%Y-%m-%d @ %H:%M:%S")\n\1 retry=3 minlen=10 difok=3 ucredit=-1 lcredit=-1 dcredit=-1 ocredit=-1 maxrepeat=3 gecoschec         # added by $(whoami) on $(date +"%Y-%m-%d @ %H:%M:%S")/" /etc/pam.d/common-password
    ```

([Tabla de Contenidos](#tabla-de-contenidos))

### Actualizaciones y Alertas de Seguridad Automáticas

#### Por Qué

Es importante mantener un servidor actualizado con los últimos **parches y actualizaciones críticos de seguridad**. De lo contrario, estás en riesgo de vulnerabilidades de seguridad conocidas que actores maliciosos podrían usar para obtener acceso no autorizado a tu servidor.

A menos que planees revisar tu servidor cada día, querrás una forma de actualizar automáticamente el sistema y/o recibir correos electrónicos sobre actualizaciones disponibles.

No quieres hacer todas las actualizaciones porque con cada actualización hay un riesgo de que algo se rompa. Es importante hacer las actualizaciones críticas pero todo lo demás puede esperar hasta que tengas tiempo de hacerlo manualmente.

#### Por Qué No

Las actualizaciones automáticas y desatendidas pueden romper tu sistema y puede que no estés cerca de tu servidor para arreglarlo. Esto sería especialmente problemático si rompiera tu acceso SSH.

#### Notas

- Cada distribución gestiona paquetes y actualizaciones de manera diferente. Hasta ahora solo tengo pasos para sistemas basados en Debian.
- Tu servidor necesitará una forma de enviar correos electrónicos para que esto funcione

#### Objetivos

- Actualizaciones automáticas, desatendidas, de parches críticos de seguridad
- Correos electrónicos automáticos de las actualizaciones pendientes restantes

#### Sistemas Basados en Debian

##### Cómo Funciona

En sistemas basados en Debian puedes usar:

- unattended-upgrades para hacer automáticamente las actualizaciones del sistema que quieras (i.e. actualizaciones críticas de seguridad)
- apt-listchanges para obtener detalles sobre cambios de paquetes antes de que sean instalados/actualizados
- apticron para recibir correos electrónicos de actualizaciones de paquetes pendientes

Usaremos unattended-upgrades para aplicar **parches críticos de seguridad**. También podemos aplicar actualizaciones estables ya que ya han sido probadas exhaustivamente por la comunidad Debian.

##### Referencias

- https://wiki.debian.org/UnattendedUpgrades
- https://debian-handbook.info/browse/stable/sect.regular-upgrades.html
- https://blog.sleeplessbeastie.eu/2015/01/02/how-to-perform-unattended-upgrades/
- https://www.vultr.com/docs/how-to-set-up-unattended-upgrades-on-debian-9-stretch
- https://github.com/mvo5/unattended-upgrades
- https://wiki.debian.org/UnattendedUpgrades#apt-listchanges
- https://www.cyberciti.biz/faq/apt-get-apticron-send-email-upgrades-available/
- https://www.unixmen.com/how-to-get-email-notifications-for-new-updates-on-debianubuntu/
- `/etc/apt/apt.conf.d/50unattended-upgrades`

##### Pasos

1. Instala unattended-upgrades, apt-listchanges, y apticron:

    ``` bash
    sudo apt install unattended-upgrades apt-listchanges apticron
    ```

1. Ahora necesitamos configurar unattended-upgrades para aplicar automáticamente las actualizaciones. Esto típicamente se hace editando los archivos `/etc/apt/apt.conf.d/20auto-upgrades` y `/etc/apt/apt.conf.d/50unattended-upgrades` que fueron creados por los paquetes. Sin embargo, porque estos archivos pueden sobrescribirse con una actualización futura, crearemos un nuevo archivo en su lugar. Crea el archivo `/etc/apt/apt.conf.d/51myunattended-upgrades` y agrega esto:

    ```
    // Enable the update/upgrade script (0=disable)
    APT::Periodic::Enable "1";

    // Do "apt-get update" automatically every n-days (0=disable)
    APT::Periodic::Update-Package-Lists "1";

    // Do "apt-get upgrade --download-only" every n-days (0=disable)
    APT::Periodic::Download-Upgradeable-Packages "1";

    // Do "apt-get autoclean" every n-days (0=disable)
    APT::Periodic::AutocleanInterval "7";

    // Send report mail to root
    //     0:  no report             (or null string)
    //     1:  progress report       (actually any string)
    //     2:  + command outputs     (remove -qq, remove 2>/dev/null, add -d)
    //     3:  + trace on    APT::Periodic::Verbose "2";
    APT::Periodic::Unattended-Upgrade "1";

    // Automatically upgrade packages from these
    Unattended-Upgrade::Origins-Pattern {
          "o=Debian,a=stable";
          "o=Debian,a=stable-updates";
          "origin=Debian,codename=${distro_codename},label=Debian-Security";
    };

    // You can specify your own packages to NOT automatically upgrade here
    Unattended-Upgrade::Package-Blacklist {
    };

    // Run dpkg --force-confold --configure -a if a unclean dpkg state is detected to true to ensure that updates get installed even when the system got interrupted during a previous run
    Unattended-Upgrade::AutoFixInterruptedDpkg "true";

    //Perform the upgrade when the machine is running because we wont be shutting our server down often
    Unattended-Upgrade::InstallOnShutdown "false";

    // Send an email to this address with information about the packages upgraded.
    Unattended-Upgrade::Mail "root";

    // Always send an e-mail
    Unattended-Upgrade::MailOnlyOnError "false";

    // Remove all unused dependencies after the upgrade has finished
    Unattended-Upgrade::Remove-Unused-Dependencies "true";

    // Remove any new unused dependencies after the upgrade has finished
    Unattended-Upgrade::Remove-New-Unused-Dependencies "true";

    // Automatically reboot WITHOUT CONFIRMATION if the file /var/run/reboot-required is found after the upgrade.
    Unattended-Upgrade::Automatic-Reboot "true";

    // Automatically reboot even if users are logged in.
    Unattended-Upgrade::Automatic-Reboot-WithUsers "true";
    ```

    **Notas**:
    - Revisa `/usr/lib/apt/apt.systemd.daily` para detalles sobre las opciones `APT::Periodic`
    - Revisa https://github.com/mvo5/unattended-upgrades para detalles sobre las opciones `Unattended-Upgrade`

1. Ejecuta una ejecución de prueba de unattended-upgrades para asegurarte de que tu archivo de configuración esté correcto:

    ``` bash
    sudo unattended-upgrade -d --dry-run
    ```

    Si todo está correcto, puedes dejarlo ejecutar cuando esté programado o forzar una ejecución con `unattended-upgrade -d`.

1. Configura apt-listchanges a tu gusto:

    ``` bash
    sudo dpkg-reconfigure apt-listchanges
    ```

1. Para apticron, las configuraciones predeterminadas son suficientemente buenas pero puedes revisarlas en `/etc/apticron/apticron.conf` si quieres cambiarlas. Por ejemplo, mi configuración se ve así:

    > ```
    > EMAIL="root"
    > NOTIFY_NO_UPDATES="1"
    > ```

([Tabla de Contenidos](#tabla-de-contenidos))

### Pool de Entropía Aleatoria Más Seguro (WIP)

#### Por Qué

WIP

#### Cómo Funciona

WIP

#### Objetivos

WIP

#### Referencias

- Gracias a [branneman](https://github.com/branneman) por esta idea según enviada en [issue #33](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/33).
- https://hackaday.com/2017/11/02/what-is-entropy-and-how-do-i-get-more-of-it/
- https://www.2uo.de/myths-about-urandom
- https://www.gnu.org/software/hurd/user/tlecarrour/rng-tools.html
- https://wiki.archlinux.org/index.php/Rng-tools
- https://www.howtoforge.com/helping-the-random-number-generator-to-gain-enough-entropy-with-rng-tools-debian-lenny
- https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/security_guide/sect-security_guide-encryption-using_the_random_number_generator

#### Pasos

1. Instala rng-tools.
   
    En sistemas basados en Debian:

    ``` bash
    sudo apt-get install rng-tools
    ```

1. Ahora necesitamos establecer el dispositivo de hardware usado para generar números aleatorios agregando esto a `/etc/default/rng-tools`:

    ```
    HRNGDEVICE=/dev/urandom
    ```
    
    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):
    
    ``` bash
    echo "HRNGDEVICE=/dev/urandom" | sudo tee -a /etc/default/rng-tools
    ```

1. Reinicia el servicio:

    ``` bash
    sudo systemctl stop rng-tools.service
    sudo systemctl start rng-tools.service
    ```

1. Prueba la aleatoriedad:
    - https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/security_guide/sect-security_guide-encryption-using_the_random_number_generator
    - https://wiki.archlinux.org/index.php/Rng-tools

([Tabla de Contenidos](#tabla-de-contenidos))

### Agregar Sistema de Seguridad de Login con Contraseña de Pánico/Secundaria/Falsa

#### Por Qué

Una herramienta útil para agregar seguridad extra de contraseña, contra ataque físico (en persona), métodos de Rescate/Robo/asalto.

#### Cómo Funciona

El pamduress agregará al usuario X una contraseña secundaria (contraseña de pánico), cuando esta contraseña coincida comenzará a ejecutar un script (este script hace lo que quieras que el usuario haga, cuando inicia sesión con ESTA contraseña de pánico).

Ejemplo Práctico y Real:
"Algún ladrón invade una casa y roba el servidor (que contiene respaldos IMPORTANTES de negocios, memorias de la vida propia y bla bla bla). No existe ningún cifrado de disco/arranque. El ladrón ha arrancado el servidor en su 'zona segura' e inicia un ataque de fuerza bruta. Ha crackeado la contraseña local por SSH con el usuario sudoer 'admin' con éxito, sí, una contraseña tonta, no LA fuerte/primaria. Inicia sesión SSH/o sesión física con esa contraseña tonta/pánico crackeada con 'admin' sudoer. Comienza a sentir que el servidor parece demasiado ocupado en menos de 2 minutos hasta congelarse.. 'wtf!?! vamos a reiniciar y continuar robando información..'.. lo siento amigo. todos los datos y el sistema fueron destruidos.".
    Conclusión, el ladrón crackeó la contraseña tonta/pánico/secundaria, y con esta contraseña está asociado un script que eliminará todos los archivos, configuración, sistema, arranque y después de eso comenzará a cargar la RAM y CPU para forzar al ladrón a reiniciar el sistema.

#### Objetivos

Prevenir acceso a persona maliciosa para acceder a información del servidor cuando obtienen una contraseña de manera forzada (asalto, pistola, rescate, ...). Por supuesto esto es útil en otras situaciones.

#### Referencias

- Gracias a [nuvious](https://github.com/nuvious/pam-duress) por esta herramienta
- Gracias a [hellresistor](https://gist.github.com/hellresistor/a4c542415a2d437e21afc235260d2366) por este Script-Herramienta-Perezoso

#### Pasos

1. Ejecuta esto (hellresistor Lazy-Tool-Script).

 ```` bash
#!/bin/bash
myownscript(){
#######################################################
## ***** EDIT THIS SCRIPT TO YOUR PROPOSES *****#

cat > "$ScriptFile" <<-EOF
#!/bin/bash
sudo rm -rf /home
#### FINISHED OWN SCRIPT ####
EOF
#######################################################
}
echo "Lets Config a PANIC PASSWORD ;)" && sleep 1
read -r -p "Want you REALLY configure A PANIC PASSWORD?? Write [ OK ] : " PAMDUR
if [[ "$PAMDUR" = "OK" ]]; then
 echo "Lets Config a PANIC USER, PASSWORD and SCRIPT ;)" && sleep 1
 while [ -z "$PANICUSR" ]
 do
  read -r -p "WRITE a Panic User to your pam-duress user [ root ]: " PANICUSR
  PANICUSR=${PANICUSR:=root}
 done
 if [ -z "$ScriptLoc" ]; then
  read -r -p "SET Script Directory with FULL PATH [ /root/.duress ]: " ScriptLoc
  ScriptLoc=${ScriptLoc:=/root/.duress}
  ScriptFile="$ScriptLoc/PanicScript.sh"
 fi
else
 echo "NOT Use PAM DURESS aKa Panic Password!!! Bye"
 exit 1
fi

sudo apt install -y git build-essential libpam0g-dev libssl-dev

cd "$HOME" || exit 1
git clone https://github.com/nuvious/pam-duress.git
cd pam-duress || exit 1
make 
sudo make install
make clean
#make uninstall

mkdir -p $ScriptLoc
sudo mkdir -p /etc/duress.d
myownscript
duress_sign $ScriptFile
chmod -R 500 $ScriptLoc
chmod 400 $ScriptLoc/*.sha256
chown -R $PANICUSR $ScriptLoc

sudo cp --preserve /etc/pam.d/common-auth /etc/pam.d/common-auth.bck

echo "
auth   	[success=2 default=ignore]	     pam_unix.so nullok_secure
auth    [success=1 default=ignore]      pam_duress.so
auth	   requisite	                    		pam_deny.so
auth	   required	                     		pam_permit.so
" | sudo tee /etc/pam.d/common-auth

read -r -p "Press <Enter> Key to Finish PAM DURESS Script!"
exit 0
 ````

([Tabla de Contenidos](#tabla-de-contenidos))

## La Red

### Firewall Con UFW (Uncomplicated Firewall)

#### Por Qué

Llámame paranoico, y no tienes que estar de acuerdo, pero quiero denegar todo el tráfico entrante y saliente de mi servidor excepto lo que permito explícitamente. ¿Por qué estaría mi servidor enviando tráfico que no conozco? ¿Y por qué estaría tráfico externo intentando acceder a mi servidor si no sé quién o qué es? Cuando se trata de buena seguridad, mi opinión es rechazar/denegar por defecto, y permitir por excepción.

Por supuesto, si no estás de acuerdo, eso está totalmente bien y puedes configurar UFW según tus necesidades.

De cualquier manera, asegurar que solo el tráfico que permitimos explícitamente es el trabajo de un firewall.

#### Cómo Funciona

El kernel Linux proporciona capacidades para monitorear y controlar el tráfico de red. Estas capacidades están expuestas al usuario final a través de utilidades de firewall. En Linux, el firewall más común es [iptables](https://en.wikipedia.org/wiki/Iptables). Sin embargo, iptables es bastante complicado y confuso (en mi opinión). Aquí es donde entra UFW. Piensa en UFW como un front-end para iptables. Simplifica el proceso de gestionar las reglas de iptables que le dicen al kernel Linux qué hacer con el tráfico de red.

**UFW** funciona permitiéndote configurar reglas que:

- **permiten** o **deniegan**
- tráfico de **entrada** o **salida**
- **hacia** o **desde** ports

Puedes crear reglas especificando explícitamente los ports o con configuraciones de aplicación que especifican los ports.

#### Objetivos

 - todo el tráfico de red, entrada y salida, bloqueado excepto aquellos que permitimos explícitamente

#### Notas

- A medida que instales otros programas, necesitarás habilitar los ports/aplicaciones necesarios.

#### Referencias

- https://launchpad.net/ufw

#### Pasos

1. Instala ufw.

    En sistemas basados en Debian:

    ``` bash
    sudo apt install ufw
    ```

1. Deniega todo el tráfico saliente:

    ``` bash
    sudo ufw default deny outgoing comment 'deny all outgoing traffic'
    ```

    > ```
    > Default outgoing policy changed to 'deny'
    > (be sure to update your rules accordingly)
    > ```

    Si no eres tan paranoico como yo, y no quieres denegar todo el tráfico saliente, puedes permitirlo en su lugar:

    ``` bash
    sudo ufw default allow outgoing comment 'allow all outgoing traffic'
    ```

1. Deniega todo el tráfico entrante:

    ``` bash
    sudo ufw default deny incoming comment 'deny all incoming traffic'
    ```

1. Obviamente queremos conexiones SSH entrantes:

    ``` bash
    sudo ufw limit in ssh comment 'allow SSH connections in'
    ```

    > ```
    > Rules updated
    > Rules updated (v6)
    > ```

1. Permite tráfico adicional según tus necesidades. Algunos casos de uso comunes:

    ``` bash
    # permitir tráfico saliente al port 53 -- DNS
    sudo ufw allow out 53 comment 'allow DNS calls out'
	
	# permitir tráfico saliente al port 123 -- NTP
    sudo ufw allow out 123 comment 'allow NTP out'

    # permitir tráfico saliente para HTTP, HTTPS, o FTP
    # apt podría necesitar estos dependiendo de qué fuentes estés usando
    sudo ufw allow out http comment 'allow HTTP traffic out'
    sudo ufw allow out https comment 'allow HTTPS traffic out'
    sudo ufw allow out ftp comment 'allow FTP traffic out'

    # permitir whois
    sudo ufw allow out whois comment 'allow whois'
    
    # permitir correos para notificaciones de estado -- elige port según tu proveedor
    sudo ufw allow out 25 comment 'allow SMTP out'
    sudo ufw allow out 587 comment 'allow SMTP out'

    # permitir tráfico saliente al port 68 -- el cliente DHCP
    # solo necesitas esto si estás usando DHCP
    sudo ufw allow out 67 comment 'allow the DHCP client to update'
    sudo ufw allow out 68 comment 'allow the DHCP client to update'
    ```
    
    **Nota**: Necesitarás permitir HTTP/HTTPS para instalar paquetes y muchas otras cosas.

1. Inicia ufw:

    ``` bash
    sudo ufw enable
    ```

    > ```
    > Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
    > Firewall is active and enabled on system startup
    > ```

1. Si quieres ver el estado:

    ``` bash
    sudo ufw status
    ```

    > ```
    > Status: active
    > 
    > To                         Action      From
    > --                         ------      ----
    > 22/tcp                     LIMIT       Anywhere                   # allow SSH connections in
    > 22/tcp (v6)                LIMIT       Anywhere (v6)              # allow SSH connections in
    > 
    > 53                         ALLOW OUT   Anywhere                   # allow DNS calls out
    > 123                        ALLOW OUT   Anywhere                   # allow NTP out
    > 80/tcp                     ALLOW OUT   Anywhere                   # allow HTTP traffic out
    > 443/tcp                    ALLOW OUT   Anywhere                   # allow HTTPS traffic out
    > 21/tcp                     ALLOW OUT   Anywhere                   # allow FTP traffic out
    > Mail submission            ALLOW OUT   Anywhere                   # allow mail out
    > 43/tcp                     ALLOW OUT   Anywhere                   # allow whois
    > 53 (v6)                    ALLOW OUT   Anywhere (v6)              # allow DNS calls out
    > 123 (v6)                   ALLOW OUT   Anywhere (v6)              # allow NTP out
    > 80/tcp (v6)                ALLOW OUT   Anywhere (v6)              # allow HTTP traffic out
    > 443/tcp (v6)               ALLOW OUT   Anywhere (v6)              # allow HTTPS traffic out
    > 21/tcp (v6)                ALLOW OUT   Anywhere (v6)              # allow FTP traffic out
    > Mail submission (v6)       ALLOW OUT   Anywhere (v6)              # allow mail out
    > 43/tcp (v6)                ALLOW OUT   Anywhere (v6)              # allow whois
    > ```

    o

    ``` bash
    sudo ufw status verbose
    ```

    > ```
    > Status: active
    > Logging: on (low)
    > Default: deny (incoming), deny (outgoing), disabled (routed)
    > New profiles: skip
    > 
    > To                         Action      From
    > --                         ------      ----
    > 22/tcp                     LIMIT IN    Anywhere                   # allow SSH connections in
    > 22/tcp (v6)                LIMIT IN    Anywhere (v6)              # allow SSH connections in
    > 
    > 53                         ALLOW OUT   Anywhere                   # allow DNS calls out
    > 123                        ALLOW OUT   Anywhere                   # allow NTP out
    > 80/tcp                     ALLOW OUT   Anywhere                   # allow HTTP traffic out
    > 443/tcp                    ALLOW OUT   Anywhere                   # allow HTTPS traffic out
    > 21/tcp                     ALLOW OUT   Anywhere                   # allow FTP traffic out
    > 587/tcp (Mail submission)  ALLOW OUT   Anywhere                   # allow mail out
    > 43/tcp                     ALLOW OUT   Anywhere                   # allow whois
    > 53 (v6)                    ALLOW OUT   Anywhere (v6)              # allow DNS calls out
    > 123 (v6)                   ALLOW OUT   Anywhere (v6)              # allow NTP out
    > 80/tcp (v6)                ALLOW OUT   Anywhere (v6)              # allow HTTP traffic out
    > 443/tcp (v6)               ALLOW OUT   Anywhere (v6)              # allow HTTPS traffic out
    > 21/tcp (v6)                ALLOW OUT   Anywhere (v6)              # allow FTP traffic out
    > 587/tcp (Mail submission (v6)) ALLOW OUT   Anywhere (v6)              # allow mail out
    > 43/tcp (v6)                ALLOW OUT   Anywhere (v6)              # allow whois
    > ```

7. Si necesitas eliminar una regla
    
    ``` bash
    sudo ufw status numbered
    [...]
    sudo ufw delete 3 #número de línea de la regla que quieres eliminar
    ```

#### Aplicaciones Predeterminadas

ufw viene con algunas aplicaciones predeterminadas. Puedes verlas con:

``` bash
sudo ufw app list
```

> ```
> Available applications:
>   AIM
>   Bonjour
>   CIFS
>   DNS
>   Deluge
>   IMAP
>   IMAPS
>   IPP
>   KTorrent
>   Kerberos Admin
>   Kerberos Full
>   Kerberos KDC
>   Kerberos Password
>   LDAP
>   LDAPS
>   LPD
>   MSN
>   MSN SSL
>   Mail submission
>   NFS
>   OpenSSH
>   POP3
>   POP3S
>   PeopleNearby
>   SMTP
>   SSH
>   Socks
>   Telnet
>   Transmission
>   Transparent Proxy
>   VNC
>   WWW
>   WWW Cache
>   WWW Full
>   WWW Secure
>   XMPP
>   Yahoo
>   qBittorrent
>   svnserve
> ```

Para obtener detalles sobre la aplicación, como qué ports incluye, escribe:

``` bash
sudo ufw app info [nombre de la aplicación]
```

> ``` bash
> sudo ufw app info DNS
> ```
> 
> ```
> Profile: DNS
> Title: Internet Domain Name Server
> Description: Internet Domain Name Server
> 
> Port:
>   53
> ```

#### Aplicación Personalizada

Si no quieres crear reglas proporcionando explícitamente el(los) número(s) de port, puedes crear tus propias configuraciones de aplicación. Para hacer esto, crea un archivo en `/etc/ufw/applications.d`.

Por ejemplo, aquí está lo que usarías para [Plex](https://support.plex.tv/articles/201543147-what-network-ports-do-i-need-to-allow-through-my-firewall/):

``` bash
cat /etc/ufw/applications.d/plexmediaserver
```

> ```
> [PlexMediaServer]
> title=Plex Media Server
> description=This opens up PlexMediaServer for http (32400), upnp, and autodiscovery.
> ports=32469/tcp|32413/udp|1900/udp|32400/tcp|32412/udp|32410/udp|32414/udp|32400/udp
> ```

Luego puedes habilitarla como cualquier otra aplicación:

```bash
sudo ufw allow plexmediaserver
```

([Tabla de Contenidos](#tabla-de-contenidos))

### Detección y Prevención de Intrusiones en iptables con PSAD

#### Por Qué

Aunque tengas un firewall para guardar tus puertas, es posible intentar entrar por fuerza bruta en cualquiera de las puertas guardadas. Queremos monitorear toda la actividad de red para detectar intentos potenciales de intrusión, como intentos repetidos de entrar, y bloquearlos.

#### Cómo Funciona

No puedo explicarlo mejor de lo que el usuario [FINESEC](https://serverfault.com/users/143961/finesec) de https://serverfault.com/ lo hizo en: https://serverfault.com/a/447604/289829.

> Fail2BAN escanea archivos log de varias aplicaciones como apache, ssh o ftp y automáticamente banea IPs que muestran signos maliciosos como intentos de login automatizados. PSAD por otro lado escanea mensajes de log de iptables e ip6tables (típicamente /var/log/messages) para detectar y opcionalmente bloquear escaneos y otros tipos de tráfico sospechoso como DDoS o intentos de fingerprinting del SO. Está bien usar ambos programas al mismo tiempo porque operan en diferentes niveles.

Y, como ya estamos usando [UFW](#firewall-con-ufw-uncomplicated-firewall) seguiremos las increíbles instrucciones de [netson](https://gist.github.com/netson) en https://gist.github.com/netson/c45b2dc4e835761fbccc para hacer que PSAD funcione con UFW.

#### Referencias

- http://www.cipherdyne.org/psad/
- http://www.cipherdyne.org/psad/docs/config.html
- https://www.thefanclub.co.za/how-to/how-install-psad-intrusion-detection-ubuntu-1204-lts-server
- https://serverfault.com/a/447604/289829
- https://serverfault.com/a/770424/289829
- https://gist.github.com/netson/c45b2dc4e835761fbccc
- Gracias a [moltenbit](https://github.com/moltenbit) por detectar el problema ([#61](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/61)) con `psadwatchd`.

#### Pasos

1. Instala psad.

    En sistemas basados en Debian:

    ``` bash
    sudo apt install psad
    ```

1. Haz un respaldo del archivo de configuración de psad `/etc/psad/psad.conf`:

    ``` bash
    sudo cp --archive /etc/psad/psad.conf /etc/psad/psad.conf-COPY-$(date +"%Y%m%d%H%M%S")
    ```

1. Revisa y actualiza las opciones de configuración en `/etc/psad/psad.conf`. Presta especial atención a estas:

   |Configuración|Establecer A
   |--|--|
   |[`EMAIL_ADDRESSES`](http://www.cipherdyne.org/psad/docs/config.html#EMAIL_ADDRESSES)|tu(s) dirección(es) de correo electrónico|
   |`HOSTNAME`|el hostname de tu servidor|
   |`EXPECT_TCP_OPTIONS`|`EXPECT_TCP_OPTIONS Y;`|
   |`ENABLE_PSADWATCHD`|`ENABLE_PSADWATCHD Y;`|
   |[`ENABLE_AUTO_IDS`](http://www.cipherdyne.org/psad/docs/config.html#ENABLE_AUTO_IDS)|`ENABLE_AUTO_IDS Y;`|
   |`ENABLE_AUTO_IDS_EMAILS`|`ENABLE_AUTO_IDS_EMAILS Y;`|

   Revisa el archivo de configuración y la documentación de psad en http://www.cipherdyne.org/psad/docs/config.html para más detalles.

1. <a name="psad_step4"></a>Ahora necesitamos hacer algunos cambios a ufw para que funcione con psad diciéndole a ufw que registre todo el tráfico para que psad pueda analizarlo. Haz esto editando **dos archivos** y agregando estas líneas **al final pero antes de la línea COMMIT**.

    Haz respaldos:

    ``` bash
    sudo cp --archive /etc/ufw/before.rules /etc/ufw/before.rules-COPY-$(date +"%Y%m%d%H%M%S")
    sudo cp --archive /etc/ufw/before6.rules /etc/ufw/before6.rules-COPY-$(date +"%Y%m%d%H%M%S")
    ```

    Edita los archivos:

    - `/etc/ufw/before.rules`
    - `/etc/ufw/before6.rules`

    Y agrega esto **al final pero antes de la línea COMMIT**:

    ```
    # registrar todo el tráfico para que psad pueda analizar
    -A INPUT -j LOG --log-tcp-options --log-prefix "[IPTABLES] "
    -A FORWARD -j LOG --log-tcp-options --log-prefix "[IPTABLES] "
    ```

    **Nota**: Estamos agregando un prefijo de log a todos los logs de iptables. Necesitaremos esto para [separar logs de iptables a su propio archivo](#archivo-de-log-separado-para-iptables).

    Por ejemplo:

    > ```
    > ...
    > 
    > # registrar todo el tráfico para que psad pueda analizar
    > -A INPUT -j LOG --log-tcp-options --log-prefix "[IPTABLES] "
    > -A FORWARD -j LOG --log-tcp-options --log-prefix "[IPTABLES] "
    > 
    > # no borres la línea 'COMMIT' o estas reglas no se procesarán
    > COMMIT
    > ```

1. Ahora necesitamos recargar/reiniciar ufw y psad para que los cambios tengan efecto:

    ``` bash
    sudo ufw reload

    sudo psad -R
    sudo psad --sig-update
    sudo psad -H
    ```

1. Analiza reglas de iptables para errores:

    ``` bash
    sudo psad --fw-analyze
    ```

    > ```
    > [+] Parsing INPUT chain rules.
    > [+] Parsing INPUT chain rules.
    > [+] Firewall config looks good.
    > [+] Completed check of firewall ruleset.
    > [+] Results in /var/log/psad/fw_check
    > [+] Exiting.
    > ```

    **Nota**: Si hubo algún problema recibirás un correo electrónico con el error.

1. Verifica el estado de psad:

    ``` bash
    sudo psad --Status
    ```

    > ```
    > [-] psad: pid file /var/run/psad/psadwatchd.pid does not exist for psadwatchd on vm
    > [+] psad_fw_read (pid: 3444)  %CPU: 0.0  %MEM: 2.2
    >     Running since: Sat Feb 16 01:03:09 2019
    > 
    > [+] psad (pid: 3435)  %CPU: 0.2  %MEM: 2.7
    >     Running since: Sat Feb 16 01:03:09 2019
    >     Command line arguments: [none specified]
    >     Alert email address(es): root@localhost
    > 
    > [+] Version: psad v2.4.3
    > 
    > [+] Top 50 signature matches:
    >         [NONE]
    > 
    > [+] Top 25 attackers:
    >         [NONE]
    > 
    > [+] Top 20 scanned ports:
    >         [NONE]
    > 
    > [+] iptables log prefix counters:
    >         [NONE]
    > 
    >     Total protocol packet counters:
    > 
    > [+] IP Status Detail:
    >         [NONE]
    > 
    >     Total scan sources: 0
    >     Total scan destinations: 0
    > 
    > [+] These results are available in: /var/log/psad/status.out
    > ```

([Tabla de Contenidos](#tabla-de-contenidos))

### Detección y Prevención de Intrusiones en Aplicaciones Con Fail2Ban

#### Por Qué

UFW le dice a tu servidor qué puertas tapar para que nadie pueda verlas, y qué puertas permitir a usuarios autorizados. PSAD monitorea actividad de red para detectar y prevenir intrusiones potenciales -- intentos repetidos de entrar.

Pero ¿qué hay de las aplicaciones/servicios que tu servidor está ejecutando, como SSH y Apache, donde tu firewall está configurado para permitir acceso? Aunque el acceso puede estar permitido eso no significa que todos los intentos de acceso sean válidos e inofensivos. ¿Qué pasa si alguien intenta entrar por fuerza bruta a una aplicación web que estás ejecutando en tu servidor? Aquí es donde entra Fail2ban.

#### Cómo Funciona

Fail2ban monitorea los logs de tus aplicaciones (como SSH y Apache) para detectar y prevenir intrusiones potenciales. Monitoreará tráfico de red/logs y prevendrá intrusiones bloqueando actividad sospechosa (ej. múltiples conexiones fallidas sucesivas en un corto período de tiempo).

#### Objetivos

- monitoreo de red para actividad sospechosa con baneo automático de IPs ofensivas

#### Notas

- Por ahora, lo único ejecutándose en este servidor es SSH así que querremos que Fail2ban monitoree SSH y banee según sea necesario.
- A medida que instales otros programas, necesitarás crear/configurar las jails apropiadas y habilitarlas.

#### Referencias

- https://www.fail2ban.org/
- https://blog.vigilcode.com/2011/05/ufw-with-fail2ban-quick-secure-setup-part-ii/
- https://dodwell.us/security/ufw-fail2ban-portscan.html
- https://www.howtoforge.com/community/threads/fail2ban-and-ufw-on-debian.77261/

#### Pasos

1. Instala fail2ban.

    En sistemas basados en Debian:

    ``` bash
    sudo apt install fail2ban
    ```

1. No queremos editar `/etc/fail2ban/fail2ban.conf` o `/etc/fail2ban/jail.conf` porque una actualización futura puede sobrescribirlos así que crearemos una copia local en su lugar. Crea el archivo `/etc/fail2ban/jail.local` y agrega esto después de reemplazar `[SEGMENTO LAN]` y `[tu correo]` con los valores apropiados:

    ```
    [DEFAULT]
    # el rango de direcciones IP que queremos ignorar
    ignoreip = 127.0.0.1/8 [SEGMENTO LAN]

    # a quién enviar correo electrónico
    destemail = [tu correo]

    # de quién es el correo electrónico
    sender = [tu correo]

    # como estamos usando exim4 para enviar correos
    mta = mail

    # recibir alertas por correo electrónico
    action = %(action_mwl)s
    ```

    **Nota**: Tu servidor necesitará poder enviar correos electrónicos para que Fail2ban pueda avisarte de actividad sospechosa y cuándo baneó una IP.

1. Necesitamos crear una jail para SSH que le diga a fail2ban que mire logs de SSH y use ufw para banear/desbanear IPs según sea necesario. Crea una jail para SSH creando el archivo `/etc/fail2ban/jail.d/ssh.local` y agregando esto:

    ```
    [sshd]
    enabled = true
    banaction = ufw
    port = ssh
    filter = sshd
    logpath = %(sshd_log)s
    maxretry = 5
    ```

    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):

    ``` bash
    cat << EOF | sudo tee /etc/fail2ban/jail.d/ssh.local
    [sshd]
    enabled = true
    banaction = ufw
    port = ssh
    filter = sshd
    logpath = %(sshd_log)s
    maxretry = 5
    EOF
    ```

1. En lo anterior le decimos a fail2ban que use ufw como `banaction`. Fail2ban viene con un archivo de configuración de acción para ufw. Puedes verlo en `/etc/fail2ban/action.d/ufw.conf`

1. Habilita fail2ban:

    ``` bash
    sudo fail2ban-client start
    sudo fail2ban-client reload
    sudo fail2ban-client add sshd # Esto puede fallar en algunos sistemas si la jail sshd se agregó por defecto
    ```

1. Para verificar el estado:

    ``` bash
    sudo fail2ban-client status
    ```

    > ```
    > Status
    > |- Number of jail:      1
    > `- Jail list:   sshd
    > ```

    ``` bash
    sudo fail2ban-client status sshd
    ```

    > ```
    > Status for the jail: sshd
    > |- Filter
    > |  |- Currently failed: 0
    > |  |- Total failed:     0
    > |  `- File list:        /var/log/auth.log
    > `- Actions
    >    |- Currently banned: 0
    >    |- Total banned:     0
    >    `- Banned IP list:
    > ```

#### Jails Personalizadas

Aún no he necesitado crear una jail personalizada. Una vez que lo haga, y descubra cómo, actualizaré esta guía. O, si sabes cómo por favor ayuda a [contribuir](#contribuyendo).

#### Desbanear una IP

Para desbanear una IP usa este comando:

``` bash
fail2ban-client set [jail] unbanip [IP]
```

`[jail]` es el nombre de la jail que tiene la IP baneada y `[IP]` es la dirección IP que quieres desbanear. Por ejemplo, para desbanear `192.168.1.100` de SSH harías:

``` bash
fail2ban-client set sshd unbanip 192.168.1.100
```

([Tabla de Contenidos](#tabla-de-contenidos))

### Detección y Prevención de Intrusiones en Aplicaciones Con CrowdSec

#### Por Qué

UFW le dice a tu servidor qué puertas tapar para que nadie pueda verlas, y qué puertas permitir a usuarios autorizados. PSAD monitorea actividad de red para detectar y prevenir intrusiones potenciales -- intentos repetidos de entrar.

CrowdSec es similar a Fail2Ban en que monitorea los logs de tus aplicaciones (como SSH y Apache) para detectar y prevenir intrusiones potenciales. Sin embargo, CrowdSec está acoplado con una comunidad que comparte inteligencia de amenazas de vuelta a CrowdSec para luego distribuir una Lista de Bloqueo Comunitaria a todos los usuarios.

#### Cómo Funciona

CrowdSec monitorea los logs de tus aplicaciones (como SSH y Apache) para detectar y prevenir intrusiones potenciales. Monitoreará tráfico de red/logs y prevendrá intrusiones bloqueando actividad sospechosa (ej. múltiples conexiones fallidas sucesivas en un corto período de tiempo). Una vez que se detecta una IP maliciosa, se agregará a tu lista de decisiones local y la información de amenazas se comparte con CrowdSec para actualizar la Lista de Bloqueo Comunitaria sobre direcciones IP maliciosas. Una vez que una dirección IP alcanza un cierto umbral de actividad maliciosa, se propagará automáticamente a todos los demás usuarios de CrowdSec para bloquear proactivamente.

#### Objetivos

- monitoreo de red para actividad sospechosa con baneo automático de IPs ofensivas

#### Notas

- Por ahora, lo único ejecutándose en este servidor es SSH así que querremos que CrowdSec monitoree SSH y banee según sea necesario.
- A medida que instales otros programas, necesitarás instalar colecciones adicionales y configurar las adquisiciones apropiadas.

#### Referencias

- https://www.crowdsec.net/
- [Lee cómo CrowdSec cuida la Lista de Bloqueo Comunitaria](https://www.crowdsec.net/our-data)
- [Lee qué inteligencia de amenazas se comparte con CrowdSec](https://docs.crowdsec.net/docs/next/central_api/intro#signal-meta-data)
- https://docs.crowdsec.net/

#### Pasos

1. Instala CrowdSec Security Engine. (IDS)

    En cualquier distribución Linux (incluyendo sistemas basados en Debian)
    
    Instala el repositorio de CrowdSec:
    ``` bash
    curl -s https://install.crowdsec.net | sudo sh
    ```

    Instala el CrowdSec Security Engine:
    ``` bash
    sudo apt install crowdsec
    ```

> [!TIP]
> si `curl | sh` no es lo tuyo, puedes encontrar métodos de instalación adicionales [aquí](https://docs.crowdsec.net/u/getting_started/installation/linux).

Por defecto mientras CrowdSec está instalando el Security Engine auto-descubrirá tus aplicaciones instaladas e instalará los parsers y escenarios apropiados para ellas. Como sabemos que la mayoría de servidores Linux están ejecutando ssh por defecto CrowdSec configurará esto automáticamente para ti.

2. Instala un Componente de Remediación. (IPS)

    CrowdSec por sí mismo es un motor de detección, como en la mayoría de infraestructuras modernas puedes tener un firewall o WAF upstream, CrowdSec no bloqueará las direcciones IP por sí mismo. Puedes instalar un Componente de Remediación para bloquear las direcciones IP detectadas por CrowdSec.
    ```bash
    sudo apt install crowdsec-firewall-bouncer-iptables
    ```

> [!TIP]
> Si tu instalación de UFW no está usando `iptables` como backend, alternativamente puedes instalar `crowdsec-firewall-bouncer-nftables`. No hay diferencia en los binarios instalados, solo el archivo de configuración es diferente.

Por defecto mientras el Componente de Remediación está instalando auto-configurará los ajustes necesarios para trabajar con el Security Engine si está desplegado en el mismo host (y si el security engine no está dentro de un ambiente de contenedor).

3. Verifica que la detección y remediación están funcionando como se pretende:

    El paquete CrowdSec viene con una herramienta CLI para verificar el estado del Security Engine y el Componente de Remediación.

    ```bash
    sudo cscli metrics
    ```

    ```bash
    Acquisition Metrics:
    ╭────────────────────────┬────────────┬──────────────┬────────────────┬────────────────────────┬───────────────────╮
    │ Source                 │ Lines read │ Lines parsed │ Lines unparsed │ Lines poured to bucket │ Lines whitelisted │
    ├────────────────────────┼────────────┼──────────────┼────────────────┼────────────────────────┼───────────────────┤
    │ file:/var/log/auth.log │ 5          │ 4            │ 1              │ 10                     │ -                 │
    │ file:/var/log/syslog   │ 30         │ -            │ 30             │ -                      │ -                 │
    ╰────────────────────────┴────────────┴──────────────┴────────────────┴────────────────────────┴───────────────────╯

    Local API Decisions:
    ╭────────────────────────────────────────────┬────────┬────────┬───────╮
    │ Reason                                     │ Origin │ Action │ Count │
    ├────────────────────────────────────────────┼────────┼────────┼───────┤
    │ crowdsecurity/http-backdoors-attempts      │ CAPI   │ ban    │ 73    │
    │ crowdsecurity/http-bad-user-agent          │ CAPI   │ ban    │ 4836  │
    │ crowdsecurity/http-path-traversal-probing  │ CAPI   │ ban    │ 87    │
    │ crowdsecurity/http-probing                 │ CAPI   │ ban    │ 2010  │
    │ crowdsecurity/thinkphp-cve-2018-20062      │ CAPI   │ ban    │ 88    │
    │ crowdsecurity/CVE-2019-18935               │ CAPI   │ ban    │ 7     │
    │ crowdsecurity/CVE-2023-49103               │ CAPI   │ ban    │ 5     │
    │ crowdsecurity/http-admin-interface-probing │ CAPI   │ ban    │ 91    │
    │ ltsich/http-w00tw00t                       │ CAPI   │ ban    │ 3     │
    │ crowdsecurity/apache_log4j2_cve-2021-44228 │ CAPI   │ ban    │ 18    │
    │ crowdsecurity/nginx-req-limit-exceeded     │ CAPI   │ ban    │ 280   │
    │ crowdsecurity/ssh-slow-bf                  │ CAPI   │ ban    │ 3412  │
    │ crowdsecurity/spring4shell_cve-2022-22965  │ CAPI   │ ban    │ 1     │
    │ crowdsecurity/ssh-cve-2024-6387            │ CAPI   │ ban    │ 24    │
    │ crowdsecurity/CVE-2023-22515               │ CAPI   │ ban    │ 2     │
    │ crowdsecurity/http-cve-2021-41773          │ CAPI   │ ban    │ 172   │
    │ crowdsecurity/netgear_rce                  │ CAPI   │ ban    │ 14    │
    │ crowdsecurity/ssh-bf                       │ CAPI   │ ban    │ 2000  │
    │ crowdsecurity/CVE-2022-35914               │ CAPI   │ ban    │ 1     │
    │ crowdsecurity/http-cve-2021-42013          │ CAPI   │ ban    │ 2     │
    │ crowdsecurity/jira_cve-2021-26086          │ CAPI   │ ban    │ 9     │
    │ crowdsecurity/http-sensitive-files         │ CAPI   │ ban    │ 166   │
    │ crowdsecurity/http-wordpress-scan          │ CAPI   │ ban    │ 272   │
    │ crowdsecurity/CVE-2022-26134               │ CAPI   │ ban    │ 5     │
    │ crowdsecurity/http-generic-bf              │ CAPI   │ ban    │ 7     │
    │ crowdsecurity/http-open-proxy              │ CAPI   │ ban    │ 948   │
    │ crowdsecurity/http-crawl-non_statics       │ CAPI   │ ban    │ 339   │
    │ crowdsecurity/http-cve-probing             │ CAPI   │ ban    │ 5     │
    │ crowdsecurity/CVE-2017-9841                │ CAPI   │ ban    │ 117   │
    │ crowdsecurity/CVE-2022-37042               │ CAPI   │ ban    │ 1     │
    │ crowdsecurity/fortinet-cve-2018-13379      │ CAPI   │ ban    │ 5     │
    ╰────────────────────────────────────────────┴────────┴────────┴───────╯

    Local API Metrics:
    ╭──────────────────────┬────────┬──────╮
    │ Route                │ Method │ Hits │
    ├──────────────────────┼────────┼──────┤
    │ /v1/alerts           │ GET    │ 2    │
    │ /v1/decisions/stream │ GET    │ 5    │
    │ /v1/usage-metrics    │ POST   │ 2    │
    │ /v1/watchers/login   │ POST   │ 4    │
    ╰──────────────────────┴────────┴──────╯

    Local API Bouncers Metrics:
    ╭────────────────────────────────┬──────────────────────┬────────┬──────╮
    │ Bouncer                        │ Route                │ Method │ Hits │
    ├────────────────────────────────┼──────────────────────┼────────┼──────┤
    │ cs-firewall-bouncer-1729025592 │ /v1/decisions/stream │ GET    │ 5    │
    ╰────────────────────────────────┴──────────────────────┴────────┴──────╯

    Local API Machines Metrics:
    ╭──────────────────────────────────────────────────┬────────────┬────────┬──────╮
    │ Machine                                          │ Route      │ Method │ Hits │
    ├──────────────────────────────────────────────────┼────────────┼────────┼──────┤
    │ <tu_machine_id_estará_aquí>                      │ /v1/alerts │ GET    │ 2    │
    ╰──────────────────────────────────────────────────┴────────────┴────────┴──────╯

    Parser Metrics:
    ╭─────────────────────────────────┬──────┬────────┬──────────╮
    │ Parsers                         │ Hits │ Parsed │ Unparsed │
    ├─────────────────────────────────┼──────┼────────┼──────────┤
    │ child-crowdsecurity/sshd-logs   │ 41   │ 4      │ 37       │
    │ child-crowdsecurity/syslog-logs │ 35   │ 35     │ -        │
    │ crowdsecurity/dateparse-enrich  │ 4    │ 4      │ -        │
    │ crowdsecurity/sshd-logs         │ 5    │ 4      │ 1        │
    │ crowdsecurity/syslog-logs       │ 35   │ 35     │ -        │
    ╰─────────────────────────────────┴──────┴────────┴──────────╯

    Scenario Metrics:
    ╭─────────────────────────────────────┬───────────────┬───────────┬──────────────┬────────┬─────────╮
    │ Scenario                            │ Current Count │ Overflows │ Instantiated │ Poured │ Expired │
    ├─────────────────────────────────────┼───────────────┼───────────┼──────────────┼────────┼─────────┤
    │ crowdsecurity/ssh-bf                │ 1             │ -         │ 1            │ 4      │ -       │
    │ crowdsecurity/ssh-bf_user-enum      │ 1             │ -         │ 1            │ 1      │ -       │
    │ crowdsecurity/ssh-slow-bf           │ 1             │ -         │ 1            │ 4      │ -       │
    │ crowdsecurity/ssh-slow-bf_user-enum │ 1             │ -         │ 1            │ 1      │ -       │
    ╰─────────────────────────────────────┴───────────────┴───────────┴──────────────┴────────┴─────────╯
    ```

La salida anterior puede ser desalentadora, pero es una buena forma de verificar que el Security Engine está leyendo logs y el Componente de Remediación está bloqueando direcciones IP. Así que un desglose rápido de cada sección:

- **Acquisition Metrics**: Esta sección muestra los logs que el Security Engine está leyendo y parseando. Si ves logs en la columna `Lines unparsed`, significa que el Security Engine no puede parsear los logs. Esto podría deberse a una mala configuración o los logs no están en el formato esperado.
- **Local API Decisions**: Esta sección muestra las decisiones que el Security Engine tiene dentro de la base de datos. Si ves logs en la columna `Count`, significa que el Security Engine ha detectado actividad maliciosa y ha bloqueado la dirección IP.
    - Orgin: Esto es de dónde vino la decisión. En este caso, es de la API Central (CAPI).
- **Local API Metrics**: Esta sección muestra el número de hits a la API Local. Esta es la API que el Security Engine usa para comunicarse con el Componente de Remediación.
- **Local API Bouncers Metrics**: Esta sección muestra el número de hits a la API Local por el Componente de Remediación.
- **Local API Machines Metrics**: Esta sección muestra el número de hits a la API Local por el Security Engine (si ejecutas múltiples Security Engine en una configuración centralizada puedes ver múltiples IDs aquí).
- **Parser Metrics**: Esta sección muestra los parsers que están siendo usados por el Security Engine. Si ves logs en la columna `Unparsed`, significa que el Security Engine no puede parsear los logs. Esto podría deberse a una mala configuración o los logs no están en el formato esperado.
- **Scenario Metrics**: Esta sección muestra los escenarios que están siendo usados por el Security Engine. Si ves logs en la columna `Current Count`, significa que el Security Engine ha detectado actividad maliciosa y está rastreando la dirección IP.

#### Desbanear una IP

Para desbanear una IP usa este comando:

``` bash
cscli decisions delete --ip [IP]
```

`[IP]` es la dirección IP que quieres desbanear. Por ejemplo, para desbanear `192.168.1.100` de SSH harías:

``` bash
cscli decisions delete --ip 192.168.1.100
```

([Tabla de Contenidos](#tabla-de-contenidos))

## La Auditoría

### Monitoreo de Integridad de Archivos/Carpetas Con AIDE (WIP)

#### Por Qué

WIP

#### Cómo Funciona

WIP

#### Objetivos

WIP

#### Referencias

- https://aide.github.io/
- https://www.hiroom2.com/2017/06/09/debian-8-file-integrity-check-with-aide/
- https://blog.rapid7.com/2017/06/30/how-to-install-and-configure-aide-on-ubuntu-linux/
- https://www.stephenrlang.com/2016/03/using-aide-for-file-integrity-monitoring-fim-on-ubuntu/
- https://www.howtoforge.com/how-to-configure-the-aide-advanced-intrusion-detection-environment-file-integrity-scanner-for-your-website
- https://www.tecmint.com/check-integrity-of-file-and-directory-using-aide-in-linux/
- https://www.cyberciti.biz/faq/debian-ubuntu-linux-software-integrity-checking-with-aide/
- https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/83

#### Pasos

1. Instala AIDE.

    En sistemas basados en Debian:
    
    ``` bash
    sudo apt install aide aide-common
    ```
    
1. Haz un respaldo del archivo de defaults de AIDE:

    ``` bash
    sudo cp -p /etc/default/aide /etc/default/aide-COPY-$(date +"%Y%m%d%H%M%S")
    ```

1. Revisa `/etc/default/aide` y establece los defaults de AIDE según tus requerimientos. Si quieres que AIDE se ejecute diariamente y te envíe correos, asegúrate de establecer `CRON_DAILY_RUN` a `yes`.

1. Haz un respaldo de los archivos de configuración de AIDE:

    ``` bash
    sudo cp -pr /etc/aide /etc/aide-COPY-$(date +"%Y%m%d%H%M%S")
    ```

1. En sistemas basados en Debian:

    - Los archivos de configuración de AIDE están en `/etc/aide/aide.conf.d/`.
    - Querrás revisar la documentación de AIDE y los archivos de configuración para establecerlos según tus requerimientos.
    - Si quieres nuevas configuraciones, para monitorear una nueva carpeta por ejemplo, querrás agregarlas a `/etc/aide/aide.conf` o `/etc/aide/aide.conf.d/`.
    - Toma un respaldo de los archivos de configuración originales: `sudo cp -pr /etc/aide /etc/aide-COPY-$(date +"%Y%m%d%H%M%S")`.

1. Crea una nueva base de datos, e instálala.
   
    En sistemas basados en Debian:

    ``` bash
    sudo aideinit
    ```
    
    > ```
    > Running aide --init...
    > Start timestamp: 2019-04-01 21:23:37 -0400 (AIDE 0.16)
    > AIDE initialized database at /var/lib/aide/aide.db.new
    > Verbose level: 6
    > 
    > Number of entries:      25973
    > 
    > ---------------------------------------------------
    > The attributes of the (uncompressed) database(s):
    > ---------------------------------------------------
    > 
    > /var/lib/aide/aide.db.new
    >   RMD160   : moyQ1YskQQbidX+Lusv3g2wf1gQ=
    >   TIGER    : 7WoOgCrXzSpDrlO6I3PyXPj1gRiaMSeo
    >   SHA256   : gVx8Fp7r3800WF2aeXl+/KHCzfGsNi7O
    >              g16VTPpIfYQ=
    >   SHA512   : GYfa0DJwWgMLl4Goo5VFVOhu4BphXCo3
    >              rZnk49PYztwu50XjaAvsVuTjJY5uIYrG
    >              tV+jt3ELvwFzGefq4ZBNMg==
    >   CRC32    : /cusZw==
    >   HAVAL    : E/i5ceF3YTjwenBfyxHEsy9Kzu35VTf7
    >              CPGQSW4tl14=
    >   GOST     : n5Ityzxey9/1jIs7LMc08SULF1sLBFUc
    >              aMv7Oby604A=
    > 
    > 
    > End timestamp: 2019-04-01 21:24:45 -0400 (run time: 1m 8s)
    > ```

1. Prueba que todo funciona sin cambios.

    En sistemas basados en Debian:

    ``` bash
    sudo aide.wrapper --check
    ```
    
    > ```
    > Start timestamp: 2019-04-01 21:24:45 -0400 (AIDE 0.16)
    > AIDE found NO differences between database and filesystem. Looks okay!!
    > Verbose level: 6
    > 
    > Number of entries:      25973
    > 
    > ---------------------------------------------------
    > The attributes of the (uncompressed) database(s):
    > ---------------------------------------------------
    > 
    > /var/lib/aide/aide.db
    >   RMD160   : moyQ1YskQQbidX+Lusv3g2wf1gQ=
    >   TIGER    : 7WoOgCrXzSpDrlO6I3PyXPj1gRiaMSeo
    >   SHA256   : gVx8Fp7r3800WF2aeXl+/KHCzfGsNi7O
    >              g16VTPpIfYQ=
    >   SHA512   : GYfa0DJwWgMLl4Goo5VFVOhu4BphXCo3
    >              rZnk49PYztwu50XjaAvsVuTjJY5uIYrG
    >              tV+jt3ELvwFzGefq4ZBNMg==
    >   CRC32    : /cusZw==
    >   HAVAL    : E/i5ceF3YTjwenBfyxHEsy9Kzu35VTf7
    >              CPGQSW4tl14=
    >   GOST     : n5Ityzxey9/1jIs7LMc08SULF1sLBFUc
    >              aMv7Oby604A=
    > 
    > 
    > End timestamp: 2019-04-01 21:26:03 -0400 (run time: 1m 18s)
    > ```

1. Prueba que todo funciona después de hacer algunos cambios.

    En sistemas basados en Debian:

    ``` bash
    sudo touch /etc/test.sh
    sudo touch /root/test.sh
    
    sudo aide.wrapper --check
    
    sudo rm /etc/test.sh
    sudo rm /root/test.sh
    
    sudo aideinit -y -f
    ```
    
    > ```
    > Start timestamp: 2019-04-01 21:37:37 -0400 (AIDE 0.16)
    > AIDE found differences between database and filesystem!!
    > Verbose level: 6
    > 
    > Summary:
    >   Total number of entries:      25972
    >   Added entries:                2
    >   Removed entries:              0
    >   Changed entries:              1
    > 
    > ---------------------------------------------------
    > Added entries:
    > ---------------------------------------------------
    > 
    > f++++++++++++++++: /etc/test.sh
    > f++++++++++++++++: /root/test.sh
    > 
    > ---------------------------------------------------
    > Changed entries:
    > ---------------------------------------------------
    > 
    > d =.... mc.. .. .: /root
    > 
    > ---------------------------------------------------
    > Detailed information about changes:
    > ---------------------------------------------------
    > 
    > Directory: /root
    >   Mtime    : 2019-04-01 21:35:07 -0400        | 2019-04-01 21:37:36 -0400
    >   Ctime    : 2019-04-01 21:35:07 -0400        | 2019-04-01 21:37:36 -0400
    > 
    > 
    > ---------------------------------------------------
    > The attributes of the (uncompressed) database(s):
    > ---------------------------------------------------
    > 
    > /var/lib/aide/aide.db
    >   RMD160   : qF9WmKaf2PptjKnhcr9z4ueCPTY=
    >   TIGER    : zMo7MvvYJcq1hzvTQLPMW7ALeFiyEqv+
    >   SHA256   : LSLLVjjV6r8vlSxlbAbbEsPcQUB48SgP
    >              pdVqEn6ZNbQ=
    >   SHA512   : Qc4U7+ZAWCcitapGhJ1IrXCLGCf1IKZl
    >              02KYL1gaZ0Fm4dc7xLqjiquWDMSEbwzW
    >              oz49NCquqGz5jpMIUy7UxA==
    >   CRC32    : z8ChEA==
    >   HAVAL    : YapzS+/cdDwLj3kHJEq8fufLp3DPKZDg
    >              U12KCSkrO7Y=
    >   GOST     : 74sLV4HkTig+GJhokvxZQm7CJD/NR0mG
    >              6jV7zdt5AXQ=
    > 
    > 
    > End timestamp: 2019-04-01 21:38:50 -0400 (run time: 1m 13s)
    > ```
    
1. Eso es todo. Si estableciste `CRON_DAILY_RUN` a `yes` en `/etc/default/aide` entonces cron ejecutará `/etc/cron.daily/aide` cada día y te enviará por correo la salida.

#### Actualizando La Base de Datos

Cada vez que hagas cambios a archivos/carpetas que AIDE monitorea, necesitarás actualizar la base de datos para capturar esos cambios. Para hacer eso en sistemas basados en Debian:

``` bash
sudo aideinit -y -f
```

([Tabla de Contenidos](#tabla-de-contenidos))

### Escaneo Anti-Virus Con ClamAV (WIP)

#### Por Qué

WIP

#### Cómo Funciona

- ClamAV es un escáner de virus
- ClamAV-Freshclam es un servicio que mantiene las definiciones de virus actualizadas
- ClamAV-Daemon mantiene el proceso `clamd` ejecutándose para hacer el escaneo más rápido

#### Objetivos

WIP

#### Notas

- Estas instrucciones **no** te dicen cómo habilitar el servicio daemon de ClamAV para asegurar que `clamd` esté ejecutándose todo el tiempo. `clamd` es solo si estás ejecutando un servidor de correo y no proporciona monitoreo en tiempo real de archivos. En su lugar, querrás escanear archivos manualmente o en un horario.

#### Referencias

- https://www.clamav.net/documents/installation-on-debian-and-ubuntu-linux-distributions
- https://wiki.debian.org/ClamAV
- https://www.osradar.com/install-clamav-debian-9-ubuntu-18/
- https://www.lisenet.com/2014/automate-clamav-to-perform-daily-system-scan-and-send-email-notifications-on-linux/
- https://www.howtoforge.com/tutorial/configure-clamav-to-scan-and-notify-virus-and-malware/
- https://serverfault.com/questions/741299/is-there-a-way-to-keep-clamav-updated-on-debian-8
- https://askubuntu.com/questions/250290/how-do-i-scan-for-viruses-with-clamav
- https://ngothang.com/how-to-install-clamav-and-configure-daily-scanning-on-centos/

#### Pasos

1. Instala ClamAV.

    En sistemas basados en Debian:

    ``` bash
    sudo apt install clamav clamav-freshclam clamav-daemon
    ```

1. Haz un respaldo del archivo de configuración de `clamav-freshclam` `/etc/clamav/freshclam.conf`:

    ``` bash
    sudo cp --archive /etc/clamav/freshclam.conf /etc/clamav/freshclam.conf-COPY-$(date +"%Y%m%d%H%M%S")
    ```
    
1. Las configuraciones predeterminadas de `clamav-freshclam` probablemente son suficientemente buenas pero si quieres cambiarlas, puedes editar el archivo `/etc/clamav/freshclam.conf` o usar `dpkg-reconfigure`:

    ``` bash
    sudo dpkg-reconfigure clamav-freshclam
    ```
    
    **Nota**: Las configuraciones predeterminadas actualizarán las definiciones 24 veces en un día. Para cambiar el intervalo, revisa la configuración `Checks` en `/etc/clamav/freshclam.conf` o usa `dpkg-reconfigure`.

1. Inicia el servicio `clamav-freshclam`:

    ``` bash
    sudo service clamav-freshclam start
    ```
    
1. Puedes asegurarte de que `clamav-freshclam` esté ejecutándose:

    ``` bash
    sudo service clamav-freshclam status
    ```
    
    > ```
    > ● clamav-freshclam.service - ClamAV virus database updater
    >    Loaded: loaded (/lib/systemd/system/clamav-freshclam.service; enabled; vendor preset: enabled)   Active: active (running) since Sat 2019-03-16 22:57:07 EDT; 2min 13s ago
    >      Docs: man:freshclam(1)
    >            man:freshclam.conf(5)
    >            https://www.clamav.net/documents
    >  Main PID: 1288 (freshclam)
    >    CGroup: /system.slice/clamav-freshclam.service
    >            └─1288 /usr/bin/freshclam -d --foreground=true
    > 
    > Mar 16 22:57:08 host freshclam[1288]: Sat Mar 16 22:57:08 2019 -> ^Local version: 0.100.2 Recommended version: 0.101.1
    > Mar 16 22:57:08 host freshclam[1288]: Sat Mar 16 22:57:08 2019 -> DON'T PANIC! Read https://www.clamav.net/documents/upgrading-clamav
    > Mar 16 22:57:15 host freshclam[1288]: Sat Mar 16 22:57:15 2019 -> Downloading main.cvd [100%]
    > Mar 16 22:57:38 host freshclam[1288]: Sat Mar 16 22:57:38 2019 -> main.cvd updated (version: 58, sigs: 4566249, f-level: 60, builder: sigmgr)
    > Mar 16 22:57:40 host freshclam[1288]: Sat Mar 16 22:57:40 2019 -> Downloading daily.cvd [100%]
    > Mar 16 22:58:13 host freshclam[1288]: Sat Mar 16 22:58:13 2019 -> daily.cvd updated (version: 25390, sigs: 1520006, f-level: 63, builder: raynman)
    > Mar 16 22:58:14 host freshclam[1288]: Sat Mar 16 22:58:14 2019 -> Downloading bytecode.cvd [100%]
    > Mar 16 22:58:16 host freshclam[1288]: Sat Mar 16 22:58:16 2019 -> bytecode.cvd updated (version: 328, sigs: 94, f-level: 63, builder: neo)
    > Mar 16 22:58:24 host freshclam[1288]: Sat Mar 16 22:58:24 2019 -> Database updated (6086349 signatures) from db.local.clamav.net (IP: 104.16.219.84)
    > Mar 16 22:58:24 host freshclam[1288]: Sat Mar 16 22:58:24 2019 -> ^Clamd was NOT notified: Can't connect to clamd through /var/run/clamav/clamd.ctl: No such file or directory
    > ```
    
    **Nota**: No te preocupes por esa línea `Local version`. Revisa https://serverfault.com/questions/741299/is-there-a-way-to-keep-clamav-updated-on-debian-8 para más detalles.

1. Haz un respaldo del archivo de configuración de `clamav-daemon` `/etc/clamav/clamd.conf`:

    ``` bash
    sudo cp --archive /etc/clamav/clamd.conf /etc/clamav/clamd.conf-COPY-$(date +"%Y%m%d%H%M%S")
    ```
    
1. Puedes cambiar las configuraciones de `clamav-daemon` editando el archivo `/etc/clamav/clamd.conf` o usando `dpkg-reconfigure`:

    ``` bash
    sudo dpkg-reconfigure clamav-daemon
    ```

#### Escaneando Archivos/Carpetas

- Para escanear archivos/carpetas usa el programa `clamscan`.
- `clamscan` se ejecuta como el usuario con el que se ejecuta así que necesita permisos de lectura a los archivos/carpetas que está escaneando.
- Usar `clamscan` como `root` es peligroso porque si un archivo es de hecho un virus existe riesgo de que pueda usar los privilegios root.
- Para escanear un archivo: `clamscan /path/to/file`.
- Para escanear un directorio: `clamscan -r /path/to/folder`.
- Puedes usar el switch `-i` para solo imprimir archivos infectados.
- Revisa las páginas `man` de `clamscan` para otros switches/opciones.

([Tabla de Contenidos](#tabla-de-contenidos))

### Detección de Rootkit Con Rkhunter (WIP)

#### Por Qué

WIP

#### Cómo Funciona

WIP

#### Objetivos

WIP

#### Referencias

- http://rkhunter.sourceforge.net/
- https://www.cyberciti.biz/faq/howto-check-linux-rootkist-with-detectors-software/
- https://www.tecmint.com/install-rootkit-hunter-scan-for-rootkits-backdoors-in-linux/

#### Pasos

1. Instala Rkhunter.

    En sistemas basados en Debian:
    
    ``` bash
    sudo apt install rkhunter
    ```

1. Haz un respaldo del archivo de defaults de rkhunter:

    ``` bash
    sudo cp -p /etc/default/rkhunter /etc/default/rkhunter-COPY-$(date +"%Y%m%d%H%M%S")
    ```

1. El archivo de configuración de rkhunter es `/etc/rkhunter.conf`. En lugar de hacer cambios a él, crea y usa el archivo `/etc/rkhunter.conf.local` en su lugar:

    ``` bash
    sudo cp -p /etc/rkhunter.conf /etc/rkhunter.conf.local
    ```
    
1. Revisa el archivo de configuración `/etc/rkhunter.conf.local` y establece según tus requerimientos. Mis recomendaciones:

    |Configuración|Nota|
    |--|--|
    |`UPDATE_MIRRORS=1`||
    |`MIRRORS_MODE=0`||
    |`MAIL-ON-WARNING=root`||
    |`COPY_LOG_ON_ERROR=1`|para guardar una copia del log si hay un error|
    |`PKGMGR=...`|establecer al valor apropiado según la documentación|
    |`PHALANX2_DIRTEST=1`|lee la documentación para saber por qué|
    |`WEB_CMD=""`|esto es para abordar un problema con el paquete Debian que deshabilita la capacidad de rkhunter de auto-actualizarse.|
    |`USE_LOCKING=1`|para prevenir problemas con rkhunter ejecutándose múltiples veces|
    |`SHOW_SUMMARY_WARNINGS_NUMBER=1`|para ver el número actual de advertencias encontradas|

1. Quieres que rkhunter se ejecute cada día y te envíe el resultado por correo. Puedes escribir tu propio script o revisar https://www.tecmint.com/install-rootkit-hunter-scan-for-rootkits-backdoors-in-linux/ para un script de ejemplo de cron que puedes usar.
   
    En sistema basado en Debian, rkhunter viene con scripts de cron. Para habilitarlos revisa `/etc/default/rkhunter` o usa `dpkg-reconfigure` y di `Yes` a todas las preguntas:
    
    ``` bash
    sudo dpkg-reconfigure rkhunter
    ```

1. Después de que hayas terminado con todos los cambios, asegúrate de que todas las configuraciones sean válidas:

    ``` bash
    sudo rkhunter -C
    ```

1. Actualiza rkhunter y su base de datos:

    ``` bash
    sudo rkhunter --versioncheck
    sudo rkhunter --update
    sudo rkhunter --propupd
    ```

1. Si quieres hacer un escaneo manual y ver la salida:

    ``` bash
    sudo rkhunter --check
    ```

([Tabla de Contenidos](#tabla-de-contenidos))

### Detección de Rootkit Con chrootkit (WIP)

#### Por Qué

WIP

#### Cómo Funciona

WIP

#### Objetivos

WIP

#### Referencias

- http://www.chkrootkit.org/
- https://www.cyberciti.biz/faq/howto-check-linux-rootkist-with-detectors-software/
- https://askubuntu.com/questions/258658/eth0-packet-sniffer-sbin-dhclient

#### Pasos

1. Instala chkrootkit.

    En sistemas basados en Debian:
    
    ``` bash
    sudo apt install chkrootkit
    ```

1. Haz un escaneo manual:

    ``` bash
    sudo chkrootkit
    ```
    
    > ```
    > ROOTDIR is `/'
    > Checking `amd'...                                           not found
    > Checking `basename'...                                      not infected
    > Checking `biff'...                                          not found
    > Checking `chfn'...                                          not infected
    > Checking `chsh'...                                          not infected
    > ...
    > Checking `scalper'...                                       not infected
    > Checking `slapper'...                                       not infected
    > Checking `z2'...                                            chklastlog: nothing deleted
    > Checking `chkutmp'...                                       chkutmp: nothing deleted
    > Checking `OSX_RSPLUG'...                                    not infected
    > ```

1. Haz un respaldo del archivo de configuración de chkrootkit `/etc/chkrootkit.conf`:

    ``` bash
    sudo cp --archive /etc/chkrootkit.conf /etc/chkrootkit.conf-COPY-$(date +"%Y%m%d%H%M%S")
    ```

1. Quieres que chkrootkit se ejecute cada día y te envíe el resultado por correo.
   
    En sistema basado en Debian, chkrootkit viene con scripts de cron. Para habilitarlos revisa `/etc/chkrootkit.conf` o usa `dpkg-reconfigure` y di `Yes` a la primera pregunta:
    
    ``` bash
    sudo dpkg-reconfigure chkrootkit
    ```

([Tabla de Contenidos](#tabla-de-contenidos))

### logwatch - analizador y reportero de logs del sistema

#### Por Qué

Tu servidor estará generando muchos logs que pueden contener información importante. A menos que planees revisar tu servidor cada día, querrás una forma de obtener un resumen por correo electrónico de los logs de tu servidor. Para lograr esto usaremos [logwatch](https://sourceforge.net/projects/logwatch/).

#### Cómo Funciona

logwatch escanea archivos de log del sistema y los resume. Puedes ejecutarlo directamente desde la línea de comandos o programarlo para ejecutarse en un horario recurrente. logwatch usa archivos de servicio para saber cómo leer/resumir un archivo de log. Puedes ver todos los archivos de servicio originales en `/usr/share/logwatch/scripts/services`.

El archivo de configuración de logwatch `/usr/share/logwatch/default.conf/logwatch.conf` especifica opciones predeterminadas. Puedes sobrescribirlas vía argumentos de línea de comandos.

#### Objetivos

- Logwatch configurado para enviar un resumen diario por correo electrónico de todo el estado y logs del servidor

#### Notas

- Tu servidor necesitará poder enviar correos electrónicos para que esto funcione
- Los pasos a continuación resultarán en logwatch ejecutándose cada día. Si quieres cambiar el horario, modifica el cronjob a tu gusto. También querrás cambiar la opción `range` para cubrir tu ventana de recurrencia. Ve https://www.badpenguin.org/configure-logwatch-for-weekly-email-and-html-output-format para un ejemplo.
- Si logwatch falla al entregar correo debido a que el correo electrónico tiene líneas largas por favor revisa https://blog.dhampir.no/content/exim4-line-length-in-debian-stretch-mail-delivery-failed-returning-message-to-sender según documentado en [issue #29](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/29). Si seguiste [Gmail y Exim4 Como MTA Con TLS Implícito](#gmail-y-exim4-como-mta-con-tls-implícito) entonces ya nos encargamos de esto en el paso #7.

#### Referencias

- Gracias a [amacheema](https://github.com/amacheema) por arreglar algunos problemas con los pasos y hacerme saber de un bug de línea larga con exim4 según documentado en [issue #29](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/29).
- https://sourceforge.net/projects/logwatch/
- https://www.digitalocean.com/community/tutorials/how-to-install-and-use-logwatch-log-analyzer-and-reporter-on-a-vps

#### Pasos

1. Instala logwatch.

    En sistemas basados en Debian:

    ``` bash
    sudo apt install logwatch
    ```

1. Para ver una muestra de lo que logwatch recopila puedes ejecutarlo directamente:

    ``` bash
    sudo /usr/sbin/logwatch --output stdout --format text --range yesterday --service all
    ```

    > ```
    > 
    >  ################### Logwatch 7.4.3 (12/07/16) ####################
    >         Processing Initiated: Mon Mar  4 00:05:50 2019
    >         Date Range Processed: yesterday
    >                               ( 2019-Mar-03 )
    >                               Period is day.
    >         Detail Level of Output: 5
    >         Type of Output/Format: stdout / text
    >         Logfiles for Host: host
    >  ##################################################################
    > 
    >  --------------------- Cron Begin ------------------------
    > ...
    > ...
    >  ---------------------- Disk Space End -------------------------
    > 
    > 
    >  ###################### Logwatch End #########################
    > ```

1. Revisa el archivo de configuración auto-documentado de logwatch `/usr/share/logwatch/default.conf/logwatch.conf` antes de continuar. No hay necesidad de cambiar nada aquí pero presta especial atención al `Output`, `Format`, `MailTo`, `Range`, y `Service` ya que esos son los que usaremos. Para nuestros propósitos, en lugar de especificar nuestras opciones en el archivo de configuración, las pasaremos como argumentos de línea de comandos en el cronjob diario que ejecuta logwatch. De esa manera, si el archivo de configuración es modificado alguna vez (ej. durante una actualización), nuestras opciones seguirán ahí.

1. Haz un respaldo del archivo de cron diario de logwatch `/etc/cron.daily/00logwatch` y quita el bit de ejecución:

    ``` bash
    sudo cp --archive /etc/cron.daily/00logwatch /etc/cron.daily/00logwatch-COPY-$(date +"%Y%m%d%H%M%S")
    sudo chmod -x /etc/cron.daily/00logwatch-COPY*
    ```

1. Por defecto, logwatch envía salida a `stdout`. Como el objetivo es obtener un correo electrónico diario, necesitamos cambiar el tipo de salida que logwatch usa para enviar correo electrónico en su lugar. Podríamos hacer esto a través del archivo de configuración arriba, pero eso se aplicaría cada vez que se ejecute -- incluso cuando lo ejecutamos manualmente y queremos ver la salida en la pantalla. En su lugar, cambiaremos el cronjob que ejecuta logwatch para enviar correo electrónico. De esta manera, cuando se ejecute manualmente, seguiremos obteniendo salida a `stdout` y cuando se ejecute por cron, enviará un correo electrónico. También nos aseguraremos de que verifique todos los servicios, y cambiaremos el formato de salida a html para que sea más fácil de leer sin importar lo que diga el archivo de configuración. En el archivo `/etc/cron.daily/00logwatch` encuentra la línea de ejecución y cámbiala a:

    ```
    /usr/sbin/logwatch --output mail --format html --mailto root --range yesterday --service all
    ```

    > ```
    > #!/bin/bash
    > 
    > #Check if removed-but-not-purged
    > test -x /usr/share/logwatch/scripts/logwatch.pl || exit 0
    > 
    > #execute
    > /usr/sbin/logwatch --output mail --format html --mailto root --range yesterday --service all
    > 
    > #Note: It's possible to force the recipient in above command
    > #Just pass --mailto address@a.com instead of --output mail
    > ```

    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):
    
    ``` bash
    sudo sed -i -r -e "s,^($(sudo which logwatch).*?),# \1         # commented by $(whoami) on $(date +"%Y-%m-%d @ %H:%M:%S")\n$(sudo which logwatch) --output mail --format html --mailto root --range yesterday --service all         # added by $(whoami) on $(date +"%Y-%m-%d @ %H:%M:%S")," /etc/cron.daily/00logwatch
    ```

1. Puedes probar el cronjob ejecutándolo:

    ``` bash
    sudo /etc/cron.daily/00logwatch
    ```
    
    **Nota**: Si logwatch falla al entregar correo debido a que el correo electrónico tiene líneas largas por favor revisa https://blog.dhampir.no/content/exim4-line-length-in-debian-stretch-mail-delivery-failed-returning-message-to-sender según documentado en [issue #29](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/29). Si seguiste [Gmail y Exim4 Como MTA Con TLS Implícito](#gmail-y-exim4-como-mta-con-tls-implícito) entonces ya nos encargamos de esto en el paso #7.

([Tabla de Contenidos](#tabla-de-contenidos))

### ss - Viendo Ports Que Tu Servidor Está Escuchando

#### Por Qué

Los ports son cómo aplicaciones, servicios, y procesos se comunican entre sí -- ya sea localmente dentro de tu servidor o con otros dispositivos en la red. Cuando tienes una aplicación o servicio (como SSH o Apache) ejecutándose en tu servidor, escuchan solicitudes en ports específicos.

Obviamente no queremos que tu servidor esté escuchando en ports de los que no sabemos. Usaremos `ss` para ver todos los ports en los que los servicios están escuchando. Esto nos ayudará a rastrear y detener servicios deshonestos, potencialmente peligrosos.

#### Objetivos

- descubrir qué ports que no son localhost están abiertos y escuchando conexiones

#### Referencias

- https://www.reddit.com/r/linux/comments/arx7st/howtosecurealinuxserver_an_evolving_howto_guide/egrib6o/
- https://www.reddit.com/r/linux/comments/arx7st/howtosecurealinuxserver_an_evolving_howto_guide/egs1rev/
- https://www.tecmint.com/find-open-ports-in-linux/
- `man ss`

#### Pasos

1. Para ver todos los ports escuchando tráfico:

    ``` bash
    sudo ss -lntup
    ```
    
    > ```
    > Netid  State      Recv-Q Send-Q     Local Address:Port     Peer Address:Port
    > udp    UNCONN     0      0                      *:68                  *:*        users:(("dhclient",pid=389,fd=6))
    > tcp    LISTEN     0      128                    *:22                  *:*        users:(("sshd",pid=4390,fd=3))
    > tcp    LISTEN     0      128                   :::22                 :::*        users:(("sshd",pid=4390,fd=4))
    > ```
    
    **Explicación de Switches**:
    - `l` = mostrar sockets escuchando
    - `n` = no intentar resolver nombres de servicio
    - `t` = mostrar sockets TCP
    - `u` = mostrar sockets UDP
    - `p` = mostrar información de proceso

1. Si ves algo sospechoso, como un port del que no estás consciente o un proceso que no conoces, investiga y remedia según sea necesario.

([Tabla de Contenidos](#tabla-de-contenidos))

### Lynis - Auditoría de Seguridad Linux

#### Por Qué

De [https://cisofy.com/lynis/](https://cisofy.com/lynis/):

> Lynis es una herramienta de seguridad probada en batalla para sistemas ejecutando Linux, macOS, o sistema operativo basado en Unix. Realiza un escaneo extensivo de salud de tus sistemas para soportar hardening del sistema y pruebas de cumplimiento.

#### Objetivos

- Lynis instalado

#### Notas

- CISOFY ofrece paquetes para muchas distribuciones. Revisa https://packages.cisofy.com/ para instrucciones de instalación específicas de tu distribución.

#### Referencias

- https://cisofy.com/documentation/lynis/get-started/
- https://packages.cisofy.com/community/#debian-ubuntu
- https://thelinuxcode.com/audit-lynis-ubuntu-server/
- https://www.vultr.com/docs/install-lynis-on-debian-8

#### Pasos

1. Instala lynis. https://cisofy.com/lynis/#installation tiene instrucciones detalladas sobre cómo instalarlo para tu distribución.

    En sistemas basados en Debian, usando el repositorio de software comunitario de CISOFY:

    ``` bash
    sudo apt install apt-transport-https ca-certificates host
    sudo wget -O - https://packages.cisofy.com/keys/cisofy-software-public.key | sudo apt-key add -
    sudo echo "deb https://packages.cisofy.com/community/lynis/deb/ stable main" | sudo tee /etc/apt/sources.list.d/cisofy-lynis.list
    sudo apt update
    sudo apt install lynis host
    ```

1. Actualízalo:

    ``` bash
    sudo lynis update info
    ```

1. Ejecuta una auditoría de seguridad:

    ``` bash
    sudo lynis audit system
    ```

    Esto escaneará tu servidor, reportará sus hallazgos de auditoría, y al final te dará sugerencias. Pasa algo de tiempo revisando la salida y aborda las brechas según sea necesario.

([Tabla de Contenidos](#tabla-de-contenidos))

### OSSEC - Detección de Intrusión en Host

#### Por Qué
De [https://github.com/ossec/ossec-hids](https://github.com/ossec/ossec-hids)
> OSSEC es una plataforma completa para monitorear y controlar tus sistemas. Mezcla juntos todos los aspectos de HIDS (detección de intrusión basada en host), monitoreo de logs y SIM/SIEM juntos en una solución simple, poderosa y de código abierto.

#### Objetivos

- OSSEC-HIDS instalado

#### Referencias

- https://www.ossec.net/docs/

#### Pasos

1. Instala OSSEC-HIDS desde fuentes
    ```bash
    sudo apt install -y libz-dev libssl-dev libpcre2-dev build-essential libsystemd-dev
    wget https://github.com/ossec/ossec-hids/archive/3.7.0.tar.gz
    tar xzf 3.7.0.tar.gz
    cd ossec-hids-3.7.0/
    sudo ./install.sh
    ```

1. Comandos útiles:

**Información del agente**

   ```bash
    sudo /var/ossec/bin/agent_control -i <AGENT_ID>
   ```
`AGENT_ID` por defecto es `000`, para estar seguro se puede usar el comando `sudo /var/ossec/bin/agent_control -l`.

**Ejecutar verificación de integridad/rootkit**

OSSEC por defecto ejecuta verificación de rootkit cada 2 horas.

   ```bash
    sudo /var/ossec/bin/agent_control -u <AGENT_ID> -r 
   ```

**Alertas**

- Todas:
    ```bash
    tail -f /var/ossec/logs/alerts/alerts.log
    ```
- Verificación de integridad:
    ```bash
    sudo cat /var/ossec/logs/alerts/alerts.log | grep -A4  -i integrity
    ```
- Verificación de rootkit:
    ```bash
     sudo cat /var/ossec/logs/alerts/alerts.log | grep -A4  "rootcheck,"
    ```

([Tabla de Contenidos](#tabla-de-contenidos))

## La Zona de Peligro

### Procede Bajo Tu Propio Riesgo

Estas secciones cubren cosas que son de alto riesgo porque hay posibilidad de que puedan hacer tu sistema inutilizable, o son consideradas innecesarias por muchos porque los riesgos superan cualquier recompensa.

**!! PROCEDE BAJO TU PROPIO RIESGO !!**

<details><summary>!! PROCEDE BAJO TU PROPIO RIESGO !!</summary>

([Tabla de Contenidos](#tabla-de-contenidos))

### Tabla de Contenidos

- [Hardening del Kernel Linux con sysctl](#hardening-del-kernel-linux-con-sysctl)
- [Proteger GRUB Con Contraseña](#proteger-grub-con-contraseña)
- [Deshabilitar Login de Root](#deshabilitar-login-de-root)
- [Cambiar umask Predeterminado](#cambiar-umask-predeterminado)
- [Software Huérfano](#software-huérfano)

([Tabla de Contenidos](#tabla-de-contenidos))

### Hardening del Kernel Linux con sysctl

<details><summary>!! PROCEDE BAJO TU PROPIO RIESGO !!</summary>

#### Por Qué

El kernel es el cerebro de un sistema Linux. Asegurarlo simplemente tiene sentido.

#### Por Qué No

Cambiar configuraciones del kernel con sysctl es riesgoso y podría romper tu servidor. Si no sabes lo que estás haciendo, no tienes el tiempo para depurar problemas, o simplemente no quieres tomar los riesgos, aconsejaría no seguir estos pasos.

#### Advertencia

No soy tan conocedor sobre hardening/asegurar un kernel Linux como me gustaría. Por mucho que odie admitirlo, no sé lo que todas estas configuraciones hacen. Mi entendimiento es que la mayoría de ellas son hardening general del kernel y rendimiento, y las otras son para proteger contra spoofing y ataques DOS.

De hecho, como no estoy 100% seguro exactamente de lo que cada configuración hace, tomé configuraciones recomendadas de numerosos sitios (todos enlazados en las referencias abajo) y las combiné para descubrir qué debería establecerse. Considero que si múltiples sitios respetables mencionan la misma configuración, probablemente sea segura.

Si tienes un mejor entendimiento de lo que estas configuraciones hacen, o tienes cualquier otro feedback/consejo sobre ellas, por favor [házmelo saber](#contactándome).

No proporcionaré código [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos) en esta sección.

#### Notas

- La documentación sobre todas las configuraciones/claves sysctl es severamente deficiente. La [documentación que puedo encontrar](https://github.com/torvalds/linux/tree/master/Documentation) parece referenciar la versión 2.2 del kernel. No pude encontrar nada más reciente. Si sabes dónde puedo, por favor [házmelo saber](#contactándome).
- Los sitios de referencia listados abajo tienen más comentarios sobre qué hace cada configuración.

#### Referencias

- https://github.com/torvalds/linux/tree/master/Documentation
- https://www.cyberciti.biz/faq/linux-kernel-etcsysctl-conf-security-hardening/
- https://geektnt.com/sysctl-conf-hardening.html
- https://linoxide.com/how-tos/linux-server-protection/
- https://github.com/klaver/sysctl/blob/master/sysctl.conf
- https://cloudpro.zone/index.php/2018/01/30/debian-9-3-server-setup-guide-part-5/

#### Pasos

1. Las configuraciones sysctl se pueden encontrar en el archivo [linux-kernel-sysctl-hardening.md](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/blob/master/linux-kernel-sysctl-hardening.md) en este repositorio.

1. Antes de hacer un cambio sysctl del kernel permanente, puedes probarlo con el comando sysctl:

    ``` bash
    sudo sysctl -w [clave=valor]
    ```

    Ejemplo:

    ``` bash
    sudo sysctl -w kernel.ctrl-alt-del=0
    ```

    **Nota**: No hay espacios en `clave=valor`, incluyendo antes y después del espacio.

1. Una vez que hayas probado una configuración, y te hayas asegurado de que funciona sin romper tu servidor, puedes hacerla permanente agregando los valores a `/etc/sysctl.conf`. Por ejemplo:

    ``` bash
    $ sudo cat /etc/sysctl.conf
    kernel.ctrl-alt-del = 0
    fs.file-max = 65535
    ...
    kernel.sysrq = 0
    ```

1. Después de actualizar el archivo puedes recargar las configuraciones o reiniciar. Para recargar:

    ``` bash
    sudo sysctl -p
    ```

**Nota**: Si sysctl tiene problemas escribiendo cualquier configuración entonces `sysctl -w` o `sysctl -p` escribirá un error a stderr. Puedes usar esto para encontrar rápidamente configuraciones inválidas en tu archivo `/etc/sysctl.conf`:

``` bash
sudo sysctl -p >/dev/null
```

</details><br />

([Tabla de Contenidos](#tabla-de-contenidos))

### Proteger GRUB Con Contraseña

<details><summary>!! PROCEDE BAJO TU PROPIO RIESGO !!</summary>

#### Por Qué

Si un actor malicioso tiene acceso físico a tu servidor, podría usar GRUB para obtener acceso no autorizado a tu sistema.

#### Por Qué No

Si olvidas la contraseña, tendrás que pasar por [algo de trabajo](https://www.cyberciti.biz/tips/howto-recovering-grub-boot-loader-password.html) para recuperar la contraseña.

#### Objetivos

- arrancar automáticamente la instalación predeterminada de Debian y requerir una contraseña para cualquier otra cosa

#### Notas

- Esto solo protegerá GRUB y cualquier cosa detrás de él como tus sistemas operativos. Revisa la documentación de tu tarjeta madre para proteger con contraseña tu BIOS para prevenir que un actor malicioso evite GRUB.

#### Referencias

- https://selivan.github.io/2017/12/21/grub2-password-for-all-but-default-menu-entries.html
- https://help.ubuntu.com/community/Grub2/Passwords
- https://computingforgeeks.com/how-to-protect-grub-with-password-on-debian-ubuntu-and-kali-linux/
- `man grub`
- `man grub-mkpasswd-pbkdf2`

#### Pasos

1. Crea un hash [Password-Based Key Derivation Function 2 (PBKDF2)](https://en.wikipedia.org/wiki/PBKDF2) de tu contraseña:

    ``` bash
    grub-mkpasswd-pbkdf2 -c 100000
    ```

   La salida a continuación es de usar `password` como contraseña:

    > ```
    > Enter password:
    > Reenter password:
    > PBKDF2 hash of your password is grub.pbkdf2.sha512.100000.2812C233DFC899EFC3D5991D8CA74068C99D6D786A54F603E9A1EFE7BAEDDB6AA89672F92589FAF98DB9364143E7A1156C9936328971A02A483A84C3D028C4FF.C255442F9C98E1F3C500C373FE195DCF16C56EEBDC55ABDD332DD36A92865FA8FC4C90433757D743776AB186BD3AE5580F63EF445472CC1D151FA03906D08A6D
    > ```

1. Copia todo **después** de `PBKDF2 hash of your password is `, **empezando desde e incluyendo** `grub.pbkdf2.sha512...` hasta el final. Necesitarás esto en el siguiente paso.

1. El programa `update-grub` usa scripts para generar archivos de configuración que usará para las configuraciones de GRUB. Crea el archivo `/etc/grub.d/01_password` y agrega el código a continuación después de reemplazar `[hash]` con el hash que copiaste del primer paso. Esto le dice a `update-grub` que use este nombre de usuario y contraseña para GRUB.

    ``` bash
    #!/bin/sh
    set -e

    cat << EOF
    set superusers="grub"
    password_pbkdf2 grub [hash]
    EOF
    ```

    Por ejemplo:

    > ``` bash
    > #!/bin/sh
    > set -e
    > 
    > cat << EOF
    > set superusers="grub"
    > password_pbkdf2 grub grub.pbkdf2.sha512.100000.2812C233DFC899EFC3D5991D8CA74068C99D6D786A54F603E9A1EFE7BAEDDB6AA89672F92589FAF98DB9364143E7A1156C9936328971A02A483A84C3D028C4FF.C255442F9C98E1F3C500C373FE195DCF16C56EEBDC55ABDD332DD36A92865FA8FC4C90433757D743776AB186BD3AE5580F63EF445472CC1D151FA03906D08A6D
    > EOF
    > ```

1. Establece el bit de ejecución del archivo para que `update-grub` lo incluya cuando actualice la configuración de GRUB:

   ``` bash
   sudo chmod a+x /etc/grub.d/01_password
   ```

1. Haz un respaldo del archivo de configuración de GRUB `/etc/grub.d/10_linux` que modificaremos y quita el bit de ejecución para que `update-grub` no intente ejecutarlo:

    ``` bash
    sudo cp --archive /etc/grub.d/10_linux /etc/grub.d/10_linux-COPY-$(date +"%Y%m%d%H%M%S")
    sudo chmod a-x /etc/grub.d/10_linux.*
    ```

1. Para hacer que la instalación predeterminada de Debian no esté restringida (**sin** la contraseña) mientras mantienes todo lo demás restringido (**con** la contraseña) modifica `/etc/grub.d/10_linux` y agrega `--unrestricted` a la variable `CLASS`.

    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):

    ``` bash
    sudo sed -i -r -e "/^CLASS=/ a CLASS=\"\${CLASS} --unrestricted\"         # added by $(whoami) on $(date +"%Y-%m-%d @ %H:%M:%S")" /etc/grub.d/10_linux
    ```

1. Actualiza GRUB con `update-grub`:

    ``` bash
    sudo update-grub
    ```

</details><br />

([Tabla de Contenidos](#tabla-de-contenidos))

### Deshabilitar Login de Root

<details><summary>!! PROCEDE BAJO TU PROPIO RIESGO !!</summary>

#### Por Qué

Si tienes sudo [configurado apropiadamente](#limitar-quién-puede-usar-sudo), entonces la cuenta **root** casi nunca necesitará iniciar sesión directamente -- ya sea en la terminal o remotamente.

#### Por Qué No

**Advertencia, esto puede causar problemas con algunas configuraciones!**

Si tu instalación usa [`sulogin`](https://linux.die.net/man/8/sulogin) (como Debian) para caer a una consola **root** durante fallos de arranque, entonces bloquear la cuenta **root** prevendrá que `sulogin` abra el shell **root** y obtendrás este error:

    Cannot open access to console, the root account is locked.
    
    See sulogin(8) man page for more details.
    
    Press Enter to continue.

Para trabajar alrededor de esto, puedes usar la opción `--force` para `sulogin`. Algunas distribuciones ya incluyen esto, u otra, solución alternativa.

Una alternativa a bloquear la cuenta **root** es establecer una contraseña **root** larga/complicada y almacenarla en un formato seguro, no digital. De esa manera la tienes cuando/si la necesitas.

#### Objetivos

- cuenta **root** bloqueada que nadie puede usar para iniciar sesión como **root**

#### Notas

- Algunas distribuciones deshabilitan el login de **root** por defecto (ej. Ubuntu) así que puede que no necesites hacer este paso. Revisa con la documentación de tu distribución.

#### Referencias

- https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=806852
- https://github.com/systemd/systemd/issues/7115
- https://github.com/karelzak/util-linux/commit/7ff1162e67164cb4ece19dd809c26272461aa254
- https://github.com/systemd/systemd/issues/11596
- https://www.reddit.com/r/selfhosted/comments/aoxd4l/new_guide_created_by_me_how_to_secure_a_linux/eg4rkfi/
- `man systemd`

#### Pasos

1. Bloquea la cuenta **root**:

    ``` bash
    sudo passwd -l root
    ```

</details><br />

([Tabla de Contenidos](#tabla-de-contenidos))

### Cambiar umask Predeterminado

<details><summary>!! PROCEDE BAJO TU PROPIO RIESGO !!</summary>

#### Por Qué

umask controla los permisos **predeterminados** de archivos/carpetas cuando se crean. Permisos inseguros de archivos/carpetas dan a otras cuentas potencialmente acceso no autorizado a tus datos. Esto puede incluir la capacidad de hacer cambios de configuración.

- Para cuentas **no-root**, no hay necesidad de que otras cuentas obtengan ningún acceso a los archivos/carpetas de la cuenta **por defecto**.
- Para la cuenta **root**, no hay necesidad de que el grupo primario de archivo/carpeta u otras cuentas tengan ningún acceso a los archivos/carpetas de **root** **por defecto**.

Cuando y si otras cuentas necesitan acceso a un archivo/carpeta, quieres otorgarlo explícitamente usando una combinación de permisos de archivo/carpeta y grupo primario.

#### Por Qué No

Cambiar el umask predeterminado puede crear problemas inesperados. Por ejemplo, si estableces umask a `0077` para **root**, entonces las cuentas **no-root** **no tendrán** acceso a archivos/carpetas de configuración de aplicación en `/etc/` lo que podría romper aplicaciones que no se ejecutan con privilegios **root**.

#### Cómo Funciona

Para explicar cómo funciona umask tendría que explicar cómo funcionan los permisos de archivos/carpetas en Linux. Como esa es una pregunta bastante complicada, te remitiré a las referencias a continuación para lectura adicional.

#### Objetivos

- establecer umask predeterminado para cuentas **no-root** a **0027**
- establecer umask predeterminado para la cuenta **root** a **0077**

#### Notas

- umask es un built-in de Bash lo que significa que un usuario puede cambiar su propia configuración umask.

#### Referencias

- https://www.linuxnix.com/umask-define-linuxunix/
- https://serverfault.com/questions/818783/which-umask-is-more-secure-in-linux-022-or-027
- https://www.cyberciti.biz/tips/understanding-linux-unix-umask-value-usage.html
- `man umask`

#### Pasos

1. Haz un respaldo de archivos que editaremos:

    ``` bash
    sudo cp --archive /etc/profile /etc/profile-COPY-$(date +"%Y%m%d%H%M%S")
    sudo cp --archive /etc/bash.bashrc /etc/bash.bashrc-COPY-$(date +"%Y%m%d%H%M%S")
    sudo cp --archive /etc/login.defs /etc/login.defs-COPY-$(date +"%Y%m%d%H%M%S")
    sudo cp --archive /root/.bashrc /root/.bashrc-COPY-$(date +"%Y%m%d%H%M%S")
    ```

1. Establece umask predeterminado para cuentas **no-root** a **0027** agregando esta línea a `/etc/profile` y `/etc/bash.bashrc`:

    ```
    umask 0027
    ```

    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):

    ``` bash
    echo -e "\numask 0027         # added by $(whoami) on $(date +"%Y-%m-%d @ %H:%M:%S")" | sudo tee -a /etc/profile /etc/bash.bashrc
    ```

1. También necesitamos agregar esta línea a `/etc/login.defs`:

    ```
    UMASK 0027
    ```

    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):

    ``` bash
    echo -e "\nUMASK 0027         # added by $(whoami) on $(date +"%Y-%m-%d @ %H:%M:%S")" | sudo tee -a /etc/login.defs
    ```

1. Establece umask predeterminado para la cuenta **root** a **0077** agregando esta línea a `/root/.bashrc`:

    ```
    umask 0077
    ```

    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):

    ``` bash
    echo -e "\numask 0077         # added by $(whoami) on $(date +"%Y-%m-%d @ %H:%M:%S")" | sudo tee -a /root/.bashrc
    ```

</details><br />

([Tabla de Contenidos](#tabla-de-contenidos))

### Software Huérfano

<details><summary>!! PROCEDE BAJO TU PROPIO RIESGO !!</summary>

#### Por Qué

A medida que uses tu sistema, e instales y desinstales software, eventualmente terminarás con software/paquetes/librerías huérfanos, o sin usar. No necesitas removerlos, pero si no los necesitas, ¿por qué mantenerlos? Cuando la seguridad es una prioridad, cualquier cosa no explícitamente necesitada es una amenaza de seguridad potencial. Quieres mantener tu servidor tan recortado y liviano como sea posible.

#### Notas

- Cada distribución gestiona software/paquetes/librerías de manera diferente así que cómo encuentras y remueves paquetes huérfanos será diferente. Hasta ahora solo tengo pasos para sistemas basados en Debian.

#### Sistemas Basados en Debian

En sistemas basados en Debian, puedes usar [deborphan](http://freshmeat.sourceforge.net/projects/deborphan/) para encontrar paquetes huérfanos.

##### <a name="software-huérfano-por-qué-no"></a>Por Qué No

Ten en cuenta, deborphan encuentra paquetes que **no tienen dependencias de paquete**. Eso no significa que no se usen. Muy bien podrías tener un paquete que uses cada día que no tiene dependencias que no querrías remover. Y, si deborphan se equivoca en algo, entonces remover paquetes críticos puede romper tu sistema.

##### Pasos

1. Instala deborphan.

    ``` bash
    sudo apt install deborphan
    ```

1. Ejecuta deborphan como **root** para ver una lista de paquetes huérfanos:

    ``` bash
    sudo deborphan
    ```

    > ```
    > libxapian30
    > libpipeline1
    > ```

1. [Asumiendo que quieres remover todos los paquetes que deborphan encuentra](#software-huérfano-por-qué-no), puedes pasar su salida a `apt` para removerlos:

    ``` bash
    sudo apt --autoremove purge $(deborphan)
    ```

</details>

</details><br />

([Tabla de Contenidos](#tabla-de-contenidos))

## Lo Misceláneo

### La Forma Simple con MSMTP
(#msmtp-alternativo)
#### Por Qué

Bien, SIMPLIFICARÉ este método, para solo enviar correos usando cuenta de Google Mail (y otros). ¡Verdaderamente Simple! :)

    ``` bash
    #!/bin/bash
    ###### POR FAVOR.... EDÍTALO...
    USEREMAIL="nombreusuariocorreo"
    DOMPROV="gmail.com"
    PWDEMAIL="contraseñaFuerte"  ## ATENCIÓN NO USAR Caracteres Especiales.. como ESPACIO # y algunos otros no todos. Siéntete libre de probar ;)
    MAILPROV="smtp.google.com:583"
    MYMAIL="$USRMAIL@$DOMPROV"
    USERLOC="root"
    #######
    apt install -y msmtp
        ln -s /usr/bin/msmtp /usr/sbin/sendmail
    #wget http://www.cacert.org/revoke.crl -O /etc/ssl/certs/revoke.crl
    #chmod 644 /etc/ssl/certs/revoke.crl
    touch /root/.msmtprc
    cat <<EOF> .msmtprc
    defaults
    account gmail
    host $MAILPROV
    port $MAILPORT
    #proxy_host 127.0.0.1
    #proxy_port 9001
    from $MYEMAIL
    timeout off 
    protocol smtp
    #auto_from [(on|off)]
    #from envelope_from
    #maildomain [domain]
    auth on
    user $USRMAIL
    passwordeval "gpg -q --for-your-eyes-only --no-tty -d /root/msmtp-mail.gpg"
    #passwordeval "gpg --quiet --for-your-eyes-only --no-tty --decrypt /root/msmtp-mail.gpg"
    tls on
    tls_starttls on
    tls_trust_file /etc/ssl/certs/ca-certificates.crt
    #tls_crl_file /etc/ssl/certs/revoke.crl
    #tls_fingerprint [fingerprint]
    #tls_key_file [file]
    #tls_cert_file [file]
    tls_certcheck on
    tls_force_sslv3 on
    tls_min_dh_prime_bits 512
    #tls_priorities [priorities]
    #dsn_notify (off|condition)
    #dsn_return (off|amount)
    #domain argument
    #keepbcc off
    logfile /var/log/mail.log
    syslog on
    account default : gmail
    EOF
    chmod 0400 /root/.msmtprc
    
       ## En pruebas .. comando automático
    # echo -e "1\n4096\n\ny\n$MYUSRMAIL\n$MYEMAIL\nmy key\nO\n$PWDMAIL\n$PWDMAIL\n" | gpg --full-generate-key 
    ##
    gpg --full-generate-key
    gpg --output revoke.asc --gen-revoke $MYEMAIL
    echo -e "$PWDEMAIL\n" | gpg -e -o /root/msmtp-mail.gpg --recipient $MYEMAIL
    echo "export GPG_TTY=\$(tty)" >> .baschrc	
    chmod 400 msmtp-mail.gpg
    
    echo "Hello there" | msmtp --debug $MYEMAIL
    echo"######################
    ## MSMTP Configurado ##
    ######################"
    ```
    
¡HECHO! ;)
([Tabla de Contenidos](#tabla-de-contenidos))

### Gmail y Exim4 Como MTA Con TLS Implícito

#### Por Qué

A menos que estés planeando configurar tu propio servidor de correo, necesitarás una forma de enviar correos electrónicos desde tu servidor. Esto será importante para alertas/mensajes del sistema.

Puedes usar cualquier cuenta de Gmail. Recomiendo que crees una específica para este servidor. De esa manera si tu servidor **es** comprometido, el actor malicioso no tendrá ninguna contraseña para tu cuenta primaria. Concedido, si tienes 2FA/MFA habilitado, y usas una contraseña de aplicación, no hay mucho que un actor malicioso pueda hacer solo con la contraseña de aplicación, pero ¿por qué tomar el riesgo?

Hay muchas guías en línea que cubren cómo configurar Gmail como MTA usando STARTTLS incluyendo una [versión previa de esta guía](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/tree/cc5edcae1cf846dd250e76b121e721d836481d2f#configure-gmail-as-mta). Con STARTTLS, se hace una conexión inicial **sin cifrar** y luego se actualiza a una conexión cifrada TLS o SSL. En su lugar, con el enfoque delineado abajo, se hace una conexión TLS cifrada desde el inicio.

También, como discutido en [issue #29](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/29) y [aquí](https://blog.dhampir.no/content/exim4-line-length-in-debian-stretch-mail-delivery-failed-returning-message-to-sender), exim4 fallará para mensajes con líneas largas. Arreglaremos esto en esta sección también.

** **IMPORTANTE** ** Como mencionado en [#106](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/106), Google ya no te permite usar la contraseña de tu cuenta para autenticación. Tienes que habilitar 2FA y luego usar una contraseña de aplicación.

#### Objetivos

- `mail` configurado para enviar correos electrónicos desde tu servidor usando [Gmail](https://mail.google.com/)
- soporte de línea larga para exim4

#### Referencias

- Gracias a [remyabel](https://github.com/remyabel) por descubrir cómo hacer que esto funcione con TLS según documentado en [issue #24](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/24) y [pull request #26](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/pull/26).
- https://wiki.debian.org/Exim
- https://wiki.debian.org/GmailAndExim4
- https://www.exim.org/exim-html-current/doc/html/spec_html/ch-encrypted_smtp_connections_using_tlsssl.html
- https://php.quicoto.com/setup-exim4-to-use-gmail-in-ubuntu/
- https://www.fastmail.com/help/technical/ssltlsstarttls.html
- exim4 falla para mensajes con líneas largas - [issue #29](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/29) y https://blog.dhampir.no/content/exim4-line-length-in-debian-stretch-mail-delivery-failed-returning-message-to-sender
- https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/106

#### Pasos

1. Instala exim4. También necesitarás openssl y ca-certificates.

    En sistemas basados en Debian:

    ``` bash
    sudo apt install exim4 openssl ca-certificates
    ```

1. Configura exim4:

    Para sistemas basados en Debian:
    ``` bash
    sudo dpkg-reconfigure exim4-config
    ```

    Se te preguntará con algunas preguntas:

    |Pregunta|Respuesta|
    |--:|--|
    |General type of mail configuration|`mail sent by smarthost; no local mail`|
    |System mail name|`localhost`|
    |IP-addresses to listen on for incoming SMTP connections|`127.0.0.1; ::1`|
    |Other destinations for which mail is accepted|(predeterminado)|
    |Visible domain name for local users|`localhost`|
    |IP address or host name of the outgoing smarthost|`smtp.gmail.com::465`|
    |Keep number of DNS-queries minimal (Dial-on-Demand)?|`No`|
    |Split configuration into small files?|`No`|

1. Haz un respaldo de `/etc/exim4/passwd.client`:

    ``` bash
    sudo cp --archive /etc/exim4/passwd.client /etc/exim4/passwd.client-COPY-$(date +"%Y%m%d%H%M%S")
    ```

1. Agrega una línea como esta a `/etc/exim4/passwd.client`

    ```
    smtp.gmail.com:tuCuenta@gmail.com:tuContraseña
    *.google.com:tuCuenta@gmail.com:tuContraseña
    ```

    **Notas**:
    - Reemplaza `tuCuenta@gmail.com` y `tuContraseña` con tus detalles. Si tienes 2FA/MFA habilitado en tu Gmail entonces necesitarás crear y usar una contraseña de aplicación aquí.
    - Siempre verifica `host smtp.gmail.com` para los dominios más actualizados a listar.

1. Este archivo tiene tu contraseña de Gmail así que necesitamos bloquearlo:

    ``` bash
    sudo chown root:Debian-exim /etc/exim4/passwd.client
    sudo chmod 640 /etc/exim4/passwd.client
    ```

1. El siguiente paso es crear un certificado TLS que exim4 usará para hacer la conexión cifrada a `smtp.gmail.com`. Puedes usar tu propio certificado, como uno de [Let's Encrypt](https://letsencrypt.org/), o crear uno tú mismo usando openssl. Usaremos un script que viene con exim4 que llama a openssl para hacer nuestro certificado:

    ``` bash
    sudo bash /usr/share/doc/exim4-base/examples/exim-gencert
    ```

    > ```
    > [*] Creating a self signed SSL certificate for Exim!
    >     This may be sufficient to establish encrypted connections but for
    >     secure identification you need to buy a real certificate!
    > 
    >     Please enter the hostname of your MTA at the Common Name (CN) prompt!
    > 
    > Generating a RSA private key
    > ..........................................+++++
    > ................................................+++++
    > writing new private key to '/etc/exim4/exim.key'
    > -----
    > You are about to be asked to enter information that will be incorporated
    > into your certificate request.
    > What you are about to enter is what is called a Distinguished Name or a DN.
    > There are quite a few fields but you can leave some blank
    > For some fields there will be a default value,
    > If you enter '.', the field will be left blank.
    > -----
    > Country Code (2 letters) [US]:[redactado]
    > State or Province Name (full name) []:[redactado]
    > Locality Name (eg, city) []:[redactado]
    > Organization Name (eg, company; recommended) []:[redactado]
    > Organizational Unit Name (eg, section) []:[redactado]
    > Server name (eg. ssl.domain.tld; required!!!) []:localhost
    > Email Address []:[redactado]
    > [*] Done generating self signed certificates for exim!
    >     Refer to the documentation and example configuration files
    >     over at /usr/share/doc/exim4-base/ for an idea on how to enable TLS
    >     support in your mail transfer agent.
    > ```

1. Instruye a exim4 a usar TLS y port 465, y [arreglar el problema de líneas largas de exim4](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/29), creando el archivo `/etc/exim4/exim4.conf.localmacros` y agregando:

    ```
    MAIN_TLS_ENABLE = 1
    REMOTE_SMTP_SMARTHOST_HOSTS_REQUIRE_TLS = *
    TLS_ON_CONNECT_PORTS = 465
    REQUIRE_PROTOCOL = smtps
    IGNORE_SMTP_LINE_LENGTH_LIMIT = true
    ```

    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):

    ``` bash
    cat << EOF | sudo tee /etc/exim4/exim4.conf.localmacros
    MAIN_TLS_ENABLE = 1
    REMOTE_SMTP_SMARTHOST_HOSTS_REQUIRE_TLS = *
    TLS_ON_CONNECT_PORTS = 465
    REQUIRE_PROTOCOL = smtps
    IGNORE_SMTP_LINE_LENGTH_LIMIT = true
    EOF
    ```

1. Haz un respaldo del archivo de configuración de exim4 `/etc/exim4/exim4.conf.template`:

    ``` bash
    sudo cp --archive /etc/exim4/exim4.conf.template /etc/exim4/exim4.conf.template-COPY-$(date +"%Y%m%d%H%M%S")
    ```

1. Agrega lo siguiente a `/etc/exim4/exim4.conf.template` después del bloque `.ifdef REMOTE_SMTP_SMARTHOST_HOSTS_REQUIRE_TLS ... .endif`:

    ```
    .ifdef REQUIRE_PROTOCOL
      protocol = REQUIRE_PROTOCOL
    .endif
    ```

    > ```
    > .ifdef REMOTE_SMTP_SMARTHOST_HOSTS_REQUIRE_TLS
    >   hosts_require_tls = REMOTE_SMTP_SMARTHOST_HOSTS_REQUIRE_TLS
    > .endif
    > .ifdef REQUIRE_PROTOCOL
    >     protocol = REQUIRE_PROTOCOL
    > .endif
    > .ifdef REMOTE_SMTP_HEADERS_REWRITE
    >   headers_rewrite = REMOTE_SMTP_HEADERS_REWRITE
    > .endif
    > ```

    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):

    ``` bash
    sudo sed -i -r -e '/^.ifdef REMOTE_SMTP_SMARTHOST_HOSTS_REQUIRE_TLS$/I { :a; n; /^.endif$/!ba; a\# added by '"$(whoami) on $(date +"%Y-%m-%d @ %H:%M:%S")"'\n.ifdef REQUIRE_PROTOCOL\n    protocol = REQUIRE_PROTOCOL\n.endif\n# end add' -e '}' /etc/exim4/exim4.conf.template
    ```

1. Agrega lo siguiente a `/etc/exim4/exim4.conf.template` dentro del bloque `.ifdef MAIN_TLS_ENABLE`:

    ```
    .ifdef TLS_ON_CONNECT_PORTS
      tls_on_connect_ports = TLS_ON_CONNECT_PORTS
    .endif
    ```

    > ```
    > .ifdef MAIN_TLS_ENABLE
    > .ifdef TLS_ON_CONNECT_PORTS
    >     tls_on_connect_ports = TLS_ON_CONNECT_PORTS
    > .endif
    > ```

    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):

    ``` bash
    sudo sed -i -r -e "/\.ifdef MAIN_TLS_ENABLE/ a # added by $(whoami) on $(date +"%Y-%m-%d @ %H:%M:%S")\n.ifdef TLS_ON_CONNECT_PORTS\n    tls_on_connect_ports = TLS_ON_CONNECT_PORTS\n.endif\n# end add" /etc/exim4/exim4.conf.template
    ```
    
1. Actualiza la configuración de exim4 para usar TLS y luego reinicia el servicio:

    ``` bash
    sudo update-exim4.conf
    sudo service exim4 restart
    ```

1. Si estás usando [UFW](#firewall-con-ufw-uncomplicated-firewall), necesitarás permitir tráfico saliente en 465. Para hacer esto crearemos un perfil de aplicación UFW personalizado y luego lo habilitaremos. Crea el archivo `/etc/ufw/applications.d/smtptls`, agrega esto, luego ejecuta `ufw allow out smtptls comment 'open TLS port 465 for use with SMPT to send e-mails'`:

    ```
    [SMTPTLS]
    title=SMTP through TLS
    description=This opens up the TLS port 465 for use with SMPT to send e-mails.
    ports=465/tcp
    ```

    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):

    ``` bash
    cat << EOF | sudo tee /etc/ufw/applications.d/smtptls
    [SMTPTLS]
    title=SMTP through TLS
    description=This opens up the TLS port 465 for use with SMPT to send e-mails.
    ports=465/tcp
    EOF

    sudo ufw allow out smtptls comment 'open TLS port 465 for use with SMPT to send e-mails'
    ```

1. Agrega algunos alias de correo para que podamos enviar correos electrónicos a cuentas locales agregando líneas como esta a `/etc/aliases`:

    ```
    user1: user1@gmail.com
    user2: user2@gmail.com
    ...
    ```

    Necesitarás agregar todas las cuentas locales que existen en tu servidor.

1. Prueba tu configuración:

    ```
    echo "test" | mail -s "Test" email@gmail.com
    sudo tail /var/log/exim4/mainlog
    ```

([Tabla de Contenidos](#tabla-de-contenidos))

### Archivo de Log Separado para iptables

#### Por Qué

Llegará un momento cuando necesitarás mirar tus logs de iptables. Tener todos los logs de iptables yendo a su propio archivo hará mucho más fácil encontrar lo que estás buscando.

#### Referencias

- https://blog.shadypixel.com/log-iptables-messages-to-a-separate-file-with-rsyslog/
- https://gist.github.com/netson/c45b2dc4e835761fbccc
- https://www.rsyslog.com/doc/v8-stable/configuration/actions.html

#### Pasos

1. El primer paso es diciéndole a tu firewall que prefije todas las entradas de log con algún string único. Si estás usando iptables directamente, harías algo como `--log-prefix "[IPTABLES] "` para todas las reglas. Nos encargamos de esto en [paso 4 de instalar psad](#psad_step4).

1. Después de que hayas agregado un prefijo a los logs del firewall, necesitamos decirle a rsyslog que envíe esas líneas a su propio archivo. Haz esto creando el archivo `/etc/rsyslog.d/10-iptables.conf` y agregando esto:

    ```
    :msg, contains, "[IPTABLES] " /var/log/iptables.log
    & stop
    ```
    
    Si estás esperando muchos datos siendo registrados por tu firewall, prefijar el nombre de archivo con un `-` ["para omitir sincronizar el archivo después de cada registro"](https://www.rsyslog.com/doc/v8-stable/configuration/actions.html#regular-file). Por ejemplo:

    ```
    :msg, contains, "[IPTABLES] " -/var/log/iptables.log
    & stop
    ```

    **Nota**: Recuerda cambiar el prefijo a lo que uses.
    
    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):

    ``` bash
    cat << EOF | sudo tee /etc/rsyslog.d/10-iptables.conf
    :msg, contains, "[IPTABLES] " /var/log/iptables.log
    & stop
    EOF
    ```

1. Como estamos registrando mensajes del firewall a un archivo diferente, necesitamos decirle a psad dónde está el nuevo archivo. Edita `/etc/psad/psad.conf` y establece `IPT_SYSLOG_FILE` a la ruta del archivo de log. Por ejemplo:

    ```
    IPT_SYSLOG_FILE /var/log/iptables.log;
    ```
    
    **Nota**: Recuerda cambiar el prefijo a lo que uses.
    
    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):
    
    ``` bash
    sudo sed -i -r -e "s/^(IPT_SYSLOG_FILE\s+)([^;]+)(;)$/# \1\2\3       # commented by $(whoami) on $(date +"%Y-%m-%d @ %H:%M:%S")\n\1\/var\/log\/iptables.log\3       # added by $(whoami) on $(date +"%Y-%m-%d @ %H:%M:%S")/" /etc/psad/psad.conf 
    ```

1. Reinicia psad y rsyslog para activar los cambios (o reinicia):

    ``` bash
    sudo psad -R
    sudo psad --sig-update
    sudo psad -H
    sudo service rsyslog restart
    ```

1. Lo último que tenemos que hacer es decirle a logrotate que rote el nuevo archivo de log para que no se vuelva demasiado grande y llene nuestro disco. Crea el archivo `/etc/logrotate.d/iptables` y agrega esto:

    ```
    /var/log/iptables.log
    {
        rotate 7
        daily
        missingok
        notifempty
        delaycompress
        compress
        postrotate
            invoke-rc.d rsyslog rotate > /dev/null
        endscript
    }
    ```
    
    [Para los perezosos](#editando-archivos-de-configuración---para-los-perezosos):
    
    ``` bash
    cat << EOF | sudo tee /etc/logrotate.d/iptables
    /var/log/iptables.log
    {
        rotate 7
        daily
        missingok
        notifempty
        delaycompress
        compress
        postrotate
            invoke-rc.d rsyslog rotate > /dev/null
        endscript
    }
    EOF
    ```

([Tabla de Contenidos](#tabla-de-contenidos))

## Sobrante

### Contactándome

Para cualquier pregunta, comentario, preocupación, feedback, o problemas, envía un [nuevo issue](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server/issues/new).

([Tabla de Contenidos](#tabla-de-contenidos))

### Enlaces Útiles

- [https://github.com/pratiktri/server_init_harden](https://github.com/pratiktri/server_init_harden) - Script Bash que automatiza algunas de las tareas que necesitas realizar en un nuevo servidor Linux para darle una cantidad básica de seguridad.

([Tabla de Contenidos](#tabla-de-contenidos))

### Agradecimientos

- https://www.reddit.com/r/linuxquestions/comments/aopzl7/new_guide_created_by_me_how_to_secure_a_linux/
- https://www.reddit.com/r/selfhosted/comments/aoxd4l/new_guide_created_by_me_how_to_secure_a_linux/
- https://news.ycombinator.com/item?id=19177435#19178618
- https://www.reddit.com/r/linuxadmin/comments/arx7xo/howtosecurealinuxserver_an_evolving_howto_guide/
- https://www.reddit.com/r/linux/comments/arx7st/howtosecurealinuxserver_an_evolving_howto_guide/
- https://github.com/moltenbit/How-To-Secure-A-Linux-Server-With-Ansible

([Tabla de Contenidos](#tabla-de-contenidos))

### Licencia y Copyright

[![CC-BY-SA](https://i.creativecommons.org/l/by-sa/4.0/88x31.png)](http://creativecommons.org/licenses/by-sa/4.0/)

[How To Secure A Linux Server](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server) por [Anchal Nigam](https://github.com/imthenachoman) está licenciado bajo [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0).

Ve [LICENSE](LICENSE.txt) para la licencia completa.

([Tabla de Contenidos](#tabla-de-contenidos))
