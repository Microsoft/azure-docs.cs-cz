---
title: Vytvoření první funkce PowerShellu v Azure
description: Naučte se, jak vytvořit první funkci PowerShellu v Azure pomocí Visual Studio Code.
author: joeyaiello
ms.author: jaiello
ms.reviewer: glenga
ms.date: 04/25/2019
ms.topic: quickstart
ms.openlocfilehash: 8f77083ae0107ed9f9c5e3943cfcec25846ff087
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227203"
---
# <a name="create-your-first-powershell-function-in-azure"></a>Vytvoření první funkce PowerShellu v Azure

Tento článek rychlý Start vás provede procesem vytvoření první funkce prostředí PowerShell bez [serveru](https://azure.com/serverless) pomocí Visual Studio Code.

![Azure Functions kódu v projektu Visual Studio Code](./media/functions-create-first-function-powershell/powershell-project-first-function.png)

Pomocí [rozšíření Azure Functions pro Visual Studio Code] vytvoříte místní funkci PowerShellu a potom ji nasadíte do nové aplikace Function App v Azure. Rozšíření je v současné době ve verzi Preview. Další informace najdete na stránce rozšíření [Rozšíření Azure Functions pro Visual Studio Code].

> [!NOTE]  
> Podpora prostředí PowerShell pro rozšíření [Azure Functions rozšíření][rozšíření azure functions pro visual studio code] je aktuálně ve výchozím nastavení zakázaná. Povolení podpory PowerShellu je jedním z kroků v tomto článku.

V operačních systémech macOS, Windows a Linux jsou podporovány následující kroky.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

* Nainstalovat [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/) na jedné z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 

* Nainstalujte [rozšíření PowerShellu pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).

* Nainstalujte [.NET Core SDK 2.2 +](https://www.microsoft.com/net/download) (vyžaduje Azure Functions Core Tools a k dispozici na všech podporovaných platformách).

* Nainstalujte verzi 2. x z [Azure Functions Core Tools](functions-run-local.md#v2).

* Budete také potřebovat aktivní předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)] 

## <a name="create-a-function-app-project"></a>Vytvoření projektu aplikace funkcí

Šablona projektu Azure Functions ve Visual Studio Code vytvoří projekt, který jde publikovat do aplikace funkcí v Azure. Aplikace funkcí umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. 

1. Ve Visual Studio Code vyberte logo Azure, aby se zobrazila oblast **Azure: Functions**, pak vyberte ikonu Vytvořit nový projekt.

    ![Vytvoření projektu aplikace funkcí](./media/functions-create-first-function-powershell/create-function-app-project.png)

1. Zvolte umístění pro pracovní prostor projektu functions a zvolte **možnost vybrat**.

    > [!NOTE]
    > Tento článek je navržený k dokončení mimo pracovní prostor. V tomto případě nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Jako jazyk projektu Function App vyberte **PowerShell** a pak **Azure Functions v2**.

1. Jako šablonu pro první funkci vyberte **Trigger http** , jako název funkce použijte `HTTPTrigger` a vyberte úroveň autorizace **funkce**.

    > [!NOTE]
    > Úroveň autorizace **funkce** vyžaduje hodnotu [klíče funkce](functions-bindings-http-webhook.md#authorization-keys) při volání koncového bodu funkce v Azure. Díky tomu je to obtížnější pro volání vaší funkce pouze komukoli.

1. Po zobrazení výzvy zvolte **Přidat do pracovního prostoru**.

Visual Studio Code vytvoří projekt aplikace Functions PowerShellu v novém pracovním prostoru. Tento projekt obsahuje konfigurační soubory [Host. JSON](functions-host-json.md) a [Local. Settings. JSON](functions-run-local.md#local-settings-file) , které se vztahují na všechny funkce v projektu. Tento [projekt PowerShell](functions-reference-powershell.md#folder-structure) je stejný jako aplikace Function App běžící v Azure.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Azure Functions Core Tools se integruje s Visual Studio Code, aby se mohl spustit a ladit Azure Functions projektu místně.  

1. Chcete-li ladit funkci, vložte volání rutiny [`Wait-Debugger`] v kódu funkce předtím, než budete chtít připojit ladicí program, a stisknutím klávesy F5 spusťte projekt Function App a připojte ladicí program. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**.

1. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP.

    ![Místní výstup Azure](./media/functions-create-first-function-powershell/functions-vscode-f5.png)

1. Přidejte řetězec dotazu `?name=<yourname>` k této adrese URL a pak pomocí `Invoke-RestMethod` spusťte požadavek následujícím způsobem:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Požadavek GET můžete také spustit z prohlížeče.

    Když zavoláte koncový bod HttpTrigger bez předání parametru `name` buď jako parametr dotazu, nebo v těle, funkce vrátí chybu [HttpStatusCode]:: důvodu chybného požadavku. Když zkontrolujete kód v běhu. ps1, uvidíte, že k této chybě dochází podle návrhu.

1. Pokud chcete zastavit ladění, stiskněte Shift + F5.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

> [!NOTE]
> Před publikováním funkcí do Azure nezapomeňte odebrat všechna volání `Wait-Debugger`. 
>
> Vytvořením aplikace Function App v Azure se zobrazí výzva k zadání názvu aplikace Function App. Pro vás jsou definovány jiné hodnoty.
> Nastavte `azureFunctions.advancedCreation` na `true`, aby se zobrazila výzva pro všechny ostatní hodnoty.

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test"></a>Spuštění funkce v Azure

Pokud chcete ověřit, jestli je publikovaná funkce spuštěná v Azure, spusťte následující příkaz PowerShellu a nahraďte parametr `Uri` adresou URL funkce HTTPTrigger z předchozího kroku. Stejně jako dřív připojíte řetězec dotazu `&name=<yourname>` k adrese URL, jako v následujícím příkladu:

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
> [Povolení integrace Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure portal]: https://portal.azure.com
[Azure Functions Core Tools]: functions-run-local.md
[Rozšíření Azure Functions pro Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[' Wait-Debugger ']: /powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6
