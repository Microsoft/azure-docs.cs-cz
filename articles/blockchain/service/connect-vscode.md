---
title: Připojení ke službě Azure Blockchain Service pomocí kódu Visual Studia
description: Připojení k síti konsorcia Azure Blockchain Service pomocí rozšíření Azure Blockchain Development Kit for Ethereum v kódu Visual Studio
ms.date: 04/22/2020
ms.topic: quickstart
ms.reviewer: caleteet
ms.openlocfilehash: 8b502966317c5d07e89de4ae70ff72b899e963e6
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084834"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Úvodní příručka: Připojení k síti konsorcia Azure Blockchain Service pomocí kódu Visual Studia

V tomto rychlém startu nainstalujete a použijete rozšíření Azure Blockchain Development Kit for Ethereum Visual Studio Code (VS Code), které se připojí ke konsorciu ve službě Azure Blockchain Service. Azure Blockchain Development Kit zjednodušuje způsob vytváření, připojování, vytváření a nasazování inteligentních smluv v knihách ethereum blockchain.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadované součásti

* Kompletní [úvodní příručka: Vytvoření člena blockchainu pomocí portálu Azure](create-member.md) nebo [úvodního startu: Vytvoření člena blockchainu služby Azure Blockchain pomocí Azure CLI](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure Blockchain Development Kit pro rozšíření Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x nebo vyšší](https://nodejs.org)
* [Git 2.10.x nebo vyšší](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) Přidejte python.exe na cestu. S Python verze 2.7.15 ve vaší cestě je nutné pro Azure Blockchain Development Kit.
* [Lanýž 5,0,0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

V systému Windows je pro modul node-gyp vyžadován nainstalovaný kompilátor jazyka C++. Můžete použít nástroje MSBuild:

* Pokud je nainstalována Visual Studio 2017, nakonfigurujte npm používat nástroje MSBuild s příkazem`npm config set msvs_version 2017 -g`
* Pokud je nainstalován visual studio 2019, nastavte cestu nástroje sestavení MS pro npm. Například `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`.
* V opačném případě nainstalujte samostatné nástroje `npm install --global windows-build-tools` sestavení VS pomocí ve zvýšeném *spuštění jako příkazového* prostředí správce.

Další informace o node-gyp najdete v [úložišti node-gyp na GitHubu](https://github.com/nodejs/node-gyp).

### <a name="verify-azure-blockchain-development-kit-environment"></a>Ověření prostředí Azure Blockchain Development Kit

Azure Blockchain Development Kit ověřuje, že byly splněny vaše požadavky na vývojové prostředí. Ověření vývojového prostředí:

Z palety příkazů VS Kód zvolte **Azure Blockchain: Zobrazit úvodní stránku**.

Azure Blockchain Development Kit spustí ověřovací skript, který trvá asi minutu. Výstup můžete zobrazit výběrem **terminálu > Nový terminál**. Na řádku nabídek terminálu vyberte v rozevíracím souboru kartu **Výstup** a **Azure Blockchain.** Úspěšné ověření vypadá tak, že je zobrazen na následujícím obrázku:

![Platné vývojové prostředí](./media/connect-vscode/valid-environment.png)

 Pokud vám chybí požadovaný nástroj, nová karta s názvem **Azure Blockchain Development Kit – preview** uvádí požadované nástroje s odkazy ke stažení.

![Aplikace požadované pro dev kit](./media/connect-vscode/required-apps.png)

Před pokračováním v rychlém startu nainstalujte všechny chybějící požadavky.

## <a name="connect-to-consortium-member"></a>Připojení ke členovi konsorcia

Ke členům konsorcia se můžete připojit pomocí rozšíření Azure Blockchain Development Kit VS Code. Po připojení ke konsorciu můžete kompilovat, vytvářet a nasazovat inteligentní kontrakty do člena konsorcia Azure Blockchain Service.

Pokud nemáte přístup ke členovi konsorcia Azure Blockchain Service, vyplňte předpoklad [Rychlý start: Vytvořte člena blockchainu pomocí portálu Azure nebo](create-member.md) [úvodního startu: Vytvořte člena blockchainu služby Azure Blockchain pomocí azure CLI](create-member-cli.md).

1. V podokně Průzkumník kódu VS rozbalte rozšíření **Azure Blockchain.**
1. Vyberte **Připojit k síti**.

   ![Připojení k síti](./media/connect-vscode/connect-consortium.png)

    Pokud se zobrazí výzva k ověření Azure, postupujte podle pokynů k ověření pomocí prohlížeče.
1. V rozevíracím souboru palety příkazů zvolte **službu Azure Blockchain** Service.
1. Zvolte předplatné a skupinu prostředků přidruženou k vašemu členovi konsorcia služby Azure Blockchain Service.
1. Vyberte si konsorcium ze seznamu.

Členové konsorcia a blockchainu jsou uvedeni na postranním panelu Průzkumníka vs kódu.

![Konsorcium zobrazené v průzkumníku](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili Azure Blockchain Development Kit pro rozšíření Ethereum VS Code k připojení ke konsorciu ve službě Azure Blockchain. Vyzkoušejte další kurz, ve které můžete použít Azure Blockchain Development Kit for Ethereum k vytvoření, sestavení, nasazení a spuštění funkce inteligentní smlouvy prostřednictvím transakce.

> [!div class="nextstepaction"]
> [Vytvářejte, vytvářejte a nasazujte inteligentní kontrakty ve službě Azure Blockchain](send-transaction.md)