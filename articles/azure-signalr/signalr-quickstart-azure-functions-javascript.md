---
title: Rychlý Start bez serveru služby Azure Signaler – JavaScript
description: Rychlý Start pro použití služby signalizace Azure a Azure Functions k vytvoření chatovací místnosti.
author: sffamily
ms.service: signalr
ms.devlang: javascript
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: fd935ffda7d16988781d5debce9333ccf2adb16f
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709758"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-javascript"></a>Rychlý Start: vytvoření chatovací místnosti pomocí Azure Functions a služby signalizace pomocí JavaScriptu

Služba signalizace Azure umožňuje snadno přidat do své aplikace funkce v reálném čase. Azure Functions je platforma bez serveru, která umožňuje spuštění kódu bez nutnosti spravovat infrastrukturu. V tomto rychlém startu se dozvíte, jak pomocí služby a funkcí signalizace vytvořit aplikaci pro chat bez serveru, která je v reálném čase.

## <a name="prerequisites"></a>Požadavky

Tento rychlý Start může běžet na macOS, Windows nebo Linux.

Ujistěte se, že máte Editor kódu, například [Visual Studio Code](https://code.visualstudio.com/) nainstalován.

Nainstalujte [Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing) , aby se spouštěly aplikace Azure Functions místně.

Azure Functions vyžaduje [Node. js](https://nodejs.org/en/download/) verze 8 nebo 10.

Aby bylo možné nainstalovat rozšíření, Azure Functions Core Tools aktuálně vyžaduje [.NET Core SDK](https://www.microsoft.com/net/download) nainstalován. Pro sestavování aplikací Azure Functions pro JavaScript ale není potřeba žádné znalosti .NET.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure Portal na <https://portal.azure.com/> pomocí svého účtu Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurace a spuštění aplikace funkce Azure Functions

1. V prohlížeči, kde je otevřený Azure Portal, zkontrolujte, že instance služby signalizace, kterou jste dříve nasadili, se úspěšně vytvořila, a to tak, že do vyhledávacího pole v horní části portálu vyhledá svůj název. Vyberte instanci, kterou chcete otevřít.

    ![Hledání instance služby Signaler](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Vyberte **klíče** a zobrazte připojovací řetězce pro instanci služby signalizace.

1. Vyberte a zkopírujte primární připojovací řetězec.

    ![Vytvořit službu Signal](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. V editoru kódu otevřete složku *Src/chat/JavaScript* v klonovaném úložišti.

1. Přejmenujte *Local. Settings. Sample. JSON* na *Local. Settings. JSON*.

1. Do pole **Local. Settings. JSON**vložte připojovací řetězec do hodnoty nastavení **AzureSignalRConnectionString** . Uložte soubor.

1. Funkce JavaScriptu jsou uspořádány do složek. V každé složce jsou dva soubory: *Function. JSON* definuje vazby, které jsou používány ve funkci, a *index. js* je tělo funkce. V této aplikaci Function App jsou dvě funkce aktivované protokolem HTTP:

    - **Negotiate** – pomocí vstupní vazby *SignalRConnectionInfo* vygeneruje a vrátí platné informace o připojení.
    - **zprávy** – přijme zprávu chatu v textu požadavku a použije výstupní vazbu *signálu* k vysílání zprávy všem připojeným klientským aplikacím.

1. V terminálu se ujistěte, že jste ve složce *Src/chat/JavaScript* . K instalaci rozšíření potřebných ke spuštění aplikace použijte Azure Functions Core Tools.

    ```bash
    func extensions install
    ```

1. Spusťte aplikaci Function App.

    ```bash
    func start
    ```

    ![Vytvořit službu Signal](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste v VS Code vytvořili a spustili aplikaci bez serveru v reálném čase. V dalším kroku se dozvíte víc o tom, jak nasadit Azure Functions z VS Code.

> [!div class="nextstepaction"]
> [Nasazení Azure Functions s využitím VS Code](/azure/javascript/tutorial-vscode-serverless-node-01)
