# Bitcoin-cli

Ahora que ya sabes cómo correr tu nodo, es necesario que aprendas un poco de qué puedes hacer con su consola y su interfaz RPC

## Bitcoind

Comencemos nuestra exploración de Bitcoin usando  `bitcoin-cli` , herramienta que permite hacer llamados RPC en forma de comandos, lo primero es que bitcoind debe estar corriendo para usar bitcoin-cli ya que esta hace llamados a bitcoind, si ya tienes un nodos o seguiste el README, tendrás un nodo corriendo, puedes revisarlo usando revisando la tabla de procesos.

Utliza el comando  `ps auxww | grep bitcoind` 

Si no está corriendo, y seguiste la configuración de la guía, ejecuta  `bitcoind`

### Verifica tu conteo de bloques

Recuerda que toda la cadena se tuvo que descargar antes de tu usar el nodo, ejecuta el comando  `bitcoin-cli getblockcount`  para mirar si si han cargado

```shell
$ bitcoin-cli getblockcount
2090688
```

Con este simple comando ya sabes que estás listo para trabajar con bitcoin-cli, puedes comparar tu alturar con un explorador, en este caso específico, de testnet como https://www.blockchain.com/explorer?view=btc-testnet

### Apóyate del comando help para conocer tus comandos

Recuerda que si necesitas más información sobre el uso de esta herramienta, puedes correr el comando más el argumento `help`, si no agregas más argumentos, obtienes todos los comandos disponibles

