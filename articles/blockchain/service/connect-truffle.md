---
title: Připojení s využitím rozšíření Truffle
description: Připojení k síti služby Azure blockchain pomocí Truffle
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 9154bc749f7db337de67f501d5e5049dfd466156
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698473"
---
# <a name="quickstart-use-truffle-to-connect-to-an-azure-blockchain-service-network"></a>Rychlý start: Použití Truffle pro připojení k síti služby Azure blockchain

Truffle je blockchain vývojové prostředí, které můžete použít pro připojení k uzlu služby Azure blockchain.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* [Vytvoření člena Azure blockchain](create-member.md)
* Nainstalujte [Truffle](https://github.com/trufflesuite/truffle). Truffle vyžaduje instalaci několika nástrojů, včetně [Node. js](https://nodejs.org), [Gitu](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Nainstalujte [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Pro Web3 je potřeba Python.
* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/download).
* Nainstalujte [rozšíření pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity).

## <a name="create-truffle-project"></a>Vytvořit projekt Truffle

1. Otevřete příkazový řádek Node. js nebo prostředí.
1. Změňte adresář na místo, kde chcete vytvořit adresář projektu Truffle.
1. Vytvořte adresář pro projekt a změňte cestu k novému adresáři. Například

    ``` bash
    mkdir truffledemo
    cd truffledemo
    ```

1. Inicializujte projekt Truffle.

    ``` bash
    truffle init
    ```

1. Do složky projektu nainstalujte Ethereem JavaScript API web3. V současné době je vyžadována verze web3 verze 1.0.0-beta. 37.

    ``` bash
    npm install web3@1.0.0-beta.37
    ```

    Během instalace se může zobrazit upozornění npm.
    
## <a name="configure-truffle-project"></a>Konfigurace projektu Truffle

Ke konfiguraci projektu Truffle potřebujete některé informace o uzlu transakce z Azure Portal.

### <a name="transaction-node-endpoint-addresses"></a>Adresy koncových bodů uzlu transakce

1. V Azure Portal přejděte na jednotlivé uzly transakce a vyberte **uzly transakcí > připojovací řetězce**.
1. Zkopírujte a uložte adresu URL koncového bodu z **https (přístupový klíč 1)** pro každý uzel transakce. Adresy koncových bodů pro konfigurační soubor inteligentních smluv budete potřebovat později v tomto kurzu.

    ![Adresa koncového bodu transakce](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Upravit konfigurační soubor

1. Spusťte Visual Studio Code a otevřete složku adresáře projektu Truffle pomocí nabídky **soubor > otevřít složku** .
1. Otevřete konfigurační soubor `truffle-config.js`Truffle.
1. Nahraďte obsah souboru následujícími konfiguračními informacemi. Přidejte proměnnou obsahující adresu koncového bodu. Nahraďte lomenou závorku hodnotami, které jste shromáždili z předchozích sekcí.

    ``` javascript
    var defaultnode = "<default transaction node connection string>";   
    var Web3 = require("web3");
    
    module.exports = {
      networks: {
        defaultnode: {
          provider: new Web3.providers.HttpProvider(defaultnode),
          network_id: "*"
        }
      }
    }
    ```

1. Uložte změny do `truffle-config.js`.

## <a name="connect-to-transaction-node"></a>Připojení k transakčnímu uzlu

Pomocí *Web3* se připojte k uzlu transakce.

1. Použijte konzolu Truffle pro připojení k výchozímu uzlu transakce.

    ``` bash
    truffle console --network defaultnode
    ```

    Truffle se připojí k výchozímu uzlu transakce a poskytne interaktivní konzolu.

    Můžete volat metody v objektu **web3** pro interakci s vaším uzlem transakce.

1. Zavolejte metodu **getBlockNumber** , která vrátí aktuální číslo bloku.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Příklad výstupu:

    ```bash
    truffle(defaultnode)> web3.eth.getBlockNumber();
    18567
    ```
1. Ukončete konzolu vývoj Truffle.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili projekt Truffle pro připojení ke svému výchozímu uzlu transakce služby Azure blockchain.

Vyzkoušejte si další kurz použití Truffle k odeslání transakce do vaší blockchain sítě konsorcia.

> [!div class="nextstepaction"]
> [Odeslat transakci](send-transaction.md)
