---
title: Použití Visual Studio Code k připojení ke službě Azure blockchain
description: Připojte se k síti konsorcia služeb Azure blockchain pomocí sady Azure blockchain Development Kit pro Ethereem Extension v Visual Studio Code
ms.date: 04/22/2020
ms.topic: quickstart
ms.reviewer: caleteet
ms.openlocfilehash: 8b502966317c5d07e89de4ae70ff72b899e963e6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "82084834"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Rychlý Start: použití Visual Studio Code pro připojení k síti konsorcia služeb Azure blockchain

V tomto rychlém startu nainstalujete a použijete pro rozšíření Ethereem Visual Studio Code (blockchain Development Kit) pro připojení k konsorciu Azure blockchain Service rozšíření (VS Code). Vývojová sada Azure blockchain zjednodušuje vytváření, připojování, sestavování a nasazování inteligentních smluv v knihách blockchain Ethereem.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

* Kompletní [rychlé zprovoznění: Vytvoření člena blockchain pomocí Azure Portal](create-member.md) nebo [rychlé zprovoznění: Vytvoření člena blockchain služby Azure BLOCKCHAIN pomocí Azure CLI](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure blockchain Development Kit pro rozšíření Ethereem](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15. x nebo vyšší](https://nodejs.org)
* [Git 2.10. x nebo vyšší](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) Přidejte python.exe k cestě. Pro Azure blockchain Development Kit se vyžaduje 2.7.15 verze Pythonu ve vaší cestě.
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

Ve Windows se pro modul gyp vyžaduje nainstalovaný kompilátor C++. Můžete použít nástroje MSBuild:

* Pokud je nainstalována aplikace Visual Studio 2017, nakonfigurujte npm pro použití nástrojů MSBuild s příkazem `npm config set msvs_version 2017 -g`
* Pokud je nainstalována sada Visual Studio 2019, nastavte cestu k nástrojům pro sestavení MS pro npm. Například `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`.
* V opačném případě nainstalujte samostatné nástroje VS Build Tools pomocí `npm install --global windows-build-tools` příkazu v příkazovém okně *Spustit jako správce* .

Další informace o Node-gyp najdete v části [úložiště Node-gyp na GitHubu](https://github.com/nodejs/node-gyp).

### <a name="verify-azure-blockchain-development-kit-environment"></a>Ověření prostředí Azure blockchain Development Kit

Sada Azure blockchain Development Kit ověřuje splnění požadavků na vývojové prostředí. Ověření vašeho vývojového prostředí:

Z palety příkazů VS Code vyberte možnost **Azure blockchain: Zobrazit úvodní stránku**.

Azure blockchain Development Kit spustí ověřovací skript, který dokončení trvá přibližně minutu. Výstup můžete zobrazit tak, že vyberete **terminál > nový terminál**. V řádku nabídek terminálu vyberte kartu **výstup** a **Azure blockchain** v rozevíracím seznamu. Úspěšné ověření vypadá jako na následujícím obrázku:

![Platné vývojové prostředí](./media/connect-vscode/valid-environment.png)

 Pokud chybí požadovaný nástroj, nová karta s názvem **Azure blockchain Development Kit – Preview** zobrazí seznam požadovaných nástrojů s odkazy ke stažení.

![Požadované aplikace pro vývojovou sadu](./media/connect-vscode/required-apps.png)

Před pokračováním v rychlém startu nainstalujte všechny chybějící požadované součásti.

## <a name="connect-to-consortium-member"></a>Připojit k členovi konsorcia

K členům konsorcia se můžete připojit pomocí rozšíření VS Code Azure blockchain Development Kit. Po připojení k konsorciu můžete kompilovat, sestavovat a nasazovat inteligentní kontrakty na člena konsorcia služeb Azure blockchain.

Pokud nemáte přístup k členovi konsorcia služeb Azure blockchain, proveďte kompletní [rychlé zprovoznění: Vytvoření členu blockchain pomocí Azure Portal](create-member.md) nebo [rychlé zprovoznění: Vytvoření člena Azure blockchain Service BLOCKCHAIN pomocí Azure CLI](create-member-cli.md).

1. V podokně Průzkumník VS Code rozbalte rozšíření **Azure blockchain** .
1. Vyberte **připojit k síti**.

   ![Připojit k síti](./media/connect-vscode/connect-consortium.png)

    Pokud se zobrazí výzva k ověření Azure, postupujte podle výzev k ověření pomocí prohlížeče.
1. V rozevírací nabídce paleta příkazů vyberte **Azure blockchain Service** .
1. Vyberte předplatné a skupinu prostředků, která je přidružená k vašemu členovi konsorcia Azure blockchain Service.
1. Ze seznamu vyberte konsorcium.

Členové konsorcia a blockchain jsou uvedeni na bočním panelu Průzkumníka VS Code.

![Konsorcium zobrazené v Průzkumníkovi](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili službu Azure blockchain Development Kit pro rozšíření Ethereem VS Code pro připojení k konsorciu ve službě Azure blockchain. Zkuste v dalším kurzu použít Azure blockchain Development Kit pro Ethereem k vytvoření, sestavení, nasazení a spuštění funkce inteligentního kontraktu prostřednictvím transakce.

> [!div class="nextstepaction"]
> [Vytváření, sestavování a nasazování inteligentních smluv ve službě Azure blockchain](send-transaction.md)