```shell
$ bitcoin-cli help
== Blockchain ==
getbestblockhash
getblock "blockhash" ( verbosity )
getblockchaininfo
getblockcount
getblockfilter "blockhash" ( "filtertype" )
getblockhash height
getblockheader "blockhash" ( verbose )
getblockstats hash_or_height ( stats )
getchaintips
getchaintxstats ( nblocks "blockhash" )
getdifficulty
getmempoolancestors "txid" ( verbose )
getmempooldescendants "txid" ( verbose )
getmempoolentry "txid"
getmempoolinfo
getrawmempool ( verbose mempool_sequence )
gettxout "txid" n ( include_mempool )
gettxoutproof ["txid",...] ( "blockhash" )
gettxoutsetinfo ( "hash_type" hash_or_height use_index )
preciousblock "blockhash"
pruneblockchain height
savemempool
scantxoutset "action" ( [scanobjects,...] )
verifychain ( checklevel nblocks )
verifytxoutproof "proof"

== Control ==
getmemoryinfo ( "mode" )
getrpcinfo
help ( "command" )
logging ( ["include_category",...] ["exclude_category",...] )
stop
uptime

== Generating ==
generateblock "output" ["rawtx/txid",...]
generatetoaddress nblocks "address" ( maxtries )
generatetodescriptor num_blocks "descriptor" ( maxtries )

== Mining ==
getblocktemplate ( "template_request" )
getmininginfo
getnetworkhashps ( nblocks height )
prioritisetransaction "txid" ( dummy ) fee_delta
submitblock "hexdata" ( "dummy" )
submitheader "hexdata"

== Network ==
addnode "node" "command"
clearbanned
disconnectnode ( "address" nodeid )
getaddednodeinfo ( "node" )
getconnectioncount
getnettotals
getnetworkinfo
getnodeaddresses ( count "network" )
getpeerinfo
listbanned
ping
setban "subnet" "command" ( bantime absolute )
setnetworkactive state

== Rawtransactions ==
analyzepsbt "psbt"
combinepsbt ["psbt",...]
combinerawtransaction ["hexstring",...]
converttopsbt "hexstring" ( permitsigdata iswitness )
createpsbt [{"txid":"hex","vout":n,"sequence":n},...] [{"address":amount,...},{"data":"hex"},...] ( locktime replaceable )
createrawtransaction [{"txid":"hex","vout":n,"sequence":n},...] [{"address":amount,...},{"data":"hex"},...] ( locktime replaceable )
decodepsbt "psbt"
decoderawtransaction "hexstring" ( iswitness )
decodescript "hexstring"
finalizepsbt "psbt" ( extract )
fundrawtransaction "hexstring" ( options iswitness )
getrawtransaction "txid" ( verbose "blockhash" )
joinpsbts ["psbt",...]
sendrawtransaction "hexstring" ( maxfeerate )
signrawtransactionwithkey "hexstring" ["privatekey",...] ( [{"txid":"hex","vout":n,"scriptPubKey":"hex","redeemScript":"hex","witnessScript":"hex","amount":amount},...] "sighashtype" )
testmempoolaccept ["rawtx",...] ( maxfeerate )
utxoupdatepsbt "psbt" ( ["",{"desc":"str","range":n or [n,n]},...] )

== Signer ==
enumeratesigners

== Util ==
createmultisig nrequired ["key",...] ( "address_type" )
deriveaddresses "descriptor" ( range )
estimatesmartfee conf_target ( "estimate_mode" )
getdescriptorinfo "descriptor"
getindexinfo ( "index_name" )
signmessagewithprivkey "privkey" "message"
validateaddress "address"
verifymessage "address" "signature" "message"

== Wallet ==
abandontransaction "txid"
abortrescan
addmultisigaddress nrequired ["key",...] ( "label" "address_type" )
backupwallet "destination"
bumpfee "txid" ( options )
createwallet "wallet_name" ( disable_private_keys blank "passphrase" avoid_reuse descriptors load_on_startup external_signer )
dumpprivkey "address"
dumpwallet "filename"
encryptwallet "passphrase"
getaddressesbylabel "label"
getaddressinfo "address"
getbalance ( "dummy" minconf include_watchonly avoid_reuse )
getbalances
getnewaddress ( "label" "address_type" )
getrawchangeaddress ( "address_type" )
getreceivedbyaddress "address" ( minconf )
getreceivedbylabel "label" ( minconf )
gettransaction "txid" ( include_watchonly verbose )
getunconfirmedbalance
getwalletinfo
importaddress "address" ( "label" rescan p2sh )
importdescriptors "requests"
importmulti "requests" ( "options" )
importprivkey "privkey" ( "label" rescan )
importprunedfunds "rawtransaction" "txoutproof"
importpubkey "pubkey" ( "label" rescan )
importwallet "filename"
keypoolrefill ( newsize )
listaddressgroupings
listdescriptors
listlabels ( "purpose" )
listlockunspent
listreceivedbyaddress ( minconf include_empty include_watchonly "address_filter" )
listreceivedbylabel ( minconf include_empty include_watchonly )
listsinceblock ( "blockhash" target_confirmations include_watchonly include_removed )
listtransactions ( "label" count skip include_watchonly )
listunspent ( minconf maxconf ["address",...] include_unsafe query_options )
listwalletdir
listwallets
loadwallet "filename" ( load_on_startup )
lockunspent unlock ( [{"txid":"hex","vout":n},...] )
psbtbumpfee "txid" ( options )
removeprunedfunds "txid"
rescanblockchain ( start_height stop_height )
send [{"address":amount,...},{"data":"hex"},...] ( conf_target "estimate_mode" fee_rate options )
sendmany "" {"address":amount,...} ( minconf "comment" ["address",...] replaceable conf_target "estimate_mode" fee_rate verbose )
sendtoaddress "address" amount ( "comment" "comment_to" subtractfeefromamount replaceable conf_target "estimate_mode" avoid_reuse fee_rate verbose )
sethdseed ( newkeypool "seed" )
setlabel "address" "label"
settxfee amount
setwalletflag "flag" ( value )
signmessage "address" "message"
signrawtransactionwithwallet "hexstring" ( [{"txid":"hex","vout":n,"scriptPubKey":"hex","redeemScript":"hex","witnessScript":"hex","amount":amount},...] "sighashtype" )
unloadwallet ( "wallet_name" load_on_startup )
upgradewallet ( version )
walletcreatefundedpsbt ( [{"txid":"hex","vout":n,"sequence":n},...] ) [{"address":amount,...},{"data":"hex"},...] ( locktime options bip32derivs )
walletdisplayaddress bitcoin address to display
walletlock
walletpassphrase "passphrase" timeout
walletpassphrasechange "oldpassphrase" "newpassphrase"
walletprocesspsbt "psbt" ( sign "sighashtype" bip32derivs )

== Zmq ==
getzmqnotifications

```

