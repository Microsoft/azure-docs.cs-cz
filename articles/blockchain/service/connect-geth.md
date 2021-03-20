---
title: Připojení ke službě Azure blockchain pomocí Geth
description: Připojení k instanci Geth na uzlu transakce služby Azure blockchain
ms.date: 05/26/2020
ms.topic: quickstart
ms.reviewer: maheshna
ms.openlocfilehash: 2c5b6a56d96ac132949052e9879c8f61d41ffb06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "83994761"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>Rychlý Start: použití Geth k připojení k uzlu transakce služby Azure blockchain

V tomto rychlém startu použijete klienta Geth k připojení k instanci Geth na uzlu transakce služby Azure blockchain. Po připojení použijte konzolu Geth k volání rozhraní API Ethereem JavaScript.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

* Nainstalovat [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* Kompletní [rychlé zprovoznění: Vytvoření člena blockchain pomocí Azure Portal](create-member.md) nebo [rychlé zprovoznění: Vytvoření člena blockchain služby Azure BLOCKCHAIN pomocí Azure CLI](create-member-cli.md)

## <a name="get-geth-connection-string"></a>Získání připojovacího řetězce Geth

Připojovací řetězec Geth můžete získat pro uzel transakce služby Azure blockchain v Azure Portal.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Přejít na člena služby Azure blockchain. Vyberte **uzly transakce** a výchozí odkaz na uzel transakce.

    ![Vybrat výchozí uzel transakce](./media/connect-geth/transaction-nodes.png)

1. Vyberte **připojovací řetězce**.
1. Zkopírujte připojovací řetězec z **https (přístupová klávesa 1)**. Pro další oddíl potřebujete řetězec.

    ![Připojovací řetězec](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Připojení k Geth

1. Otevřete příkazový řádek nebo prostředí.
1. Pomocí příkazu Geth připojit dílčí příkaz připojte ke spuštěné instanci Geth na svém uzlu transakce. Vložte připojovací řetězec jako argument pro příkaz připojit dílčí příkaz. Například:

    ``` bash
    geth attach <connection string>
    ```

1. Po připojení k konzoli Ethereem pro uzel transakce můžete použít rozhraní API pro Ethereem JavaScript.

    Pomocí následujícího rozhraní API můžete například zjistit chainId.

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    V tomto příkladu je chainId 661.

    ![Možnost služby Azure blockchain](./media/connect-geth/geth-attach.png)

1. Pokud se chcete odpojit od konzoly, zadejte `exit` .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili klienta Geth pro připojení k instanci Geth na uzlu transakce služby Azure blockchain. Zkuste v dalším kurzu použít Azure blockchain Development Kit pro Ethereem k vytvoření, sestavení, nasazení a spuštění funkce inteligentního kontraktu prostřednictvím transakce.

> [!div class="nextstepaction"]
> [Vytváření, sestavování a nasazování inteligentních smluv ve službě Azure blockchain](send-transaction.md)
