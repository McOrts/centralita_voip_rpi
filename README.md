# INSTALACIÓN Y CONFIGURACIÓN DE UNA CENTRALITA VOIP CON ASTERISK Y FREEPBX

Práctica de instalación y configuración de una centralita de VoIP basada en Asterisk como servidor proxy y de registro SIP. Usando el configurador FreePBX sobre una SOC de Raspberry Pi.

[Asterisk](https://www.asterisk.org/) es un software libre que hace funciones de centralita telefónica y con la cual se puede realizar la gestión de diferentes dispositivos VoIP a partir de extensiones, así como configurar mensajes automáticos, etc.

[FreePBX](https://www.freepbx.org/) es otro software libre que proporciona herramientas para construir un sistema telefónico personalizado según las necesidades.

[Raspberry Pi](https://www.raspberrypi.org/) es una serie de ordenadores de placa reducida, una sola placa contiene todos los componentes necesarios, es simple y de bajo coste. Fue desarrollada en el Reino Unido por la Raspberry Pi Foundation, con el objetivo de poner en manos de las personas de todo el mundo el poder de la informática y la creación digital.​

![](RackMultipart20210606-4-1y57iyd_html_2cd082f0de9c2ad6.png)

**Índice** :

**[Instalación](#_a3lc5ewvi0cd) 5**

[Requisitos de hardware y software](#_e0p2zz5bgffm) 5

[Arquitectura de sistemas para la PoC](#_rg3dhidmm64i) 5

[Instalación de Asterisk-FreePBX](#_6gkr19yeirqx) 5

[Añadir extensiones y teléfonos](#_66zhbi9azp82) 12

[Configurar un softphone](#_tuxbn8f9lwbi) 13

[Configurar un Teléfono IP](#_1y7p0ryts568) 15

[Analizando la configuración](#_4fkvd3tbov1u) 16

[Haciendo la primera llamada](#_9nucbw2effry) 19

![](RackMultipart20210606-4-1y57iyd_html_389e1f00b5218e9d.jpg)

##


## Instalación

## **Requisitos de hardware y software**

- **Raspberry Pi (RPi)**: Al menos el modelo 3B.
- **Tarjeta de memoria micro SD:** de al menos 8 GB de capacidad.
- **1 lector de tarjetas de memoria SD**
- **1 cable HDMI**
- **1 teclado USB**
- **1 alimentador de cable micro USB**
- **Software** [**BalenaEtcher**](https://www.balena.io/etcher/) **.** para grabar la imagen ISO en la tarjeta SD

## **Arquitectura de sistemas para la PoC**

## ![](RackMultipart20210606-4-1y57iyd_html_17ab1e41dca0c682.png)


Dispondremos de un router con servidor DHCP con funciones de switch para dar conectividad a todos los dispositivos cableados: la Raspberry Pi, el punto de acceso wifi, los teléfonos IP y los PC con _ShoftPhone_.

El Punto de Acceso wifi se ha montado para suplir la carencia de conectividad WiFi del router. Así podremos utilizar _SmartPhone_ con aplicaciones _ShoftPhone_ para llamar.

## **Instalación de Asterisk-FreePBX**

![](RackMultipart20210606-4-1y57iyd_html_985ca9523768a56f.png) ![](RackMultipart20210606-4-1y57iyd_html_3a66a48c23244a76.png)

###


###


###


###


1. Lo primero y más importante es descargar el archivo de imagen de Asterisk y asegurarse de extraer el archivo .img del archivo zip. [Este es el sitio web oficial para la descarga](https://oeey72xjc477aind5jd4le7iai-ac4c6men2g7xr2a-raspberry-asterisk.translate.goog/downloads/). Esto nos descargará el fichero raspbx-10-10-2020.zip
2. Descomprimimos el ,zip obteniendo un archivo .img
3. Descargamos, instalamos y ejecutamos el software [BalenaEtcher](https://www.balena.io/etcher/). Con el lector de tarjetas SD conectado y la tarjeta micro SD introducida, se hace la grabación de la imagen utilizando esta herramienta:

![](RackMultipart20210606-4-1y57iyd_html_a1049fb2d9bef320.png)

1. Una vez que el proceso anterior ha acabado. Desconectamos la tarjeta y la introducimos en la RPi.
2. Conectamos la RPi al monitor y teclado. Y la encendemos. Mostrará el siguiente proceso de arranque:

![](RackMultipart20210606-4-1y57iyd_html_3dd97284ae8eb507.png)

1. Una vez que aparece el mensaje de inicio de sesión, se ingresa el nombre de usuario como &quot; **root**&quot; y la contraseña como &quot; **raspberry**&quot;. Seguidamente la consola nos confirmará la disponibilidad de la aplicación RaspPBX con mensajes de versión y la lista de comandos de bajo nivel para añadir extensiones y funcionalidades:

![](RackMultipart20210606-4-1y57iyd_html_f9a042964b50cb03.png)i

1. Aquí hay dos opciones de configuración para la conexión a red:
  1. Por cable. No hay que nacer nada. Ya que obtendrá su IP del servidor DHCP. Pero tendremos que averiguar la IP asignada. Se puede utilizar el comando: ifconfig
  2. O podemos configurar la Wifi manualmente con una IP fija (asegurarse que está libre para que no haya conflictos):
    1. Ingrese el siguiente comando para configurar WiFi escribiendo una configuración de este tipo
    2. sudo nano /etc/network/interfaces

auto wlan0

allow-hotplug wlan0

iface wlan0 inet static

address 192.168.1.199

netmask 255.255.255.0

gateway 192.168.1.1

wpa-conf /etc/wpa\_supplicant/wpa\_supplicant.conf

    1. Ctrl-X, luego escriba Y para salir y guardar
    2. Ingrese el siguiente comando:
    3. sudo nano /etc/wpa\_supplicant/wpa\_supplicant.conf

network={

ssid=&quot;YOUR\_NETWORK\_NAME&quot;

psk=&quot;YOUR\_NETWORK\_PASSWORD&quot;

proto=RSN

key\_mgmt=WPA-PSK

pairwise=CCMP

auth\_alg=OPEN

}

    1. Ctrl-X, luego escriba Y para salir y guardar
    2. Escriba reiniciar y presione enter para reiniciar raspberry pi
    3. Una vez logueado en el sistema con el usuario **root** y la password elegida. Tecleamos el siguiente comando para ver qué conectividad tenemos y la IP asignada:
    4. ifconfig

![](RackMultipart20210606-4-1y57iyd_html_611741c56222b869.png)

1. Ahora ya podemos entrar en la aplicación FreePBX: desde nuestro navegador favorito sencillamente poniendo la dirección de la IP de la RPI, que en nuestro caso es 192.168.1.119.
2. En el primer acceso se nos va a pedir el usuario y contraseña del administrador. Así como una dirección de mail:

![](RackMultipart20210606-4-1y57iyd_html_26219f1ce6fa2610.png)

1. Hacemos clic en Administración de FreePBX

![](RackMultipart20210606-4-1y57iyd_html_57f049d95105be71.png)

1. Escribimos el nombre de usuario (admin) y la contraseña que se creó en el paso anterior.
2. Hacemos clic en Continuar

![](RackMultipart20210606-4-1y57iyd_html_a914f4aceb817cfc.png)

1. Seleccionamos las siguientes configuraciones:
  1. **Idioma del mensaje** de sonido: seleccionamos el idioma del mensaje en el menú desplegable
  2. **Idioma del sistema** : seleccionamos el idioma del sistema en el menú desplegable
  3. **Zona horaria** : seleccionamos la zona horaria adecuada
2. Hacemos clic en Enviar

![](RackMultipart20210606-4-1y57iyd_html_f7baa511cea63f4f.png) ![](RackMultipart20210606-4-1y57iyd_html_21e65bab08b1aac.png)

1. El Asterisk / FreePBX está listo para usar:

##


## **Añadir extensiones y teléfonos**

Una vez logueado como administrador tendremos la capacidad de añadir extensiones. Iniciamos el formulario desde la opción de Aplicaciones/Extensiones:

![](RackMultipart20210606-4-1y57iyd_html_3a60d17afc97ff5f.png)

La configuración más adecuada para hacer una conexión sencilla sin tener que hacer configuraciones adicionales en nuestros elementos de red como abrir puertos, es el tipo SIP. Y preferiblemente la versión _&#39;chan\_pjsip&#39;_ por compatibilidad con programas _softphone._ ![](RackMultipart20210606-4-1y57iyd_html_d3d3e11fcc34e70a.png)

En el formulario de la configuración de la extensión, sólo tendremos que rellenar los tres campos imprescindibles del número de extensión, nombre y clave:

![](RackMultipart20210606-4-1y57iyd_html_65088f11944b3aac.png)

Tras pulsar el botón de _Submit_. Esta nueva entrada se añadirá a la lista de extensiones. Pero hasta que no apliquemos los cambios pulsando _Apply Config_, esta configuración no quedará guardada. Si la Raspberry Pi es un modelo antiguo (3 o 2) el proceso puede tardar más de un minuto.

![](RackMultipart20210606-4-1y57iyd_html_c4a77dcdde13999e.png)

### **Configurar un** _ **softphone** _ ![](RackMultipart20210606-4-1y57iyd_html_9af120dabd75d2fc.png)

Vamos a utilizar [Zoiper](https://www.zoiper.com/)tanto en un equipo Windows 10 como en iOS. Por lo que, una vez descargado el programa, lo instalaremos.

Tras ejecutar el programa y aceptar su uso bajo licencia o libre. Nos pedirá la IP de nuestro servidor Asterisk para comprobar la conectividad.

Si obtenemos verde en el protocolo UDP, podremos pasar a añadir una cuenta (account) desde el botón [Add]:

![](RackMultipart20210606-4-1y57iyd_html_37d54ea65f01f69.png)

La configuración es tan simple como poner la IP de la Raspberry Pi en el campo _Domain_ el user name coincide con la extensión y el _Password_ registrado anteriormente en la consola de administración de FreePBX.

![](RackMultipart20210606-4-1y57iyd_html_c98fb915d2df9764.png)

Cuando la aplicación se conecte con el servidor, aparecerá el símbolo de ok en color verde pudiendo hacer y recibir llamadas.

El registro de todas las llamadas queda reflejado en la parte derecha de la aplicación.

### **Configurar un** _ **Teléfono IP** _

A fin de hacer la práctica lo más completa posible. Vamos a configurar un teléfono IP aprovechando que el router utilizad (MikroTik RB201) tiene activado el servido DHCP que nos asignará IP al teléfono.

La configuración tendremos que hacerla tanto en FreePBX añadiendo una nueva extensión también del tipo PJSIP. Como en el propio terminal. Para lo que tenemos dos opciones: hacerlo desde las opciones de menú de su propio display. O desde la página web de configuración que nos sirve el teléfono cuando accedemos desde un navegador a su IP.

![](RackMultipart20210606-4-1y57iyd_html_35f7284de7cb817.png)

En cualquier caso tendremos que seguir estos pasos de configuración:

1. Selecciona el menú desplegable &quot;Cuentas&quot;\&gt; Selecciona &quot;Cuenta 1&quot;\&gt; Selecciona &quot;Configuración general&quot;
  1. &quot;Nombre de cuenta&quot; debe ser la extensión que se creó en FreePBX
  2. &quot;Servidor&quot; será la IP
  3. El &quot;ID de usuario SIP&quot; es el número de extensión
  4. El &quot;ID de autenticación&quot; es el número de extensión
  5. Pegue la clave secreta de FreePBX en el cuadro de texto &quot;Autenticar contraseña&quot;
  6. &quot;Nombre&quot; puede ser lo que se quiera. P.e. &quot;TestPhone&quot;
2. Vuelva a la administración de Grandstream\&gt; Selecciona: &quot;Cuenta 1&quot;\&gt; Seleccione &quot;Configuración general&quot;
3. Poner la dirección IP del paso anterior en el campo de texto &quot;Servidor SIP&quot;
4. Hacer clic en &quot;Guardar&quot; para aplicar los cambios de configuración.
5. Seleccionar la &quot;Configuración SIP&quot;\&gt; &quot;Configuración básica&quot;.
6. Seleccionar el &quot;Puerto SIP local&quot; en el campo de texto (cada teléfono único debe tener su propia entrada única)

### **Analizando la configuración**

Las configuraciones hechas a través de la aplicación frontal de FreePBX va quedar grabadas en varios ficheros del sistema que podemos ver y que no debemos modificar tal y como advierten los mensajes que contienen.

- **sip\_additional.conf**

Este archivo contiene los detalles de las extensiones configuradas con el tipo SIP que la aplicación tiene identificada como _Legacy_. O sea en desuso.

![](RackMultipart20210606-4-1y57iyd_html_5e7906538ee0b376.png)

- **jpsip.aor\_custom.conf**

Las extensiones configuradas con el tipo JPSIP quedan registradas en una serie de archivos. Este contiene un resumen con algunos parámetros de limitación u caducidad:

![](RackMultipart20210606-4-1y57iyd_html_523abd794ad47e0b.png)

- **extensions\_additional.conf**

En este archivo encontraremos en el apartado [ext-local] la relación de todas extensiones configuradas manualmente con el tipo en la última línea.

![](RackMultipart20210606-4-1y57iyd_html_4c2c081c1e8711d0.png)

Por otra parte es posible acceder al programa nativo de administración de consola de Asterisk como si se tratara de un servidor sin FreePBX.

![](RackMultipart20210606-4-1y57iyd_html_279e250325c49a82.png)

Si estamos trabajando con extensiones de tipo PJSIP, el comando: &quot;pjsip list endpoints&quot; es muy útil para listar los usuarios y su estado.

## ![](RackMultipart20210606-4-1y57iyd_html_6020bd49e422daf8.png) **Haciendo la primera llamada**

Una vez asegurada la asignación de IP a cada uno de los teléfonos IP físicos o por software. Podremos hacer llamadas indiscriminadamente entre ellos ya que no hemos configurado ningún tipo de restricción.

Por parte del servidor FreePBX tenemos un _dashboard_ muy completo con la información de configuración y estado más relevante. En el marco de _Summary_ tenemos una _checklist_ que debe estar toda en verde.

![](RackMultipart20210606-4-1y57iyd_html_21e65bab08b1aac.png)

![](RackMultipart20210606-4-1y57iyd_html_4f38e851009174df.png)

En este punto ya podemos hacer una llamada. Por ejemplo, desde la aplicación del smartphone donde tenemos configurada la terminal 102, vamos a llamar al número 101 que es el _shoftphone_ en un ordenador portátil. ![](RackMultipart20210606-4-1y57iyd_html_3d425576e59512e3.png)

![](RackMultipart20210606-4-1y57iyd_html_ebe592c36f7a20d2.jpg)

![](RackMultipart20210606-4-1y57iyd_html_1e3b2f10e98a9d53.jpg)

Página 6