Ahora que ya conoces los comandos, seguro quieres entender unos más que otros, por tanto agrega como argumento uno de los comandos disponibles, con ellos obtendrás más información del mismo, para este ejemplo usaremos  `bitcoin-cli help getzqnotifications`

```shell
$ bitcoin-cli help getzmqnotifications
getzmqnotifications

Returns information about the active ZeroMQ notifications.

Result:
[                         (json array)
  {                       (json object)
    "type" : "str",       (string) Type of notification
    "address" : "str",    (string) Address of the publisher
    "hwm" : n             (numeric) Outbound message high water mark
  },
  ...
]

Examples:

> bitcoin-cli getzmqnotifications 
> curl --user myusername --data-binary '{"jsonrpc": "1.0", "id": "curltest", "method": "getzmqnotifications", "params": []}' -H 'content-type: text/plain;' http://127.0.0.1:8332/
```

### Crea tu billetera

Para probar múltiples funcionalidades en bitcoin, es necesario que crees una billetera de prueba, recuerda, no es obligatorio, es solo para este ejercicio práctico. Alcrear una billetera creas una llave pública y una privada, con estas, podrás gastar los fondos que recibes en tu billetera, recuerda, en el curso hablamos de bitcoin core y cómo este sistema almacena la información en el archivo `wallet.dat`  dentro del directorio `/blockchain-data/.bitcoin/data/testnet3/wallets/` directory. En las últimas versiones de Bitcoin Core no se crea una billetera por defecto, por tanto es necesario que crees una sin nombre ("")

```shell
bitcoin-cli createwallet ""
{
  "name": "",
  "warning": ""
}

```

Ahora, si revisas tu directorio de billeteras encontrarás información relacionada con las mismas. 

```shell
$ ls /blockchain-data/.bitcoin/data/testnet3/wallets/
database  db.log  wallet.dat
```

Ya tenemos nuestra billetera, ahora creemos una dirección para poder recibir bitcoins.

## Crea tu dirección

Utiliza el comando `bitcoin-cli getnewaddress`, de este, podrás obtener 3 tipos de direcciones:

* Legacy
* Segwit
  * P2sh-segwit
  * Bech32(por defecto obtendremos esta si no especificamos las otras.)

**NOTA**: Usaremos direcciones de tipo legacy para probar que todo funcione correctamente.

Crea tu dirección especificando el argumento `addresstype`

```shell
$ bitcoin-cli getnewaddress -addresstype legacy
mmz9CTJHxPYMX5NcWushn6cj6Z9Vn7B3rN
```

¿ Notas que esta dirección comienza con una m ? esto significa que estamos usando una dirección en testnet tipo Legacy, un 2 significa p2sh y tb1 una Bech32

## Usa variables en tu línea de comandos

Usa variables en tu línea de comandos para controlar variables largas, complejas e ilegibles con una simple substitución

```shell
$ VARIABLE=$(command)
```

Si quisieramos crear una nueva dirección podriamos hacer algo como: 

```shell
$ unset NUEVADIRECCION1
$ NUEVADIRECCION1=$(bitcoin-cli getnewaddress "" legacy)
```

Para revisarla solo debes usar ECHO

```shell
$ echo $NUEVADIRECCION1
mjej5S76YfjGvQ8XLXYmZMinjGtA9EEX6D
```

Estas listo para recibir bitcoin a tu billetera.

## Recibe tu primera transacción

