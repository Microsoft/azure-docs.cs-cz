---
title: Použití inteligentních kontraktů ve službě Azure blockchain
description: V tomto kurzu se naučíte, jak pomocí služby Azure blockchain nasadit inteligentní kontrakt a spustit funkci prostřednictvím transakce.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 07/31/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
ms.openlocfilehash: 1843bd66e11a6686c9ae81fb8e30c7b030e889b7
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705133"
---
# <a name="tutorial-use-smart-contracts-on-azure-blockchain-service"></a>Kurz: Použití inteligentních kontraktů ve službě Azure blockchain

V tomto kurzu použijete sadu Azure blockchain Development Kit pro Ethereem k vytvoření a nasazení inteligentního kontraktu a potom spustíte funkci inteligentního kontraktu prostřednictvím transakce v síti konsorcia blockchain.

Pomocí Azure blockchain Development Kit pro Ethereem:

> [!div class="checklist"]
> * Připojení ke členu blockchain služby Azure blockchain Service Consortium
> * Vytvoření inteligentního kontraktu
> * Nasazení inteligentního kontraktu
> * Provedení funkce inteligentního kontraktu prostřednictvím transakce
> * Dotaz na stav kontraktu

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Kompletní [rychlé spuštění: Vytvořte člena blockchain pomocí Azure Portal](create-member.md) nebo [rychlého startu: Vytvoření členu blockchain služby Azure blockchain pomocí Azure CLI](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure blockchain Development Kit pro rozšíření Ethereem](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js](https://nodejs.org)
* [Git](https://git-scm.com)
* [Python](https://www.python.org/downloads/release/python-2715/). Přidejte Python. exe do cesty. Pro Azure blockchain Development Kit se vyžaduje Python ve vaší cestě.
* [Truffle](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI](https://github.com/trufflesuite/ganache-cli)

### <a name="verify-azure-blockchain-development-kit-environment"></a>Ověření prostředí Azure blockchain Development Kit

Sada Azure blockchain Development Kit ověřuje splnění požadavků na vývojové prostředí. Ověření vašeho vývojového prostředí:

Z palety příkazů vs Code vyberte **blockchain Azure: Zobrazit úvodní stránku**

Azure blockchain Development Kit spustí ověřovací skript, který dokončení trvá přibližně minutu. Výstup můžete zobrazit tak, že vyberete **terminál > nový terminál**. V řádku nabídek terminálu vyberte kartu **výstup** a **Azure blockchain** v rozevíracím seznamu. Úspěšné ověření vypadá jako na následujícím obrázku:

![Platné vývojové prostředí](./media/send-transaction/valid-environment.png)

 Pokud chybí požadovaný nástroj, nová karta s názvem **Azure blockchain Development Kit – Preview** obsahuje seznam požadovaných aplikací k instalaci a odkazy na stažení nástrojů.

![Požadované aplikace pro vývojovou sadu](./media/send-transaction/required-apps.png)

## <a name="connect-to-consortium-member"></a>Připojit k členovi konsorcia

K členům konsorcia se můžete připojit pomocí rozšíření VS Code Azure blockchain Development Kit. Po připojení k konsorciu můžete kompilovat, sestavovat a nasazovat inteligentní kontrakty na člena konsorcia služeb Azure blockchain.

Pokud nemáte přístup k členovi konsorcia služeb Azure blockchain, proveďte potřebný [rychlý Start: Vytvořte člena blockchain pomocí Azure Portal](create-member.md) nebo [rychlého startu: Vytvořte člen blockchain služby Azure blockchain pomocí Azure CLI](create-member-cli.md).

1. V podokně aplikace Visual Studio Code (VS Code) rozbalte rozšíření **Azure blockchain** .
1. Vyberte **připojit k konsorciu**.

   ![Připojení k konsorciu](./media/send-transaction/connect-consortium.png)

    Pokud se zobrazí výzva k ověření Azure, postupujte podle výzev k ověření pomocí prohlížeče.
1. V rozevírací nabídce paleta příkazů vyberte **připojit ke službě Azure blockchain Service Consortium** .
1. Vyberte předplatné a skupinu prostředků, která je přidružená k vašemu členovi konsorcia Azure blockchain Service.
1. Ze seznamu vyberte konsorcium.

Členové konsorcia a blockchain jsou uvedeni na postranním panelu Průzkumníka sady Visual Studio.

![Konsorcium zobrazené v Průzkumníkovi](./media/send-transaction/consortium-node.png)

## <a name="create-a-smart-contract"></a>Vytvoření inteligentního kontraktu

Sada Azure blockchain Development Kit pro Ethereem používá projektové šablony a nástroje Truffle, které vám pomůžou vytvářet kontrakty pro generování a nasazení.

1. Z palety příkazů vs Code vyberte **blockchain Azure: Nový projekt**soliding.
1. Vyberte **vytvořit základní projekt**.
1. Vytvořte novou složku s názvem `HelloBlockchain` a **Vyberte možnost Nová cesta k projektu**.

Sada Azure blockchain Development Kit vytvoří a inicializuje nový projekt Solider pro vás. Základní projekt obsahuje vzorový **HelloBlockchain** a všechny potřebné soubory pro sestavování a nasazování členů konsorcia ve službě Azure blockchain. Vytvoření projektu může trvat několik minut. Průběh můžete sledovat na panelu terminálu VS Code, když vyberete výstup pro Azure blockchain.

Struktura projektu vypadá jako v následujícím příkladu:

   ![Projekt solidosti](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Vytvoření inteligentního kontraktu

Inteligentní kontrakty se nacházejí v adresáři **kontraktů** projektu. Inteligentní smlouvy kompilujete před jejich nasazením do blockchain. Použijte příkaz **kontrakty sestavení** pro zkompilování všech inteligentních kontraktů v projektu.

1. Na bočním panelu Průzkumníka VS Code rozbalte složku **smlouvy** ve vašem projektu.
1. Klikněte pravým tlačítkem na **HelloBlockchain. Sol** a v nabídce vyberte **kontrakty sestavení** .

    ![Smlouvy sestavení](./media/send-transaction/build-contracts.png)

Azure blockchain Development Kit používá Truffle ke kompilaci inteligentních smluv.

![Kompilovat výstup](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Nasazení inteligentního kontraktu

Truffle používá skripty pro migraci k nasazení svých smluv do sítě Ethereem. Migrace jsou soubory JavaScriptu, které se nacházejí v adresáři **migrace** projektu.

1. Pokud chcete nasadit vaši inteligentní kontrakt, klikněte pravým tlačítkem na **HelloBlockchain. Sol** a v nabídce vyberte **nasadit smlouvy** .
1. Vyberte svou síť konsorcia Azure blockchain v rámci **služby Truffle-config. js**. Při vytváření projektu byla do konfiguračního souboru Truffle projektu přidána síť blockchain Consortium.
1. Vyberte možnost **generovat klávesové zkratky**. Vyberte název souboru a uložte symbolický soubor do složky projektu. Například, `myblockchainmember.env`. K vygenerování Ethereem privátního klíče pro člena blockchain se použije symbolický soubor.

Azure blockchain Development Kit používá Truffle ke spouštění migračního skriptu k nasazení kontraktů do blockchain.

![Kontrakt se úspěšně nasadil.](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Volání funkce kontraktu

Funkce **SendRequest** kontraktu **HelloBlockchain** změní proměnnou stavu **RequestMessage** . Změna stavu blockchain sítě se provádí prostřednictvím transakce. Můžete vytvořit skript pro spuštění funkce **SendRequest** prostřednictvím transakce.

1. V kořenovém adresáři projektu Truffle vytvořte nový soubor a pojmenujte ho `sendrequest.js`. Do souboru přidejte následující kód JavaScriptu Web3.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Když Azure blockchain Development Kit vytvoří projekt, konfigurační soubor Truffle se vygeneruje s podrobnostmi o koncovém bodu vaší konsorcia blockchain Network. Otevřete v projektu **Truffle-config. js** . Konfigurační soubor obsahuje seznam dvou sítí: jeden s názvem vývoj a druhý se stejným názvem jako konsorcium.
1. V podokně terminálu VS Code použijte Truffle ke spuštění skriptu v síti konsorcia blockchain. V panelu nabídek podokna terminálu vyberte kartu **terminál** a **PowerShell** v rozevíracím seznamu.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    Nahraďte \<blockchain\> síť názvem sítě blockchain definované v **Truffle-config. js**.

Truffle spustí skript ve vaší síti blockchain.

![Výstup skriptu](./media/send-transaction/execute-transaction.png)

Při spuštění funkce kontraktu prostřednictvím transakce není transakce zpracována, dokud nebude vytvořen blok. Funkce, které mají být spuštěny prostřednictvím transakce, vracejí ID transakce namísto návratové hodnoty.

## <a name="query-contract-state"></a>Dotaz na stav kontraktu

Funkce inteligentních kontraktů mohou vracet aktuální hodnotu proměnných stavu. Pojďme přidat funkci, která vrátí hodnotu stavové proměnné.

1. Do **HelloBlockchain. Sol**Přidejte funkci **GetMessage** do **HelloBlockchain** Smart Contract.

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    Funkce vrátí zprávu uloženou v proměnné stavu na základě aktuálního stavu kontraktu.

1. Kliknutím pravým tlačítkem na **HelloBlockchain. Sol** a kliknutím na možnost **sestavit smlouvy** z nabídky zkompilujete změny v rámci inteligentního kontraktu.
1. Nasazení provedete tak, že kliknete pravým tlačítkem na **HelloBlockchain. Sol** a v nabídce kliknete na **nasadit smlouvy** .
1. Dále vytvořte skript pomocí volání funkce GetMessage. V kořenovém adresáři projektu Truffle vytvořte nový soubor a pojmenujte ho `getmessage.js`. Do souboru přidejte následující kód JavaScriptu Web3.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. V podokně terminálu VS Code spusťte skript na blockchain síti pomocí Truffle. V panelu nabídek podokna terminálu vyberte kartu **terminál** a **PowerShell** v rozevíracím seznamu.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    Nahraďte \<blockchain\> síť názvem sítě blockchain definované v **Truffle-config. js**.

Skript se dotazuje na inteligentní kontrakt zavoláním funkce GetMessage. Vrátí se aktuální hodnota **RequestMessage** stavu proměnné.

![Výstup skriptu](./media/send-transaction/execute-get.png)

Všimněte si, že hodnota není **Hello, blockchain!** . Místo toho je vrácená hodnota zástupný symbol. Když změníte a nasadíte smlouvu, kontrakt získá novou adresu kontraktu a proměnné stavu jsou přiřazené hodnoty v konstruktoru inteligentního kontraktu. Truffle Sample **2_deploy_contracts. js** nasadí inteligentní kontrakt a předá jako argument hodnotu zástupného symbolu. Konstruktor nastaví proměnnou stavu **RequestMessage** na hodnotu zástupného symbolu a vrátí to, co je vráceno.

1. Chcete-li nastavit proměnnou stavu **RequestMessage** a zadat dotaz na hodnotu, spusťte znovu skripty **SendRequest. js** a GetMessage **. js** .

    ![Výstup skriptu](./media/send-transaction/execute-set-get.png)

    **SendRequest. js** nastaví proměnnou stavu **RequestMessage** na **Hello, blockchain!** a **GetMessage. js** vyžádá kontrakt pro hodnotu **RequestMessage** stavu a vrátí Hello **, blockchain!** .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete prostředky odstranit tak, že `myResourceGroup` odstraníte skupinu prostředků, kterou jste vytvořili v rychlém startu průvodce *vytvořením blockchain člena* .

Odstranění skupiny prostředků:

1. V Azure Portal přejděte do **skupiny prostředků** v levém navigačním podokně a vyberte skupinu prostředků, kterou chcete odstranit.
1. Vyberte **Odstranit skupinu prostředků**. Potvrďte odstranění zadáním názvu skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili ukázkový projekt s ukázkou pomocí Azure blockchain Development Kit. Vystavíte a nasadili jste inteligentní kontrakt a pak jste volali funkci prostřednictvím transakce v síti konsorcia blockchain, která je hostovaná ve službě Azure blockchain.

> [!div class="nextstepaction"]
> [Vývoj aplikací blockchain pomocí služby Azure blockchain](develop.md)
