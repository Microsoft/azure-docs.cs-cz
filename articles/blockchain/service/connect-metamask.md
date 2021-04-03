---
title: Připojení MetaMask k síti služby Azure blockchain
description: Připojte se k síti služby Azure blockchain pomocí MetaMask a nasaďte inteligentní kontrakt.
ms.date: 09/12/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 4a45e02a861ff20a4dc774668a4e008f9b42aeea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530433"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Rychlý Start: použití MetaMask k připojení a nasazení inteligentního kontraktu

V tomto rychlém startu použijete MetaMask k připojení k síti služby Azure blockchain a pomocí Remix nasadíte inteligentní kontrakt. Metamask je rozšíření prohlížeče pro správu etherového kapesního a provádění akcí v rámci inteligentních smluv.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Kompletní [rychlé zprovoznění: Vytvoření člena blockchain pomocí Azure Portal](create-member.md) nebo [rychlé zprovoznění: Vytvoření člena blockchain služby Azure BLOCKCHAIN pomocí Azure CLI](create-member-cli.md)
* Nainstalovat [rozšíření prohlížeče MetaMask](https://metamask.io)
* Generování [kapesního](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time) MetaMask

## <a name="get-endpoint-address"></a>Získat adresu koncového bodu

Pro připojení k síti blockchain potřebujete adresu koncového bodu služby Azure blockchain. Adresa koncového bodu a přístupové klávesy jsou v Azure Portal.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Přejděte ke členu služby Azure blockchain.
1. Vyberte **uzly transakce** a výchozí odkaz na uzel transakce.

    ![Vybrat výchozí uzel transakce](./media/connect-metamask/transaction-nodes.png)

1. Vyberte **připojovací řetězce > přístupové klíče**.
1. Zkopírujte adresu koncového bodu z **https (přístupový klíč 1)**.

    ![Připojovací řetězec](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Připojit MetaMask

1. Otevřete rozšíření prohlížeče MetaMask a přihlaste se.
1. V rozevíracím seznamu síť vyberte  **vlastní RPC**.

    ![Vlastní RPC](./media/connect-metamask/custom-rpc.png)

1. V **nové síti > nové adresy URL RPC** vložte adresu koncového bodu, kterou jste zkopírovali výše.
1. Vyberte **Uložit**.

    Pokud bylo připojení úspěšné, zobrazí se v rozevíracím seznamu síť privátní síť.

    ![Nová síť](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Nasazení inteligentního kontraktu

Remix je vývojové prostředí založené na prohlížeči. Pomocí MetaMask a Remix společně můžete nasadit a provádět akce s inteligentními kontrakty.

1. V prohlížeči přejděte na adresu `https://remix.ethereum.org`.
1. Na kartě **Domů** v části **soubor** vyberte **nový soubor** .

    Pojmenujte nový soubor `simple.sol` .

    ![Vytvořit soubor](./media/connect-metamask/create-file.png)

    Vyberte **OK**.
1. V editoru Remix vložte do následujícího **jednoduchého kódu inteligentního kontraktu** .

    ```solidity
    pragma solidity ^0.5.0;
             
    contract simple {
        uint balance;
                 
        constructor() public{
            balance = 0;
        }
                 
        function add(uint _num) public {
            balance += _num;
        }
                 
        function get() public view returns (uint){
            return balance;
        }
    }
    ```

    **Jednoduchá smlouva** deklaruje stavovou proměnnou s názvem **Zůstatek**. Jsou definovány dvě funkce. Funkce **Add** přidá číslo k **vyvážení**. Funkce **Get** vrátí hodnotu **zůstatku**.
1. Chcete-li zkompilovat kontrakt, nejprve vyberte podokno kompilátor nehustoty a pak vyberte možnost  **zkompilovat jednoduchý. Sol**.

    ![Snímek obrazovky znázorňuje kompilovaný kontrakt.](./media/connect-metamask/compile.png)

1. Vyberte podokno **nasazení & spuštění** a pak nastavte **prostředí** na **vložené Web3** pro připojení prostřednictvím MetaMask k vašemu členovi blockchain.

    ![Karta spustit](./media/connect-metamask/injected-web3.png)

1. Vyberte **jednoduchý** kontrakt a pak **nasazení**.

    ![Snímek obrazovky znázorňuje nasazení a spuštění transakcí s vybraným kontraktem a vybraným nasazením.](./media/connect-metamask/deploy.png)


1. Oznámení MetaMask upozorní na nedostatečné prostředky k provedení transakce.

    V případě veřejné sítě blockchain budete potřebovat ether pro platbu za náklady na transakci. Vzhledem k tomu, že se jedná o soukromou síť v konsorciu, můžete nastavit cenu za plyn na nulu.

1.  Vyberte **poplatek za plyn > upravit > Upřesnit** a nastavte **cenu za plyn** na 0.

    ![Cena za plyn](./media/connect-metamask/gas-price.png)

    Vyberte **Uložit**.

1. Vyberte **Potvrdit** a nasaďte inteligentní kontrakt do blockchain.
1. V části **nasazené smlouvy** rozbalte **jednoduchý** kontrakt.

    ![Nasazený kontrakt](./media/connect-metamask/deployed-contract.png)

    Dvě akce, **Přidání** a **získání**, mapování na funkce definované v kontraktu.

1. Chcete-li provést transakci **Přidání** na blockchain, zadejte číslo, které chcete přidat, a pak vyberte **Přidat**. Můžete obdržet zprávu o selhání odhadu plynu z Remix: "posíláte transakci do soukromého blockchain, která nevyžaduje plyn." Vyberte **Odeslat transakci** pro vynucení transakce.
1. Podobně jako při nasazení kontraktu upozornění MetaMask upozorní na nedostatečné prostředky k provedení transakce.

    Vzhledem k tomu, že se jedná o soukromou síť v konsorciu, můžeme nastavit cenu za plyn na nulu.

1. Vyberte **poplatek za plyn > upravit > Upřesnit**, nastavte **cenu plynu** na 0 a vyberte **Uložit**.
1. Vyberte **Potvrdit** a odešlete transakci do blockchain.
1. Vyberte **získat** akci. Toto je volání pro dotazování na data uzlu. Transakce není potřebná.

Podokno ladění Remix obsahuje podrobnosti o transakcích blockchain:

![Historie ladění](./media/connect-metamask/debug.png)

Můžete si prohlédnout **jednoduché** Vytvoření kontraktu, transakci pro **jednoduché přidávání** a volání metody **Simple. Get**.

Chcete-li zobrazit historii transakcí v MetaMask, otevřete rozšíření prohlížeče MetaMask a vyhledejte v části **Historie** protokol nasazených kontraktů a transakcí.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili rozšíření prohlížeče MetaMask pro připojení k uzlu transakce služby Azure blockchain, nasazení inteligentní smlouvy a odeslání transakce do blockchain. Zkuste v dalším kurzu použít Azure blockchain Development Kit pro Ethereem a Truffle k vytvoření, sestavení, nasazení a spuštění funkce inteligentního kontraktu prostřednictvím transakce.

> [!div class="nextstepaction"]
> [Vytváření, sestavování a nasazování inteligentních smluv ve službě Azure blockchain](send-transaction.md)