Para poder comenzar a transar con bitcoin, necesitas un poco de bitcoin, en testnet podemos usar *faucets*, herramientas que nos permiten "obtener" bitcoin, recuerda que en testnet, este bitcoin no es real.

Algunos *faucets* destacados:

* https://bitcoinfaucet.uo1.net/
* https://testnet-faucet.mempool.co/

¿ Qué necesitas para recibir valor a través del *faucet* ? Una dirección en la que puedas recibir,  a través de la interfaz web, agregarás esta y debes esperar a que tu balance se vea modificado.

## Verifica tu balance 

Recuerda que Bitcoin cuenta con problemas de latencia, por tanto, cuando uses el comando `bitcoin-cli getbalance` luego de realizar el proceso en el faucet, te encontrarás con un balance de 0.000000!

En este caso, puedes usar el comando `bitcoin-cli getunconfirmedbalance` para conocer el balance de las transacciones iniciadas pero que todavía no existen dentro de un bloque

```
$ bitcoin-cli getunconfirmedbalance
0.0000100
```

Nota: Si los valores no cambian en alguno de los 2 comando en los próximos 10 minutos, puede que tu faucet tenga problemas, se paciente y escoge un diferente.

Si quieres revisar la profundidad de tu transacción en la cadena de bloques, puedes usar un comando como: 

```shell
$  bitcoin-cli getbalance "*" 1
0.00030000
$  bitcoin-cli getbalance "*" 2
0.00000000
```

Este par de comandos ilustran que nuestra transaccion fue "confirmada una vez" pero no 2, recuerda que esto sirve en la red principal, en testnet, obtienes el valor y puedes usarlo inmediatamente.

Usa `bitcoin-cli getwalletinfo` para obtener la información de tu billetera

```shell
$ bitcoin-cli getwalletinfo
{
  "walletname": "",
  "walletversion": 169900,
  "format": "bdb",
  "balance": 0.00130000,
  "unconfirmed_balance": 0.00000000,
  "immature_balance": 0.00000000,
  "txcount": 2,
  "keypoololdest": 1646800749,
  "keypoolsize": 999,
  "hdseedid": "4592a117c09c14e1fd29a6362f9f6210e2ace559",
  "keypoolsize_hd_internal": 1000,
  "paytxfee": 0.00000000,
  "private_keys_enabled": true,
  "avoid_reuse": false,
  "scanning": false,
  "descriptors": false
}

```

## Conoce el id de tu transacción

Ejecuta el comando  `bitcoin-cli listtransactions` para obtener información de las transacciones: 

```shell
$ bitcoin-cli listtransactions
[
  {
    "address": "mmz9CTJHxPYMX5NcWushn6cj6Z9Vn7B3rN",
    "category": "receive",
    "amount": 0.00100000,
    "label": "-addresstype",
    "vout": 0,
    "confirmations": 13,
    "blockhash": "000000000000023dce81441242e85899a534490a59326085b374ff5aa20d305b",
    "blockheight": 2186029,
    "blockindex": 3,
    "blocktime": 1646803011,
    "txid": "6497cbf66ac34e1fd11728497da5aa652d9802b33442e2c4a7b040e3aec6295b",
    "walletconflicts": [
    ],
    "time": 1646802997,
    "timereceived": 1646802997,
    "bip125-replaceable": "no"
  },
  {
    "address": "mmz9CTJHxPYMX5NcWushn6cj6Z9Vn7B3rN",
    "category": "receive",
    "amount": 0.00030000,
    "label": "-addresstype",
    "vout": 1,
    "confirmations": 10,
    "blockhash": "00000000000000e58c78bbc73060e0d00cab8211ff0267bac7233df9946e2967",
    "blockheight": 2186032,
    "blockindex": 2,
    "blocktime": 1646803337,
    "txid": "760298ba63aaed6a938bd587dfdcfc84761d81568bcabfa90c4e2464f3cd3fa8",
    "walletconflicts": [
    ],
    "time": 1646803257,
    "timereceived": 1646803257,
    "bip125-replaceable": "no"
  }

```

