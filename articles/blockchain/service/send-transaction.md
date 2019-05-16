---
title: Odeslání transakce pomocí služby Azure Blockchain
description: Kurz o tom, jak pomocí Azure Blockchain Service můžete nasadit inteligentní smlouvy a odeslat privátní transakce.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 0b5e39e9cf2fc3ffe91db6587bc1ed1bab079e93
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65777323"
---
# <a name="tutorial-send-transactions-using-azure-blockchain-service"></a>Kurz: Odeslání transakce pomocí služby Azure Blockchain

V tomto kurzu budete vytvářet uzly transakce otestovat kontrakt a transakce o ochraně osobních údajů.  Použijete Truffle místní vývojové prostředí vytvářet a nasazovat inteligentní smlouvy a odesílat privátní transakce.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Přidání uzlů transakce
> * Pomocí Truffle nasadit inteligentní kontraktu
> * Odeslat do transakce
> * Ověření ochrany osobních údajů transakce

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Kompletní [vytvořit člena blockchainu pomocí webu Azure portal](create-member.md)
* Kompletní [rychlý start: Slouží k připojení do sítě konsorcia Truffle](connect-truffle.md)
* Truffle vyžaduje instalaci několika nástrojů včetně [Node.js](https://nodejs.org), [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git), a [Truffle](https://github.com/trufflesuite/truffle).

    Chcete-li rychle nastavit ve Windows 10, nainstalovat [Ubuntu ve Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6) prostředí Unix Bash terminálu nainstalujte [Truffle](https://github.com/trufflesuite/truffle). Ubuntu ve Windows distribuce zahrnuje Node.js a Git.

* Install [Visual Studio Code](https://code.visualstudio.com/Download)
* Nainstalujte [rozšíření Visual Studio Code Solidity](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)

## <a name="create-transaction-nodes"></a>Vytvoření transakce uzlů

Ve výchozím nastavení budete mít jeden uzel transakce. My budeme přidávat další dvě. Jeden z uzlů se účastní privátní transakce. Druhý není součástí privátní transakce.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte do vaší Azure Blockchain člena a vyberte **transakce uzly > Přidat**.
1. Zadejte nastavení pro nový uzel transakci s názvem `alpha`.

    ![Vytvořit uzel transakce](./media/send-transaction/create-node.png)

    | Nastavení | Hodnota | Popis |
    |---------|-------|-------------|
    | Název | `alpha` | Název uzlu transakce. Název slouží k vytvoření adresu serveru DNS pro koncový bod transakce uzlu. Například, `alpha-mymanagedledger.blockchain.azure.com`. |
    | Heslo | silné heslo | Heslo se používá pro přístup ke koncovému bodu uzlu transakce se základním ověřováním.

1. Vyberte **Vytvořit**.

    Nový uzel transakce zřizování trvá asi 10 minut.

1. Opakujte kroky 2 až 4 přidejte uzel transakce `beta`.

Zatímco probíhá zřizování uzly, můžete pokračovat v kurzu. Po dokončení zřizování, budete mít tři uzly transakce.

## <a name="open-truffle-project"></a>Otevřete projekt Truffle

1. Otevřete terminál prostředí Bash.
1. Změnit cesty k adresáři projektu Truffle z požadovaného softwaru [rychlý start: Slouží k připojení do sítě konsorcia Truffle](connect-truffle.md). Například

    ```bash
    cd truffledemo
    ```

1. Spuštění Truffle interaktivní vývojové konzole.

    ``` bash
    truffle develop
    ```

    Truffle vytvoří blockchain místním vývojovém a nabízí interaktivní konzolu.

## <a name="connect-to-transaction-node"></a>Připojení k transakčnímu uzlu

Web3 použijte pro připojení k výchozí uzel transakcí a vytvoření účtu služby. Web3 připojovací řetězec můžete získat z webu Azure portal.

1. Na webu Azure Portal, přejděte k uzlu transakce výchozí a vyberte **transakce uzly > ukázkový kód > Web3**.
1. Zkopírujte JavaScript z **HTTPS (přístupový klíč 1)** ![Web3 ukázkový kód](./media/send-transaction/web3-code.png)

1. Vložte kód jazyka JavaScript Web3 pro výchozí uzel transakce do konzoly Truffle interaktivní vývoj. Kód vytvoří objekt Web3, který je připojený k uzlu služby Azure Blockchain transakce.

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    ```

    Můžete volat metody u objektu Web3 k interakci s transakcí uzlu.

1. Vytvořte nový účet na výchozí uzel transakce. Nahraďte parametr heslo na silné heslo.

    ```bash
    web3.eth.personal.newAccount("1@myStrongPassword");
    ```

    Ujistěte se, poznamenejte si adresu účtu, který je vrácen a heslo, které jste použili k další části.

1. Ukončete Truffle vývojové prostředí.

    ```bash
    .exit
    ```

## <a name="configure-truffle-project"></a>Konfigurace projektu Truffle

Ke konfiguraci projektu Truffle, budete potřebovat některé informace o uzlu transakce z portálu Azure portal.

### <a name="transaction-node-public-key"></a>Transakce uzel veřejný klíč

Každý uzel transakce má veřejný klíč. Veřejný klíč vám umožní odesílat privátní transakce do uzlu. Aby bylo možné odesílat z výchozí uzel transakci do transakce *alfa* transakce uzel, je nutné *alfa* transakce uzel veřejný klíč.

V seznamu uzlu transakce můžete získat veřejný klíč. Zkopírujte veřejný klíč pro uzel alfa a uložte hodnotu v pozdější části kurzu.

![Seznam uzlů transakce](./media/send-transaction/node-list.png)

### <a name="transaction-node-endpoint-addresses"></a>Adresy koncových bodů uzel transakce

1. Na webu Azure Portal, přejděte k jednotlivým uzlům transakce a vyberte **transakce uzly > připojovací řetězce**.
1. Zkopírujte a uložte adresu URL koncového bodu z **HTTPS (přístupový klíč 1)** pro každý uzel transakce. Adresy koncových bodů pro konfigurační soubor inteligentní smlouvy potřebovat později v tomto kurzu.

    ![Adresa koncového bodu transakce](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Upravte konfigurační soubor

1. Spusťte Visual Studio Code a otevřete Truffle projektu adresáře složky pomocí **soubor > Otevřít složku** nabídky.
1. Otevřete konfigurační soubor Truffle `truffle-config.js`.
1. Nahraďte obsah souboru následující informace o konfiguraci. Přidejte proměnné obsahující informace o účtu a adresy koncových bodů. V částech ostré závorky nahraďte hodnotami, které jste shromáždili z předchozí části.

``` javascript
var defaultnode = "<default transaction node connection string>";
var alpha = "<alpha transaction node connection string>";
var beta = "<beta transaction node connection string>";

var myAccount = "<account address>";
var myPassword = "<account password>";

var Web3 = require("web3");
```

Přidejte kód konfigurace, který **module.exports** oddílu konfigurace.

```javascript
module.exports = {
  networks: {
    defaultnode: {
      provider:(() =>  {
      const AzureBlockchainProvider = new Web3.providers.HttpProvider(defaultnode);

      const web3 = new Web3(AzureBlockchainProvider);
      web3.eth.personal.unlockAccount(myAccount, myPassword);

      return AzureBlockchainProvider;
      })(),

      network_id: "*",
      gas: 0,
      gasPrice: 0,
      from: myAccount
    },
    alpha: {
      provider: new Web3.providers.HttpProvider(alpha),
      network_id: "*",
      gas: 0,
      gasPrice: 0
    },
    beta: {
      provider: new Web3.providers.HttpProvider(beta),
      network_id: "*",
      gas: 0,
      gasPrice: 0
    }
  }
}
```

## <a name="create-smart-contract"></a>Vytvoření inteligentního kontraktu

Ve složce **kontrakty**, vytvořte nový soubor s názvem `SimpleStorage.sol`. Přidejte následující kód.

```solidity
pragma solidity >=0.4.21 <0.6.0;

contract SimpleStorage {
    string public storedData;

    constructor(string memory initVal) public {
        storedData = initVal;
    }

    function set(string memory x) public {
        storedData = x;
    }

    function get() view public returns (string memory retVal) {
        return storedData;
    }
}
```

Ve složce **migrace**, vytvořte nový soubor s názvem `2_deploy_simplestorage.js`. Přidejte následující kód.

```solidity
var SimpleStorage = artifacts.require("SimpleStorage.sol");

module.exports = function(deployer) {

  // Pass 42 to the contract as the first constructor parameter
  deployer.deploy(SimpleStorage, "42", {privateFor: ["<alpha node public key>"], from:"<Account address>"})  
};
```

Nahraďte hodnoty v lomených závorkách.

| Hodnota | Popis
|-------|-------------
| \<veřejný klíč alfa uzlu\> | Veřejný klíč alfa uzlu
| \<Adresa účtu\> | Účet adresu vytvořenou ve výchozí uzel transakce.

V tomto příkladu počáteční hodnotu **storeData** je hodnota nastavena na 42.

**privateFor** definuje uzly, na které je k dispozici smlouvy. V tomto příkladu uzel transakce výchozí účet můžete přetypovat privátní transakcí, které **alfa** uzlu. Budete muset přidat veřejných klíčů pro všechny účastníky privátní transakce. Pokud nechcete zahrnout **privateFor:** a **z:**, inteligentní smlouvy transakce jsou veřejné a můžou je zobrazit všichni členové consortium.

Uložte všechny soubory tak, že vyberete **soubor > Uložit vše**.

## <a name="deploy-smart-contract"></a>Nasazení inteligentních kontraktu

Nasazení pomocí Truffle `SimpleStorage.sol` výchozí transakce uzlu sítě.

```bash
truffle migrate --network defaultnode
```

Truffle nejprve zkompiluje a pak nasadí **SimpleStorage** inteligentní kontraktu.

Příklad výstupu:

```
pat@DESKTOP:/mnt/c/truffledemo$ truffle migrate --network defaultnode

2_deploy_simplestorage.js
=========================

   Deploying 'SimpleStorage'
   -------------------------
   > transaction hash:    0x3f695ff225e7d11a0239ffcaaab0d5f72adb545912693a77fbfc11c0dbe7ba72
   > Blocks: 2            Seconds: 12
   > contract address:    0x0b15c15C739c1F3C1e041ef70E0011e641C9D763
   > account:             0x1a0B9683B449A8FcAd294A01E881c90c734735C3
   > balance:             0
   > gas used:            0
   > gas price:           0 gwei
   > value sent:          0 ETH
   > total cost:          0 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:                   0 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0 ETH
```

## <a name="validate-contract-privacy"></a>Ověřit smlouvy o ochraně osobních údajů

Z důvodu ochrany osobních údajů smlouvy, smlouvy hodnot se dá dotazovat jenom z uzlů jsme deklarované v **privateFor**. V tomto příkladu zadáme dotaz výchozí transakce uzel vzhledem k tomu, že účet existuje v tomto uzlu. Pomocí konzole Truffle se připojte k výchozí uzel transakce.

```bash
truffle console --network defaultnode
```

Provedení příkazu, který vrací hodnotu instance kontraktu.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Pokud je úspěšné dotazování výchozí uzel transakce, je vrácena hodnota 42.

Příklad výstupu:

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network defaultnode
truffle(defaultnode)> SimpleStorage.deployed().then(function(instance){return instance.get();})
'42'
```

Ukončete konzolu.

```bash
.exit
```

Vzhledem k tomu, že můžeme deklarovat **alfa** uzlu veřejný klíč v **privateFor**, jsme můžete zadávat dotazy **alfa** uzlu. Pomocí konzole Truffle připojit k **alfa** uzlu.

```bash
truffle console --network alpha
```

Provedení příkazu, který vrací hodnotu instance kontraktu.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Pokud dotazuje **alfa** uzlu je úspěšný, je vrácena hodnota 42.

Příklad výstupu:

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network alpha
truffle(alpha)> SimpleStorage.deployed().then(function(instance){return instance.get();})
'42'
```

Ukončete konzolu.

```bash
.exit
```

Protože jsme nedeklarovalo **beta** uzlu veřejný klíč v **privateFor**, nebude schopný zadávat dotazy **beta** uzlu z důvodu smlouvy o ochraně osobních údajů. Pomocí konzole Truffle připojit k **beta** uzlu.

```bash
truffle console --network beta
```

Provedení příkazu, který vrací hodnotu instance kontraktu.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Dotazování **beta** uzel selže, protože kontrakt je privátní.

Příklad výstupu:

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network beta
truffle(beta)> SimpleStorage.deployed().then(function(instance){return instance.get();})
Thrown:
Error: Returned values aren't valid, did it run Out of Gas?
    at XMLHttpRequest._onHttpResponseEnd (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:345:8)
    at XMLHttpRequest._setReadyState (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:219:8)
    at XMLHttpRequestEventTarget.dispatchEvent (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request-event-target.ts:44:13)
    at XMLHttpRequest.request.onreadystatechange (/mnt/c/truffledemo/node_modules/web3-providers-http/src/index.js:96:13)
```

Ukončete konzolu.

```bash
.exit
```

## <a name="send-a-transaction"></a>Odeslat do transakce

Vytvořte soubor s názvem `sampletx.js`. Uložte jej v kořenové složce vašeho projektu.

Tento skript nastaví kontrakt **storedData** 65 hodnoty proměnné. Přidejte kód do nového souboru.

```javascript
var SimpleStorage = artifacts.require("SimpleStorage");

module.exports = function(done) {
  console.log("Getting deployed version of SimpleStorage...")
  SimpleStorage.deployed().then(function(instance) {
    console.log("Setting value to 65...");
    return instance.set("65", {privateFor: ["<alpha node public key>"], from:"<Account address>"});
  }).then(function(result) {
    console.log("Transaction:", result.tx);
    console.log("Finished!");
    done();
  }).catch(function(e) {
    console.log(e);
    done();
  });
};
```

Nahraďte hodnoty v lomených závorkách. pak soubor uložte.

| Hodnota | Popis
|-------|-------------
| \<veřejný klíč alfa uzlu\> | Veřejný klíč alfa uzlu
| \<Adresa účtu\> | Účet adresu vytvořenou ve výchozí uzel transakce.

**privateFor** definuje uzly, na které je k dispozici transakce. V tomto příkladu uzel transakce výchozí účet můžete přetypovat privátní transakcí, které **alfa** uzlu. Budete muset přidat veřejných klíčů pro všechny účastníky privátní transakce.

Použití Truffle ke spuštění skriptu pro výchozí uzel transakce.

```bash
truffle exec sampletx.js --network defaultnode
```

Provedení příkazu, který vrací hodnotu instance kontraktu.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Pokud transakce byla úspěšná, vrátí se hodnota 65.

Příklad výstupu:

```
Getting deployed version of SimpleStorage...
Setting value to 65...
Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
Finished!
```

Ukončete konzolu.

```bash
.exit
```

## <a name="validate-transaction-privacy"></a>Ověření ochrany osobních údajů transakce

Z důvodu ochrany osobních údajů transakce, transakce lze provést pouze na uzlech jsme deklarované v **privateFor**. V tomto příkladu budeme provádět transakce vzhledem k tomu, že můžeme deklarovat **alfa** uzlu veřejný klíč v **privateFor**. Spustit transakci na pomocí Truffle **alfa** uzlu.

```bash
truffle exec sampletx.js --network alpha
```

Provedení příkazu, který vrací hodnotu instance kontraktu.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Pokud transakce byla úspěšná, vrátí se hodnota 65.

Příklad výstupu:

```
Getting deployed version of SimpleStorage...
Setting value to 65...
Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
Finished!
```

Ukončete konzolu.

```bash
.exit
```

V tomto kurzu přidáte dva uzly transakce k předvedení kontrakt a transakce o ochraně osobních údajů. Použít výchozí uzel k nasazení soukromé inteligentní smlouvy. Ochrana osobních údajů dotazování hodnot smlouvy a provádí transakce s blockchainem testování.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky můžete odstranit tak, že odstraníte `myResourceGroup` skupiny prostředků vytvořené službou Azure Blockchain.

Pokud chcete odstranit skupinu prostředků:

1. Na webu Azure Portal, přejděte na **skupiny prostředků** v levém navigačním podokně a vyberte skupinu prostředků, kterou chcete odstranit.
1. Vyberte **Odstranit skupinu prostředků**. Ověření odstranění proveďte tak, že zadáte název skupiny prostředků a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vývoj pomocí služby Azure Blockchain blockchainové aplikace](develop.md)
