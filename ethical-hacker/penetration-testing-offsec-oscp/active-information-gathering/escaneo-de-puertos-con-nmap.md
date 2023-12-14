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

# Escaneo de puertos con nmap

La huella que cada escaneo de Nmap deja en la red y los hosts escaneados.

Un escaneo TCP predeterminado de Nmap escaneará los 1000 puertos más populares en una máquina determinada. Antes de comenzar a ejecutar escaneos a ciegas, examinemos la cantidad de tráfico enviado por este tipo de escaneo. Escanearemos una de las máquinas del laboratorio mientras monitoreamos la cantidad de tráfico enviado al host objetivo usando iptables.

Utilizaremos varias opciones de iptables. Primero, usemos la opción -I para insertar una nueva regla en una cadena determinada, que en este caso incluye tanto la cadena INPUT (entrada) como la OUTPUT (salida), seguida del número de regla. Podemos usar -s para especificar una dirección IP de origen, -d para especificar una dirección IP de destino y -j para ACCEPTAR el tráfico. Finalmente, usaremos la opción -Z para poner a cero los contadores de paquetes y bytes en todas las cadenas.

```
sudo iptables -I INPUT 1 -s 192.168.50.149 -j ACCEPT
sudo iptables -I OUTPUT 1 -d 192.168.50.149 -j ACCEPT
sudo iptables -Z
```

Habiendo configurado nuestras reglas de iptables para el escaneo, generemos algo de tráfico usando nmap:

```
nmap 192.168.50.149
Starting Nmap 7.92 ( https://nmap.org ) at 2022-03-09 05:12 EST
Nmap scan report for 192.168.50.149
Host is up (0.10s latency).
Not shown: 989 closed tcp ports (conn-refused)
PORT      STATE SERVICE
53/tcp    open  domain
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
Nmap done: 1 IP address (1 host up) scanned in 10.95 seconds
```

Ahora revisemos algunas estadísticas de iptables para tener una idea más clara de cuánto tráfico generó nuestro escaneo. Podemos usar la opción `-v` para agregar verbosidad a nuestra salida, `-n` para habilitar la salida numérica y `-L` para listar las reglas presentes en todas las cadenas.

```
sudo iptables -vn -L
Chain INPUT (policy ACCEPT 1270 packets, 115K bytes)
pkts bytes target
prot opt in out source
1196 47972 ACCEPT all -- * * 192.168.50.149

Chain OUTPUT (policy ACCEPT 1264 packets, 143K bytes)
pkts bytes target
prot opt in out source destination
1218 72640 ACCEPT all -- * * 0.0.0.0/0 192.168.50.149
```

Según la salida, este escaneo predeterminado de 1000 puertos generó alrededor de 72 KB de tráfico.

Usemos iptables -Z para poner a cero los contadores de paquetes y bytes en todas las cadenas nuevamente y ejecutemos otro escaneo de nmap, esta vez usando -p para especificar todos los puertos TCP.

```
sudo iptables -Z
```

Al comprender la cantidad de tráfico que genera un escaneo de puertos, podemos tomar decisiones más informadas sobre cuándo y cómo realizar un escaneo. Además, al monitorear el tráfico del escaneo, podemos identificar posibles anomalías que podrían indicar que el objetivo está detectando o bloqueando nuestros intentos de escaneo.

Habiendo configurado iptables, ejecutemos un escaneo de Nmap en un host local probando explícitamente todos los 65535 puertos TCP:

```
nmap -p 1-65535 192.168.50.149
Starting Nmap 7.92 ( https://nmap.org ) at 2022-03-09 05:23 EST
Nmap scan report for 192.168.50.149
Host is up (0.11s latency).
Not shown: 65510 closed tcp ports (conn-refused)
PORT    STATE SERVICE
53/tcp    open  domain
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
5985/tcp  open  wsman
9389/tcp  open  adws
47001/tcp open  winrm
49664/tcp open  unknown
...
Nmap done: 1 IP address (1 host up) scanned in 2141.22 seconds
```