Encontramos 2 transacciones con una cantidad específica y unos datos relevantes de la misma, recuerda que es MUY IMPORTANTE que uses una nueva dirección para cada transacción, por privacidad, conserva tu pseudoaninimidad en la red.

Toma cualquiera de tus transacciones y revísala, usando el comando` bitcoin-cli gettransaction [id de la transaccíon]`, para nuestro ejemplo, tomarelos la primera: 

```shell
$ bitcoin-cli gettransaction "6497cbf66ac34e1fd11728497da5aa652d9802b33442e2c4a7b040e3aec6295b"
{
  "amount": 0.00100000,
  "confirmations": 19,
  "blockhash": "000000000000023dce81441242e85899a534490a59326085b374ff5aa20d305b",
  "blockheight": 2186029,
  "blockindex": 3,
  "blocktime": 1646803011,
  "txid": "6497cbf66ac34e1fd11728497da5aa652d9802b33442e2c4a7b040e3aec6295b",
  "walletconflicts": [
  ],
  "time": 1646802997,
  "timereceived": 1646802997,
  "bip125-replaceable": "no",
  "details": [
    {
      "address": "mmz9CTJHxPYMX5NcWushn6cj6Z9Vn7B3rN",
      "category": "receive",
      "amount": 0.00100000,
      "label": "-addresstype",
      "vout": 0
    }
  ],
  "hex": "02000000000101c6b8f0bcb1143d0ccbaba3f603ea79709e01df362037de34ccfd44c8e6001d3d0000000000feffffff02a0860100000000001976a91446f51660c85b8b46e5830ae40014010b8861f9ec88acb8561300000000001600145adc5a2ddae248e820a1963a46eee539a8ebc1090247304402201a80bad27f30f8900c101d352cdc50606284b7633046741ae98e3b30f5eb9eda02200a6db2f47b4649a5f67ee7a02e13ccc9a16517ee32df1da34d49e6b3b083f1f10121030d18dba4800ebbedf40120f9da6fbd544a342a927c85c69eb21bdd513934c2eb2b5b2100"
}
```

Ya conoces el detalle de tu transacción, pero seguro te preguntarás, ¿ Donde están los scripts? ¿ Donde está un detalle más profundo de la misma? para ello debes tener algo claro, si ejecutas el comando  `bitcoin-cli help gettransaction` te darás cuenta que existen 2 argumentos opcionales,  `include_watchonly` y  `verbose` cambiando su valor, en este caso un valor booleano podrás tener información sobre direcciones watch-only o sobre mayor información sobre la transaccion, es ese segundo valor el importante para nosotros.

Ejecutaremos entonces el comando como `bitcoin-cli gettransaction[id de la transaccíon] false true`:

