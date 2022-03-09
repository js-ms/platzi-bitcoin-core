# Platzi-bitcoin-core
Repositorio que contiene script y guía para montar tu propio nodo de Bitcoin

### Disco:

Si usas Bitcoin Core en mainnet, configura un disco(en la nube, externo, etc)
que te permita almacenar la cadena de bloques(Blockchain) completa.

Cadena de bloques e indice de transacciones: 600 GB aproximadamente.

Para el ejemplo del curso usaremos Testnet, necesitas 60 GB aproximadamente para almacenar la cadena de bloques.

Instala tu editor favorito, como emacs o usa nano o vi(por defecto)
```shell
sudo apt update && sudo apt upgrade -y && sudo apt install -y emacs

# abrelo y luego ciérralo
emacs

# cambia el dueño de la configuraci+on de emacs
sudo chown -R ubuntu ~/.emacs.d
```

Si usas un disco externo para la cadena, que no ha sido inicializado, comienza por inicializarlo con un nombre como /blockchain-data/

```shell

# Revisa el almacenamiento
lsblk
# Obtendras el nombre por defecto del volumen xxxxx

# Revisa que tu almacenamiento esté vacío
sudo file -s /dev/xxxxx
# Debería mostrarte algo como "/dev/xxxxx: data" que sifnifica que está vacío

# Dale formato al almacenamiento como ext4
sudo mkfs -t ext4 /dev/xxxxx

# Crea un directorio para el volumen y montalo
sudo mkdir /blockchain-data
sudo mount /dev/xxxxx /blockchain-data/
cd /blockchain-data

# Revisa si tienes espacio suficiente
df -h .
# Deberia mostrarte el espacio dentro del volumen

# Monta la partición de manera automática, pero primera crea un backup de la configuración
sudo cp /etc/fstab /etc/fstab.bak
sudo vi /etc/fstab

# Crea una nueva entrada en el archivo
/dev/xxxxx /blockchain-data ext4 defaults,nofail 0 0

# Guarda y montalo, luego prueba:
sudo mount -a
# No debería mostrar errores

# Apropiate del directorio:
sudo chown `whoami` /blockchain-data
```

## Usar Tor

Si quieres correr tu nodo detrás de Tor, aigue estas instrucciones, si no, continúa.

Instrucciones:

```shell
sudo apt-get update && sudo apt install -y apt-transport-https

# Edita las fuentes para instalar los paquetes
sudo vi /etc/apt/sources.list.d/tor.list

deb https://deb.torproject.org/torproject.org focal main
deb-src https://deb.torproject.org/torproject.org focal main

# Obtén la llave PGP para Tor y agregala a GPG
sudo curl https://deb.torproject.org/torproject.org/A3C4F0F979CAA22CDBA8F512EE8CBC9E886DDD89.asc | sudo gpg --import
sudo gpg --export A3C4F0F979CAA22CDBA8F512EE8CBC9E886DDD89 | sudo apt-key add -

# Instala tor
sudo apt update && sudo apt install -y tor deb.torproject.org-keyring

# Agrega un usuario para tor
sudo usermod -a -G user-tor `whoami`
```

Configura Tor:

```shell
# Edita la configuración
sudo vi /etc/tor/torrc
```

```
# Añade esta líneas en la el encabezado del archivo:

ControlPort 9051
CookieAuthentication 1
CookieAuthFileGroupReadable 1
Log notice stdout
SOCKSPort 9050
```

```shell
# Reinicia el servicio
sudo service tor restart
```

Revisa si el servicio está funcionando

```shell
curl --socks5 localhost:9050 --socks5-hostname localhost:9050 -s https://check.torproject.org/ | cat | grep -m 1 Congratulations | xargs
```

Esto debe mostrarte un  `Congratulations`


## Instalando Bitcoin Core

### Instalación:

```shell
# Instala las dependencias necesarias para correr bitcoin core 
# ccache no es requerida pero puede servirte. https://github.com/ccache/ccache
# Si quieres conocer más https://github.com/bitcoin/bitcoin/blob/master/doc/dependencies.md 
sudo apt install git build-essential libtool autotools-dev automake pkg-config libssl-dev libevent-dev bsdmainutils libboost-system-dev libboost-filesystem-dev libboost-chrono-dev libboost-program-options-dev libboost-test-dev libboost-thread-dev libminiupnpc-dev libzmq3-dev

# Clona el código fuente de github, puedes hacerlo desde el repositorio de Bitcoin o través de un fork que generes
git clone -b v22.0 https://github.com/bitcoin/bitcoin.git

# Muévete al directorio
cd bitcoin/

# Corre este comando para poder usar BerkeleyDB (Base de datos para la billetera)
./contrib/install_db4.sh `pwd`

# Ten en cuenta la salida del proceso anterior, el export te servirá más adelante

# Ejecuta export BDB_PREFIX='<PATH-TO>/db4' antes de correr el script autogen.sh
./autogen.sh

# En tu configure puedes usar cuantas banderas necesites
# Para configurar tu complilación, por ejemplo CXXFLAGS u otras banderas como --with-zmq, --without-gui BDB libs es necesario para que puedas usar la billetera, recuerda que esto es solo un ejemplo, a nivel productivo no deberías usar la billetera.
# Puedes ejecutar ./configure --help para encontrar todas las opciones posibles.
./configure BDB_LIBS="-L${BDB_PREFIX}/lib -ldb_cxx-4.8" BDB_CFLAGS="-I${BDB_PREFIX}/include" CXXFLAGS="--param ggc-min-expand=1 --param ggc-min-heapsize=32768" --with-zmq --without-gui 

# make construye tu programa, usamos nproc para conocer cuantos núcleos tiene nuestro procesador.
# -j especifica el número de tareas a correr de manera simultánea
# Esto puede tomar un tiempo....
make -j "$(($(nproc)+1))"

# Puedes usar ccache para acelerar el proceso si reconstruyes múltiples veces, para más información https://github.com/bitcoin/bitcoin/blob/master/doc/productivity.md 

# Make install permite usar bitcoind y bitcoin-cli en cualquier parte de nuestro sistema operativo
sudo make install
```

