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
