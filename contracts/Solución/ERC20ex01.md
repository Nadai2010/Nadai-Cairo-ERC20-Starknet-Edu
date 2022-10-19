## Configuraciones basicas de protostar

Si quiere usar [Protostar](https://github.com/Nadai2010/Nadai-ERC20-Protostar-Cairo), siga esta repo para configurarla, sino evite este punto.

## Implementación de un ERC20

* Llame a [`ex1_assign_rank()`](contracts/Evaluator.cairo#L134) en el contrato del evaluador para recibir un ticker aleatorio para su token ERC20, así como un suministro de token inicial (1 pt). Puede leer su ticker asignado y suministrarlo a través de la [página del evaluador en Voyager](https://goerli.voyager.online/contract/0x14ece8a1dcdcc5a56f01a987046f2bd8ddfb56bc358da050864ae6da5f71394) llamando a los captadores [`read_ticker()`]((contracts/Evaluator.cairo#L93 )) y [`read_supply()`](contracts/Evaluator.cairo#L102).
* Cree un contrato de token ERC20 con el ticker y el suministro adecuados. Puede usar [esta implementación](https://github.com/OpenZeppelin/cairo-contracts/blob/main/src/openzeppelin/token/erc20/ERC20.cairo) como base (2 pts) 
* Implementarlo en el testnet (verifique el constructor para los argumentos necesarios. También tenga en cuenta que los argumentos deben ser decimales). (1 punto)

## Cree un contrato de token ERC20. 

* Puedes usar esta implementación como base el Smart ajustado [ERC20ex02](https://github.com/Nadai2010/Nadai-Cairo-ERC20-Starknet-Edu/blob/master/contracts/Soluci%C3%B3n/ERC20ex02.cairo)
* Despliéguelo en la red de prueba, verifique en el constructor, simbolo, total supply y los argumentos necesarios. 

```bash
starknet-compile contracts/token/ERC20/ERC20.cairo --output artifacts/ERC20.json
starknet deploy --contract ERC20 --inputs arg1 arg2 arg3 --network alpha-goerli 
```

## Solución

El objetivo de este ejercicio es aprender a implementar un contrato inteligente en la red de prueba Goerli de StarkNet y aprender a interactuar con ellos usando el explorador de bloques Voyager de StarkNet y la billetera Argent X de StarkNet.

Nota: si aún no lo ha hecho, asegúrese de instalar la extensión del navegador Argent X y crear una cuenta en Goerli para seguir el taller.

Primero iremos ha escribir en el contrato a través de la [página del evaluador en Voyager](https://goerli.voyager.online/contract/0x14ece8a1dcdcc5a56f01a987046f2bd8ddfb56bc358da050864ae6da5f71394) y nos asignaremos un rango, usando la opción `ex1_assign_rank`.


![Graph](/contracts/Imagenes/rangoex01.png)

* [Hash](https://goerli.voyager.online/tx/0x59548951a225aefa81fc06539db53a70f482af2f3edb6f2cd45abe6f8aa9aee)


Ahora que tenemos asigando iremos a revisar la llamada `read_ticker `(Simbolo) asignado y el `read_supply` 

![Graph](/contracts/Imagenes/tickerex01.png)

![Graph](/contracts/Imagenes/supplyex01.png)


Para comenzar, he realizado la base del Smart desde [Wizard OpenZeppelin](https://wizard.openzeppelin.com/cairo#erc20) en el que hemos ajustado. luego en `ERC20ex02.cairo` dentro de la carpeta de Solución y en el que tendremos que cambiar los valores asignados para el ejercicio. En este caso Simbolo será `AKKG` y supply `760000000000000000000`.

![Graph](/contracts/Imagenes/smartex02.png)


Clonaremos la repo 
```bash
gh repo clone Nadai2010/Nadai-Cairo-ERC20-Starknet-Edu
cd Nadai-Cairo-ERC20-Starknet-Edu
```

Luego iremos a la configuración de `protostar.toml` e indicaremos que smart haremos `build` y `deploy`. En el apartado protostar.contracts, indicaremos el nombre del smart `ERC20ex02` = [Añadimos la ruta de ERC20.cairo]

El build creará dos archivos  en la carpeta de `build` que son `ERC20ex02.json` y `ERC20ex02_abi.json`. Usando el comando siguiente:

```bash
protostar build
```

### Configuración de Argumentos

Al implementar un contrato inteligente, debemos proporcionar los argumentos del constructor. Veamos qué espera el constructor como argumentos.

```bash
@constructor
func constructor{
        syscall_ptr: felt*,
        pedersen_ptr: HashBuiltin*,
        range_check_ptr
}(owner: felt, recipient: felt) {
    ERC20.initializer('Nadai Token', 'AKKG', 18);
    Ownable.initializer(owner);

    ERC20._mint(recipient, Uint256(760000000000000000000, 0));
    return ();                      
}
```

En nuestro caso, necesitaremos proporcionar los argumentos del `Owner` y `recipient` , en el mismo orden en que se declaran en el constructor, haciendo que nuestro comando de implementación se vea así:

```bash
starknet deploy --contract comp/ERC20ex02.json \
  --inputs <owner> <recipient> --network alpha-goerli --no_wallet
```

* CON PROTOSTAR

```bash
protostar deploy ./build/ERC20ex02.json --network alpha-goerli -i <owner> <recipient>
```

Para transformar hexadecimales en felt, necesitaremos usar la biblioteca de utilidades proporcionada por el equipo de StarkWare que voy a copiar en nuestro proyecto como `utils.py`.

En este tutorial no vamos a pasar a `felt` sino la wallet que usaremos para el deploy. Ya que el nombre, símbolo, supply, decimales están ya definidos en el contrato.

Ahora podemos usar la biblioteca utils.py para derivar la representación `felt` de nuestras cadenas y direcciones.

Dentro de la carpeta de la ruta del archivo ejutamos los comandos.

```bash
python3 -i utils.py
```

Ahora empezamos si todo ha ido bien las conversiones.

```bash
hex_to_felt("0x05B23d29C5b33a1cf0c4e50F798f9E22c7254913368996436682852EfCF69f86")
```
* 2576485153152103101814659868666844275075629902050884189831195075733091688326

Ahora que tenemos la representación `felt` del valor requerido por el constructor, podemos implementar el contrato inteligente en la red de prueba Goerli de StarkNet.

```bash
starknet deploy --contract comp/ERC20ex02.json \
  --inputs 2576485153152103101814659868666844275075629902050884189831195075733091688326 2576485153152103101814659868666844275075629902050884189831195075733091688326 --network alpha-goerli --no_wallet
```
>>>
Deploy transaction was sent.
Contract address: 0x022bb...
Transaction hash: 0x0564...

* CON PROTOSTAR

```bash
protostar deploy ./build/ERC20ex02.json --network alpha-goerli -i 2576485153152103101814659868666844275075629902050884189831195075733091688326 2576485153152103101814659868666844275075629902050884189831195075733091688326
```

En este caso enviarà un mensaje con la creacción y hash.

```bash
Deploy transaction was sent.
Contract address: 0x022bb88e1219fddbca1cc9ac3927798e7af0268bbb439733d9a72a47557b7cd1
Transaction hash: 0x0564e186f63d941f5b82a9f67c4c313b1641ed6919dc5cf56615e2f6a2bd28bd
```

Una vez que el secuenciador recoge la transacción y la agrega a un bloque, podemos explorar nuestro contrato inteligente ERC20 recientemente implementado utilizando Voyager.

[Deploy ERC20ex02](https://goerli.voyager.online/contract/0x022bb88e1219fddbca1cc9ac3927798e7af0268bbb439733d9a72a47557b7cd1)

## Lectura del simbolo y supply del ERC20

Una vez se ha deployado el smart podemos ir a leer si se han ejecutado los valores que nos hacen falta para el Evaluador.

![Graph](/contracts/Imagenes/totalex02.png)

![Graph](/contracts/Imagenes/total2ex02.png)

Como podemos ver todo parece correcto. 

## Verificación de ejercio

Ahora estamos listos para enviar nuestro contrato inteligente ERC721 [Deploy ERC20ex02](https://goerli.voyager.online/contract/0x022bb88e1219fddbca1cc9ac3927798e7af0268bbb439733d9a72a47557b7cd1) al [Evaluador](https://goerli.voyager.online/contract/0x14ece8a1dcdcc5a56f01a987046f2bd8ddfb56bc358da050864ae6da5f71394) usando la función de `submit_erc20_solution`. Enviamos así nuestro contrato inteligente ERC20 al Evaluador.

![Graph](/contracts/Imagenes/submiterc20ex02.png)

* [Hash](https://goerli.voyager.online/tx/0x54c10d6e85ace788daa130b5e452fdad2f3d5de09f95e0c30bb4109b727893d)


Si todo salió según lo planeado, deberíamos poder verificar que nuestra billetera Argent X `0x05B23d29C5b33a1cf0c4e50F798f9E22c7254913368996436682852EfCF69f86` si obtuvo 6 puntos en el contrato inteligente [Point Counter](https://goerli.voyager.online/contract/0x228c0e6db14052a66901df14a9e8493c0711fa571860d9c62b6952997aae58b).

![Graph](/contracts/Imagenes/balanceofex01.png)

También puede revisar de una forma mas entretenida sus resultados en la siguiente web 

* https://starknet-tutorials.vercel.app

![Graph](/contracts/Imagenes/puntosex01.png)