```shell
$ bitcoin-cli gettransaction "6497cbf66ac34e1fd11728497da5aa652d9802b33442e2c4a7b040e3aec6295b" false true
{
  "amount": 0.00100000,
  "confirmations": 25,
  "blockhash": "000000000000023dce81441242e85899a534490a59326085b374ff5aa20d305b",
  "blockheight": 2186029,
  "blockindex": 3,
  "blocktime": 1646803011,
  "txid": "6497cbf66ac34e1fd11728497da5aa652d9802b33442e2c4a7b040e3aec6295b",
  "walletconflicts": [
  ],
  "time": 1646802997,
  "timereceived": 1646802997,
  "bip125-replaceable": "no",
  "details": [
    {
      "address": "mmz9CTJHxPYMX5NcWushn6cj6Z9Vn7B3rN",
      "category": "receive",
      "amount": 0.00100000,
      "label": "-addresstype",
      "vout": 0
    }
  ],
  "hex": "02000000000101c6b8f0bcb1143d0ccbaba3f603ea79709e01df362037de34ccfd44c8e6001d3d0000000000feffffff02a0860100000000001976a91446f51660c85b8b46e5830ae40014010b8861f9ec88acb8561300000000001600145adc5a2ddae248e820a1963a46eee539a8ebc1090247304402201a80bad27f30f8900c101d352cdc50606284b7633046741ae98e3b30f5eb9eda02200a6db2f47b4649a5f67ee7a02e13ccc9a16517ee32df1da34d49e6b3b083f1f10121030d18dba4800ebbedf40120f9da6fbd544a342a927c85c69eb21bdd513934c2eb2b5b2100",
  "decoded": {
    "txid": "6497cbf66ac34e1fd11728497da5aa652d9802b33442e2c4a7b040e3aec6295b",
    "hash": "1c2e183642f43ea67f3c0e8fb1ef6f311a08cb4c863ab2cc8dba8d348e17fe82",
    "version": 2,
    "size": 225,
    "vsize": 144,
    "weight": 573,
    "locktime": 2186027,
    "vin": [
      {
        "txid": "3d1d00e6c844fdcc34de372036df019e7079ea03f6a3abcb0c3d14b1bcf0b8c6",
        "vout": 0,
        "scriptSig": {
          "asm": "",
          "hex": ""
        },
        "txinwitness": [
          "304402201a80bad27f30f8900c101d352cdc50606284b7633046741ae98e3b30f5eb9eda02200a6db2f47b4649a5f67ee7a02e13ccc9a16517ee32df1da34d49e6b3b083f1f101",
          "030d18dba4800ebbedf40120f9da6fbd544a342a927c85c69eb21bdd513934c2eb"
        ],
        "sequence": 4294967294
      }
    ],
    "vout": [
      {
        "value": 0.00100000,
        "n": 0,
        "scriptPubKey": {
          "asm": "OP_DUP OP_HASH160 46f51660c85b8b46e5830ae40014010b8861f9ec OP_EQUALVERIFY OP_CHECKSIG",
          "hex": "76a91446f51660c85b8b46e5830ae40014010b8861f9ec88ac",
          "address": "mmz9CTJHxPYMX5NcWushn6cj6Z9Vn7B3rN",
          "type": "pubkeyhash"
        }
      },
      {
        "value": 0.01267384,
        "n": 1,
        "scriptPubKey": {
          "asm": "0 5adc5a2ddae248e820a1963a46eee539a8ebc109",
          "hex": "00145adc5a2ddae248e820a1963a46eee539a8ebc109",
          "address": "tb1qttw95tw6ufywsg9pjcaydmh98x5whsgf3ps7ty",
          "type": "witness_v0_keyhash"
        }
      }
    ]
  }
}
```

Tenemos la información completa de nuestra transacción, estas listo para enviar tu primera transacción

## Envía tu primera transacción

Bitcoin-cli permite enviar bitcoin de 3 maneras distintas:

* Como un comando simple.
* Como una transacción cruda.
* Como una transacción cruda con cálculos.

La forma más simple es a través de su comando `sendtoaddress` 

## Configura tu tarifa transaccional

Es necesario definir una tarifa transaccional, `bitcoind` en múltiples escenarios calcula estas tarifas por ti, si quieres tener un mayor control añade el campo `mintxfee` en `bitcoin.conf`, de esta manera te podrás asegurar de que se configure una tarifa mínima de xxx satoshis por kByte de datos en tu transacción.

## Obtén una dirección

Si tienes otro nodo sobre la red de testnet, si un amigo(a) tuyo la corre, si tienes una billetera que soporte esta red puedes obtener una dirección, un QR que puedes escanear y usar para pagar, en nuestro caso usaremos `tb1ql7w62elx9ucw4pj5lgw4l028hmuw80sndtntxt` que es la dirección de retorno en el `faucet`

## Envía tu transacción

Estás listo para enviar tu transacción, usa el comando ` bitcoin-cli sendtoaddress [direccion] [cantidad en btc]`

```shell
$ txid=$(bitcoin-cli sendtoaddress tb1ql7w62elx9ucw4pj5lgw4l028hmuw80sndtntxt 0.0001)
$ echo $txid
cd04f65bfca14d15ddb5b42c891450f1780506305c91eba51d007f50afcaaa85
```

