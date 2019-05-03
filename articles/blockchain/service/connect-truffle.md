---
title: Připojte se pomocí Truffle
description: Připojení k síti Azure Blockchain Service Truffle
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 037f37d6a8e1c41579403dbf7c9dd265efbb5d10
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026953"
---
# <a name="quickstart-use-truffle-to-connect-to-a-an-azure-blockchain-service-network"></a>Rychlý start: Použít pro připojení k Truffle síť služby Azure Blockchain

Truffle je blockchain vývojové prostředí, které můžete použít pro připojení k uzlu služby Azure Blockchain.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* [Vytvoření Azure Blockchain člena](create-member.md)
* Truffle vyžaduje instalaci několika nástrojů včetně [Node.js](https://nodejs.org), [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git), a [Truffle](https://github.com/trufflesuite/truffle).

    Chcete-li rychle nastavit ve Windows 10, nainstalovat [Ubuntu ve Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6) prostředí Unix Bash terminálu nainstalujte [Truffle](https://github.com/trufflesuite/truffle). Ubuntu ve Windows distribuce zahrnuje Node.js a Git.

## <a name="create-truffle-project"></a>Vytvoření projektu Truffle

1. Otevřete terminál prostředí Bash.
1. Změnit adresář, ve kterém chcete vytvořit adresář projektu Truffle. Například:

    ``` bash
    cd /mnt/c
    ```

1. Vytvořte adresář pro projekt a změňte cestu k do nového adresáře. Například:

    ``` bash
    mkdir truffledemo
    cd truffledemo
    ```

1. Nainstalujte rozhraní API pro JavaScript Etherea web3 ve složce projektu. V současné době 1.0.0-beta.37 verze web3 verze je povinný.

    ``` bash
    npm install web3@1.0.0-beta.37
    ```

    Můžete obdržet upozornění npm během instalace.

1. Inicializujte Truffle projektu.

    ``` bash
    truffle init
    ```

1. Spuštění Truffle interaktivní vývojové konzole.

    ``` bash
    truffle develop
    ```

    Truffle vytvoří blockchain místním vývojovém a nabízí interaktivní konzolu.

## <a name="connect-to-transaction-node"></a>Připojení k uzlu transakce

Použijeme Web3 pro připojení k uzlu transakce. Web3 připojovací řetězec můžete získat z webu Azure portal.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte do vaší služby Azure Blockchain člena. Vyberte **transakce uzly** a výchozí transakce uzel propojení.

    ![Vyberte výchozí uzel transakce](./media/connect-truffle/transaction-nodes.png)

1. Vyberte **ukázkový kód > Web3**.
1. Zkopírujte JavaScript z **HTTPS (přístupový klíč 1)**. Budete potřebovat kód pro jeho Truffle interaktivní vývojové konzole.

    ![Web3 kódu](./media/connect-truffle/web3-code.png)

1. Vložte kód jazyka JavaScript v předchozím kroku do konzoly Truffle interaktivní vývoj. Kód vytvoří objekt web3, který je připojený k uzlu služby Azure Blockchain transakce.

    Příklad výstupu:

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    truffle(develop)>
     ```

    Můžete volat metody ve **web3** objektu k interakci s transakcí uzlu.

1. Volání **getBlockNumber** metoda vrátí aktuální blok číslo.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Příklad výstupu:

    ```bash
    truffle(develop)> web3.eth.getBlockNumber();
    18567
    ```
1. Ukončete konzolu Truffle vývoje.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili projekt Truffle pro připojení k vaší službě Azure Blockchain Service výchozí transakce uzlu.

Zkuste dalším kurzu se používat k odesílání transakce do vaší sítě konsorcia blockchain Truffle.

> [!div class="nextstepaction"]
> [Odeslat do transakce](send-transaction.md)