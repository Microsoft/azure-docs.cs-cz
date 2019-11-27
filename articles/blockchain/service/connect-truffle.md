---
title: Připojení ke službě Azure blockchain pomocí Truffle
description: Připojení k síti služby Azure blockchain pomocí Truffle
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 9ea96aa32072775fe6fb9563442f5e3564d37ea5
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455797"
---
# <a name="quickstart-use-truffle-to-connect-to-azure-blockchain-service"></a>Rychlý Start: použití Truffle pro připojení ke službě Azure blockchain

V tomto rychlém startu použijete Truffle připojit k uzlu transakce služby Azure blockchain. Pak použijete interaktivní konzolu Truffle k volání metod **web3** pro interakci se sítí blockchain.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Kompletní [rychlé zprovoznění: Vytvoření člena blockchain pomocí Azure Portal](create-member.md) nebo [rychlé zprovoznění: Vytvoření člena blockchain služby Azure BLOCKCHAIN pomocí Azure CLI](create-member-cli.md)
* Nainstalujte [Truffle](https://github.com/trufflesuite/truffle). Truffle vyžaduje instalaci několika nástrojů, včetně [Node. js](https://nodejs.org), [Gitu](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Nainstalujte [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Pro Web3 je potřeba Python.

## <a name="create-truffle-project"></a>Vytvořit projekt Truffle

1. Otevřete příkazový řádek Node. js nebo prostředí.
1. Změňte adresář na místo, kde chcete vytvořit adresář projektu Truffle.
1. Vytvořte adresář pro projekt a změňte cestu k novému adresáři. Například:

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

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).
1. Přejít na člena služby Azure blockchain. Vyberte **uzly transakce** a výchozí odkaz na uzel transakce.

    ![Vybrat výchozí uzel transakce](./media/connect-truffle/transaction-nodes.png)

1. Vyberte **připojovací řetězce**.
1. Zkopírujte připojovací řetězec z **https (přístupová klávesa 1)** . Pro další oddíl potřebujete řetězec.

    ![Připojovací řetězec](./media/connect-truffle/connection-string.png)

### <a name="edit-configuration-file"></a>Upravit konfigurační soubor

Dále je potřeba aktualizovat konfigurační soubor Truffle pomocí koncového bodu uzlu transakce.

1. Ve složce projektu **truffledemo** otevřete konfigurační soubor Truffle `truffle-config.js` v editoru.
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

1. Uložte změny `truffle-config.js`.

## <a name="connect-to-transaction-node"></a>Připojení k transakčnímu uzlu

Pomocí *Web3* se připojte k uzlu transakce.

1. Použijte konzolu Truffle pro připojení k výchozímu uzlu transakce. V příkazovém řádku nebo prostředí spusťte následující příkaz:

    ``` bash
    truffle console --network defaultnode
    ```

    Truffle se připojí k výchozímu uzlu transakce a poskytne interaktivní konzolu.

    Můžete volat metody v objektu **web3** pro interakci se sítí blockchain.

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

V tomto rychlém startu jste použili Truffle k připojení k výchozímu uzlu transakce služby Azure blockchain a použili interaktivní konzolu pro návrat aktuální číslo blockchain bloku.

Zkuste v dalším kurzu použít Azure blockchain Development Kit pro Ethereem k vytvoření, sestavení, nasazení a spuštění funkce inteligentního kontraktu prostřednictvím transakce.

> [!div class="nextstepaction"]
> [Vytváření, sestavování a nasazování inteligentních smluv ve službě Azure blockchain](send-transaction.md)
