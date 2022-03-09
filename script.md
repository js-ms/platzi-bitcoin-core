# Script

Repositorio que contiene script y guía para montar tu propio nodo de Bitcoin

Seguro ya corriste tu propio nodo y has usado `bitcoin-cli`, herramienta que permire ejecutar distintos comandos RPC que soportan la creación y control de Bitcoin; transacciones, fondos, consultas, etc.

Bitcoin incluye un lenguaje de scripting que permite crear condiciones complejas a satisfacer para redimir fondos, si las firmas y los bloqueos de tiempo son las bases de los contratos inteligentes, Bitcoin Script está fundamentado en estas herramientas, siendo el próximo paso para explotar todas las capacidades de Bitcoin.

### Transacciones

La base de Bitcoin es la habilidad de crear transacciones, a través de un lenguaje simple de programación. Cada transacción contiene un problema criptográfico que bloquea los fondos de la misma para su posterior uso.

Cada transacción es validada por el remitente con la solución de un problema criptográfico que está almacenado en un script. La nueva transacción se "bloquea" para el destinatario con un nuevo problema criptográfico que también se almacena como un script, estos scripts, están escritos en Bitcoin Script.

Script es un lenguaje con mucho flexibilidad, que pemite que estos bloqueos sean distintos unos de otros, existen múltiples mecanismos para distintos tipos de llaves:

* OP_CHECKSIG: Revisa una llave pública contra una firma digital, es la base de las direcciones P2PKH.
* OP_CHECKMULTISIG: Revisa múltiples llaves que autorizan una transacción
* OP_CHECKLOCKTIMEVERIFY: son *timelocks* más complejos.
* OP_RETURN: Marca una transacción que no se puede gastar, que contiene datos.

## Revisa los scripts de una transacción

Seguro no te has dado cuenta, pero estos scripts siempre hacen parte de una transacción, para que te des cuenta de ellos, crearemos una transacción cruda y la examinaremos.

### Crea una transacción de prueba

Usaremos una transacción de prueba para revisar el script de bloqueo y desbloqueo, esta transacción usará una dirección P2PKH o Legacy, sus siglas significan *Pay-to-Pubkey Hash*, paga a un hash de la llave pública de un destinatario.

```shell
$ utxo_txid=$(bitcoin-cli listunspent | jq -r '.[1] | .txid') 
$ utxo_vout=$(bitcoin-cli listunspent | jq -r '.[1] | .vout')
$ destinatario=$(bitcoin-cli -named getrawchangeaddress address_type=legacy)
$ txhexcrudo=$(bitcoin-cli -named createrawtransaction inputs='''[ { "txid": "'$utxo_txid'", "vout": '$utxo_vout' } ]''' outputs='''{ "'$destinatario'": 0.0009 }''')
$ transaccionfirmada=$(bitcoin-cli -named signrawtransactionwithwallet hexstring=$txhexcrudo | jq -r '.hex')
```

### Examina tu transacción

Usa el comando `decoderawtransaction` sobre  `$transaccionfirmada`:

```shell
$ bitcoin-cli -named decoderawtransaction hexstring=$transaccionfirmada
{
  "txid": "34151dac704d94a269cd33f80be34c122152edc9bfbb9323852966bf0ce937ed",
  "hash": "34151dac704d94a269cd33f80be34c122152edc9bfbb9323852966bf0ce937ed",
  "version": 2,
  "size": 191,
  "vsize": 191,
  "weight": 764,
  "locktime": 0,
  "vin": [
    {
      "txid": "bb4362dec15e67d366088f5493c789f22fb4a604e767dae1f6a631687e2784aa",
      "vout": 0,
      "scriptSig": {
        "asm": "304402201cc39005b076cb06534cd084fcc522e7bf937c4c9654c1c9dfba68b92cbab7d1022066f273178febc7a37568e2e9f4dec980a2e9a95441abe838c7ef64c39d85849c[ALL] 0315a0aeb37634a71ede72d903acae4c6efa77f3423dcbcd6de3e13d9fd989438b",
        "hex": "47304402201cc39005b076cb06534cd084fcc522e7bf937c4c9654c1c9dfba68b92cbab7d1022066f273178febc7a37568e2e9f4dec980a2e9a95441abe838c7ef64c39d85849c01210315a0aeb37634a71ede72d903acae4c6efa77f3423dcbcd6de3e13d9fd989438b"
      },
      "sequence": 4294967295
    }
  ],
  "vout": [
    {
      "value": 0.00090000,
      "n": 0,
      "scriptPubKey": {
        "asm": "OP_DUP OP_HASH160 06b5c6ba5330cdf738a2ce91152bfd0e71f9ec39 OP_EQUALVERIFY OP_CHECKSIG",
        "hex": "76a91406b5c6ba5330cdf738a2ce91152bfd0e71f9ec3988ac",
        "reqSigs": 1,
        "type": "pubkeyhash",
        "addresses": [
          "mg8S7F1gY3ivV9M9GrWwe6ziWvK2MFquCf"
        ]
      }
    }
  ]
}
```

