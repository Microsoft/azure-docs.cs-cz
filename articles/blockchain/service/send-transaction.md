---
title: Kurz vytvoření, sestavení, & nasazení inteligentních kontraktů – Azure blockchain Service
description: Kurz týkající se použití rozšíření Azure blockchain Development Kit pro rozšíření Ethereem v Visual Studio Code k vytvoření, sestavení a nasazení inteligentních kontraktů ve službě Azure blockchain Service.
ms.date: 11/30/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: 4c2df952480d2c30de10838c3d0f7714fc7e6126
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628641"
---
# <a name="tutorial-create-build-and-deploy-smart-contracts-on-azure-blockchain-service"></a>Kurz: vytvoření, sestavení a nasazení inteligentních smluv ve službě Azure blockchain

V tomto kurzu pomocí rozšíření Azure blockchain Development Kit pro Ethereem v Visual Studio Code vytvoříte, sestavíte a nasadíte inteligentní smlouvu na Azure blockchain Service. Pomocí vývojové sady také spustíte funkci inteligentního kontraktu prostřednictvím transakce.

Pomocí Azure blockchain Development Kit pro Ethereem:

> [!div class="checklist"]
> * Vytvoření inteligentního kontraktu
> * Nasazení inteligentního kontraktu
> * Provedení funkce inteligentního kontraktu prostřednictvím transakce

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Kompletní [rychlé zprovoznění: použití Visual Studio Code pro připojení k síti konsorcia Azure blockchain](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure blockchain Development Kit pro rozšíření Ethereem](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15. x nebo vyšší](https://nodejs.org/download)
* [Git 2.10. x nebo vyšší](https://git-scm.com)
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

Ve Windows se pro modul gyp vyžaduje nainstalovaný kompilátor C++. Můžete použít nástroje MSBuild:

* Pokud je nainstalována aplikace Visual Studio 2017, nakonfigurujte npm pro použití nástrojů MSBuild s příkazem `npm config set msvs_version 2017 -g`
* Pokud je nainstalována sada Visual Studio 2019, nastavte cestu k nástrojům pro sestavení MS pro npm. Například `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`.
* V opačném případě nainstalujte samostatné nástroje VS Build Tools pomocí `npm install --global windows-build-tools` příkazu v příkazovém okně *Spustit jako správce* .

Další informace o Node-gyp najdete v části [úložiště Node-gyp na GitHubu](https://github.com/nodejs/node-gyp).

## <a name="create-a-smart-contract"></a>Vytvoření inteligentního kontraktu

Sada Azure blockchain Development Kit pro Ethereem používá projektové šablony a nástroje Truffle, které vám pomůžou vytvářet kontrakty pro generování a nasazení. Než začnete, dokončete potřebný [rychlý Start: pomocí Visual Studio Code se připojte k síti konsorcia služeb Azure blockchain](connect-vscode.md). Rychlý Start vás provede instalací a konfigurací sady Azure blockchain Development Kit pro Ethereem.

1. Z palety příkazů VS Code vyberte **blockchain: nový projekt soliding**.
1. Vyberte **vytvořit základní projekt**.
1. Vytvořte novou složku s názvem `HelloBlockchain` a **Vyberte možnost Nová cesta k projektu**.

Sada Azure blockchain Development Kit vytvoří a inicializuje nový projekt Solider pro vás. Základní projekt obsahuje vzorový **HelloBlockchain** a všechny potřebné soubory pro sestavování a nasazování členů konsorcia ve službě Azure blockchain. Vytvoření projektu může trvat několik minut. Průběh můžete sledovat na panelu terminálu VS Code, když vyberete výstup pro Azure blockchain.

Struktura projektu vypadá jako v následujícím příkladu:

   ![Projekt solidosti](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Vytvoření inteligentního kontraktu

Inteligentní kontrakty se nacházejí v adresáři **kontraktů** projektu. Inteligentní smlouvy kompilujete před jejich nasazením do blockchain. Použijte příkaz **kontrakty sestavení** pro zkompilování všech inteligentních kontraktů v projektu.

1. Na bočním panelu Průzkumníka VS Code rozbalte složku **smlouvy** ve vašem projektu.
1. Klikněte pravým tlačítkem na **HelloBlockchain. Sol** a v nabídce vyberte **kontrakty sestavení** .

    ![Výběr nabídky smlouvy pro sestavení ](./media/send-transaction/build-contracts.png)

Azure blockchain Development Kit používá Truffle ke kompilaci inteligentních smluv.

![Výstup kompilátoru Truffle](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Nasazení inteligentního kontraktu

Truffle používá skripty pro migraci k nasazení svých smluv do sítě Ethereem. Migrace jsou soubory JavaScriptu, které se nacházejí v adresáři **migrace** projektu.

1. Pokud chcete nasadit vaši inteligentní kontrakt, klikněte pravým tlačítkem na **HelloBlockchain. Sol** a v nabídce vyberte **nasadit smlouvy** .
1. V paletě příkazů vyberte síť Azure blockchain Consortium. Při vytváření projektu byla do konfiguračního souboru Truffle projektu přidána síť blockchain Consortium.
1. Vyberte možnost **generovat klávesové zkratky**. Vyberte název souboru a uložte symbolický soubor do složky projektu. Například, `myblockchainmember.env`. K vygenerování Ethereem privátního klíče pro člena blockchain se použije symbolický soubor.

Azure blockchain Development Kit používá Truffle ke spouštění migračního skriptu k nasazení kontraktů do blockchain.

![Kontrakt se úspěšně nasadil.](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function&quot;></a>Volání funkce kontraktu
Funkce **SendRequest** kontraktu **HelloBlockchain** změní proměnnou stavu **RequestMessage** . Změna stavu blockchain sítě se provádí prostřednictvím transakce. Můžete vytvořit skript pro spuštění funkce **SendRequest** prostřednictvím transakce.

1. V kořenovém adresáři projektu Truffle vytvořte nový soubor a pojmenujte ho `sendrequest.js` . Do souboru přidejte následující kód JavaScriptu Web3.

    ```javascript
    var HelloBlockchain = artifacts.require(&quot;HelloBlockchain");
        
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

1. Když Azure blockchain Development Kit vytvoří projekt, konfigurační soubor Truffle se vygeneruje s podrobnostmi o koncovém bodu vaší konsorcia blockchain Network. Otevřete **truffle-config.js** v projektu. Konfigurační soubor obsahuje seznam dvou sítí: jeden s názvem vývoj a druhý se stejným názvem jako konsorcium.
1. V podokně terminálu VS Code použijte Truffle ke spuštění skriptu v síti konsorcia blockchain. V panelu nabídek podokna terminálu vyberte kartu **terminál** a **PowerShell** v rozevíracím seznamu.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    Nahraďte \<blockchain network\> názvem blockchain sítě definované v **truffle-config.js**.

Truffle spustí skript ve vaší síti blockchain.

![Výstup ukazující transakci byl odeslán.](./media/send-transaction/execute-transaction.png)

Při spuštění funkce kontraktu prostřednictvím transakce není transakce zpracována, dokud nebude vytvořen blok. Funkce, které mají být spuštěny prostřednictvím transakce, vracejí ID transakce namísto návratové hodnoty.

## <a name="query-contract-state"></a>Dotaz na stav kontraktu

Funkce inteligentních kontraktů mohou vracet aktuální hodnotu proměnných stavu. Pojďme přidat funkci, která vrátí hodnotu stavové proměnné.

1. Do **HelloBlockchain. Sol** Přidejte funkci **GetMessage** do **HelloBlockchain** Smart Contract.

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
1. Nasazení provedete tak, že kliknete pravým tlačítkem na **HelloBlockchain. Sol** a v nabídce kliknete na **nasadit smlouvy** . Po zobrazení výzvy vyberte v paletě příkazů síť konsorcia Azure blockchain Consortium.
1. Dále vytvořte skript pomocí volání funkce **GetMessage** . V kořenovém adresáři projektu Truffle vytvořte nový soubor a pojmenujte ho `getmessage.js` . Do souboru přidejte následující kód JavaScriptu Web3.

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

    Nahraďte \<blockchain network\> názvem blockchain sítě definované v **truffle-config.js**.

Skript se dotazuje na inteligentní kontrakt zavoláním funkce GetMessage. Vrátí se aktuální hodnota **RequestMessage** stavu proměnné.

![Výstup z dotazu GetMessage zobrazující aktuální hodnotu RequestMessage stavu proměnné](./media/send-transaction/execute-get.png)

Všimněte si, že hodnota není **Hello, blockchain!**. Místo toho je vrácená hodnota zástupný symbol. Když změníte a nasadíte smlouvu, je změna smlouvy nasazena na novou adresu a proměnné stavu jsou přiřazeny hodnoty v konstruktoru inteligentních kontraktů. Vzorový **2_deploy_contracts.js** skript pro migraci nasadí inteligentní kontrakt a předá jako argument hodnotu zástupného symbolu. Konstruktor nastaví proměnnou stavu **RequestMessage** na hodnotu zástupného symbolu a vrátí to, co je vráceno.

1. Chcete-li nastavit proměnnou stavu **RequestMessage** a zadat dotaz na hodnotu, spusťte znovu skripty **sendrequest.js** a **getmessage.js** .

    ![Výstup ze skriptů SendRequest a GetMessage ukazující RequestMessage byl nastaven.](./media/send-transaction/execute-set-get.png)

    **sendrequest.js** nastaví proměnnou stavu **RequestMessage** na **Hello, blockchain!** a **getmessage.js** vyhledá kontrakt pro hodnotu **RequestMessage** stavu a vrátí **Hello, blockchain!**.
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete prostředky odstranit tak, že odstraníte `myResourceGroup` skupinu prostředků, kterou jste vytvořili v rychlém startu průvodce *vytvořením blockchain člena* .

Odstranění skupiny prostředků:

1. V Azure Portal přejděte do **skupiny prostředků** v levém navigačním podokně a vyberte skupinu prostředků, kterou chcete odstranit.
1. Vyberte **Odstranit skupinu prostředků**. Potvrďte odstranění zadáním názvu skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili ukázkový projekt s ukázkou pomocí Azure blockchain Development Kit. Vystavíte a nasadili jste inteligentní kontrakt a pak jste volali funkci prostřednictvím transakce v síti konsorcia blockchain, která je hostovaná ve službě Azure blockchain.

> [!div class="nextstepaction"]
> [Vývoj aplikací blockchain pomocí služby Azure blockchain](develop.md)
