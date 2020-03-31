---
title: Připojení ke službě Azure Blockchain Service pomocí Getho
description: Připojení k instanci Geth v transakčním uzlu služby Azure Blockchain Service
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 9da78eac1dc429bcc0ad52bb9cb2f1fb743a90d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74455826"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>Úvodní příručka: Připojení k transakčnímu uzlu služby Azure Blockchain Service pomocí Getho

V tomto rychlém startu použijete klienta Geth k připojení k instanci Geth v transakčním uzlu služby Azure Blockchain Service. Po připojení použijete konzolu Geth JavaScript k volání rozhraní WEB3 JavaScript Dapp API.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Instalace [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* Kompletní [úvodní příručka: Vytvoření člena blockchainu pomocí portálu Azure](create-member.md) nebo [úvodního startu: Vytvoření člena blockchainu služby Azure Blockchain pomocí Azure CLI](create-member-cli.md)

## <a name="get-geth-connection-string"></a>Získat připojovací řetězec Geth

Připojovací řetězec Geth pro transakční uzel Služby Azure Blockchain můžete získat na webu Azure Portal.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Přejděte na člena služby Azure Blockchain Service. Vyberte **uzly transakce** a výchozí odkaz na uzel transakce.

    ![Vybrat výchozí uzel transakce](./media/connect-geth/transaction-nodes.png)

1. Vyberte **připojovací řetězce**.
1. Zkopírujte připojovací řetězec z **protokolu HTTPS (přístupový klíč 1).** Potřebujete řetězec pro další část.

    ![Připojovací řetězec](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Připojení k Gethu

1. Otevřete příkazový řádek nebo prostředí.
1. Pomocí podpříkazu Geth připojit připojit ke spuštění Geth instance na uzlu transakce. Vložte připojovací řetězec jako argument pro připojovací podpříkaz. Například:

    ``` bash
    geth attach <connection string>
    ```

1. Po připojení ke konzoli Ethereum u transakčního uzlu můžete volat rozhraní web3 JavaScript Dapp API nebo admin API.

    Například použijte následující rozhraní API k vyhledání chainId.

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    V tomto příkladu chainId je 661.

    ![Možnost služby Azure Blockchain](./media/connect-geth/geth-attach.png)

1. Chcete-li se od `exit`konzole odpojit, zadejte příkaz .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili klienta Geth připojit k instanci Geth na uzlu transakce Služby Azure Blockchain. Vyzkoušejte další kurz, ve které můžete použít Azure Blockchain Development Kit for Ethereum k vytvoření, sestavení, nasazení a spuštění funkce inteligentní smlouvy prostřednictvím transakce.

> [!div class="nextstepaction"]
> [Vytvářejte, vytvářejte a nasazujte inteligentní kontrakty ve službě Azure Blockchain](send-transaction.md)