Encontramos entonces ambos scripts en distintas partes de la transaccion,  en vin encontramos el script de desbloqueo, en out encontramos el scriptpubKey o script de bloqueo

### Revisa tus scripts

Mira ambos y verás que incluyen distinas representaciones, el hexadecimal es el valor que se almacena, pero el asm puede ayudarte a entender un poco más, revisandolo, encontrarás algo como esto: 

```shell
04402201cc39005b076cb06534cd084fcc522e7bf937c4c9654c1c9dfba68b92cbab7d1022066f273178febc7a37568e2e9f4dec980a2e9a95441abe838c7ef64c39d85849c[ALL] 0315a0aeb37634a71ede72d903acae4c6efa77f3423dcbcd6de3e13d9fd989438b
```

Todos lo que ves es la firma generada por llave privada seguido por la llave publica.

Revisa el script de bloqueo y encontrarás lo obvio

```
OP_DUP OP_HASH160 06b5c6ba5330cdf738a2ce91152bfd0e71f9ec39 OP_EQUALVERIFY OP_CHECKSIG
```

Este es el método estándar de Bitcoin para un script de bloqueo P2PKH.

### Corre tu primer script

Seguro con el curso ya conociste un poco sobre el orden y la pila de ejecución, el siguiente ejemplo te dará un mayor contexto, realizaremos la suma de 1 y 2: 

```shell
Script: 1 2 OP_ADD
Stack: [ ]

Script: 2 OP_ADD
Stack: [ 1 ]

Script: OP_ADD
Stack: [ 1 2 ]
```

¿ Recuerdas los OP_CODES ? cada vez que Script encuentra uno, lo evalúa, cada operador ingresa o retira elementos de la pila para procesar, en este caso, OPP_ADD retira 2 y deja el resultado del proceso: 

```
Script:
Running: 1 2 OP_ADD
Stack: [ 3 ]
```

Otros ejemplos con mayor complejidad: 

```
Script: 3 2 OP_ADD 4 OP_SUB
Stack: [ ]

Script: 2 OP_ADD 4 OP_SUB
Stack: [ 3 ]

Script: OP_ADD 4 OP_SUB
Stack: [ 3 2 ]

Script: 4 OP_SUB
Running: 3 2 OP_ADD
Stack: [ 5 ]

Script: OP_SUB
Stack: [ 5 4 ]

Script: 
Running: 5 4 OP_SUB
Stack: [ 1 ]
```



### Prueba tu primer script

Script te permite un control sobre las transacciones, recuerda que los scripts están aislados de la red, por seguridad, es posible crear un script válido que no pueda ser redimible, asi que si vas a crear tus propios scripts, realiza cuantas pruebas sean necesarias antes de poner dinero en juego.

### Instala btcdeb

la herramienta Bitcoin Script Debugger es la mejor forma de depurar un script, primero, clona su repositorio: 

```shell
$ sudo apt-get install git
$ git clone https://github.com/bitcoin-core/btcdeb.git
```

Ahora que ya clonaste el repositorio instalana C++ y algunos paquetes necesarios.

```shell
$ sudo apt-get install autoconf libtool g++ pkg-config make
```

Instala readline, herramienta que permite navegar sobre el depurador 

```shell
$ sudo apt-get install libreadline-dev
```

Ahora, instala btcdeb:

```shell
$ cd btcdeb
$ ./autogen.sh
$ ./configure
$ make
$ sudo make install
```

Revisa si cuentas con una copia en tu máquina:

```shell
$ which btcdeb
/usr/local/bin/btcdeb
```

### Usa btcdeb

btcdeb es un depurador más, toma un script de entrada como argumento, puedes usar el comando `step` para navegar sobre el script y la pila de ejecución

### Algunos ejemplos

Suma de  `1 2 OP_ADD`

