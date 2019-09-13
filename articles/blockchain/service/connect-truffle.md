---
title: Připojení s využitím rozšíření Truffle
description: Připojení k síti služby Azure blockchain pomocí Truffle
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 08/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: ea64e3fe4789ac0558463ded81c8179db83469d5
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932593"
---
# <a name="quickstart-use-truffle-to-connect-to-a-transaction-node"></a>Rychlý start: Použití rozšíření Truffle pro připojení k transakčnímu uzlu

Truffle je blockchain vývojové prostředí, které můžete použít pro připojení k uzlu transakce služby Azure blockchain.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Kompletní [rychlé spuštění: Vytvořte člena blockchain pomocí Azure Portal](create-member.md) nebo [rychlého startu: Vytvoření členu blockchain služby Azure blockchain pomocí Azure CLI](create-member-cli.md)
* Nainstalujte [Truffle](https://github.com/trufflesuite/truffle). Truffle vyžaduje instalaci několika nástrojů, včetně [Node. js](https://nodejs.org), [Gitu](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Nainstalujte [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Pro Web3 je potřeba Python.

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

1. V Azure Portal přejděte na výchozí uzel transakce a vyberte **uzly transakce > připojovací řetězce**.
1. Zkopírujte a uložte adresu URL koncového bodu z **https (přístupový klíč 1)** . Adresy koncových bodů pro konfigurační soubor inteligentních smluv budete potřebovat později v tomto kurzu.

    ![Adresa koncového bodu transakce](./media/connect-truffle/endpoint.png)

### <a name="edit-configuration-file"></a>Upravit konfigurační soubor

Dále je potřeba aktualizovat konfigurační soubor Truffle pomocí koncového bodu uzlu transakce.

1. Ve složce projektu **truffledemo** otevřete konfigurační soubor `truffle-config.js` Truffle v editoru.
1. Nahraďte obsah souboru následujícími konfiguračními informacemi. Přidejte proměnnou obsahující adresu koncového bodu. Nahraďte lomenou závorku hodnotami, které jste shromáždili z předchozí části.

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

1. Použijte konzolu Truffle pro připojení k výchozímu uzlu transakce. V příkazovém řádku nebo prostředí spusťte následující příkaz:

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
1. Ukončete konzolu Truffle.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili projekt Truffle pro připojení ke svému výchozímu uzlu transakce služby Azure blockchain.

Zkuste v dalším kurzu použít Azure blockchain Development Kit pro Ethereem a Truffle k vytvoření, sestavení, nasazení a spuštění funkce inteligentního kontraktu prostřednictvím transakce.

> [!div class="nextstepaction"]
> [Použití Visual Studio Code k vytváření, sestavování a nasazování inteligentních smluv](send-transaction.md)
