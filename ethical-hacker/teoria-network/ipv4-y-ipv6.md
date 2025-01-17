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

# IPv4 y IPv6

### La Dirección IPv4

Un host necesita una dirección IPv4 para participar en Internet y en casi todas las LAN hoy en día. La dirección IPv4 es una dirección de red lógica que identifica a un host en particular Debe configurarse correctamente y ser única dentro de la red LAN, para posibilitar la comunicación local. También debe configurarse correctamente y ser única en el mundo, para posibilitar la comunicación remota. Así es como un host puede comunicarse con otros dispositivos en Internet.

Se asigna una dirección IPv4 a la conexión de la interfaz de red para un host. Esta conexión generalmente es una tarjeta de interfaz de red (NIC) instalada en el dispositivo. Algunos servidores pueden tener más de una NIC, y cada una de ellas tiene su propia dirección IPv4.

Cada paquete que se envía por Internet tiene una dirección IPv4 de origen y de destino.

### Octetos y notación decimal con puntos

Las direcciones IPv4 tienen 32 bits de longitud. Aquí hay una dirección IPv4 en binario:\
**11010001101001011100100000000001**

Observe lo difícil que es leer esta dirección. ¡Imagine tener que configurar dispositivos con una serie de 32 bits! Por esta razón, los 32 bits se agrupan en cuatro bytes de 8 bits llamados octetos así:\
**11010001.10100101.11001000.00000001**

El IPv4 binario anterior se convierte en esta representación decimal con puntos:\
**209.165.200.1**

### Redes y Hosts

> **¿Que es un Host?**
>
> Cuando se habla de host se refiere a los distintos dispositivos ,con el host se puede indicar que nombre de hosts corresponde a una determinada dirección IP.
>
> **Ejemplo:**\
> Cuando realiza una maquina en HackTheBox generalmente deberá dirigirse al archivo /etc/host
>
> para poder avanzar con la maquina si es un sitio web deberá colocar en el archivo:\
> 10.11.0.251  snoopy.htb
>
> Ahora con eso podrás colocar snoopy.htb en el navegador y te cargara la misma pagina que antes te cargaba con la IP.

La dirección IPv4 lógica de 32 bits  consta de dos partes, la de red y la de host. Las dos partes se requieren en una dirección IPv4. Ambas redes tienen la máscara de sub red 255.255.255.0. La máscara de sub red se utiliza para identificar la red a la que está conectado el host.

A modo de ejemplo, hay un host con la dirección IPv4 192.168.5.11 y la máscara de sub red 255.255.255.0. Los primeros tres octetos (192.168.5), identifican la porción de red de la dirección, y el último octeto (11) identifica al host. Esto se conoce como direccionamiento jerárquico, debido a que la porción de red indica la red en la que cada dirección host única está ubicada. Los enrutadores solo necesitan conocer cómo llegar a cada red en lugar de conocer la ubicación de cada host individual.

Con el direccionamiento IPv4 pueden existir varias redes lógicas en una red física, si la porción de red de las direcciones del host correspondiente a la red es diferente.&#x20;



### Unidifusión

La estructura de una dirección IPv4; cada una tiene una parte de red y una parte de host. Existen diferentes formas de enviar un paquete desde un dispositivo de origen, y estas diferentes transmisiones afectan a las direcciones IPv4 de destino.

La transmisión unidifusión se refiere a un dispositivo que envía un mensaje a otro dispositivo en comunicaciones uno a uno.

Un paquete de unidifusión tiene una dirección IP de destino que es una dirección de unidifusión que va a un único destinatario. Una dirección IP de origen sólo puede ser una dirección de unidifusión, ya que el paquete sólo puede originarse de un único origen. Esto es independiente de si la dirección IP de destino es una unidifusión, difusión o multidifusión.

### Difusión

Transmisión de transmisión hace referencia a un dispositivo que envía un mensaje a todos los dispositivos de una red en comunicaciones unipersonales.

Los paquetes de difusión tienen una dirección IPv4 de destino que contiene solo números uno (1) en la porción de host.

Todos los dispositivos del mismo dominio de difusión deben procesar un paquete de difusión. Un dominio de difusión identifica todos los hosts del mismo segmento de red. Una transmisión puede ser dirigida o limitada. Una difusión dirigida se envía a todos los hosts de una red específica. Por ejemplo, un host de la red 172.16.4.0/24 envía un paquete a la dirección 172.16.4.255. Se envía una difusión limitada a 255.255.255.255. De manera predeterminada, los enrutadores no reenvían difusiones.

