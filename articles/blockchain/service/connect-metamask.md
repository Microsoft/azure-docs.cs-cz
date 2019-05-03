---
title: Připojení k síti Azure Blockchain Service MetaMask
description: Připojení k síti Azure Blockchain Service MetaMask a nasazovat inteligentní kontraktu.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: db029cee6edcd14d29c83964e5bf75aa45077e7e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026897"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Rychlý start: Použití MetaMask k připojení a nasazovat inteligentní kontraktu

V tomto rychlém startu použijete MetaMask připojení k síti služba Blockchain v Azure a použití Remix nasadit inteligentní kontraktu. Metamask je rozšíření prohlížeče ke správě Ether peněženky a provádět akce, inteligentní kontraktu.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* [Vytvoření Azure Blockchain člena](create-member.md)
* Nainstalujte [MetaMask rozšíření prohlížeče](https://metamask.io)
* Generování MetaMask [wallet](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time)

## <a name="get-endpoint-address"></a>Získat adresu koncového bodu

Budete potřebovat adresu koncového bodu služby Azure Blockchain k připojení k síti blockchain. Adresa a přístupových klíčů můžete najít koncový bod na webu Azure Portal.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte do vaší služby Azure Blockchain člena. Vyberte **transakce uzly** a výchozí transakce uzel propojení.

    ![Vyberte výchozí uzel transakce](./media/connect-metamask/transaction-nodes.png)

1. Vyberte **připojovací řetězce > přístupové klíče**.
1. Zkopírujte adresu koncového bodu z **HTTPS (přístupový klíč 1)**. Budete potřebovat adresu pro další části.

    ![Připojovací řetězec](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Připojit MetaMask

1. Otevřete MetaMask rozšíření prohlížeče a přihlaste se.
1. V rozevírací nabídce sítě, vyberte **vlastní RPC**.

    ![Vlastní RPC](./media/connect-metamask/custom-rpc.png)

1. V **novou síť > novou adresu URL vzdáleného volání Procedur**, zadejte svoji adresu koncového bodu zkopírovali v předchozí části.
1. Vyberte **Uložit**.

    Pokud připojení úspěšné, zobrazí se v rozevíracím seznamu sítě privátní sítě.

    ![Nové sítě](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Nasazení inteligentních kontraktu

Remix je založené na prohlížeči Solidity vývojové prostředí. Pomocí MetaMask a Remix společně, můžete nasadit a provádět s nimi akce chytrých kontraktů.

1. V prohlížeči přejděte na adresu `https://remix.ethereum.org`.
1. Vyberte **Run** (Spustit). 

    MetaMask sady vaše **prostředí** k **vložený Web3** a **účet** k vaší síti.

    ![Karta spuštění](./media/connect-metamask/injected-web3.png)

1. Vyberte **vytvořit nový soubor**.

    Pojmenujte nový soubor `simple.sol`.

    ![Vytvořit soubor](./media/connect-metamask/create-file.png)

    Vyberte **OK**.

1. V editoru Remix vložte následující **jednoduché inteligentní smlouvy** kódu.

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

    **Jednoduché smlouvy** deklaruje proměnnou stavu s názvem **Zůstatek**. Existují dvě funkce definované. **Přidat** číslo, které přidává funkce **Zůstatek**. **Získat** funkce vrátí hodnotu **Zůstatek**.

1. Pro kompilaci smlouvy, vyberte **kompilaci > Start ke kompilaci**. Pokud je úspěšná, zobrazí se zeleného pole s názvem kontraktu.

    ![Kompilovat](./media/connect-metamask/compile.png)

1. Chcete-li spustit smlouvy, vyberte **spustit** kartu. Vyberte **jednoduché** smlouvy pak **nasadit**.

    ![Vlastní RPC](./media/connect-metamask/deploy.png)

1. MetaMask oznámení se zobrazí, upozorní vás dostatek prostředků k provedení transakce.

    Blockchain veřejné sítě budete potřebovat Ether platí pro transakční náklady. Protože privátní sítě v konsorcium, můžete nastavit plynu cena na nulu.

1.  Vyberte **plynu poplatek > Upravit > Upřesnit**, nastavte **ceny plynu** na hodnotu 0.

    ![Ceny plynu](./media/connect-metamask/gas-price.png)

    Vyberte **Uložit**.

1. Vyberte **potvrdit** nasadit inteligentní smlouvy blockchainu.
1. V **nasazené kontrakty** části, rozbalte **jednoduché** kontraktu.

    ![Nasazené kontraktu](./media/connect-metamask/deployed-contract.png)

    Existují dvě akce **přidat** a **získat** , která mapují na funkce definované v kontraktu.

1. K provedení **přidat** transakce s blockchainem, zadejte číslo přidávat pak vyberte **přidat**.
1. Podobně jako při nasazení kontrakt, MetaMask zobrazí oznámení výstrah je nedostatek finančních prostředků k provedení transakce.

    Protože se jedná do privátní sítě v konsorcium, jsme ceny plynu nastavit na nulu.

1.  Vyberte **plynu poplatek > Upravit > Upřesnit**, nastavte **ceny plynu** 0, a vyberte možnost **Uložit**.
1. Vyberte **potvrdit** provádět transakce v blockchainu.
1. Vyberte **získat** akce. Toto je volání data uzlu dotazu. Transakce, není nutná.
1. V panelu ladění Remix uvidíte informace o transakcích v blockchainu.

    ![Ladění historie](./media/connect-metamask/debug.png)

    Zobrazí se **jednoduché** smlouvy vytváření transakcí pro **simple.add**a volání **simple.get**.

1. Zobrazí se také historie transakcí v MetaMask. Otevřete MetaMask rozšíření prohlížeče.
1. V **historie** části, zobrazí se protokol transakcí a nasazené kontraktu.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste použili rozšíření prohlížeče MetaMask k připojení k uzlu služby Azure Blockchain transakce, nasadit inteligentní smlouvy a odeslat transakci blockchainu. Vyzkoušejte další výukový kurz k nasazení a odeslat transakci pomocí Truffle.

> [!div class="nextstepaction"]
> [Odeslat do transakce](send-transaction.md)