# platzi-bitcoin-core
Repositorio que contiene script y guía para montar tu propio nodo de Bitcoin

### Disco:

Si usas Bitcoin Core en mainnet, configura un disco(en la nube, externo, etc)
que te permita almacenar la cadena de bloques(Blockchain) completa.

Cadena de bloques e indice de transacciones: 600 GB aproximadamente.

Instala tu editor favorito, como emacs o usa nano(por defecto)
```shell
sudo apt update && sudo apt upgrade -y && sudo apt install -y emacs

# abrelo y luego ciérralo
emacs

# cambia el dueño de la configuraci+on de emacs
sudo chown -R ubuntu ~/.emacs.d
```

Si usas un disco externo para la cadena, que no ha sido inicializado, comienza por 
inicializarlo con un nombre como /blockchain-data/

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
sudo emacs /etc/fstab

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
sudo emacs /etc/apt/sources.list.d/tor.list

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
sudo emacs /etc/tor/torrc
```

```
# Añande esta líneas en la el encabezado del archivo:

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

Instalación:

```
sudo apt install git build-essential libtool autotools-dev automake pkg-config libssl-dev libevent-dev bsdmainutils libboost-system-dev libboost-filesystem-dev libboost-chrono-dev libboost-program-options-dev libboost-test-dev libboost-thread-dev libminiupnpc-dev libzmq3-dev
git clone -b v22.0 https://github.com/bitcoin/bitcoin.git
cd bitcoin/
./autogen.sh
./configure CXXFLAGS="--param ggc-min-expand=1 --param ggc-min-heapsize=32768" --enable-cxx --with-zmq --without-gui --disable-shared --with-pic --disable-tests --disable-bench --enable-upnp-default --disable-wallet
# Esto puede tomar un tiempo....
make -j "$(($(nproc)+1))"
sudo make install
```

Configura los directorios en el almacenamiento de volumen y crea eñ directorio para el archivot de configuración:

```shell
mkdir -p /blockchain-data/.bitcoin/data && mkdir ~/.bitcoin
```

Descarga y usa el [script] para generar las credenciales.

```shell
wget https://raw.githubusercontent.com/bitcoin/bitcoin/master/share/rpcauth/rpcauth.py
python ./rpcauth.py bitcoinrpc
# Esto pondrá la autenticación en bitcoin.conf
# Guarda la contraseña, te servirá para servicios que crees
```

Edita el archivo de configuración.

```shell
emacs ~/.bitcoin/bitcoin.conf
```

Añade lo siguiente

```ini
# Añade la mejor altura:
assumevalid=

# Correlo como daemon
daemon=1

# Configura el archivo de almacenamiento
datadir=/blockchain-data/.bitcoin/data

# Configura el numero de megabytrd usados por RAM, osea el 50% aproximadamente
dbcache=3000

# Añade visibilidad al mempool y llamados RPC para debug
debug=mempool
debug=rpc

# Apaga la billetera, no la usaremos
disablewallet=1

# Si usas tor, no te preocupes por escuchar a. pares.
listen=0

# Pon un límite al número de megabytes necesitados en el mempool
maxmempool=100

# Limita la carga a los pares 
maxuploadtarget=1000

# Apaga SPV nodes.
nopeerbloomfilters=1
peerbloomfilters=0

# No aceptes multisig deprecado
permitbaremultisig=0

# Configura la autenticación RPC
rpcauth=

# Prende el servidor RPC
server=1

# Reduce el tamaño del archivos de logs al reiniciar el nodo
shrinkdebuglog=1

# Configura testnet si es necesario 
testnet=1

# Iniciar el índice de busqueda de transacciones
txindex=1

# Enciende la publicación ZMQ
zmqpubrawblock=tcp://127.0.0.1:28332
zmqpubrawtx=tcp://127.0.0.1:28333
```

Usas Tor? añade estas líneas:

```ini
# Peer en mainnet
addnode=gyn2vguc35viks2b.onion
addnode=kvd44sw7skb5folw.onion
addnode=nkf5e6b7pl4jfd4a.onion
addnode=yu7sezmixhmyljn4.onion
addnode=3ffk7iumtx3cegbi.onion
addnode=3nmbbakinewlgdln.onion
addnode=4j77gihpokxu2kj4.onion
addnode=546esc6botbjfbxb.onion
addnode=5at7sq5nm76xijkd.onion
addnode=77mx2jsxaoyesz2p.onion
addnode=7g7j54btiaxhtsiy.onion
addnode=a6obdgzn67l7exu3.onion
addnode=ab64h7olpl7qpxci.onion
addnode=am2a4rahltfuxz6l.onion
addnode=azuxls4ihrr2mep7.onion
addnode=bitcoin7bi4op7wb.onion
addnode=bitcoinostk4e4re.onion
addnode=bk7yp6epnmcllq72.onion
addnode=bmutjfrj5btseddb.onion
addnode=ceeji4qpfs3ms3zc.onion
addnode=clexmzqio7yhdao4.onion
addnode=gb5ypqt63du3wfhn.onion
addnode=h2vlpudzphzqxutd.onion

# Solo usa Tor
onlynet=onion

# Conectate al proxy de Tor
proxy=127.0.0.1:9050
```

Inicia Bitcoin Core:

```shell
bitcoind
```

Añade Bitcoin Core a crontab:

```shell
crontab -e
```

Añade una línea:

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





