## La Red

### Firewall Con UFW (Uncomplicated Firewall)

#### Por Qué

Llámame paranoico, y no tienes que estar de acuerdo, pero quiero denegar todo el tráfico entrante y saliente de mi servidor excepto lo que explícitamente permito. ¿Por qué mi servidor estaría enviando tráfico saliente que no conozco? ¿Y por qué tráfico externo estaría intentando acceder a mi servidor si no sé quién o qué es? Cuando se trata de buena seguridad, mi opinión es rechazar/denegar por defecto, y permitir por excepción.

Por supuesto, si no estás de acuerdo, está totalmente bien y puedes configurar UFW para adaptarse a tus necesidades.

De cualquier manera, asegurar que solo el tráfico que explícitamente permitimos es el trabajo de un firewall.

#### Cómo Funciona

El kernel de Linux proporciona capacidades para monitorear y controlar el tráfico de red. Estas capacidades se exponen al usuario final a través de utilidades de firewall. En Linux, el firewall más común es [iptables](https://en.wikipedia.org/wiki/Iptables). Sin embargo, iptables es bastante complicado y confuso (en mi opinión). Aquí es donde entra UFW. Piensa en UFW como un frontend para iptables. Simplifica el proceso de administrar las reglas de iptables que le dicen al kernel de Linux qué hacer con el tráfico de red.

**UFW** funciona permitiéndote configurar reglas que:

- **permiten** o **deniegan**
- tráfico **entrante** o **saliente**
- **a** o **desde** ports

Puedes crear reglas especificando explícitamente los ports o con configuraciones de aplicaciones que especifican los ports.

#### Objetivos

- todo el tráfico de red, entrada y salida, bloqueado excepto aquellos que explícitamente permitamos

#### Notas

- A medida que instales otros programas, necesitarás habilitar los ports/aplicaciones necesarios.

#### Referencias

- https://launchpad.net/ufw
- `man ufw`

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
    # permitir tráfico saliente al puerto 53 -- DNS
    sudo ufw allow out 53 comment 'allow DNS calls out'

	# permitir tráfico saliente al puerto 123 -- NTP
    sudo ufw allow out 123 comment 'allow NTP out'

    # permitir tráfico saliente para HTTP, HTTPS, o FTP
    # apt podría necesitar estos dependiendo de qué fuentes estés usando
    sudo ufw allow out http comment 'allow HTTP traffic out'
    sudo ufw allow out https comment 'allow HTTPS traffic out'
    sudo ufw allow out ftp comment 'allow FTP traffic out'

    # permitir whois
    sudo ufw allow out whois comment 'allow whois'

    # permitir correos para notificaciones de estado -- elige el puerto según tu proveedor
    sudo ufw allow out 25 comment 'allow SMTP out'
    sudo ufw allow out 587 comment 'allow SMTP out'

    # permitir tráfico saliente al puerto 68 -- el cliente DHCP
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

1. Si quieres ver un status:

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

7. Si necesitas eliminar una regla, primero lista las reglas numeradas:

    ``` bash
    sudo ufw status numbered
    ```

    Luego elimina la regla con:

    ``` bash
    sudo ufw delete [number]
    ```

([Tabla de Contenidos](#tabla-de-contenidos))
