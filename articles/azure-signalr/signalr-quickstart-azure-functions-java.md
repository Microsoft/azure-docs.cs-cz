---
title: Funkce SignalR služba bez serveru rychlý start Azure – Java
description: Rychlý start pro vytvoření chatovací místnosti pomocí služby Azure SignalR a Azure Functions.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: 9e4e64b99a69e523547bae04146c7460d08bc1df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775842"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-java"></a>Rychlý start: Vytvoření chatovací místnosti s Azure Functions a SignalR služby pomocí Javy

Služba Azure SignalR umožňuje snadné přidávání funkcí do aplikací v reálném čase. Řešení Azure Functions představuje bezserverovou platformu, která umožňuje spouštět kód, aniž byste museli spravovat nějakou infrastrukturu. V tomto rychlém startu se dozvíte, jak pomocí služby SignalR a Functions sestavíte bezserverovou aplikaci pro chat v reálném čase.

## <a name="prerequisites"></a>Požadavky

Toto Rychlé zprovoznění je možné spustit v systémech macOS, Windows nebo Linux.

Budete potřebovat nainstalovaný editor kódu, jako je třeba [Visual Studio Code](https://code.visualstudio.com/).

Nainstalujte si [nástroje Azure Functions Core Tools verze 2](https://github.com/Azure/azure-functions-core-tools#installing), abyste mohli aplikace funkcí Azure spouštět místně.

> [!NOTE]
> Použití služby SignalR vazby v Javě, ujistěte se, že používáte verzi 2.4.419 nebo vyšší nástrojů Azure Functions Core (verze hostitele 2.0.12332).

K instalaci rozšíření vyžadují v současnosti nástroje Azure Functions Core Tools nainstalovanou sadu [.NET Core SDK](https://www.microsoft.com/net/download). K sestavování aplikací funkcí Azure v JavaScriptu však není potřeba žádná znalost architektury .NET.

K vývoji aplikace funkcí pomocí Javy potřebujete následující:

* [Java Developer Kit](https://www.azul.com/downloads/zulu/) verze 8.
* [Apache Maven](https://maven.apache.org) verze 3.0 nebo novější.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com/> pomocí svého účtu Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurace a spuštění aplikace Azure Functions

1. V prohlížeči, ve kterém máte otevřený Azure Portal, si ověřte, že se úspěšně vytvořila instance služby SignalR Service, kterou jste nasadili dříve. Vyhledejte její název pomocí vyhledávacího pole v horní části stránky portálu. Instanci vyberte a otevřete.

    ![Vyhledání instance služby SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Výběrem možnosti **Klíče** zobrazte připojovací řetězce instance služby SignalR.

1. Vyberte a zkopírujte primární připojovací řetězec.

    ![Vytvoření služby SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. V editoru kódu, otevřete *src/chat/java* složky naklonované úložiště.

1. Přejmenujte soubor *local.settings.sample.json* na *local.settings.json*.

1. V souboru **local.settings.json** vložte připojovací řetězec do hodnoty nastavení **AzureSignalRConnectionString**. Uložte soubor.

1. Hlavní soubor, který obsahuje funkce jsou v *src/chat/java/src/main/java/com/function/Functions.java*:

    - **negotiate** – používá vstupní vazbu *SignalRConnectionInfo* ke generování a vracení informací o platném připojení.
    - **SendMessage** – přijme zprávu chat v textu požadavku a používá *SignalR* výstupní vazbu k vysílání zpráv do všech připojení klientských aplikací.

1. V terminálu se ujistit, že jste *src/chat/java* složky. Vytvoření aplikace function app.

    ```bash
    mvn clean package
    ```

1. Místní spuštění aplikace function app.

    ```bash
    mvn azure-functions:run
    ```

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu vytvořené a spustili aplikaci v reálném čase bez serveru pomocí nástroje Maven. V dalším kroku informace o tom, jak vytvářet funkce Azure Java od začátku.

> [!div class="nextstepaction"]
> [Vytvoření první funkce pomocí Javy a Mavenu](../azure-functions/functions-create-first-java-maven.md)