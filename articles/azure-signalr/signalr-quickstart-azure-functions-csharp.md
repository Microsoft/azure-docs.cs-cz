---
title: Funkce SignalR služba bez serveru rychlý start Azure –C#
description: Rychlý start pro vytvoření chatovací místnosti pomocí služby Azure SignalR a Azure Functions.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: df1b2cdd265b9cf48801174d1f0cd9936eae5f8b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264983"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-c"></a>Rychlý start: Vytvoření chatovací místnosti s Azure Functions a služby SignalR pomocí jazyka C\#

Služba Azure SignalR umožňuje snadné přidávání funkcí do aplikací v reálném čase. Řešení Azure Functions představuje bezserverovou platformu, která umožňuje spouštět kód, aniž byste museli spravovat nějakou infrastrukturu. V tomto rychlém startu se dozvíte, jak pomocí služby SignalR a Functions sestavíte bezserverovou aplikaci pro chat v reálném čase.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte nainstalovanou sadu Visual Studio 2017, můžete stáhnout a použít **bezplatnou verzi** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.

V tomto kurzu může také spustit na příkazovém řádku (macOS, Windows nebo Linuxem) pomocí [nástrojů Azure Functions Core (v2)](https://github.com/Azure/azure-functions-core-tools#installing), [.NET Core SDK](https://dotnet.microsoft.com/download)a váš oblíbený editor kódu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com/> pomocí svého účtu Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurace a spuštění aplikace Azure Functions

1. Spusťte Visual Studio (nebo jiného editoru kódu) a otevřete řešení v *src/chatovací a csharp* složky naklonovaného úložiště.

1. V prohlížeči, ve kterém máte otevřený Azure Portal, si ověřte, že se úspěšně vytvořila instance služby SignalR, kterou jste nasadili dříve. Vyhledejte její název pomocí vyhledávacího pole v horní části stránky portálu. Instanci vyberte a otevřete.

    ![Vyhledání instance služby SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Výběrem možnosti **Klíče** zobrazte připojovací řetězce instance služby SignalR.

1. Vyberte a zkopírujte primární připojovací řetězec.

1. Přejděte zpět do sady Visual Studio a v Průzkumníkovi řešení přejmenujte *local.settings.sample.json* na *local.settings.json*.

1. V souboru **local.settings.json** vložte připojovací řetězec do hodnoty nastavení **AzureSignalRConnectionString**. Uložte soubor.

1. Otevřete **Functions.cs**. Tato aplikace funkcí zahrnuje dvě funkce aktivované protokolem HTTP:

    - **GetSignalRInfo**: Používá vstupní vazbu *SignalRConnectionInfo* ke generování a vracení informací o platném připojení.
    - **SendMessage**: Přijímá zprávy chatu v textu žádosti a používá výstupní vazbu *SignalR* k vysílání zpráv všem připojeným klientským aplikacím.

1. Použijte jednu z následujících možností a spusťte aplikaci Azure Function místně.

    - **Visual Studio**: V nabídce *Ladit* vyberte *Spustit ladění* a spusťte aplikaci.

        ![Ladění aplikace](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-debug-vs.png)

    - **Příkazový řádek**: Spusťte následující příkaz pro spuštění funkce hostitele.

        ```bash
        func start
        ```

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu vytvořené a spustili aplikaci v reálném čase bez serveru v sadě Visual Studio. Dále se dozvíte více o tom, jak vyvíjet a nasazovat Azure Functions pomocí sady Visual Studio.

> [!div class="nextstepaction"]
> [Vývoj Azure Functions pomocí sady Visual Studio](../azure-functions/functions-develop-vs.md)