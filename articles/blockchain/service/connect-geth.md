---
title: Použití Geth se připojit ke službě Azure Blockchain
description: Připojení k síti Azure Blockchain Service Geth
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 0716a9326a54ae31d4f355fe5f4c88488339b390
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026918"
---
# <a name="quickstart-use-geth-to-connect-to-a-transaction-node"></a>Rychlý start: Použít Geth pro připojení k uzlu transakce

Geth je přejít Etherea klienta, která vám pomůže se připojit k instanci Geth do služby Azure Blockchain transakce uzlu.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* [Vytvoření Azure Blockchain člena](create-member.md)

## <a name="get-the-geth-connection-string"></a>Získání připojovacího řetězce Geth

Připojovací řetězec Geth najdete na webu Azure Portal.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte do vaší služby Azure Blockchain člena. Vyberte **transakce uzly** a výchozí transakce uzel propojení.

    ![Vyberte výchozí uzel transakce](./media/connect-geth/transaction-nodes.png)

1. Vyberte **připojovací řetězce**.
1. Zkopírujte připojovací řetězec z **HTTPS (přístupový klíč 1)**. Příkaz budete potřebovat pro další části.

    ![Připojovací řetězec](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Připojte se k Geth

1. Otevřete příkazový řádek nebo prostředí.
1. Použití Geth připojit podpříkaz se připojit k běžící instanci Geth uzlu transakce. Vložte připojovací řetězec jako argument pro dílčí příkaz připojit. Například:

    ```
    geth attach <connection string>
    ```

1. Po připojení ke konzole ethereum během transakce uzlu, můžete volat rozhraní API jazyka JavaScript Dapp web3 nebo rozhraní API pro správu.

    Chcete-li zjistit, chainId například použijte následující rozhraní API.

    ```bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    V tomto příkladu je chainId 297.

    ![Možnost Azure Blockchain služby](./media/connect-geth/geth-attach.png)

1. Chcete-li odpojit z konzoly, zadejte `exit`.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste použili Geth klienta se připojit k instanci Geth do služby Azure Blockchain transakce uzlu. Vyzkoušejte další výukový kurz k nasazení a odeslat transakci pomocí Truffle.

> [!div class="nextstepaction"]
> [Odeslat do transakce](send-transaction.md)