Como era de esperar, el escaneo de todos los puertos TCP genera considerablemente más tráfico:

```
sudo iptables -vn -L
Chain INPUT (policy ACCEPT 67996 packets, 6253K bytes)
pkts bytes target
prot opt in out source
68724 2749K ACCEPT all -- * * 192.168.50.149

Chain OUTPUT (policy ACCEPT 67923 packets, 7606K bytes)
pkts bytes target
prot opt in out source destination
68807 4127K ACCEPT all -- * * 0.0.0.0/0 192.168.50.149
```

El escaneo completo del puerto local generó aproximadamente 4 MB de tráfico, una cantidad significativamente mayor que el escaneo predeterminado de 1000 puertos. Sin embargo, este escaneo completo ha descubierto más puertos que el escaneo TCP predeterminado.

Nuestros resultados implican que un escaneo completo de Nmap de una red de clase C (254 hosts) resultaría en el envío de más de 1000 MB de tráfico a la red. Idealmente, un escaneo completo de puertos TCP y UDP de cada máquina objetivo proporcionaría la información más precisa sobre los servicios de red expuestos. Sin embargo, claramente necesitamos equilibrar las restricciones de tráfico con el descubrimiento de puertos y servicios abiertos adicionales a través de un escaneo más exhaustivo. Esto es especialmente cierto para redes más grandes, como una evaluación de red de clase A o B.

> Existen escáneres de puertos modernos como MASSCAN y RustScan que, aunque son más rápidos que Nmap, generan una cantidad considerable de tráfico concurrente. Nmap, por otro lado, utiliza un enfoque más metódico y escalonado, lo que puede ser beneficioso para evitar la detección y la denegación de servicio.

### Explorando las técnicas de escaneo de Nmap: SYN Scanning

El escaneo SYN, también conocido como "Stealth".Ofrece muchos beneficios y, por lo tanto, es la opción de escaneo predeterminada cuando no se especifica ninguna opción en un comando de nmap y el usuario tiene los privilegios de sockets raw requeridos.

**¿Cómo funciona?**

El escaneo SYN es un método de escaneo de puertos TCP que implica enviar paquetes SYN a varios puertos en una máquina objetivo sin completar el protocolo de enlace TCP de tres vías. Si un puerto TCP está abierto, la máquina objetivo debería enviar un paquete SYN-ACK, informándonos de que el puerto está abierto. En este punto, el escáner de puertos no se molesta en enviar el ACK final para completar el protocolo de enlace.

**Ejemplo:**

```
sudo nmap -sS 192.168.50.149
```

### Escaneo de puertos de Nmap: TCP Connect Scan y UDP

Cuando un usuario que ejecuta Nmap no tiene privilegios de socket raw, Nmap utilizará por defecto la técnica de escaneo de conexión TCP. A diferencia del escaneo SYN, que no requiere privilegios elevados, el escaneo de conexión TCP utiliza la API Berkeley sockets para realizar el protocolo de enlace de tres vías. Esto implica que el escaneo de conexión TCP tarda mucho más en completarse que un escaneo SYN.

Podemos necesitar realizar un escaneo de conexión en ocasiones, como cuando escaneamos a través de ciertos tipos de proxies. Podemos usar la opción `-sT` para iniciar un escaneo de conexión.

```
nmap -sT 192.168.50.149
```

### Sobre el escaneo UDP

Al realizar un escaneo UDP, Nmap utilizará una combinación de dos métodos diferentes para determinar si un puerto está abierto o cerrado. Para la mayoría de los puertos, utilizará el método estándar "ICMP puerto inalcanzable" descrito anteriormente enviando un paquete vacío a un puerto determinado. Sin embargo, para puertos comunes, como el puerto 161 que utiliza SNMP, enviará un paquete SNMP específico del protocolo en un intento de obtener una respuesta de una aplicación enlazada a ese puerto.

