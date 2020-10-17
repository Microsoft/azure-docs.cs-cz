---
title: Pomocí jazyka Java vytvořte chatovací místnost s Azure Functions a službou signalizace
description: Rychlý Start pro použití služby signalizace Azure a Azure Functions k vytvoření chatovací místnosti pomocí jazyka Java.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.custom: devx-track-java
ms.openlocfilehash: fac992ba0d6be945fcb3184d27394ff58d98c44c
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151020"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Rychlý Start: použití jazyka Java k vytvoření chatovací místnosti s Azure Functions a službou Signal

Služba signalizace Azure umožňuje snadno přidat do aplikace funkce v reálném čase a Azure Functions je platforma bez serveru, která umožňuje spuštění kódu bez nutnosti spravovat infrastrukturu. V tomto rychlém startu pomocí jazyka Java sestavíte aplikaci chatu bez serveru, která využívá službu a funkce signalizace v reálném čase.

## <a name="prerequisites"></a>Předpoklady

- Editor kódu, například [Visual Studio Code](https://code.visualstudio.com/)
- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing). Slouží ke spouštění aplikací Azure Functions místně.

   > [!NOTE]
   > Požadované vazby služby signalizace v jazyce Java se podporují jenom v nástrojích Azure Function Core verze 2.4.419 (hostitel verze 2.0.12332) nebo vyšší.

   > [!NOTE]
   > Pro instalaci rozšíření Azure Functions Core Tools vyžaduje, aby byl [.NET Core SDK](https://www.microsoft.com/net/download) nainstalovaný. K sestavování aplikací funkcí Azure v JavaScriptu však není potřeba žádná znalost architektury .NET.

- [Java Developer Kit](https://www.azul.com/downloads/zulu/) verze 8
- [Apache Maven](https://maven.apache.org) verze 3.0 nebo novější

> [!NOTE]
> Toto Rychlé zprovoznění je možné spustit v systémech macOS, Windows nebo Linux.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsjava)

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com/> pomocí svého účtu Azure.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsjava)

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsjava)

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsjava)

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurace a spuštění aplikace funkcí Azure

1. V prohlížeči, ve kterém máte otevřený Azure Portal, si ověřte, že se úspěšně vytvořila instance služby SignalR Service, kterou jste nasadili dříve. Vyhledejte její název pomocí vyhledávacího pole v horní části stránky portálu. Instanci vyberte a otevřete.

    ![Vyhledání instance služby SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Výběrem možnosti **Klíče** zobrazte připojovací řetězce instance služby SignalR.

1. Vyberte a zkopírujte primární připojovací řetězec.

    ![Vytvoření služby SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. V editoru kódu otevřete složku *Src/chat/Java* v klonovaném úložišti.

1. Přejmenujte soubor *local.settings.sample.json* na *local.settings.json*.

1. V souboru **local.settings.json** vložte připojovací řetězec do hodnoty nastavení **AzureSignalRConnectionString**. Soubor uložte.

1. Hlavní soubor obsahující funkce jsou v části *Src/chat/Java/src/Main/Java/com/Function/Functions. Java*:

    - **negotiate** – používá vstupní vazbu *SignalRConnectionInfo* ke generování a vracení informací o platném připojení.
    - **SendMessage** – přijme zprávu chatu v textu požadavku a použije výstupní vazbu *signálu* k vysílání zprávy všem připojeným klientským aplikacím.

1. V terminálu se ujistěte, že jste ve složce *Src/chat/Java* . Sestavte aplikaci Function App.

    ```bash
    mvn clean package
    ```

1. Spusťte aplikaci Function App lokálně.

    ```bash
    mvn azure-functions:run
    ```
[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsjava)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsjava)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsjava)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili a spustili aplikaci bez serveru v reálném čase s využitím Maven. V dalším kroku se dozvíte, jak vytvořit Azure Functions Java od začátku.

> [!div class="nextstepaction"]
> [Vytvoření první funkce pomocí Java a Maven](../azure-functions/functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java%252cprogramming-language-java)

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/asrs/qsjava)