Los paquetes de difusión usan recursos en la red y hacen que cada host receptor en la red procese el paquete. Por lo tanto, se debe limitar el tráfico de difusión para que no afecte negativamente el rendimiento de la red o de los dispositivos. Debido a que los enrutadores separan los dominios de difusión, la subdivisión de redes puede mejorar el rendimiento de la red al eliminar el exceso de tráfico de difusión.



### Multidifusión

La transmisión de multidifusión reduce el tráfico al permitir que un host envíe un único paquete a un grupo seleccionado de hosts que estén suscritos a un grupo de multidifusión.

Un paquete de multidifusión es un paquete con una dirección IP de destino que es una dirección de multidifusión. IPv4 reservó las direcciones de 224.0.0.0 a 239.255.255.255 como rango de multidifusión.

Los hosts que reciben paquetes de multidifusión particulares se denominan clientes de multidifusión. Los clientes de multidifusión utilizan servicios solicitados por un programa cliente para subscribirse al grupo de multidifusión.

Cada grupo de multidifusión está representado por una sola dirección IPv4 de destino de multidifusión. Cuando un host IPv4 se suscribe a un grupo de multidifusión, el host procesa los paquetes dirigidos a esta dirección de multidifusión y los paquetes dirigidos a la dirección de unidifusión asignada exclusivamente.

Los protocolos de enrutamiento como OSPF utilizan transmisiones de multidifusión. Por ejemplo, los routeres habilitados con OSPF se comunican entre sí mediante la dirección de multidifusión OSPF reservada 224.0.0.5. Sólo los dispositivos habilitados con OSPF procesarán estos paquetes con 224.0.0.5 como dirección IPv4 de destino. Todos los demás dispositivos ignorarán estos paquetes.

### Direcciones IPv4 Públicas y Privadas

Del mismo modo que hay diferentes formas de transmitir un paquete IPv4, también hay diferentes tipos de direcciones IPv4. Algunas direcciones IPv4 no se pueden usar para salir a Internet, y otras se asignan específicamente para enrutar a Internet. Algunos se utilizan para verificar una conexión y otros se autoasignan. Como administrador de red, eventualmente se familiarizará con los tipos de direcciones IPv4, pero por ahora, al menos debe saber qué son y cuándo usarlas.

Las direcciones IPv4 públicas son direcciones que se enrutan globalmente entre routeres de proveedores de servicios de Internet (ISP). Sin embargo, no todas las direcciones IPv4 disponibles pueden usarse en Internet. Existen bloques de direcciones denominadas direcciones privadas que la mayoría de las organizaciones usan para asignar direcciones IPv4 a los hosts internos.

{% code fullWidth="false" %}
```

Rango de direcciones	        Tipo de dirección
0.0.0.0 a 0.255.255.255	        Privado
10.0.0.0 a 10.255.255.255	Privado
172.16.0.0 a 172.31.255.255	Privado
192.168.0.0 a 192.168.255.255	Privado
169.254.0.0 a 169.254.255.255	Auto-configuración de direcciones IP
Todo lo demás	                Público
```
{% endcode %}

### Direcciones IPv4 de uso especial

Existen ciertas direcciones, como la dirección de red y la dirección de difusión, que no se pueden asignar a los hosts. También hay direcciones especiales que pueden asignarse a los hosts, pero con restricciones respecto de la forma en que dichos hosts pueden interactuar dentro de la red.

**Direcciones de loopback**

Las direcciones de bucle invertido (loopback) (127.0.0.0 /8 o 127.0.0.1 a 127.255.255.254) se identifican más comúnmente como solo 127.0.0.1. Estas son direcciones especiales utilizadas por un host para dirigir el tráfico hacia sí mismo. Por ejemplo, el comando **ping** se usa comúnmente para probar conexiones a otros hosts.

**Direcciones de enlace local**

Direcciones link-local o direcciones IP privadas automáticas (APIPA) 169.254.0.0/16o 169.254.0.1 a 169.254.255.254 Los utiliza un cliente de Windows para auto configurarse en caso de que el cliente no pueda obtener un direccionamiento IP a través de otros métodos. Las direcciones locales de vínculo se pueden utilizar en una conexión de punto a punto, pero no se utilizan comúnmente para este propósito.

### Ipv6

> NAT, o Network Address Translation, es un mecanismo que permite a dispositivos con direcciones IP privadas comunicarse con dispositivos con direcciones IP públicas.

