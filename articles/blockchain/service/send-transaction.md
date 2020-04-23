---
title: Vytváření, vytváření & nasazení kurzů inteligentních smluv – Služba Azure Blockchain Service
description: Kurz o tom, jak používat Azure Blockchain Development Kit pro rozšíření Ethereum v kódu Visual Studio k vytvoření, sestavení a nasazení inteligentní smlouvy ve službě Azure Blockchain.
ms.date: 04/22/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: dc23c680dfb2ed33cae2a251af16e1b1f25c6ac7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086653"
---
# <a name="tutorial-create-buildanddeploysmartcontracts-on-azure-blockchain-service"></a>Kurz: Vytváření, vytváření a nasazování inteligentních kontraktů ve službě Azure Blockchain Service

V tomto kurzu použijte azure blockchain development kit pro ethereum rozšíření v kódu Visual Studio vytvořit, sestavit a nasadit inteligentní smlouvy na Azure Blockchain Service. Můžete také použít vývojovou sadu k provedení funkce inteligentní smlouvy prostřednictvím transakce.

Azure Blockchain Development Kit pro ethereum používáte k:

> [!div class="checklist"]
> * Vytvoření inteligentní smlouvy
> * Nasazení inteligentní smlouvy
> * Provedení funkce inteligentní smlouvy prostřednictvím transakce

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadované součásti

