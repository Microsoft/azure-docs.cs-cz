---
title: 'Rychlý Start bez serveru služby Azure Signaler – C #'
description: Rychlý Start pro použití služby signalizace Azure a Azure Functions k vytvoření chatovací místnosti pomocí jazyka C#.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/25/2020
ms.author: zhshang
ms.openlocfilehash: be26fdafe0a8a52669fe41fd5514c808f10df745
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369123"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-c"></a>Rychlý Start: vytvoření chatovací místnosti pomocí Azure Functions a služby signalizace pomocí jazyka C\#

Služba Azure SignalR Service umožňuje snadné přidávání funkcí v reálném čase do aplikací. Řešení Azure Functions představuje bezserverovou platformu, která umožňuje spouštět kód, aniž byste museli spravovat nějakou infrastrukturu. V tomto Rychlém zprovoznění se dozvíte, jak pomocí služby SignalR Service a Functions sestavit bezserverovou aplikaci pro chat v reálném čase.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte nainstalovanou aplikaci Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [edici Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.

Tento kurz můžete spustit také na příkazovém řádku (macOS, Windows nebo Linux) pomocí [Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing), [.NET Core SDK](https://dotnet.microsoft.com/download)a vašeho oblíbeného editoru kódu.

Pokud ještě nemáte předplatné Azure, [vytvořte ho zdarma](https://azure.microsoft.com/free/dotnet) ještě před tím, než začnete.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qscsharp)

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com/> pomocí svého účtu Azure.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qscsharp)

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qscsharp)

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qscsharp)

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurace a spuštění aplikace funkcí Azure

1. Spusťte sadu Visual Studio (nebo jiný Editor kódu) a otevřete řešení ve složce *Src/chat/CSharp* klonovaného úložiště.

1. V prohlížeči, ve kterém máte otevřený Azure Portal, si ověřte, že se úspěšně vytvořila instance služby SignalR Service, kterou jste nasadili dříve. Vyhledejte její název pomocí vyhledávacího pole v horní části stránky portálu. Instanci vyberte a otevřete.

    ![Vyhledání instance služby SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Výběrem možnosti **Klíče** zobrazte připojovací řetězce instance služby SignalR.

1. Vyberte a zkopírujte primární připojovací řetězec.

1. Zpět v aplikaci Visual Studio – **Průzkumník řešení**přejmenovat *local.settings.sample.jsna* na *local.settings.jszapnuto*.

1. V souboru *local.settings.json* vložte připojovací řetězec do hodnoty nastavení **AzureSignalRConnectionString**. Uložte soubor.

1. Otevřete *Functions.cs*. Tato aplikace funkcí zahrnuje dvě funkce aktivované protokolem HTTP:

    - **GetSignalRInfo** – používá `SignalRConnectionInfo` vstupní vazbu k vygenerování a vrácení platných informací o připojení.
    - **SendMessage**: Přijímá zprávy chatu v textu žádosti a používá výstupní vazbu *SignalR* k vysílání zpráv všem připojeným klientským aplikacím.

1. Pomocí jedné z následujících možností můžete spustit aplikaci funkce Azure místně.

    - **Visual Studio**: v nabídce *ladění* vyberte *Spustit ladění* , aby se aplikace spustila.

        ![Ladění aplikace](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-debug-vs.png)

    - **Příkazový řádek**: spusťte následující příkaz, který spustí hostitele funkce.

        ```bash
        func start
        ```
[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qscsharp)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qscsharp)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qscsharp)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili a spustili aplikaci bez serveru v reálném čase v aplikaci Visual Studio. Dále se dozvíte více o tom, jak vyvíjet a nasazovat Azure Functions pomocí sady Visual Studio.

> [!div class="nextstepaction"]
> [Vývoj Azure Functions pomocí sady Visual Studio](../azure-functions/functions-develop-vs.md)

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qscsharp)
