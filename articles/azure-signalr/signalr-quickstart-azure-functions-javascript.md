---
title: Vytvoření chatovací místnosti s Azure Functions a službou Signal pomocí JavaScriptu
description: Rychlý Start pro použití služby signalizace Azure a Azure Functions k vytvoření chatovací místnosti pomocí JavaScriptu.
author: sffamily
ms.service: signalr
ms.devlang: javascript
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: zhshang
ms.custom: devx-track-js
ms.openlocfilehash: 061dce01d2437d04d371ac65c115a1d95136fb5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94874691"
---
# <a name="quickstart-use-javascript-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Rychlý Start: použití JavaScriptu k vytvoření chatovací místnosti s Azure Functions a službou signalizace

Služba signalizace Azure umožňuje snadno přidat do aplikace funkce v reálném čase a Azure Functions je platforma bez serveru, která umožňuje spuštění kódu bez nutnosti spravovat infrastrukturu. V tomto rychlém startu pomocí JavaScriptu sestavíte aplikaci Chat bez serveru, která používá službu a funkce signalizace v reálném čase.

## <a name="prerequisites"></a>Předpoklady

- Editor kódu, například [Visual Studio Code](https://code.visualstudio.com/)
- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing), verze 2 nebo novější. Slouží ke spouštění aplikací Azure Functions místně.
- [Node.js](https://nodejs.org/en/download/), verze 10. x

   > [!NOTE]
   > Příklady by měly fungovat s jinými verzemi Node.js, další informace najdete v [dokumentaci k verzím modulu runtime Azure Functions](../azure-functions/functions-versions.md#languages) .

> [!NOTE]
> Toto Rychlé zprovoznění je možné spustit v systémech macOS, Windows nebo Linux.

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsjs)prosím o tom.

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com/> pomocí svého účtu Azure.

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsjs)prosím o tom.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsjs)prosím o tom.

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsjs)prosím o tom.

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurace a spuštění aplikace funkcí Azure

1. V prohlížeči, ve kterém máte otevřený Azure Portal, si ověřte, že se úspěšně vytvořila instance služby SignalR Service, kterou jste nasadili dříve. Vyhledejte její název pomocí vyhledávacího pole v horní části stránky portálu. Instanci vyberte a otevřete.

    ![Vyhledání instance služby SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Výběrem možnosti **Klíče** zobrazte připojovací řetězce instance služby SignalR.

1. Vyberte a zkopírujte primární připojovací řetězec.

    ![Snímek obrazovky, který zvýrazní primární připojovací řetězec.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. V editoru kódu otevřete složku *Src/chat/JavaScript* v klonovaném úložišti.

1. Přejmenujte soubor *local.settings.sample.json* na *local.settings.json*.

1. V souboru **local.settings.json** vložte připojovací řetězec do hodnoty nastavení **AzureSignalRConnectionString**. Soubor uložte.

1. Funkce JavaScriptu jsou uspořádané do složek. V každé složce jsou dva soubory. Soubor *function.json* definuje vazby, které se používají ve funkci, a soubor *index.js* je tělo funkce. Tato aplikace funkcí zahrnuje dvě funkce aktivované protokolem HTTP:

    - **negotiate** – používá vstupní vazbu *SignalRConnectionInfo* ke generování a vracení informací o platném připojení.
    - **messages** – přijímá zprávy chatu v textu požadavku a používá výstupní vazbu *SignalR* k vysílání zpráv všem připojeným klientským aplikacím.

1. V terminálu se ujistěte, že jste ve složce *Src/chat/JavaScript* . Aplikace funkcí spusťte.

    ```bash
    func start
    ```

    ![Vytvoření služby SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)
    
Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsjs)prosím o tom.

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsjs)prosím o tom.

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsjs)prosím o tom.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste v VS Code vytvořili a spustili aplikaci bez serveru v reálném čase. Dále můžete získat více informací o postupu nasazení Azure Functions přes VS Code.

> [!div class="nextstepaction"]
> [Nasazení Azure Functions přes VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01)