### Pruebas Unitarias:

- `make check`, o
- `make -j "$(($(nproc)+1))" check` para usar múltiples hilos en Linux

### Pruebas Funcionales: 

* `test/functional/test_runner.py` o el mismo comando con `-j 20` para correr las pruebas más rápido en paralelo  `--extended` para correr la *suite* de pruebas extendida  o usa  `--help` para revisar cómo puedes ejecutar las pruebas 

### Configuración:

Configura los directorios en el almacenamiento que configuraste(blockchain-data) y crea el directorio de datos donde los archivos de Bitcoin estarán almacenados, incluyendo el archivo de la billetera.

```shell
mkdir -p /blockchain-data/.bitcoin/data && mkdir ~/.bitcoin
```

Descarga y usa el  [script](https://github.com/bitcoin/bitcoin/blob/master/share/rpcauth/rpcauth.py) para generar las credenciales RPC.

```shell
wget https://raw.githubusercontent.com/bitcoin/bitcoin/master/share/rpcauth/rpcauth.py
python ./rpcauth.py bitcoinrpc
# Este script te entregará el string de autenticación para añadir al archivo bitcoin.conf
# Guarda la contraseña, te servirá para servicios que generes más adelante (Ejemplo, lightning)
```

Edita el archivo de configuración.

```shell
vi ~/.bitcoin/bitcoin.conf
```

Añade lo siguiente

```ini
# Añade la mejor altura:
# Si tienes otro nodo, utiliza getbestblockhash para agregar este valor, ejemplo: 00000000000000522f7f46b8c1ba3cee6287535b124ecbd00ac7cb911d5a573
assumevalid=0

# Correlo como daemon
daemon=1

# Elige en qué red vas a correr tu nodo, tambien puedes usar mainnet, regtest o signnet
testnet=1 

# Configura el directorio de datos
datadir=/blockchain-data/.bitcoin/data

# Configura el numero de megabytes usados por RAM
dbcache=3000

# Añade visibilidad al mempool y llamados RPC para debug
debug=mempool
debug=rpc

# Escucha a tus pares
listen=1

# Pon un límite al número de megabytes necesitados en el mempool
maxmempool=100

# Limita la carga a los pares 
maxuploadtarget=1000

# Apaga SPV nodes.
nopeerbloomfilters=1
peerbloomfilters=0

# Configura la autenticación RPC con el valor que obtuviste al correr el script
rpcauth=

# Prende el servidor RPC
server=1

# Reduce el tamaño del archivos de logs al reiniciar el nodo
shrinkdebuglog=1

# Iniciar el índice de busqueda de transacciones
txindex=1

# Enciende la publicación ZMQ
zmqpubrawblock=tcp://127.0.0.1:28332
zmqpubrawtx=tcp://127.0.0.1:28333

```

Existen muchos otros parámetros que puedes añadir dependiendo de tus necesidades, puedes encontrar un ejemplo [aquí](https://github.com/bitcoin/bitcoin/blob/master/share/examples/bitcoin.conf)

**Nota**:  Si usas Tor debes añadir algunos pares ya conocidos usando `addnode=addnode=direccion.onion` , Puedes agregar cuantos pares quieras, debes conectarte al proxy usando `proxy=anfitrion:puerto`.

Inicia Bitcoin Core:

```shell
bitcoind
```

Añade Bitcoin Core a crontab:

```shell
crontab -e
```

Añade la siguiente línea:

```
# Inicia Bitcoin Core al iniciar el sistema
@reboot /usr/local/bin/bitcoind
```

Crea un link para revisar el log de Bitcoin Core

```shell
# Mainnet:
ln -s /blockchain/.bitcoin/data/debug.log ~/bitcoind-mainnet.log

# Testnet:
ln -s /blockchain/.bitcoin/data/testnet3/debug.log ~/bitcoind-testnet.log
```

Tu nodo ya está corriendo, puedes pasar a la sección bitcoin-cli para encontrar los comandos que puedes ejecutar.