```shell
$ btcdeb '[1 2 OP_ADD]' 
btcdeb 0.2.19 -- type `btcdeb -h` for start up options
warning: ambiguous input 1 is interpreted as a numeric value; use OP_1 to force into opcode
warning: ambiguous input 2 is interpreted as a numeric value; use OP_2 to force into opcode
miniscript failed to parse script; miniscript support disabled
valid script
3 op script loaded. type `help` for usage information
script  |  stack 
--------+--------
1       | 
2       | 
OP_ADD  | 
#0000 1
```

Primer paso, muestra el script, de ahora en adelante usaremos `step` para avanzar en el proceso:

```shell
btcdeb> step
		<> PUSH stack 01
script  |  stack 
--------+--------
2       | 01
OP_ADD  | 
#0001 2
```

```shell
btcdeb> step
		<> PUSH stack 02
script  |  stack 
--------+--------
OP_ADD  |      02
        | 01
#0002 OP_ADD
```

```shell
btcdeb> step
		<> POP  stack
		<> POP  stack
		<> PUSH stack 03
script  |  stack 
--------+--------
        | 03
```

Llegamos al final del script, con el valor 3 para retornar.

Resta: `3 2 OP_ADD 4 OP_SUB`:

```shell
$ btcdeb '[3 2 OP_ADD 4 OP_SUB]'
btcdeb 0.2.19 -- type `btcdeb -h` for start up options
warning: ambiguous input 3 is interpreted as a numeric value; use OP_3 to force into opcode
warning: ambiguous input 2 is interpreted as a numeric value; use OP_2 to force into opcode
warning: ambiguous input 4 is interpreted as a numeric value; use OP_4 to force into opcode
miniscript failed to parse script; miniscript support disabled
valid script
5 op script loaded. type `help` for usage information
script  |  stack 
--------+--------
3       | 
2       | 
OP_ADD  | 
4       | 
OP_SUB  | 
#0000 3
btcdeb> step
		<> PUSH stack 03
script  |  stack 
--------+--------
2       | 03
OP_ADD  | 
4       | 
OP_SUB  | 
#0001 2
btcdeb> 
		<> PUSH stack 02
script  |  stack 
--------+--------
OP_ADD  |      02
4       | 03
OP_SUB  | 
#0002 OP_ADD
btcdeb> 
		<> POP  stack
		<> POP  stack
		<> PUSH stack 05
script  |  stack 
--------+--------
4       | 05
OP_SUB  | 
#0003 4
btcdeb> 
		<> PUSH stack 04
script  |  stack 
--------+--------
OP_SUB  |      04
        | 05
#0004 OP_SUB
btcdeb> 
		<> POP  stack
		<> POP  stack
		<> PUSH stack 01
script  |  stack 
--------+--------
        | 01
```

Usaremos entonces btcdeb como nuestra herramienda de depuración, también existen herramientas online que te permitirán ejecutar tus scripts.

Nota: Aunque estos depuradores son excelentes herramientas, todavía no estamos probando con Bitcoin, no podemos garantizar los resultado, para ello necesitamos usar Testnet, embebiendo estos scripts en transacciones.

### Crea tu propio script P2PKH

Estas direcciones han perdido popularidad gracias a las nuevas creaciones y avances, pero siguen siendo el mejor ejemplo para entender Script y Bitcoin.

Es necesario entender tu script de desbloqueo, a través de un scriptPubKey en una transaccion, que está diseñada para solicitar que el destinatario tenga una llave privada asocidada con la dirección de Bitcoin a la cual se envió una cantidad. El destinatario debe satisfacer que la llave pública esté asociada a la llave privada y que la firma fuera genera a partir de esa llave

Recuerda la transacción que creamos anteriormente, puedes observar lo siguiente: 

```
304402201cc39005b076cb06534cd084fcc522e7bf937c4c9654c1c9dfba68b92cbab7d1022066f273178febc7a37568e2e9f4dec980a2e9a95441abe838c7ef64c39d85849c[ALL] 0315a0aeb37634a71ede72d903acae4c6efa77f3423dcbcd6de3e13d9fd989438b
```

Podemos ver su scriptsig que tiene 2 valores, la firma y la llave publica separadas por un [All], esto es, un script de desbloqueo.

Recuerda que cada acción tiene una reacción, un scripy de desloqueo permite gastar una salida no usada. puedes examintar eotnoces la información vin, revela que se está desbloqueado vout0

Puedes examinat esta UTXO usando `gettransaction`.