* Kompletní [úvodní příručka: Připojení k síti konsorcia Azure Blockchain Service pomocí kódu Visual Studia](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure Blockchain Development Kit pro rozšíření Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x nebo vyšší](https://nodejs.org/download)
* [Git 2.10.x nebo vyšší](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) Přidejte python.exe na cestu. Python verze 2.7.15 ve vaší cestě je vyžadován pro Azure Blockchain Development Kit.
* [Lanýž 5,0,0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

V systému Windows je pro modul node-gyp vyžadován nainstalovaný kompilátor jazyka C++. Můžete použít nástroje MSBuild:

* Pokud je nainstalována Visual Studio 2017, nakonfigurujte npm používat nástroje MSBuild s příkazem`npm config set msvs_version 2017 -g`
* Pokud je nainstalován visual studio 2019, nastavte cestu nástroje sestavení MS pro npm. Například `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`.
* V opačném případě nainstalujte samostatné nástroje `npm install --global windows-build-tools` sestavení VS pomocí ve zvýšeném *spuštění jako příkazového* prostředí správce.

Další informace o node-gyp najdete v [úložišti node-gyp na GitHubu](https://github.com/nodejs/node-gyp).

## <a name="create-a-smart-contract"></a>Vytvoření inteligentní smlouvy

Azure Blockchain Development Kit for Ethereum používá šablony projektů a lanýžové nástroje, které pomáhají vytvářet, vytvářet a nasazovat smlouvy. Než začnete, vyplňte požadovaný [úvodní příručku: Pomocí kódu Visual Studia se připojte k síti konsorcia Azure Blockchain Service](connect-vscode.md). Rychlý start vás provede instalací a konfigurací Azure Blockchain Development Kit pro ethereum.

1. Z palety příkazů VS Kód zvolte **Azure Blockchain: New Solidity Project**.
1. Zvolte **Vytvořit základní projekt**.
1. Vytvořte novou `HelloBlockchain` složku s názvem a **Vyberte novou cestu k projektu**.

Azure Blockchain Development Kit vytvoří a inicializuje nový projekt Solidity pro vás. Základní projekt obsahuje ukázkovou inteligentní smlouvu **HelloBlockchain** a všechny potřebné soubory pro sestavení a nasazení pro člena konsorcia ve službě Azure Blockchain Service. Vytvoření projektu může trvat několik minut. Průběh terminálového panelu VS Code můžete sledovat výběrem výstupu pro Azure Blockchain.

Struktura projektu vypadá jako následující příklad:

   ![Projekt pevnosti](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Vytvoření inteligentnísmlouvy

Inteligentní smlouvy jsou umístěny v adresáři **smluv** projektu. Před nasazením inteligentních kontraktů do blockchainu kompilujete chytré kontrakty. Pomocí příkazu **Sestavit smlouvy** zkompilujte všechny inteligentní kontrakty v projektu.

1. V postranním panelu Průzkumníka kódu VS rozbalte složku **smluv** v projektu.
1. Klikněte pravým tlačítkem na **HelloBlockchain.sol** a z nabídky zvolte **Sestavit smlouvy.**

    ![Zvolte nabídka Sestavit smlouvy. ](./media/send-transaction/build-contracts.png)

Azure Blockchain Development Kit používá Lanýže ke kompilaci inteligentních smluv.

![Výstup překladače lanýžů](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Nasazení inteligentní smlouvy

Lanýž používá migrační skripty k nasazení smluv do sítě Ethereum. Migrace jsou soubory JavaScriptu umístěné v adresáři **migrace** projektu.

1. Pokud chcete nasadit inteligentní kontrakt, klikněte pravým tlačítkem na **HelloBlockchain.sol** a z nabídky zvolte **Nasadit kontrakty.**
1. V paletě příkazů zvolte síť konsorcia Azure Blockchain. Blockchainová síť konsorcia byla přidána do konfiguračního souboru lanýžů projektu při vytváření projektu.
1. Zvolte **Generovat mnemotechnické pomůcky**. Zvolte název souboru a uložte mnemotechnické pomůcky do složky projektu. Například, `myblockchainmember.env`. Mnemotechnické soubory se používají ke generování soukromého klíče Ethereum pro vašeho člena blockchainu.

Azure Blockchain Development Kit používá Lanýže ke spuštění skriptu migrace k nasazení smluv do blockchainu.

![Úspěšně nasazená smlouva](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Volání funkce smlouvy

Funkce **SendRequest** kontraktu **HelloBlockchain** změní proměnnou **stavu RequestMessage.** Změna stavu blockchainové sítě se provádí prostřednictvím transakce. Pomocí stránky inteligentní interakce kontraktu Azure Blockchain Development Kit můžete volat funkci **SendRequest** prostřednictvím transakce.

1. Chcete-li pracovat s inteligentní smlouvou, klikněte pravým tlačítkem na **HelloBlockchain.sol** a z nabídky zvolte **Zobrazit stránku interakce inteligentní smlouvy.**

    ![Z nabídky zvolte Zobrazit stránku interakce inteligentní smlouvy.](./media/send-transaction/contract-interaction.png)

1. Stránka interakce umožňuje zvolit nasazenou verzi smlouvy, funkce volání, zobrazit aktuální stav a zobrazit metadata.

    ![Příklad stránky interakce inteligentní smlouvy](./media/send-transaction/interaction-page.png)

1. Chcete-li volat inteligentní funkci smlouvy, vyberte akci smlouvy a předat své argumenty. Zvolte **SendRequest** smluvní akce a zadejte **Hello, Blockchain!** pro parametr **requestMessage.** Vyberte **Execute** pro volání **sendrequest** funkce prostřednictvím transakce.

    ![Spustit akci SendRequest](./media/send-transaction/sendrequest-action.png)

Po zpracování transakce, část interakce odráží změny stavu.

![Změny stavu smlouvy](./media/send-transaction/contract-state.png)

Funkce SendRequest nastaví pole **RequestMessage** a **State.** Aktuální stav **requestMessage** je argument, který jste **předali Hello, Blockchain**. Hodnota pole **Stav** zůstává **Požadavek**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už není potřeba, můžete odstranit prostředky `myResourceGroup` odstraněním skupiny prostředků, které jste vytvořili v *příkazu Vytvořit požadavek člena blockchainu.*

Odstranění skupiny prostředků:

1. Na webu Azure Portal přejděte do **skupiny prostředků** v levém navigačním podokně a vyberte skupinu prostředků, kterou chcete odstranit.
1. Vyberte **Odstranit skupinu prostředků**. Ověřte odstranění zadáním názvu skupiny prostředků a vyberte **odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili ukázkový projekt Solidity pomocí Azure Blockchain Development Kit. Vytvořili jste a nasadili inteligentní kontrakt, který se pak nazývá funkce prostřednictvím transakce v síti blockchainového konsorcia hostované ve službě Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Vývoj blockchainových aplikací pomocí služby Azure Blockchain Service](develop.md)