```
sudo nmap -sU 192.168.50.149
```

Es importante tener en cuenta que el escaneo UDP puede ser más ruidoso que el escaneo TCP y puede generar respuestas de dispositivos que no sean el objetivo previsto.

### Escaneo UDP y Escaneo Combinado UDP/TCP

El escaneo UDP (-sU) también se puede usar junto con un escaneo TCP SYN (-sS) .

```
sudo nmap -sU -sS 192.168.50.149
```

### Escaneo de red (Network Sweeping)

Para lidiar con grandes volúmenes de hosts, o para intentar conservar el tráfico de red, podemos intentar sondear los objetivos utilizando técnicas de Network Sweeping en las que comenzamos con escaneos amplios y luego usamos escaneos más específicos contra los hosts de interés.

Al realizar un barrido de red con Nmap usando la opción `-sn`, el proceso de descubrimiento de host consiste en algo más que enviar una solicitud de eco ICMP. Nmap también envía un paquete SYN TCP al puerto 443, un paquete ACK TCP al puerto 80 y una solicitud de marca de tiempo ICMP para verificar si un host está disponible.

```
nmap -sn 192.168.50.1-253
```

### Escaneo de red con Nmap: Greppable Output y Escaneos Específicos

Buscar máquinas activas usando el comando `grep` en una salida estándar de Nmap puede ser engorroso. En lugar de eso, usemos el parámetro de salida "greppable" de Nmap, `-oG`, para guardar estos resultados en un formato más manejable.

```
nmap -v -sn 192.168.50.1-253 -oG ping-sweep.txt
```

Luego podemos usar `grep` para encontrar hosts activos en el archivo de salida:

```
kali@kali:~$ grep Up ping-sweep.txt | cut -d " " -f 2
192.168.50.6
192.168.50.8
192.168.50.9
...
```

También podemos escanear en busca de puertos TCP o UDP específicos en la red, sondeando servicios y puertos comunes en un intento de localizar sistemas que puedan ser útiles o tener vulnerabilidades conocidas. Este escaneo tiende a ser más preciso que un ping sweep.

```
nmap -p 80 192.168.50.1-253 -oG web-sweep.txt
```

**A continuación, podemos usar `grep` para encontrar hosts con el puerto 80 abierto:**

```
grep open web-sweep.txt | cut -d" " -f2
192.168.50.6
192.168.50.20
192.168.50.21
```

Para ahorrar tiempo y recursos de red, también podemos escanear varias IP, sondeando una lista corta de puertos comunes. Por ejemplo, realicemos un escaneo de conexión TCP para los 20 puertos TCP principales con la opción `--top-ports` y habilitemos la detección de la versión del sistema operativo, el escaneo de scripts y el traceroute con `-A`.

```
nmap -sT -A --top-ports=20 192.168.50.1-253 -oG top-port-sweep.txt
```

El archivo `nmap-services` nos ayuda a identificar rápidamente los servicios más comunes que se ejecutan en los hosts que escaneamos.

### Detección de Huella Dactilar del Sistema Operativo con Nmap

Nmap puede identificar el sistema operativo de un host inspeccionando los paquetes recibidos del objetivo y comparando la huella dactilar con una lista conocida. Por defecto, Nmap solo mostrará el sistema operativo detectado si la huella digital recuperada es muy precisa.

Para obtener una idea aproximada del sistema operativo objetivo, podemos incluir la opción `--osscan-guess` para forzar a Nmap a imprimir el resultado adivinado, incluso si no es completamente exacto.

**Aquí hay un ejemplo de un escaneo simple de huella digital del sistema operativo de Nmap:**

