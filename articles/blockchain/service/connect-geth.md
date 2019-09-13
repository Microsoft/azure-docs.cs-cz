---
title: Připojení ke službě Azure blockchain pomocí Geth
description: Připojení k síti služby Azure blockchain pomocí Geth
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: a26899e291c4f44d3c9d91032b2ee191ba03133a
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931778"
---
# <a name="quickstart-use-geth-to-connect-to-a-transaction-node"></a>Rychlý start: Použití rozšíření Geth pro připojení k transakčnímu uzlu

Geth je klient Ethereem, který můžete použít pro připojení k instanci Geth v uzlu transakce služby Azure blockchain.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Nainstalovat [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* Kompletní [rychlé spuštění: Vytvořte člena blockchain pomocí Azure Portal](create-member.md) nebo [rychlého startu: Vytvoření členu blockchain služby Azure blockchain pomocí Azure CLI](create-member-cli.md)

## <a name="get-the-geth-connection-string"></a>Získání připojovacího řetězce Geth

Připojovací řetězec Geth můžete najít v Azure Portal.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte ke členu služby Azure blockchain. Vyberte **uzly transakce** a výchozí odkaz na uzel transakce.

    ![Vybrat výchozí uzel transakce](./media/connect-geth/transaction-nodes.png)

1. Vyberte **připojovací řetězce**.
1. Zkopírujte připojovací řetězec z **https (přístupová klávesa 1)** . Pro další část budete potřebovat příkaz.

    ![Připojovací řetězec](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Připojení k Geth

1. Otevřete příkazový řádek nebo prostředí.
1. Pomocí příkazu Geth připojit dílčí příkaz připojte ke spuštěné instanci Geth na svém uzlu transakce. Vložte připojovací řetězec jako argument pro příkaz připojit dílčí příkaz. Například

    ```
    geth attach <connection string>
    ```

1. Po připojení k konzoli Ethereem pro uzel transakce můžete volat rozhraní API web3 pro Dapp nebo rozhraní API pro správu.

    Pomocí následujícího rozhraní API můžete například zjistit chainId.

    ```bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    V tomto příkladu je chainId 297.

    ![Možnost služby Azure blockchain](./media/connect-geth/geth-attach.png)

1. Pokud se chcete odpojit od konzoly, `exit`zadejte.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili klienta Geth pro připojení k instanci Geth na uzlu transakce služby Azure blockchain. Zkuste v dalším kurzu použít Azure blockchain Development Kit pro Ethereem a Truffle k vytvoření, sestavení, nasazení a spuštění funkce inteligentního kontraktu prostřednictvím transakce.

> [!div class="nextstepaction"]
> [Použití Visual Studio Code k vytváření, sestavování a nasazování inteligentních smluv](send-transaction.md)