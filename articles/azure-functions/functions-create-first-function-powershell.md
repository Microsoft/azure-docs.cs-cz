---
title: Vytvoření první funkce PowerShellu pomocí Azure Functions
description: Naučte se, jak vytvořit první funkci PowerShellu v Azure pomocí Visual Studio Code.
services: functions
keywords: ''
author: joeyaiello
manager: jeconnoc
ms.author: jaiello
ms.reviewer: glenga
ms.date: 04/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.devlang: powershell
ms.openlocfilehash: c9de4cec417625bb8451457652dacb61550c31b0
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248338"
---
# <a name="create-your-first-powershell-function-in-azure-preview"></a>Vytvoření první funkce PowerShellu v Azure (Preview)

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Tento článek rychlý Start vás provede procesem vytvoření první funkce prostředí PowerShell bez [serveru](https://azure.com/serverless) pomocí Visual Studio Code.

![Azure Functions kódu v projektu Visual Studio Code](./media/functions-create-first-function-powershell/powershell-project-first-function.png)

Pomocí [Azure Functions rozšíření pro Visual Studio Code] vytvoříte místní funkci PowerShellu a potom ji nasadíte do nové aplikace Function App v Azure. Rozšíření je aktuálně ve verzi Preview. Další informace najdete na stránce rozšíření [Azure Functions rozšíření pro Visual Studio Code] .

> [!NOTE]  
> Podpora prostředí PowerShell pro rozšíření [Azure Functions rozšíření][azure functions rozšíření pro visual studio code] je aktuálně ve výchozím nastavení zakázaná. Povolení podpory PowerShellu je jedním z kroků v tomto článku.

V operačních systémech macOS, Windows a Linux jsou podporovány následující kroky.

## <a name="prerequisites"></a>Požadované součásti

K dokončení tohoto rychlého startu:

* Nainstalovat [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/) na jednu z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 

* Nainstalujte [rozšíření PowerShellu pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).

* Nainstalujte [.NET Core SDK 2.2 +](https://www.microsoft.com/net/download) (vyžaduje Azure Functions Core Tools a k dispozici na všech podporovaných platformách).

* Nainstalujte verzi 2. x z [Azure Functions Core Tools](functions-run-local.md#v2).

* Budete také potřebovat aktivní předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)] 

## <a name="create-a-function-app-project"></a>Vytvoření projektu Function App

Šablona projektu Azure Functions v Visual Studio Code vytvoří projekt, který lze publikovat do aplikace Function App v Azure. Aplikace Function App umožňuje seskupit funkce jako logickou jednotku pro správu, nasazování a sdílení prostředků. 

1. V Visual Studio Code vyberte logo Azure, které se zobrazí v oblasti **Azure: Functions** , a pak vyberte ikonu Vytvořit nový projekt.

    ![Vytvoření projektu Function App](./media/functions-create-first-function-powershell/create-function-app-project.png)

1. Zvolte umístění pro pracovní prostor projektu functions a zvolte **možnost vybrat**.

    > [!NOTE]
    > Tento článek je navržený tak, aby se dokončil mimo pracovní prostor. V takovém případě nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Jako jazyk projektu Function App vyberte **PowerShell (Preview)** a pak **Azure Functions v2**.

1. Jako šablonu pro první funkci vyberte **Trigger http** , jako název funkce použijte `HTTPTrigger` a vyberte úroveň autorizace **funkce**.

    > [!NOTE]
    > Úroveň autorizace **funkce** vyžaduje hodnotu [klíče funkce](functions-bindings-http-webhook.md#authorization-keys) při volání koncového bodu funkce v Azure. Díky tomu je to obtížnější pro volání vaší funkce pouze komukoli.

1. Po zobrazení výzvy vyberte **Přidat do pracovního prostoru**.

Visual Studio Code vytvoří projekt aplikace Functions PowerShellu v novém pracovním prostoru. Tento projekt obsahuje konfigurační soubory [Host. JSON](functions-host-json.md) a [Local. Settings. JSON](functions-run-local.md#local-settings-file) , které se vztahují na všechny funkce v projektu. Tento [projekt PowerShell](functions-reference-powershell.md#folder-structure) je stejný jako aplikace Function App běžící v Azure.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Azure Functions Core Tools se integruje s Visual Studio Code, aby se mohl spustit a ladit Azure Functions projektu místně.  

1. Chcete-li ladit funkci, vložte volání do rutiny [`Wait-Debugger`] v kódu funkce před tím, než budete chtít připojit ladicí program, a stisknutím klávesy F5 spusťte projekt Function App a připojte ladicí program. Výstup z klíčových nástrojů se zobrazí na panelu **terminálu** .

1. Na panelu **terminálu** zkopírujte koncový bod adresy URL funkce aktivované protokolem HTTP.

    ![Místní výstup Azure](./media/functions-create-first-function-powershell/functions-vscode-f5.png)

1. Přidejte řetězec dotazu `?name=<yourname>` k této adrese URL a pak použijte `Invoke-RestMethod` k provedení žádosti následujícím způsobem:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Požadavek GET můžete také spustit z prohlížeče.

    Když zavoláte koncový bod HttpTrigger bez předání parametru `name` buď jako parametr dotazu, nebo v těle, funkce vrátí chybu [HttpStatusCode]:: důvodu chybného požadavku. Když zkontrolujete kód v běhu. ps1, uvidíte, že k této chybě dochází podle návrhu.

1. Chcete-li zastavit ladění, stiskněte klávesy Shift + F5.

Po ověření, že se funkce na místním počítači spustí správně, je čas publikovat projekt do Azure.

> [!NOTE]
> Před publikováním funkcí do Azure nezapomeňte odebrat všechna volání `Wait-Debugger`. 

> [!NOTE]
> Vytvořením Function App v Azure se zobrazí výzva k Function Appmu názvu. Nastavte azureFunctions. advancedCreation na true, aby se zobrazila výzva pro všechny ostatní hodnoty.

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test"></a>Spuštění funkce v Azure

Pokud chcete ověřit, jestli je publikovaná funkce spuštěná v Azure, spusťte následující příkaz PowerShellu a nahraďte parametr `Uri` adresou URL funkce HTTPTrigger z předchozího kroku. Stejně jako dřív připojíte řetězec dotazu `&name=<yourname>` k adrese URL, jak je uvedeno v následujícím příkladu:

```powershell
PS > Invoke-WebRequest -Method Get -Uri "https://glengatest-vscode-powershell.azurewebsites.net/api/HttpTrigger?code=nrY05eZutfPqLo0som...&name=PowerShell"

StatusCode        : 200
StatusDescription : OK
Content           : Hello PowerShell
RawContent        : HTTP/1.1 200 OK
                    Content-Length: 16
                    Content-Type: text/plain; charset=utf-8
                    Date: Thu, 25 Apr 2019 16:01:22 GMT

                    Hello PowerShell
Forms             : {}
Headers           : {[Content-Length, 16], [Content-Type, text/plain; charset=utf-8], [Date, Thu, 25 Apr 2019 16:01:22 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 16
```

## <a name="next-steps"></a>Další kroky

Použili jste Visual Studio Code k vytvoření aplikace funkce PowerShellu pomocí jednoduché funkce aktivované protokolem HTTP. Další informace o [ladění funkce PowerShellu](functions-debug-powershell-local.md) můžete také získat místně pomocí Azure Functions Core Tools. Projděte si [příručku pro vývojáře Azure Functions PowerShellu](functions-reference-powershell.md).

> [!div class="nextstepaction"]
> [Povolit integraci Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure portal]: https://portal.azure.com
[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions rozšíření pro Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[' Wait-Debugger ']: /powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6
