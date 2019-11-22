---
title: Použití Visual Studio Code k připojení ke službě Azure blockchain
description: Připojte se k síti konsorcia služeb Azure blockchain pomocí sady Azure blockchain Development Kit pro Ethereem Extension v Visual Studio Code
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/19/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: c5880282ada9a2789c0a583568ba7e77624ebfe3
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286701"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Rychlý Start: použití Visual Studio Code pro připojení k síti konsorcia služeb Azure blockchain

V tomto rychlém startu nainstalujete a použijete službu Azure blockchain Development Kit pro rozšíření Visual Studio Code Ethereem pro připojení k konsorciu služby Azure blockchain. Vývojová sada Azure blockchain zjednodušuje vytváření, připojování, sestavování a nasazování inteligentních smluv v knihách blockchain Ethereem.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Kompletní [rychlé zprovoznění: Vytvoření člena blockchain pomocí Azure Portal](create-member.md) nebo [rychlé zprovoznění: Vytvoření člena blockchain služby Azure BLOCKCHAIN pomocí Azure CLI](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure blockchain Development Kit pro rozšíření Ethereem](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node. js 10.15. x nebo vyšší](https://nodejs.org/download)
* [Git 2.10. x nebo vyšší](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) Přidejte Python. exe do cesty. Pro Azure blockchain Development Kit se vyžaduje Python verze 2.7.15 ve vaší cestě.
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

V systému Windows je potřebný C++ kompilátor vyžadován pro modul Node-gyp. Můžete použít nástroje MSBuild:

* Pokud je nainstalována aplikace Visual Studio 2017, nakonfigurujte npm pro použití nástrojů MSBuild s příkazem `npm config set msvs_version 2017 -g`
* Pokud je nainstalována sada Visual Studio 2019, nastavte cestu k nástrojům pro sestavení MS pro npm. Například `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`.
* V opačném případě nainstalujte samostatné nástroje VS Build Tools pomocí `npm install --global windows-build-tools` v příkazovém okně *Spusťte správce* se zvýšenými oprávněními.

Další informace o Node-gyp najdete v části [úložiště Node-gyp na GitHubu](https://github.com/node-gyp).

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

1. V podokně aplikace Visual Studio Code (VS Code) rozbalte rozšíření **Azure blockchain** .
1. Vyberte **připojit k konsorciu**.

   ![Připojení k konsorciu](./media/connect-vscode/connect-consortium.png)

    Pokud se zobrazí výzva k ověření Azure, postupujte podle výzev k ověření pomocí prohlížeče.
1. V rozevírací nabídce paleta příkazů vyberte **připojit ke službě Azure blockchain Service Consortium** .
1. Vyberte předplatné a skupinu prostředků, která je přidružená k vašemu členovi konsorcia Azure blockchain Service.
1. Ze seznamu vyberte konsorcium.

Členové konsorcia a blockchain jsou uvedeni na postranním panelu Průzkumníka sady Visual Studio.

![Konsorcium zobrazené v Průzkumníkovi](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili službu Azure blockchain Development Kit pro rozšíření Ethereem Visual Studio Code pro připojení k konsorciu ve službě Azure blockchain. Zkuste v dalším kurzu použít Azure blockchain Development Kit pro Ethereem k vytvoření, sestavení, nasazení a spuštění funkce inteligentního kontraktu prostřednictvím transakce.

> [!div class="nextstepaction"]
> [Použití Visual Studio Code k vytváření, sestavování a nasazování inteligentních smluv](send-transaction.md)