## Examina tu transacción

Usa tu txid para que revises la información asociada a tu transacción

```shell
{
  "amount": -0.00010000,
  "fee": -0.00000316,
  "confirmations": 1,
  "blockhash": "0000000000000a955c8638963ffb2b7f5448ac3293331d41318525172d91d92c",
  "blockheight": 2186081,
  "blockindex": 13,
  "blocktime": 1646806313,
  "txid": "cd04f65bfca14d15ddb5b42c891450f1780506305c91eba51d007f50afcaaa85",
  "walletconflicts": [
  ],
  "time": 1646806298,
  "timereceived": 1646806298,
  "bip125-replaceable": "no",
  "details": [
    {
      "address": "tb1ql7w62elx9ucw4pj5lgw4l028hmuw80sndtntxt",
      "category": "send",
      "amount": -0.00010000,
      "vout": 0,
      "fee": -0.00000316,
      "abandoned": false
    }
  ],
  "hex": "0200000001a83fcdf364244e0ca9bfca8b56811d7684fcdcdf87d58b936aedaa63ba980276010000006a473044022037158785b076b533057d4a5eaacca03f0013ad3b59d5fbd0b622262ce3c75d55022021ff6e7eccb2d280d511524969909cf8d2f4ab278338c59e1950adea7514a0e20121034b7ff58d284e603f498328ef1293f5ce30687a8e59f50e2375061704f19ffe16feffffff021027000000000000160014ff9da567e62f30ea8654fa1d5fbd47bef8e3be13e44c000000000000160014d811bf9a7550879ee3d4b6dc7b94a71916fea033605b2100",
  "decoded": {
    "txid": "cd04f65bfca14d15ddb5b42c891450f1780506305c91eba51d007f50afcaaa85",
    "hash": "cd04f65bfca14d15ddb5b42c891450f1780506305c91eba51d007f50afcaaa85",
    "version": 2,
    "size": 219,
    "vsize": 219,
    "weight": 876,
    "locktime": 2186080,
    "vin": [
      {
        "txid": "760298ba63aaed6a938bd587dfdcfc84761d81568bcabfa90c4e2464f3cd3fa8",
        "vout": 1,
        "scriptSig": {
          "asm": "3044022037158785b076b533057d4a5eaacca03f0013ad3b59d5fbd0b622262ce3c75d55022021ff6e7eccb2d280d511524969909cf8d2f4ab278338c59e1950adea7514a0e2[ALL] 034b7ff58d284e603f498328ef1293f5ce30687a8e59f50e2375061704f19ffe16",
          "hex": "473044022037158785b076b533057d4a5eaacca03f0013ad3b59d5fbd0b622262ce3c75d55022021ff6e7eccb2d280d511524969909cf8d2f4ab278338c59e1950adea7514a0e20121034b7ff58d284e603f498328ef1293f5ce30687a8e59f50e2375061704f19ffe16"
        },
        "sequence": 4294967294
      }
    ],
    "vout": [
      {
        "value": 0.00010000,
        "n": 0,
        "scriptPubKey": {
          "asm": "0 ff9da567e62f30ea8654fa1d5fbd47bef8e3be13",
          "hex": "0014ff9da567e62f30ea8654fa1d5fbd47bef8e3be13",
          "address": "tb1ql7w62elx9ucw4pj5lgw4l028hmuw80sndtntxt",
          "type": "witness_v0_keyhash"
        }
      },
      {
        "value": 0.00019684,
        "n": 1,
        "scriptPubKey": {
          "asm": "0 d811bf9a7550879ee3d4b6dc7b94a71916fea033",
          "hex": "0014d811bf9a7550879ee3d4b6dc7b94a71916fea033",
          "address": "tb1qmqgmlxn42zreac75kmw8h998ryt0agpnaxv5zu",
          "type": "witness_v0_keyhash"
        }
      }
    ]
  }
}
```