IPv6 está diseñado para ser el sucesor de IPv4. IPv6 tiene un espacio de direcciones más grande de 128 bits, que proporciona 340 undecillones (es decir, 340 seguidos de 36 ceros) posibles direcciones. Sin embargo, IPv6 es más que solo direcciones más extensas.

Cuando el IETF comenzó a desarrollar un sucesor de IPv4, aprovechó esta oportunidad para corregir las limitaciones de IPv4 e incluir mejoras. Un ejemplo es el Protocolo de mensajes de control de Internet versión 6 (ICMPv6), que incluye la resolución de direcciones y la configuración automática de direcciones que no se encuentran en ICMP para IPv4 (ICMPv4).

El agotamiento del espacio de direcciones IPv4 fue el factor que motivó la migración a IPv6. A medida que África, Asia y otras áreas del mundo están más conectadas a Internet, no hay suficientes direcciones IPv4 para acomodar este crecimiento. Como se muestra en la ilustración, a cuatro de cinco Registros Regionales de Internet (RIR) se les agotaron las direcciones IPv4.

### Las técnicas de migración de IPv4 a IPv6

#### Doble Pila/Dual stack

Doble pila permite que IPv4 e IPv6 coexistan en el mismo segmento de red. Los dispositivos dual-stack ejecutan pilas de protocolos IPv4 e IPv6 de manera simultánea.

#### Tunelización

La tunelización es un método para transportar un paquete IPv6 a través de una red IPv4. El paquete IPv6 se encapsula dentro de un paquete IPv4.

#### Traducción

La Traducción de Direcciones de Redes 64 (NAT64) permite que los dispositivos con IPv6 habilitado se comuniquen con dispositivos con IPv4 habilitado mediante una técnica de traducción similar a la NAT para IPv4.

### Formatos de direccionamiento IPv6

Las direcciones IPv6 son mucho más grandes que las direcciones IPv4, por lo que es poco probable que se nos quede sin ellas.

Las direcciones IPv6 tienen una longitud de 128 bits y se escriben como una cadena de valores hexadecimales. Cada cuatro bits está representado por un solo dígito hexadecimal; para un total de 32 valores hexadecimales, como se muestra en la figura. Las direcciones IPv6 no distinguen entre mayúsculas y minúsculas, y pueden escribirse en minúsculas o en mayúsculas.

### Omitir ceros iniciales

La primera regla para ayudar a reducir la notación de las direcciones IPv6 es omitir los ceros iniciales en cualquier hexteto, omitir ceros a la izquierda:

* 01ab se puede representar como 1ab
* 09f0 se puede representar como 9f0
* 0a00 se puede representar como a00
* 00ab se puede representar como ab

Esta regla solo es válida para los ceros iniciales, y NO para los ceros finales; de lo contrario.

### Dos puntos dobles

La segunda regla para ayudar a reducir la notación de las direcciones IPv6 es que dos puntos dobles (: :) puede reemplazar cualquier cadena única y contigua de uno o más hextetos de 16 bits que consisten en todos los ceros. Por ejemplo, 2001:db8:cafe: 1:0:0:0:1 (0 iniciales omitidos) podría representarse como 2001:db8:cafe:1: :1. Los dos puntos dobles (: :) se utilizan en lugar de los hextetos de tres ceros (0: 0: 0).

Los dos puntos dobles (::) se pueden utilizar solamente una vez dentro de una dirección; de lo contrario, habría más de una dirección resultante posible. Cuando se utiliza junto con la técnica de omisión de ceros iniciales, la notación de direcciones IPv6 generalmente se puede reducir de manera considerable. Esto se suele conocer como “formato comprimido”.

Aquí hay un ejemplo del uso incorrecto del dos puntos dobles: 2001:db8: :abcd: :1234.

Los dos puntos dobles se utilizan dos veces en el ejemplo anterior. Aquí están las posibles expansiones de esta dirección de formato comprimido incorrecto:

* 2001:db8::abcd:0000:0000:1234
* 2001:db8::abcd:0000:0000:0000:1234
* 2001:db8:0000:abcd::1234
* 2001:db8:0000:0000:abcd::1234

Si una dirección tiene más de una cadena contigua de hextetos, todos 0, la práctica recomendada es usar los dos puntos dobles (::) en la cadena más larga. Si las cadenas son iguales, la primera cadena debe usar los dos puntos dobles (::).

### Encabezados Paquete IPV4

