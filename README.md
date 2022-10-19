# Tutorial Nadai ERC20 on StarkNet

¡Bienvenidos! Este es un taller automatizado que explicará cómo implementar un token ERC20 en StarkNet y personalizarlo para realizar funciones específicas. El estándar ERC20 se describe [aquí](https://docs.openzeppelin.com/contracts/3.x/api/token/erc20). Está dirigido a desarrolladores que: 

- Comprender la sintaxis de Cairo 
- Comprender el estándar de token ERC20.

Este tutorial ha sido escrito por Florian Charlier ([@trevis_dev](https://twitter.com/trevis_dev)) en colaboración de Henri Lieutaud and Lucas Levy, basado en los originales de Henri[ERC20 101](https://github.com/l-henri/erc20-101) y [ERC20 102](https://github.com/l-henri/erc20-102) tutoriales de Solidity.

Repo original [Aquí](https://github.com/starknet-edu/starknet-erc20).

## Introducción

### Disclaimer

No espere ningún tipo de beneficio al usar esto, aparte de aprender un montón de cosas interesantes sobre StarkNet, el primer paquete acumulativo de validez de propósito general en Ethereum Mainnet. 

StarkNet todavía está en Alfa. Esto significa que el desarrollo está en curso y que la pintura no está seca en todas partes. Las cosas mejorarán y, mientras tanto, ¡hacemos que las cosas funcionen con un poco de cinta adhesiva aquí y allá! 

### ¿Cómo funciona?

El objetivo de este tutorial es personalizar e implementar un contrato ERC20 en StarkNet. Su progreso será verificado por un [contrato de evaluador](contratos/Evaluador.cairo), implementado en StarkNet, que le otorgará puntos en forma de [tokens ERC20](contratos/token/ERC20/TUTOERC20.cairo). 

Cada ejercicio requerirá que agregue funcionalidad a su token ERC20. 

Para cada ejercicio, deberá escribir una nueva versión en su contrato, implementarlo y enviarlo al evaluador para su corrección.

### ¿Dónde estoy? 

Este taller es el segundo de una serie destinada a enseñar cómo construir en StarkNet. Echa un vistazo a lo siguiente:
​

| Topic                                             | GitHub repo                                                                            |
| ------------------------------------------------- | -------------------------------------------------------------------------------------- |
| Learn how to read Cairo code                      | [Cairo 101](https://github.com/starknet-edu/starknet-cairo-101)                        |
| Deploy and customize an ERC721 NFT                | [StarkNet ERC721](https://github.com/starknet-edu/starknet-erc721)                     |
| Deploy and customize an ERC20 token (you are here)| [StarkNet ERC20](https://github.com/starknet-edu/starknet-erc20)                       |
| Build a cross layer application                   | [StarkNet messaging bridge](https://github.com/starknet-edu/starknet-messaging-bridge) |
| Debug your Cairo contracts easily                 | [StarkNet debug](https://github.com/starknet-edu/starknet-debug)                       |
| Design your own account contract                  | [StarkNet account abstraction](https://github.com/starknet-edu/starknet-accounts)      |

### Proporcionar comentarios y obtener ayuda 

Una vez que haya terminado de trabajar en este tutorial, ¡sus comentarios serán muy apreciados!

Complete [este formulario](https://forms.reform.app/starkware/untitled-form-4/kaes2e) para informarnos qué podemos hacer para mejorarlo. 

Y si tiene dificultades para seguir adelante, ¡háganoslo saber! Este taller está destinado a ser lo más accesible posible; queremos saber si no es el caso. 

¿Tienes alguna pregunta? Únase a nuestro servidor [Discord server](https://starknet.io/discord), regístrese y únase al canal #tutorials-support. ¿Está interesado en seguir talleres en línea sobre cómo aprender a desarrollar en StarkNet? [Subscríbete aquí](http://eepurl.com/hFnpQ5)

### Contribuyendo 

Este proyecto se puede mejorar y evolucionará a medida que StarkNet madure. ¡Sus contribuciones son bienvenidas! Aquí hay cosas que puede hacer para ayudar: 

- Crea una sucursal con una traducción a tu idioma .
- Corrija los errores si encuentra algunos. 
- Agregue una explicación en los comentarios del ejercicio si cree que necesita más explicación.
- Agregue ejercicios que muestren su característica favorita de El Cairo​.


## Preparándose para trabajar 

### Paso 1: Clonar el repositorio 

- Oficial:

```bash
git clone https://github.com/starknet-edu/starknet-erc20
cd starknet-erc721
```

- Nadai con Soluciones:

```bash
gh repo clone Nadai2010/Nadai-Cairo-ERC20-Starknet-Edu
cd Nadai-Cairo-ERC20-Starknet-Edu
```
### Paso 2: Configure su entorno 

Hay dos formas de configurar su entorno en StarkNet: Una instalación local o usando un contenedor docker.

- Para usuarios de Mac y Linux, recomendamos either
- Para usuarios de Windows recomendamos docker 

Para obtener instrucciones de configuración de producción, escribimos [este artículo](https://medium.com/starknet-edu/the-ultimate-starknet-dev-environment-716724aef4a7).

#### Opción A: Configurar un entorno Python local 

Configure el entorno siguiendo [estas instrucciones](https://starknet.io/docs/quickstart.html#quickstart)
- Instalar [OpenZeppelin's cairo contracts](https://github.com/OpenZeppelin/cairo-contracts).

```bash
pip install openzeppelin-cairo-contracts
```

#### Opción B: Usar un entorno dockerizado

- Linux y macos

Para mac m1: 

```bash
alias cairo='docker run --rm -v "$PWD":"$PWD" -w "$PWD" shardlabs/cairo-cli:latest-arm'
```

Para amd procesadores

```bash
alias cairo='docker run --rm -v "$PWD":"$PWD" -w "$PWD" shardlabs/cairo-cli:latest'
```

- Windows

```bash
docker run --rm -it -v ${pwd}:/work --workdir /work shardlabs/cairo-cli:latest
```

#### Paso 3: Pruebe que puede compilar el proyecto contratos de compilación

```bash
starknet-compile contracts/Evaluator.cairo
```

## Trabajando en el tutorial 

### Flujo de trabajo 

Para hacer este tutorial tendrás que interactuar con el contrato [`Evaluator.cairo`](contracts/Evaluator.cairo). Para validar un ejercicio tendrás que:

- Leer el código del evaluador para averiguar qué se espera de su contrato 
- Personaliza el código de tu contrato 
- Despliéguelo en la red de prueba de StarkNet. Esto se hace usando la CLI. 
- Registre su ejercicio para corrección, usando la función de `submit_exercise` en el evaluador. Esto se hace usando Voyager. 
- Llame a la función correspondiente en el contrato del evaluador para corregir su ejercicio y recibir sus puntos. Esto se hace usando Voyager. 

Por ejemplo para resolver el primer ejercicio el flujo de trabajo sería el siguiente: 


`deploy a smart contract that answers ex1` &rarr; `call submit_exercise on the evaluator providing your smart contract address` &rarr; `call ex2_test_erc20 on the evaluator contract`

**Su objetivo es reunir tantos puntos ERC20-101 como sea posible.** Tenga en cuenta : 

- La función de 'transferencia' de ERC20-101 ha sido deshabilitada para alentarlo a terminar el tutorial con una sola dirección Para recibir puntos, el evaluador debe alcanzar las llamadas a la función distribuir_punto. 
- Este repositorio contiene dos interfaces ([`IERC20Solution.cairo`](contracts/IERC20Solution.cairo) y [`IExerciseSolution.cairo`](contracts/IERC20Solution.cairo)). Por ejemplo, para la primera parte, su contrato ERC20 deberá ajustarse a la primera interfaz para validar los ejercicios; es decir, su contrato debe implementar todas las funciones descritas en `IERC20Solution.cairo`. 

- **Realmente recomendamos que lea el contrato de [`Evaluator.cairo`](contracts/Evaluator.cairo) para comprender completamente lo que se espera de cada ejercicio**. En este archivo Léame se proporciona una descripción de alto nivel de lo que se espera de cada ejercicio. 

- El contrato de Evaluador a veces necesita realizar pagos para comprar sus tokens. ¡Asegúrate de que tenga suficientes fichas ficticias para hacerlo! De lo contrario, debe obtener tokens ficticios del contrato de tokens ficticios y enviarlos al evaluador.


### Direcciones y contratos oficiales

| Contract code                                                     | Contract on voyager                                                                                                                                                           |
| ----------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Points counter ERC20](contracts/token/ERC20/TUTOERC20.cairo)     | [0x228c0e6db14052a66901df14a9e8493c0711fa571860d9c62b6952997aae58b](https://goerli.voyager.online/contract/0x228c0e6db14052a66901df14a9e8493c0711fa571860d9c62b6952997aae58b) |
| [Evaluator](contracts/Evaluator.cairo)                            | [0x14ece8a1dcdcc5a56f01a987046f2bd8ddfb56bc358da050864ae6da5f71394](https://goerli.voyager.online/contract/0x14ece8a1dcdcc5a56f01a987046f2bd8ddfb56bc358da050864ae6da5f71394) |
| [Dummy ERC20 token (DTK20)](contracts/token/ERC20/DTKERC20.cairo) | [0x66aa72ce2916bbfc654fd18f9c9aaed29a4a678274639a010468a948a5e2a96](https://goerli.voyager.online/contract/0x66aa72ce2916bbfc654fd18f9c9aaed29a4a678274639a010468a948a5e2a96) |

​
​
## Lista de tareas 

¡Hoy implementará su propio token ERC20 en StarkNet! 

El tutorial está estructurado en dos partes

- En la primera parte (ejercicios 1 a 9), deberá implementar un contrato ERC-20. 
- En la segunda parte (ejercicios 10 a 18), implementará otro contrato que tendrá que interactuar con tokens ERC20.

### Ejercicio 1: Implementación de un ERC20 

- Llame a [`ex1_assign_rank()`](contracts/Evaluator.cairo#L134) en el contrato del evaluador para recibir un ticker aleatorio para su token ERC20, así como un suministro de token inicial (1 pt). Puede leer su ticker asignado y suministrarlo a través de la [página del evaluador en Voyager](https://goerli.voyager.online/contract/0x14ece8a1dcdcc5a56f01a987046f2bd8ddfb56bc358da050864ae6da5f71394) llamando a los captadores [`read_ticker()`]((contracts/Evaluator.cairo#L93 )) y [`read_supply()`](contracts/Evaluator.cairo#L102).
- Cree un contrato de token ERC20 con el ticker y el suministro adecuados. Puede usar [esta implementación](https://github.com/OpenZeppelin/cairo-contracts/blob/main/src/openzeppelin/token/erc20/ERC20.cairo) como base (2 pts) 
- Implementarlo en el testnet (verifique el constructor para los argumentos necesarios. También tenga en cuenta que los argumentos deben ser decimales). (1 punto)



```bash
starknet-compile contracts/token/ERC20/ERC20.cairo --output artifacts/ERC20.json
starknet deploy --contract ERC20 --inputs arg1 arg2 arg3 --network alpha-goerli 
```

- Llame a [`submit_erc20_solution()`](contracts/Evaluator.cairo#L733) en el Evaluador para establecer el contrato que desea evaluar (2 puntos) (los 3 puntos anteriores para el ERC20 y la implementación también se atribuyen en ese paso).

--------------
- **Nadai con Soluciones [Ejercicio 1](https://github.com/Nadai2010/Nadai-Cairo-ERC20-Starknet-Edu/blob/master/contracts/Soluci%C3%B3n/ERC20ex01.md)**
--------------

### Ejercicio 2: Verificación de su ERC20

- Llame a [`ex2_test_erc20()`](contracts/Evaluator.cairo#L150) en el evaluador para que verifique el ticker y suministre y atribuya sus puntos (2 puntos)

-------------
- **Nadai con Soluciones [Ejercicio 2](https://github.com/Nadai2010/Nadai-Cairo-ERC20-Starknet-Edu/blob/master/contracts/Soluci%C3%B3n/ERC20ex02.md)**
--------------

### Ejercicio 3: 