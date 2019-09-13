---
title: Použití Azure blockchain Development Kit pro Ethereem pro připojení ke službě Azure blockchain Service
description: Připojte se k síti konsorcia služeb Azure blockchain pomocí sady Azure blockchain Development Kit pro Ethereem Extension v Visual Studio Code
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 82b71a9d837ed3cb1d9461c0de2559178685b4b4
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935814"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Rychlý start: Použití Visual Studio Code k připojení k síti konsorcia Azure blockchain

V tomto rychlém startu nainstalujete a použijete službu Azure blockchain Development Kit pro rozšíření Visual Studio Code Ethereem pro připojení k konsorciu služby Azure blockchain. Vývojová sada Azure blockchain zjednodušuje vytváření, připojování, sestavování a nasazování inteligentních smluv v knihách Ethereem. 

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

![Platné vývojové prostředí](./media/connect-vscode/valid-environment.png)

 Pokud chybí požadovaný nástroj, nová karta s názvem **Azure blockchain Development Kit – Preview** obsahuje seznam požadovaných aplikací k instalaci a odkazy na stažení nástrojů.

![Požadované aplikace pro vývojovou sadu](./media/connect-vscode/required-apps.png)

Před pokračováním v rychlém startu nainstalujte všechny chybějící požadované součásti.

## <a name="connect-to-consortium-member"></a>Připojit k členovi konsorcia

K členům konsorcia se můžete připojit pomocí rozšíření VS Code Azure blockchain Development Kit. Po připojení k konsorciu můžete kompilovat, sestavovat a nasazovat inteligentní kontrakty na člena konsorcia služeb Azure blockchain.

Pokud nemáte přístup k členovi konsorcia služeb Azure blockchain, proveďte potřebný [rychlý Start: Vytvořte člena blockchain pomocí Azure Portal](create-member.md) nebo [rychlého startu: Vytvořte člen blockchain služby Azure blockchain pomocí Azure CLI](create-member-cli.md).

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

V tomto rychlém startu jste použili službu Azure blockchain Development Kit pro rozšíření Ethereem Visual Studio Code pro připojení k konsorciu ve službě Azure blockchain. Zkuste v dalším kurzu použít Azure blockchain Development Kit pro Ethereem a Truffle k vytvoření, sestavení, nasazení a spuštění funkce inteligentního kontraktu prostřednictvím transakce.

> [!div class="nextstepaction"]
> [Použití Visual Studio Code k vytváření, sestavování a nasazování inteligentních smluv](send-transaction.md)