---
title: Odesílání transakcí pomocí služby Azure blockchain
description: Kurz, jak pomocí služby Azure blockchain nasadit inteligentní kontrakt a poslat soukromou transakci.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 3cfbbdc5b95d1607738b132980320d2ff7c99788
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698380"
---
# <a name="tutorial-send-transactions-using-azure-blockchain-service"></a>Kurz: Odesílání transakcí pomocí služby Azure blockchain

V tomto kurzu vytvoříte uzly transakcí pro testování smluv a ochrany osobních údajů v transakci.  Truffle použijete k vytvoření místního vývojového prostředí a nasazení inteligentního kontraktu a odeslání privátní transakce.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Přidat uzly transakce
> * Nasazení inteligentního kontraktu pomocí Truffle
> * Odeslat transakci
> * Ověřit ochranu osobních údajů transakce

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Dokončení [Vytvoření člena blockchain pomocí Azure Portal](create-member.md)
* Kompletní [rychlé spuštění: Použití Truffle pro připojení k síti konsorcia](connect-truffle.md)
* Nainstalujte [Truffle](https://github.com/trufflesuite/truffle). Truffle vyžaduje instalaci několika nástrojů, včetně [Node. js](https://nodejs.org), [Gitu](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Nainstalujte [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Pro Web3 je potřeba Python.
* Nainstalovat [Visual Studio Code](https://code.visualstudio.com/Download)
* Nainstalovat [rozšíření pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)

## <a name="create-transaction-nodes"></a>Vytváření transakčních uzlů

Ve výchozím nastavení máte jeden uzel transakce. Přidáváme ještě dva další. Jeden z uzlů se účastní privátní transakce. Druhá není obsažena v privátní transakci.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte do svého člena Azure blockchain a vyberte **uzly transakce > přidat**.
1. Dokončete nastavení pro nový uzel transakce s názvem `alpha`.

    ![Vytvořit uzel transakce](./media/send-transaction/create-node.png)

    | Nastavení | Hodnota | Popis |
    |---------|-------|-------------|
    | Name | `alpha` | Název uzlu transakce. Název slouží k vytvoření adresy DNS pro koncový bod uzlu transakce. Například, `alpha-mymanagedledger.blockchain.azure.com`. |
    | Heslo | Silné heslo | Heslo se používá pro přístup ke koncovému bodu uzlu transakce pomocí základního ověřování.

1. Vyberte **Vytvořit**.

    Zřizování nového uzlu transakce trvá přibližně 10 minut.

1. Opakujte kroky 2 až 4 pro přidání uzlu transakce s názvem `beta`.

Během zřizování uzlů můžete pokračovat v kurzu. Po dokončení zřizování budete mít tři uzly transakce.

## <a name="open-truffle-console"></a>Otevřít konzolu Truffle

1. Otevřete příkazový řádek Node. js nebo prostředí.
1. Změňte cestu k adresáři projektu Truffle z požadovaného [rychlého startu: Pomocí Truffle se připojte k síti](connect-truffle.md)konsorcia. Například

    ```bash
    cd truffledemo
    ```

1. Použijte konzolu Truffle pro připojení k výchozímu uzlu transakce.

    ``` bash
    truffle console --network defaultnode
    ```

    Truffle se připojí k výchozímu uzlu transakce a poskytne interaktivní konzolu.

## <a name="create-ethereum-account"></a>Vytvořit účet Ethereem

Pomocí Web3 se připojte k výchozímu uzlu transakce a vytvořte účet Ethereem. Můžete volat metody v objektu Web3 pro interakci s vaším uzlem transakce.

1. Vytvoří nový účet na výchozím uzlu transakce. Nahraďte parametr password vlastním silným heslem.

    ```bash
    web3.eth.personal.newAccount("1@myStrongPassword");
    ```

    Poznamenejte si vrácenou adresu účtu a heslo. V další části budete potřebovat adresu a heslo účtu Ethereem.

1. Ukončete vývojové prostředí Truffle.

    ```bash
    .exit
    ```

## <a name="configure-truffle-project"></a>Konfigurace projektu Truffle

Ke konfiguraci projektu Truffle potřebujete některé informace o uzlu transakce z Azure Portal.

### <a name="transaction-node-public-key"></a>Veřejný klíč uzlu transakce

Každý uzel transakce má veřejný klíč. Veřejný klíč umožňuje odeslat do uzlu soukromou transakci. Aby bylo možné odeslat transakci z výchozího uzlu transakce do uzlu transakce *alfa* , budete potřebovat veřejný klíč uzlu *Alpha* transakce.

Veřejný klíč můžete získat ze seznamu uzly transakce. Zkopírujte veřejný klíč pro uzel Alpha a uložte hodnotu pro pozdější období v tomto kurzu.

![Seznam uzlů transakcí](./media/send-transaction/node-list.png)

### <a name="transaction-node-endpoint-addresses"></a>Adresy koncových bodů uzlu transakce

1. V Azure Portal přejděte na jednotlivé uzly transakce a vyberte **uzly transakcí > připojovací řetězce**.
1. Zkopírujte a uložte adresu URL koncového bodu z **https (přístupový klíč 1)** pro každý uzel transakce. Adresy koncových bodů pro konfigurační soubor inteligentních smluv budete potřebovat později v tomto kurzu.

    ![Adresa koncového bodu transakce](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Upravit konfigurační soubor

1. Spusťte Visual Studio Code a otevřete složku adresáře projektu Truffle pomocí nabídky **soubor > otevřít složku** .
1. Otevřete konfigurační soubor `truffle-config.js`Truffle.
1. Nahraďte obsah souboru následujícími konfiguračními informacemi. Přidejte proměnné obsahující adresy koncových bodů a informace o účtu. Nahraďte oddíly lomené závorky hodnotami, které jste shromáždili z předchozích částí.

    ``` javascript
    var defaultnode = "<default transaction node connection string>";
    var alpha = "<alpha transaction node connection string>";
    var beta = "<beta transaction node connection string>";
    
    var myAccount = "<Ethereum account address>";
    var myPassword = "<Ethereum account password>";
    
    var Web3 = require("web3");
    
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
          gasPrice: 0,
          from: myAccount
        },
        alpha: {
          provider: new Web3.providers.HttpProvider(alpha),
          network_id: "*",
        },
        beta: {
          provider: new Web3.providers.HttpProvider(beta),
          network_id: "*",
        }
      },
      compilers: {
        solc: {
          evmVersion: "byzantium"
        }
      }
    }
    ```

1. Uložte změny do `truffle-config.js`.

## <a name="create-smart-contract"></a>Vytvořit inteligentní kontrakt

1. Ve složce **smlouvy** vytvořte nový soubor s názvem `SimpleStorage.sol`. Přidejte následující kód.

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
    
1. Ve složce **migrace** vytvořte nový soubor s názvem `2_deploy_simplestorage.js`. Přidejte následující kód.

    ```solidity
    var SimpleStorage = artifacts.require("SimpleStorage.sol");
    
    module.exports = function(deployer) {
    
      // Pass 42 to the contract as the first constructor parameter
      deployer.deploy(SimpleStorage, "42", {privateFor: ["<alpha node public key>"], from:"<Ethereum account address>"})  
    };
    ```

1. Nahraďte hodnoty v lomených závorkách.

    | Value | Popis
    |-------|-------------
    | \<alpha node public key\> | Veřejný klíč uzlu Alpha
    | \<Ethereum account address\> | Adresa ethereem účtu vytvořená ve výchozím uzlu transakce

    V tomto příkladu je počáteční hodnota hodnoty **storeData** nastavená na 42.

    **privateFor** definuje uzly, ke kterým je kontrakt k dispozici. V tomto příkladu může účet výchozího uzlu transakce přetypovat soukromé transakce na uzel **alfa** . Můžete přidat veřejné klíče pro všechny účastníky privátní transakce. Pokud nezahrnete **privateFor:** a **from:** , transakce Smart kontraktu jsou veřejné a mohou je zobrazit všichni členové konsorcia.

1. Uložte všechny soubory tak, že vyberete **soubor > Uložit vše**.

## <a name="deploy-smart-contract"></a>Nasazení inteligentního kontraktu

K nasazení `SimpleStorage.sol` do výchozí sítě uzlu transakce použijte Truffle.

```bash
truffle migrate --network defaultnode
```

Truffle nejprve zkompiluje a potom nasadí **SimpleStorage** Smart Contract.

Příklad výstupu:

```
admin@desktop:/mnt/c/truffledemo$ truffle migrate --network defaultnode

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

## <a name="validate-contract-privacy"></a>Ověření ochrany osobních údajů smlouvy

Z důvodu ochrany osobních údajů smluv se hodnoty smluv můžou dotazovat jenom z uzlů, které jsme deklarovali v **privateFor**. V tomto příkladu se můžeme dotazovat na výchozí uzel transakce, protože účet v tomto uzlu existuje. 

1. Pomocí konzoly Truffle se připojte k výchozímu uzlu transakce.

    ```bash
    truffle console --network defaultnode
    ```

1. V konzole Truffle spusťte kód, který vrací hodnotu instance kontraktu.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Pokud je dotazování na výchozí uzel transakce úspěšný, je vrácena hodnota 42. Příklad:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network defaultnode
    truffle(defaultnode)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Ukončete konzolu Truffle.

    ```bash
    .exit
    ```

Vzhledem k tomu, že jsme v **privateFor**deklarovali veřejný klíč uzlu **alfa** , můžeme se dotázat na uzel **alfa** .

1. Pomocí konzoly Truffle se připojte k uzlu **Alpha** .

    ```bash
    truffle console --network alpha
    ```

1. V konzole Truffle spusťte kód, který vrací hodnotu instance kontraktu.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Pokud je dotaz na uzel **Alpha** úspěšný, je vrácena hodnota 42. Příklad:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network alpha
    truffle(alpha)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Ukončete konzolu Truffle.

    ```bash
    .exit
    ```

Vzhledem k tomu, že jsme v **privateFor**nedeklarovali veřejný klíč uzlu **beta verze** , nebudeme moct zadat dotaz na uzel **beta** z důvodu ochrany osobních údajů od smlouvy.

1. Pomocí konzoly Truffle se připojte k uzlu **beta verze** .

    ```bash
    truffle console --network beta
    ```

1. Spusťte kód, který vrátí hodnotu instance kontraktu.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

1. Dotaz na uzel **beta verze** se nezdařil, protože kontrakt je privátní. Příklad:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network beta
    truffle(beta)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    Thrown:
    Error: Returned values aren't valid, did it run Out of Gas?
        at XMLHttpRequest._onHttpResponseEnd (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:345:8)
        at XMLHttpRequest._setReadyState (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:219:8)
        at XMLHttpRequestEventTarget.dispatchEvent (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request-event-target.ts:44:13)
        at XMLHttpRequest.request.onreadystatechange (/mnt/c/truffledemo/node_modules/web3-providers-http/src/index.js:96:13)
    ```

1. Ukončete konzolu Truffle.

    ```bash
    .exit
    ```
    
## <a name="send-a-transaction"></a>Odeslat transakci

1. Vytvořte soubor s názvem `sampletx.js`. Uložte ho do kořenového adresáře vašeho projektu.
1. Následující skript nastaví hodnotu proměnné **storedData** kontraktu na 65. Přidejte kód do nového souboru.

    ```javascript
    var SimpleStorage = artifacts.require("SimpleStorage");
    
    module.exports = function(done) {
      console.log("Getting deployed version of SimpleStorage...")
      SimpleStorage.deployed().then(function(instance) {
        console.log("Setting value to 65...");
        return instance.set("65", {privateFor: ["<alpha node public key>"], from:"<Ethereum account address>"});
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

    Nahraďte hodnoty v lomených závorkách a pak soubor uložte.

    | Hodnota | Popis
    |-------|-------------
    | \<alpha node public key\> | Veřejný klíč uzlu Alpha
    | \<Ethereum account address\> | Adresa ethereem účtu vytvořená ve výchozím uzlu transakce.

    **privateFor** definuje uzly, ke kterým je transakce k dispozici. V tomto příkladu může účet výchozího uzlu transakce přetypovat soukromé transakce na uzel **alfa** . Je nutné přidat veřejné klíče pro všechny účastníky privátní transakce.

1. Použijte Truffle pro spuštění skriptu pro výchozí uzel transakce.

    ```bash
    truffle exec sampletx.js --network defaultnode
    ```

1. V konzole Truffle spusťte kód, který vrací hodnotu instance kontraktu.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Pokud byla transakce úspěšná, vrátí se hodnota 65. Příklad:
    
    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```

1. Ukončete konzolu Truffle.

    ```bash
    .exit
    ```
    
## <a name="validate-transaction-privacy"></a>Ověřit ochranu osobních údajů transakce

Z důvodu ochrany osobních údajů transakce je možné transakce provádět pouze na uzlech, které jste deklarovali v **privateFor**. V tomto příkladu můžeme provádět transakce, protože jsme deklarovali veřejný klíč uzlu **alfa** v **privateFor**. 

1. Použijte Truffle ke spuštění transakce na uzlu **Alpha** .

    ```bash
    truffle exec sampletx.js --network alpha
    ```
    
1. Spusťte kód, který vrací hodnotu instance kontraktu.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```
    
    Pokud byla transakce úspěšná, vrátí se hodnota 65. Příklad:

    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```
    
1. Ukončete konzolu Truffle.

    ```bash
    .exit
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete prostředky odstranit odstraněním `myResourceGroup` skupiny prostředků, kterou jste vytvořili ve službě Azure blockchain.

Odstranění skupiny prostředků:

1. V Azure Portal přejděte do **skupiny prostředků** v levém navigačním podokně a vyberte skupinu prostředků, kterou chcete odstranit.
1. Vyberte **Odstranit skupinu prostředků**. Potvrďte odstranění zadáním názvu skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste přidali dva uzly transakce k předvedení smlouvy a ochrany osobních údajů v transakci. Použili jste výchozí uzel pro nasazení privátního kontraktu Smart. Pomocí dotazu na hodnoty kontraktu a provádění transakcí na blockchain jste otestovali soukromí.

> [!div class="nextstepaction"]
> [Vývoj aplikací blockchain pomocí služby Azure blockchain](develop.md)