El encabezado del paquete IPv4 se utiliza para garantizar que este paquete se entrega en su siguiente parada en el camino a su dispositivo final de destino.

El encabezado de paquetes IPv4 consta de campos que contienen información importante sobre el paquete.

### Campos del Encabezado del Paquete IPV4

Los campos significativos en el encabezado IPv4 incluyen lo siguiente:

* **Versión -** Contiene un valor binario de 4 bits establecido en 0100 que identifica esto como un paquete IPv4.
* **Servicios Diferenciados o DiffServ (DS) -** Este campo, formalmente conocido como Tipo de servicio (ToS), es un campo de 8 bits que se utiliza para determinar la prioridad de cada paquete. Los seis bits más significativos del campo DiffServ son los bits de punto de código de servicios diferenciados (DSCP) y los dos últimos bits son los bits de notificación de congestión explícita (ECN).
* **Tiempo de Duración (Time to Live, TTL) -** El TTL contiene un valor binario de 8 bits que se utiliza para limitar la vida útil de un paquete. El dispositivo de origen del paquete IPv4 establece el valor TTL inicial. Se reduce en uno cada vez que el paquete es procesado por un router. Si el campo TTL llega a cero, el router descarta el paquete y envía a la dirección IP de origen un mensaje de tiempo superado del protocolo de mensajes de control de Internet (ICMP). Debido a que el router disminuye el TTL de cada paquete, el router también debe volver a calcular la suma de comprobación del encabezado.
* **Protocolo -** Este campo se utiliza para identificar el protocolo del siguiente nivel. Este valor binario de 8 bits indica el tipo de carga de datos que lleva el paquete, lo que permite que la capa de red transmita los datos al protocolo de capa superior apropiado. ICMP (1), TCP (6) y UDP (17) son algunos valores comunes.
* **Suma de comprobación de encabezado -** Se utiliza para detectar daños en el encabezado IPv4.
* **Dirección IPv4 de origen -** Contiene un valor binario de 32 bits que representa la dirección IPv4 de origen del paquete. La dirección IPv4 de origen es siempre una dirección unicast.
* **Dirección IPv4 de destino -** Contiene un valor binario de 32 bits que representa la dirección IPv4 de destino del paquete. La dirección IPv4 de destino es una dirección unicast, multicast o de difusión.

### Encabezado del Paquete IPv6

Los campos en el encabezado del paquete IPv6 incluyen lo siguiente:

* **Versión -** Este campo contiene un valor binario de 4 bits establecido en 0110 que identifica esto como un paquete IP versión 6.
* **Clase de Tráfico -** Este campo de 8 bits es el equivalente al campo Servicios Diferenciados (DS) de IPv4.
* **Etiqueta de Flujo -** Este campo de 20 bits sugiere que todos los routers manipulen del mismo modo los paquetes con la misma etiqueta de flujo.
* **Longitud de Carga Útil -** Este campo de 16 bits indica la longitud de la parte de los datos o la carga útil del paquete IPv6. Esto no incluye la longitud del encabezado IPv6, que es un encabezado fijo de 40 bytes.
* **Siguiente Encabezado -** Este campo de 8 bits equivale al campo Protocolo IPv4. Es un valor que indica el tipo de contenido de datos que lleva el paquete, lo que permite que la capa de red transmita la información al protocolo de capa superior apropiado.
* **Límite de Saltos -** Este campo de 8 bits reemplaza al campo TTL de IPv4. Cada router que reenvía el paquete reduce este valor en 1. Cuando el contador llega a 0, el paquete se descarta y se reenvía un mensaje ICMPv6 Tiempo excedido al host emisor. Esto indica que el paquete no llegó a su destino porque se excedió el límite de saltos. A diferencia de IPv4, IPv6 no incluye una suma de comprobación de encabezado IPv6, ya que esta función se realiza tanto en las capas inferior como superior. Esto significa que la suma de comprobación no necesita ser recalculada por cada router cuando disminuye el campo Límite de saltos, lo que también mejora el rendimiento de la red.
* **Dirección IPv6 de Origen -** Este campo de 128 bits identifica la dirección IPv6 del host emisor.
* **Dirección IPv6 de Destino -** Este campo de 128 bits identifica la dirección IPv6 del host receptor.

#### Referencias

{% embed url="https://skillsforall.com/course/networking-basics?courseLang=en-US" %}

{% embed url="https://skillsforall.com/course/networking-devices-and-initial-configuration?courseLang=en-US" %}
