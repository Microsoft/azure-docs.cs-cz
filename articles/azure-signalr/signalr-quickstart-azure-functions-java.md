---
title: Vytvoření chatovací místnosti pomocí služby Azure Functions and SignalR Service pomocí Javy
description: Rychlé zprovoznění popisující vytvoření chatovací místnosti pomocí služby Azure SignalR Service a Azure Functions.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: 890fc381afe0146e721e084e2dcd7eae9215d004
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77083200"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Úvodní příručka: Pomocí Javy můžete vytvořit chatovací místnost se službou Azure Functions and SignalR Service

Služba Azure SignalR service umožňuje snadno přidávat funkce v reálném čase do vaší aplikace a Azure Functions je platforma bez serveru, která vám umožní spouštět kód bez správy jakékoli infrastruktury. V tomto rychlém startu použijete jazyk Java k vytvoření chatovací aplikace bez serveru v reálném čase pomocí služby SignalR service a funkcí.

## <a name="prerequisites"></a>Požadavky

- Editor kódu, například [Visual Studio Code](https://code.visualstudio.com/)
- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Základní nástroje Azure Functions](https://github.com/Azure/azure-functions-core-tools#installing). Používá se ke spuštění aplikací Azure Function místně.

   > [!NOTE]
   > Požadované vazby služby SignalR v jazyce Java jsou podporovány jenom ve verzi Azure Function Core Tools verze 2.4.419 (hostitelská verze 2.0.12332) nebo vyšší.

   > [!NOTE]
   > Chcete-li nainstalovat rozšíření, nástroje Azure Functions Core Tools vyžaduje nainstalovanou sadu [.NET Core SDK.](https://www.microsoft.com/net/download) K sestavování aplikací funkcí Azure v JavaScriptu však není potřeba žádná znalost architektury .NET.

- [Java Developer Kit](https://www.azul.com/downloads/zulu/) verze 8
- [Apache Maven](https://maven.apache.org) verze 3.0 nebo novější

> [!NOTE]
> Toto Rychlé zprovoznění je možné spustit v systémech macOS, Windows nebo Linux.

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

1. V editoru kódu otevřete složku *src/chat/java* v naktoklá úložišti.

1. Přejmenujte soubor *local.settings.sample.json* na *local.settings.json*.

1. V souboru **local.settings.json** vložte připojovací řetězec do hodnoty nastavení **AzureSignalRConnectionString**. Uložte soubor.

1. Hlavní soubor, který obsahuje funkce jsou v *src/chat/java/src/main/java/com/function/Functions.java*:

    - **negotiate** – používá vstupní vazbu *SignalRConnectionInfo* ke generování a vracení informací o platném připojení.
    - **sendMessage** - Přijme zprávu chatu v textu požadavku a používá výstup *SignalR* vazbu vysílat zprávu do všech připojených klientských aplikací.

1. V terminálu se ujistěte, že jste ve složce *src/chat/java.* Vytvořte aplikaci funkcí.

    ```bash
    mvn clean package
    ```

1. Spusťte aplikaci funkce místně.

    ```bash
    mvn azure-functions:run
    ```

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili a spustili aplikaci bez serveru v reálném čase pomocí aplikace Maven. Další informace o tom, jak vytvořit funkce Java Azure od začátku.

> [!div class="nextstepaction"]
> [Vytvořte si první funkci s Javou a Maven](../azure-functions/functions-create-first-java-maven.md)