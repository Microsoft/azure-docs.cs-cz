---
title: Azure SignalR Service rychlý start bez serveru - Python
description: Rychlé zprovoznění popisující vytvoření chatovací místnosti pomocí služby Azure SignalR Service a Azure Functions.
author: anthonychu
ms.service: signalr
ms.devlang: python
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: antchu
ms.openlocfilehash: 0cf8705cf2567a60129681c2db41b0868f8fe182
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75392155"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>Úvodní příručka: Vytvoření chatovací místnosti pomocí Azure Functions and SignalR Service pomocí Pythonu

Služba Azure SignalR Service umožňuje snadné přidávání funkcí v reálném čase do aplikací. Řešení Azure Functions představuje bezserverovou platformu, která umožňuje spouštět kód, aniž byste museli spravovat nějakou infrastrukturu. V tomto Rychlém zprovoznění se dozvíte, jak pomocí služby SignalR Service a Functions sestavit bezserverovou aplikaci pro chat v reálném čase.

## <a name="prerequisites"></a>Požadavky

Toto Rychlé zprovoznění je možné spustit v systémech macOS, Windows nebo Linux.

Budete potřebovat nainstalovaný editor kódu, jako je třeba [Visual Studio Code](https://code.visualstudio.com/).

Nainstalujte [nástroje Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (verze 2.7.1505 nebo vyšší) a spusťte aplikace Python Azure Function místně.

Funkce Azure vyžaduje [Python 3.6 nebo 3.7](https://www.python.org/downloads/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com/> pomocí svého účtu Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurace a spuštění aplikace funkcí Azure

1. V prohlížeči, ve kterém máte otevřený Azure Portal, si ověřte, že se úspěšně vytvořila instance služby SignalR Service, kterou jste nasadili dříve. Vyhledejte její název pomocí vyhledávacího pole v horní části stránky portálu. Instanci vyberte a otevřete.

    ![Vyhledání instance služby SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Výběrem možnosti **Klíče** zobrazte připojovací řetězce instance služby SignalR.

1. Vyberte a zkopírujte primární připojovací řetězec.

    ![Vytvoření služby SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. V editoru kódu otevřete složku *src/chat/python* v naklonovaném úložišti.

1. Chcete-li místně vyvíjet a testovat funkce Pythonu, musíte pracovat v prostředí Pythonu 3.6 nebo 3.7. Spusťte následující příkazy a vytvořte `.venv`a aktivujte virtuální prostředí s názvem .

    **Linux nebo macOS:**

    ```bash
    python3.7 -m venv .venv
    source .venv/bin/activate
    ```

    **Windows:**

    ```powershell
    py -3.7 -m venv .venv
    .venv\scripts\activate
    ```

1. Přejmenujte soubor *local.settings.sample.json* na *local.settings.json*.

1. V souboru **local.settings.json** vložte připojovací řetězec do hodnoty nastavení **AzureSignalRConnectionString**. Uložte soubor.

1. Funkce Pythonu jsou uspořádány do složek. V každé složce jsou dva soubory: *function.json* definuje vazby, které se používají ve funkci a * \_ \_init\_\_.py* je tělo funkce. Tato aplikace funkcí zahrnuje dvě funkce aktivované protokolem HTTP:

    - **negotiate** – používá vstupní vazbu *SignalRConnectionInfo* ke generování a vracení informací o platném připojení.
    - **messages** – přijímá zprávy chatu v textu požadavku a používá výstupní vazbu *SignalR* k vysílání zpráv všem připojeným klientským aplikacím.

1. V terminálu s aktivovaným virtuálním prostředím se ujistěte, že jste ve složce *src/chat/python.* Nainstalujte potřebné balíčky Pythonu pomocí PIP.

    ```bash
    python -m pip install -r requirements.txt
    ```

1. Aplikace funkcí spusťte.

    ```bash
    func start
    ```

    ![Spustit aplikaci funkcí](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili a spustili aplikaci bez serveru v reálném čase v kódu VS. Dále můžete získat více informací o postupu nasazení Azure Functions přes VS Code.

> [!div class="nextstepaction"]
> [Nasazení Azure Functions přes VS Code](/azure/javascript/tutorial-vscode-serverless-node-01)
