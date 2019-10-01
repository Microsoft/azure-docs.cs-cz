---
title: Rychlý Start bez serveru služby signálů Azure – Python
description: Rychlý Start pro použití služby signalizace Azure a Azure Functions k vytvoření chatovací místnosti.
author: anthonychu
ms.service: signalr
ms.devlang: python
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: antchu
ms.openlocfilehash: 728111548176a0a3212b1677eeb192ccdc47fe88
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709499"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>Rychlý Start: vytvoření chatovací místnosti pomocí Azure Functions a služby signalizace pomocí Pythonu

Služba signalizace Azure umožňuje snadno přidat do své aplikace funkce v reálném čase. Azure Functions je platforma bez serveru, která umožňuje spuštění kódu bez nutnosti spravovat infrastrukturu. V tomto rychlém startu se dozvíte, jak pomocí služby a funkcí signalizace vytvořit aplikaci pro chat bez serveru, která je v reálném čase.

## <a name="prerequisites"></a>Požadavky

Tento rychlý Start může běžet na macOS, Windows nebo Linux.

Ujistěte se, že máte Editor kódu, například [Visual Studio Code](https://code.visualstudio.com/) nainstalován.

Pokud chcete spouštět aplikace funkcí v Pythonu pro Azure místně, nainstalujte [Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing) (verze 2.7.1505 nebo novější).

Azure Functions vyžaduje [Python 3,6](https://www.python.org/downloads/).

Aby bylo možné nainstalovat rozšíření, Azure Functions Core Tools aktuálně vyžaduje [.NET Core SDK](https://www.microsoft.com/net/download) nainstalován. Pro sestavování aplikací Azure Functions v Pythonu se ale nevyžadují žádné znalosti .NET.

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

1. V editoru kódu otevřete složku *Src/chat/Python* v klonovaném úložišti.

1. Aby bylo možné místně vyvíjet a testovat funkce Pythonu, je nutné pracovat v prostředí Python 3,6. Spuštěním následujících příkazů vytvořte a aktivujte virtuální prostředí s názvem `.venv`.

    **Linux nebo macOS:**

    ```bash
    python3.6 -m venv .venv
    source .venv/bin/activate
    ```

    **Systému**

    ```powershell
    py -3.6 -m venv .venv
    .venv\scripts\activate
    ```

1. Přejmenujte *Local. Settings. Sample. JSON* na *Local. Settings. JSON*.

1. Do pole **Local. Settings. JSON**vložte připojovací řetězec do hodnoty nastavení **AzureSignalRConnectionString** . Uložte soubor.

1. Funkce Pythonu jsou uspořádány do složek. V každé složce jsou dva soubory: *Function. JSON* definuje vazby, které jsou používány ve funkci, a *\_ @ no__t-3init @ no__t-4\_.py* je tělo funkce. V této aplikaci Function App jsou dvě funkce aktivované protokolem HTTP:

    - **Negotiate** – pomocí vstupní vazby *SignalRConnectionInfo* vygeneruje a vrátí platné informace o připojení.
    - **zprávy** – přijme zprávu chatu v textu požadavku a použije výstupní vazbu *signálu* k vysílání zprávy všem připojeným klientským aplikacím.

1. V terminálu se ujistěte, že jste ve složce *Src/chat/Python* . K instalaci rozšíření potřebných ke spuštění aplikace použijte Azure Functions Core Tools.

    ```bash
    func extensions install
    ```

1. Spusťte aplikaci Function App.

    ```bash
    func start
    ```

    ![Spustit aplikaci Function App](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste v VS Code vytvořili a spustili aplikaci bez serveru v reálném čase. V dalším kroku se dozvíte víc o tom, jak nasadit Azure Functions z VS Code.

> [!div class="nextstepaction"]
> [Nasazení Azure Functions s využitím VS Code](/azure/javascript/tutorial-vscode-serverless-node-01)
