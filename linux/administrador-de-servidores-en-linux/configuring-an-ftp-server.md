---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Configuring an FTP Server

### Cómo instalar vsftpd en Linux

***

***

\
**vsftpd** (demonio FTP muy seguro) es uno de los mejores y más populares servidores FTP para Linux. También existen otros, pero vsftpd es lo que recomendamos usar. Puede utilizar el comando apropiado a continuación para instalar vsftpd con [el administrador de paquetes](https://linuxconfig.org/comparison-of-major-linux-package-management-systems) de su sistema .

Para instalar vsftpd en [Ubuntu](https://linuxconfig.org/ubuntu-linux-download) , [Debian](https://linuxconfig.org/debian-linux-download) y [Linux Mint](https://linuxconfig.org/linux-mint-download) :

```
$ sudo apto instalar vsftpd
```

Para instalar vsftpd en [Fedora](https://linuxconfig.org/fedora-linux-download) , [CentOS](https://linuxconfig.org/centos-linux-download) , [AlmaLinux](https://linuxconfig.org/almalinux-download) y [Red Hat](https://linuxconfig.org/red-hat-linux-download) :

```
$ sudo dnf instalar vsftpd
```

Para instalar vsftpd en [Arch Linux](https://linuxconfig.org/arch-linux-download) y [Manjaro](https://linuxconfig.org/manjaro-linux-download) :

```
$ sudo pacman -S vsftpd
```

### Configurar un servidor vsftpd

Después de la instalación, realizaremos una configuración básica para que el servidor FTP esté en funcionamiento:

1.  Siempre es una buena práctica mantener una copia de seguridad del archivo de configuración original, en caso de que algo salga mal más adelante. Cambiemos el nombre del archivo de configuración predeterminado:

    ```
    $ sudo mv /etc/vsftpd.conf /etc/vsftpd.conf_orig
    ```
2.  Cree un nuevo archivo de configuración vsftpd usando nano o el editor de texto que prefiera:

    ```
    $ sudo nano /etc/vsftpd.conf
    ```
3.  Copie la siguiente configuración base en su archivo. Esta configuración será suficiente para un servidor FTP básico y luego podrá modificarse para las necesidades específicas de su entorno una vez que haya verificado que funciona correctamente:

    ```bash
    listen=NO
    listen_ipv6=YES
    anonymous_enable=NO
    local_enable=YES
    write_enable=YES
    local_umask=022
    dirmessage_enable=YES
    use_localtime=YES
    xferlog_enable=YES
    connect_from_port_20=YES
    chroot_local_user=YES
    secure_chroot_dir=/var/run/vsftpd/empty
    pam_service_name=vsftpd
    rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
    rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
    ssl_enable=NO
    pasv_enable=Yes
    pasv_min_port=10000
    pasv_max_port=10100
    allow_writeable_chroot=YES
    ```

    Copiar

    Pegue las líneas anteriores en su archivo /etc/vsftpd.conf recién creado y luego [guarde los cambios y cierre el archivo](https://linuxconfig.org/how-to-save-and-exit-file-using-nano-editor) .



    \


    ***

    ***
4.  Es posible que su firewall de Linux esté configurado para bloquear las conexiones a FTP actualmente, pero ejecutar el comando apropiado a continuación para su distribución creará una excepción para permitir el tráfico:

    En Ubuntu y sistemas que utilizan ufw (firewall sencillo):

    ```
    $ sudo ufw permite desde cualquier puerto 20,21 proto tcp
    ```

    En distribuciones basadas en RHEL o cualquier otra que utilice firewalld:

    ```
    $ sudo firewall-cmd --zone=public --permanent --add-service=ftp
    ```

    O si solo estás usando iptables y no tienes una interfaz de firewall:

    ```
    $ sudo iptables -A ENTRADA -m estado --estado NUEVO,ESTABLECIDO -m tcp -p tcp --dport 20,21 -j ACEPTAR
    ```
5.  Con el archivo de configuración guardado y las reglas del firewall actualizadas, reinicie vsftpd para aplicar los nuevos cambios:

    ```
    $ sudo systemctl reiniciar vsftpd
    ```

### Crear un usuario FTP

Nuestro servidor FTP está listo para recibir conexiones entrantes, por lo que ahora es el momento de crear una nueva cuenta de usuario que usaremos para conectarnos al servicio FTP.

1.  Utilice este primer comando para crear una nueva cuenta llamada `ftpuser`y el segundo comando para establecer una contraseña para la cuenta:

    ```
    $ sudo useradd -m ftpuser
    $ sudo contraseña ftpuser
    Nueva contraseña:
    Reescriba nueva contraseña:
    passwd: contraseña actualizada correctamente
    ```
2.  Para verificar que todo funciona correctamente, debe almacenar al menos un archivo en el directorio de inicio de ftpuser. Este archivo debería estar visible cuando iniciemos sesión en FTP en los siguientes pasos.

    ```
    $ sudo bash -c "echo PRUEBA DE FTP > /home/ftpuser/FTP-TEST"
    ```

### Conéctese al servidor FTP a través de la línea de comando

1.  Ahora debería poder conectarse a su servidor FTP ya sea por dirección IP o nombre de host. Para conectarse desde la línea de comando y verificar que todo esté funcionando, abra una terminal y use el `ftp`comando para conectarse a su dirección de loopback (127.0.0.1).

    ```
    $ ftp 127.0.0.1
    ```


2.  Como puede ver en la captura de pantalla anterior, pudimos iniciar sesión en el servidor FTP especificando el nombre de usuario y la contraseña que configuramos anteriormente. A continuación, intentemos emitir un `ls`comando, que debería enumerar el archivo de prueba que creamos en los pasos anteriores.

    ```
    ftp>ls
    ```



***

***

\
Su resultado debería verse como la captura de pantalla anterior, indicando un inicio de sesión exitoso y un `ls`comando que revela nuestro archivo de prueba que creamos anteriormente.

### Conéctese al servidor FTP a través de GUI

La mayoría de los entornos de escritorio tienen una forma integrada de conectarse a servidores FTP. Incluso si el suyo no lo hace, hay muchos clientes FTP gratuitos disponibles para Linux. En las instrucciones a continuación, usaremos el entorno de escritorio GNOME en Ubuntu para conectarnos al servidor FTP. Si está ejecutando otra GUI, busque una opción para conectarse a un servidor externo en su [administrador de archivos](https://linuxconfig.org/best-file-manager-for-linux) ; desde allí, las instrucciones deberían ser más o menos las mismas que se muestran a continuación:

1.  En su administrador de archivos, haga clic en "Otras ubicaciones" (puede llamarse de otra manera si no usa GNOME) e ingrese `ftp://127.0.0.1`en el cuadro "Conectar al servidor" en la parte inferior de la ventana y haga clic en conectar.


2.  Elija "usuario registrado" y luego ingrese las credenciales de la cuenta FTP que configuramos anteriormente y haga clic en conectar.


3.  Tras una conexión exitosa, verá el archivo de prueba que creó anteriormente. Ahora podrá descargar y ver este archivo, o cargar su propio contenido en el directorio.



### Permitir acceso anónimo en vsftpd

Hasta ahora hemos visto cómo crear nuevos usuarios que puedan acceder al servidor FTP. Si desea que otros puedan acceder a su servidor FTP sin proporcionar un nombre de usuario y contraseña, puede configurar la autenticación anónima. Siga los pasos a continuación para configurarlo.

1.  Primero, necesitaremos editar el `/etc/vsftpd.conf`archivo, así que ábrelo con nano o cualquier otro editor de texto.

    ```
    $ sudo nano /etc/vsftpd.conf
    ```
2.  A continuación, busque la `anonymous_enable=NO`línea y cambie la configuración a `YES`.

    ```
    anónimo_enable=SÍ
    ```
3.  Cuando termine, salga de este archivo mientras guarda los nuevos cambios, luego reinicie el servicio vsftpd para que los cambios surtan efecto.

    ```
    $ sudo systemctl reiniciar vsftpd
    ```

    ***

    ***
4.  Para probar el inicio de sesión anónimo, emita el `ftp 127.0.0.1`comando, utilícelo `anonymous`como nombre de usuario y una contraseña en blanco. Debería recibir un `230 Login successful`mensaje como se muestra en la captura de pantalla a continuación.



### Cambiar el número de puerto FTP predeterminado

De forma predeterminada, el protocolo FTP escucha en el puerto 21 para la autenticación del usuario y en el puerto 20 para la transferencia de datos. Sin embargo, podemos cambiar este comportamiento realizando una pequeña edición en el `/etc/vsftpd.conf`archivo. En la parte inferior del archivo, use la `listen_port`directiva para especificar un puerto diferente para que lo use vsftpd. Por ejemplo, agregar la siguiente línea le indicará a vsftpd que escuche en el puerto 2121:

```
puerto_escucha=2121
```

\