```shell
$ bitcoin-cli gettransaction "bb4362dec15e67d366088f5493c789f22fb4a604e767dae1f6a631687e2784aa"
{
  "amount": 0.00095000,
  "confirmations": 12,
  "blockhash": "0000000075a4c1519da5e671b15064734c42784eab723530a6ace83ca1e66d3f",
  "blockheight": 1780789,
  "blockindex": 132,
  "blocktime": 1594841768,
  "txid": "bb4362dec15e67d366088f5493c789f22fb4a604e767dae1f6a631687e2784aa",
  "walletconflicts": [
  ],
  "time": 1594841108,
  "timereceived": 1594841108,
  "bip125-replaceable": "no",
  "details": [
    {
      "address": "mmX7GUoXq2wVcbnrnFJrGKsGR14fXiGbD9",
      "category": "receive",
      "amount": 0.00095000,
      "label": "",
      "vout": 0
    }
  ],
  "hex": "020000000001011efcc3bf9950ac2ea08c53b43a0f8cc21e4b5564e205f996f7cadb7d13bb79470000000017160014c4ea10874ae77d957e170bd43f2ee828a8e3bc71feffffff0218730100000000001976a91441d83eaffbf80f82dee4c152de59a38ffd0b602188ac713b10000000000017a914b780fc2e945bea71b9ee2d8d2901f00914a25fbd8702473044022025ee4fd38e6865125f7c315406c0b3a8139d482e3be333727d38868baa656d3d02204b35d9b5812cb85894541da611d5cec14c374ae7a7b8ba14bb44495747b571530121033cae26cb3fa063c95e2c55a94bd04ab9cf173104555efe448b1bfc3a68c8f873342c1b00"
}
```

Pero como lo ves, no obtuviste  `scriptPubKey`, necesitas un paso adicional, al decodificar la transacción

```shell
$ hex=$(bitcoin-cli gettransaction "bb4362dec15e67d366088f5493c789f22fb4a604e767dae1f6a631687e2784aa" | jq -r '.hex')
$ bitcoin-cli decoderawtransaction $hex
{
  "txid": "bb4362dec15e67d366088f5493c789f22fb4a604e767dae1f6a631687e2784aa",
  "hash": "6866490b16a92d68179e1cf04380fd08f16ec80bf66469af8d5e78ae624ff202",
  "version": 2,
  "size": 249,
  "vsize": 168,
  "weight": 669,
  "locktime": 1780788,
  "vin": [
    {
      "txid": "4779bb137ddbcaf796f905e264554b1ec28c0f3ab4538ca02eac5099bfc3fc1e",
      "vout": 0,
      "scriptSig": {
        "asm": "0014c4ea10874ae77d957e170bd43f2ee828a8e3bc71",
        "hex": "160014c4ea10874ae77d957e170bd43f2ee828a8e3bc71"
      },
      "txinwitness": [
        "3044022025ee4fd38e6865125f7c315406c0b3a8139d482e3be333727d38868baa656d3d02204b35d9b5812cb85894541da611d5cec14c374ae7a7b8ba14bb44495747b5715301",
        "033cae26cb3fa063c95e2c55a94bd04ab9cf173104555efe448b1bfc3a68c8f873"
      ],
      "sequence": 4294967294
    }
  ],
  "vout": [
    {
      "value": 0.00095000,
      "n": 0,
      "scriptPubKey": {
        "asm": "OP_DUP OP_HASH160 41d83eaffbf80f82dee4c152de59a38ffd0b6021 OP_EQUALVERIFY OP_CHECKSIG",
        "hex": "76a91441d83eaffbf80f82dee4c152de59a38ffd0b602188ac",
        "reqSigs": 1,
        "type": "pubkeyhash",
        "addresses": [
          "mmX7GUoXq2wVcbnrnFJrGKsGR14fXiGbD9"
        ]
      }
    },
    {
      "value": 0.01063793,
      "n": 1,
      "scriptPubKey": {
        "asm": "OP_HASH160 b780fc2e945bea71b9ee2d8d2901f00914a25fbd OP_EQUAL",
        "hex": "a914b780fc2e945bea71b9ee2d8d2901f00914a25fbd87",
        "reqSigs": 1,
        "type": "scripthash",
        "addresses": [
          "2N9yWARt5E3TQsX2RjsauxSZaEZVhinAS4h"
        ]
      }
    }
  ]
}
```

