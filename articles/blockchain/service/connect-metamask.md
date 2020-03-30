---
title: Připojení metamasku k síti služby Azure Blockchain Service
description: Připojte se k síti služby Azure Blockchain Service pomocí MetaMask a nasaďte inteligentní kontrakt.
ms.date: 09/12/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: c0bad9efde44ce53f6b0656af3ac4af32ffe051d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79205113"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Úvodní příručka: Připojení a nasazení inteligentnísmlouvy pomocí metamasku

V tomto rychlém startu se pomocí Aplikace MetaMask připojíte k síti služby Azure Blockchain Service a pomocí remixu nasadíte inteligentní kontrakt. Metamask je rozšíření prohlížeče pro správu peněženky Ether a provádění inteligentních smluvních akcí.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Kompletní [úvodní příručka: Vytvoření člena blockchainu pomocí portálu Azure](create-member.md) nebo [úvodního startu: Vytvoření člena blockchainu služby Azure Blockchain pomocí Azure CLI](create-member-cli.md)
* Instalace [rozšíření prohlížeče MetaMask](https://metamask.io)
* Generovat [peněženku](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time) MetaMask

## <a name="get-endpoint-address"></a>Získat adresu koncového bodu

K připojení k blockchainové síti potřebujete adresu koncového bodu služby Azure Blockchain Service. Adresa koncového bodu a přístupové klíče jsou na webu Azure Portal.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Přejděte na člena služby Azure Blockchain Service.
1. Vyberte **uzly transakce** a výchozí odkaz na uzel transakce.

    ![Vybrat výchozí uzel transakce](./media/connect-metamask/transaction-nodes.png)

1. Vyberte **připojovací řetězce > přístupových kláves**.
1. Zkopírujte adresu koncového bodu z **protokolu HTTPS (přístupový klíč 1).**

    ![Připojovací řetězec](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Připojit metamasku

1. Otevřete rozšíření prohlížeče MetaMask a přihlaste se.
1. V rozevíracím seznamu sítě vyberte **vlastní vzdálené volání procedur**.

    ![Vlastní vzdálené volání procedur](./media/connect-metamask/custom-rpc.png)

1. V **nové síťové > nové adresy URL vzdáleného volání procedur**vložte adresu koncového bodu, kterou jste zkopírovali výše.
1. Vyberte **Uložit**.

    Pokud bylo připojení úspěšné, zobrazí se v rozevíracím seznamu síť.

    ![Nová síť](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Nasazení inteligentní smlouvy

Remix je vývojové prostředí Solidity založené na prohlížeči. Pomocí MetaMask a Remix společně můžete nasadit a provést akce na inteligentní smlouvy.

1. V prohlížeči přejděte na adresu `https://remix.ethereum.org`.
1. Na kartě **Domů** v části **Soubor**vyberte **Nový soubor** .

    Pojmenujte `simple.sol`nový soubor .

    ![Vytvořit soubor](./media/connect-metamask/create-file.png)

    Vyberte **OK**.
1. V editoru Remix vložte následující **jednoduchý kód inteligentní smlouvy.**

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

    **Jednoduchý kontrakt** deklaruje proměnnou stavu s názvem **balance**. Jsou definovány dvě funkce. Funkce **add** přidá k **vyvážení**číslo . Funkce **get** vrátí hodnotu **zůstatku**.
1. Chcete-li zkompilovat smlouvu, nejprve vyberte podokno kompilátoru Solidity a poté vyberte **soubor Kompilovat simple.sol**.

    ![Kompilaci](./media/connect-metamask/compile.png)

1. Vyberte podokno **Nasadit & spustit** a nastavte **prostředí** na **Injected Web3** pro připojení přes MetaMask k vašemu členovi blockchainu.

    ![Karta Spustit](./media/connect-metamask/injected-web3.png)

1. Vyberte **jednoduchou** smlouvu a pak **nasadit**.

    ![Nasazení](./media/connect-metamask/deploy.png)


1. Oznámení metamaskvás vás upozorní na nedostatek finančních prostředků k provedení transakce.

    Pro veřejnou blockchainovou síť byste potřebovali Ether, abyste zaplatili transakční náklady. Vzhledem k tomu, že se jedná o soukromou síť v konsorciu, můžete nastavit cenu plynu na nulu.

1.  Vyberte **poplatek za plyn > Upravit > Upřesnit**, nastavte **cenu plynu** na 0.

    ![Cena plynu](./media/connect-metamask/gas-price.png)

    Vyberte **Uložit**.

1. Kliknutím **na Potvrdit** nasadíte inteligentní kontrakt do blockchainu.
1. V části **Nasazené smlouvy** rozbalte **jednoduchou** smlouvu.

    ![Nasazená smlouva](./media/connect-metamask/deployed-contract.png)

    Dvě akce, **přidat** a **získat**, mapovat na funkce definované ve smlouvě.

1. Chcete-li provést transakci **přidání** na blockchainu, zadejte číslo, které chcete přidat, a pak vyberte **přidat**. Můžete získat zprávu o selhání odhadu plynu od společnosti Remix: "Odesíláte transakci do soukromého blockchainu, který nevyžaduje plyn." Vyberte **Odeslat transakci,** chcete-li transakci vynutit.
1. Podobně jako při nasazení smlouvy, oznámení MetaMask vás upozorní na nedostatek finančních prostředků k provedení transakce.

    Vzhledem k tomu, že se jedná o soukromou síť v konsorciu, můžeme nastavit cenu plynu na nulu.

1. Vyberte **Poplatek za plyn > Upravit > Upřesnit**, nastavte **cenu plynu** na 0 a vyberte **Uložit**.
1. Chcete-li transakci odeslat do blockchainu, vyberte **možnost Potvrdit.**
1. Vyberte **získat** akci. Toto je volání dat uzlu dotazu. Transakce není potřeba.

Podokno ladění Remixu zobrazuje podrobnosti o transakcích na blockchainu:

    ![Debug history](./media/connect-metamask/debug.png)

    You can see the **simple** contract creation, transaction for **simple.add**, and call to **simple.get**.

Chcete-li zobrazit historii transakcí v MetaMask, otevřete rozšíření prohlížeče MetaMask a podívejte se do části **Historie** pro protokol nasazených smluv a transakcí.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili rozšíření prohlížeče MetaMask pro připojení k transakčnímu uzlu služby Azure Blockchain Service, nasazení inteligentní smlouvy a odeslání transakce do blockchainu. Vyzkoušejte další kurz, ve které můžete použít Azure Blockchain Development Kit for Ethereum a Lanýže k vytvoření, sestavení, nasazení a spuštění funkce inteligentní smlouvy prostřednictvím transakce.

> [!div class="nextstepaction"]
> [Vytvářejte, vytvářejte a nasazujte inteligentní kontrakty ve službě Azure Blockchain](send-transaction.md)