```
sudo nmap -O 192.168.50.14 --osscan-guess
...
Running (JUST GUESSING): Microsoft Windows 2008|2012|2016|7|Vista (88%)
OS CPE: cpe:/o:microsoft:windows_server_2008::sp1
cpe:/o:microsoft:windows_server_2008:r2 cpe:/o:microsoft:windows_server_2012:r2
cpe:/o:microsoft:windows_server_2016 cpe:/o:microsoft:windows_7
cpe:/o:microsoft:windows_vista::sp1:home_premium
Aggressive OS guesses: Microsoft Windows Server 2008 SP1 or Windows Server 2008 R2
(88%), Microsoft Windows Server 2012 or Windows Server 2012 R2 (88%), Microsoft
Windows Server 2012 R2 (88%), Microsoft Windows Server 2012 (87%), Microsoft Windows
Server 2016 (87%), Microsoft Windows 7 (86%), Microsoft Windows Vista Home Premium SP1
(85%), Microsoft Windows 7 Professional (85%)
No exact OS matches for host (If you know what OS is running on it, see
https://nmap.org/submit/ ).
...
```

Tenga en cuenta que la detección de huellas dactilares del sistema operativo no siempre es 100% precisa, a menudo debido a dispositivos de red como firewalls o proxies que reescriben los encabezados de los paquetes en la comunicación.

Una vez que hemos reconocido el sistema operativo subyacente, podemos ir más allá e identificar los servicios que se ejecutan en puertos específicos inspeccionando las pancartas de servicio con el parámetro `-A`, que también ejecuta varios scripts de enumeración de servicios y SO contra el objetivo.

**Ejemplo:**

```
nmap -sT -A 192.168.50.14
```

### Escaneo de servicios con Nmap

La captura de banners afecta significativamente la cantidad de tráfico utilizado, así como la velocidad de nuestro escaneo. Siempre debemos tener en cuenta las opciones que usamos con Nmap y cómo afectan nuestros escaneos.

Los banners pueden ser modificados por los administradores del sistema y configurados intencionalmente para falsificar nombres de servicios con el fin de engañar a posibles atacantes.

El script `http-headers`, por ejemplo, intenta conectarse al servicio HTTP en un sistema de destino y determinar los encabezados admitidos.

Esta información es esencial para comprender cómo se comunican los servicios con el cliente y, en algunos casos, puede revelar vulnerabilidades o configuraciones incorrectas.

```
nmap --script http-headers 192.168.50.6
```

### Escaneo de puertos con PowerShell

La función `Test-NetConnection` verifica si una IP responde al ICMP y si un puerto TCP especificado en el host objetivo está abierto.

Por ejemplo, desde el cliente de Windows 11, podemos verificar si el puerto SMB 445 está abierto en un controlador de dominio de la siguiente manera:

```
Test-NetConnection -Port 445 192.168.50.151
ComputerName : 192.168.50.151
RemoteAddress : 192.168.50.151
RemotePort : 445
InterfaceAlias : Ethernet0
SourceAddress : 192.168.50.152
TcpTestSucceeded : True
```

El valor devuelto en el parámetro `TcpTestSucceeded` indica que el puerto 445 está abierto.

Podemos programar aún más todo el proceso para escanear los primeros 1024 puertos en el controlador de dominio con la línea única de PowerShell que se muestra a continuación. Para hacerlo, necesitamos instanciar un objeto `TcpClient` Socket, ya que `Test-NetConnection` envía tráfico adicional que no es necesario para nuestros propósitos.

```
1..1024 | % {echo ((New-Object
Net.Sockets.TcpClient).Connect("192.168.50.151", $_)) "TCP port $_ is open"} 2>$null
TCP port 88 is open
```

Comenzamos canalizando los primeros 1024 enteros en un bucle for que asigna el valor entero incremental a la variable `$_`. Luego, creamos un objeto `Net.Sockets.TcpClient` y realizamos una conexión TCP contra la IP objetivo en ese puerto específico, y si la conexión es exitosa, muestra un mensaje de registro que incluye el puerto TCP abierto.

A continuación, exploraremos algunas técnicas avanzadas de escaneo de redes, como la detección de servicios y la identificación de sistemas operativos.