Ahora si puedes ver vout 0 y ver su script de bloqueo es `OP_DUP OP_HASH160 41d83eaffbf80f82dee4c152de59a38ffd0b6021 OP_EQUALVERIFY OP_CHECKSIG`. Esta es la forma de hacerlo en el pasado.

### Corre tu script

```shell
Script: <signature> <pubKey> OP_DUP OP_HASH160 <pubKeyHash> OP_EQUALVERIFY OP_CHECKSIG
```

Con este script, puedes examinar como una UTXO P2PKH se desbloquea.

Agregas las constantes y duplicas la llave pública  `OP_DUP`:

```
Script: <signature> <pubKey> OP_DUP OP_HASH160 <pubKeyHash> OP_EQUALVERIFY OP_CHECKSIG
Stack: [ ]

Script: <pubKey> OP_DUP OP_HASH160 <pubKeyHash> OP_EQUALVERIFY OP_CHECKSIG
Stack: [ <signature> ]

Script: OP_DUP OP_HASH160 <pubKeyHash> OP_EQUALVERIFY OP_CHECKSIG
Stack: [ <signature> <pubKey> ]

Script: OP_HASH160 <pubKeyHash> OP_EQUALVERIFY OP_CHECKSIG
Running: <pubKey> OP_DUP
Stack: [ <signature> <pubKey> <pubKey> ]
```

Se duplica para verificar los 2 elementos importantes, la llave pública y la firma

Ahora, `OP_HASH160` retira la `<pubKey>` de la pila, utiliza una función de hash y la agrega la pila

```
Script: <pubKeyHash> OP_EQUALVERIFY OP_CHECKSIG
Running: <pubKey> OP_HASH160
Stack: [ <signature> <pubKey> <pubKeyHash> ]
```

Luego, debes colocar el  `<pubKeyHash>que venía en el script de bloqueo

```
Script: OP_EQUALVERIFY OP_CHECKSIG
Stack: [ <signature> <pubKey> <pubKeyHash> <pubKeyHash> ]
```

`OP_EQUALVERIFY`:

* `OP_EQUAL`, que retira 2 elemento de la pila y retorna verdadero o falso al compararlos.
*  `OP_VERIFY` retira el resultado y marca la transacci{on como inválida o falsa l.)

Assuming the two `<pubKeyHash>es` are equal, you will have the following result:

Asumiento que son iguales  los 2 <pubKeyHash> tenemos el resultado: 

```
Script: OP_CHECKSIG
Running: <pubKeyHash> <pubKeyHash> OP_EQUALVERIFY
Stack: [ <signature> <pubKey> ]
```

Ya probamos que la llave pública provide satisface las condiciones, ahora, como comprar la firma? 

```
Script:
Running: <signature> <pubKey> OP_CHECKSIG
Stack: [ True ]
```

El script cumple con su labor de manera exitosa

### Use btcdeb para correr un ejemplo de P2PKH

```
$ btcdeb '[304402201cc39005b076cb06534cd084fcc522e7bf937c4c9654c1c9dfba68b92cbab7d1022066f273178febc7a37568e2e9f4dec980a2e9a95441abe838c7ef64c39d85849c 0315a0aeb37634a71ede72d903acae4c6efa77f3423dcbcd6de3e13d9fd989438b OP_DUP OP_HASH160 41d83eaffbf80f82dee4c152de59a38ffd0b6021 OP_EQUALVERIFY OP_CHECKSIG]'
btcdeb 0.2.19 -- type `btcdeb -h` for start up options
unknown key ID 41d83eaffbf80f82dee4c152de59a38ffd0b6021: returning fake key
valid script
7 op script loaded. type `help` for usage information
script                                                             |                                                             stack 
-------------------------------------------------------------------+-------------------------------------------------------------------
304402201cc39005b076cb06534cd084fcc522e7bf937c4c9654c1c9dfba68b... | 
0315a0aeb37634a71ede72d903acae4c6efa77f3423dcbcd6de3e13d9fd989438b | 
OP_DUP                                                             |
OP_HASH160                                                         |
41d83eaffbf80f82dee4c152de59a38ffd0b6021                           | 
OP_EQUALVERIFY                                                     | 
OP_CHECKSIG                                                        | 
                                                                   | 
                                                                   | 
#0000 304402201cc39005b076cb06534cd084fcc522e7bf937c4c9654c1c9dfba68b92cbab7d1022066f273178febc7a37568e2e9f4dec980a2e9a95441abe838c7ef64c39d85849c
```

