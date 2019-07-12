---
title: Vytvoření první funkce Powershellu s využitím Azure Functions
description: Zjistěte, jak vytvořit první funkci prostředí PowerShell v Azure pomocí Visual Studio Code.
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
ms.openlocfilehash: cb175191cb080cffb6feb52b724c29568fd549a5
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706518"
---
# <a name="create-your-first-powershell-function-in-azure-preview"></a>Vytvoření první funkce prostředí PowerShell v Azure (preview)

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Tento článek Rychlý start vás provede procesem vytvoření vaší první [bez serveru](https://azure.com/serverless) funkce prostředí PowerShell pomocí Visual Studio Code.

![Kód služby Azure Functions v projektu Visual Studio Code](./media/functions-create-first-function-powershell/powershell-project-first-function.png)

Můžete použít [Rozšíření Azure Functions pro Visual Studio Code] vytvořit funkci Powershellu místně a nasadili ji do nové aplikace function app v Azure. Rozšíření je v současné době ve verzi Preview. Další informace najdete na stránce rozšíření [Rozšíření Azure Functions pro Visual Studio Code].

> [!NOTE]  
> Podpora Powershellu [rozšíření Azure Functions][rozšíření azure functions pro visual studio code] je ve výchozím nastavení zakázána. Povolení podpory Powershellu je jedním z kroků v tomto článku.

Následující kroky jsou podporovány v systémech macOS, Windows a operační systémy založené na Linuxu.

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* Nainstalujte [Powershellu Core](/powershell/scripting/install/installing-powershell-core-on-windows)

* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/) na jedné z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 

* Nainstalujte [rozšíření prostředí PowerShell pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).

* Nainstalujte [.NET Core SDK 2.2 a](https://www.microsoft.com/net/download) (pomocí nástrojů Azure Functions Core povinné a k dispozici na všech podporovaných platformách).

* Nainstalovat verzi 2.x [nástrojů Azure Functions Core](functions-run-local.md#v2).

* Budete potřebovat aktivní předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)] 

## <a name="create-a-function-app-project"></a>Vytvoření projektu aplikace funkcí

Šablona projektu Azure Functions ve Visual Studio Code vytvoří projekt, který jde publikovat do aplikace funkcí v Azure. Aplikace funkcí umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. 

1. Ve Visual Studio Code, vyberte logo Azure zobrazíte **Azure: Funkce** oblast a vyberte ikonu Vytvořit nový projekt.

    ![Vytvoření projektu aplikace funkcí](./media/functions-create-first-function-powershell/create-function-app-project.png)

1. Vyberte umístění pro váš pracovní prostor funkce projekt a zvolte **vyberte**.

    > [!NOTE]
    > Tento článek je navržený k dokončení mimo pracovní prostor. V tomto případě nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Vyberte **Powershellu (preview)** jako jazyk pro váš projekt aplikace funkcí a potom **Azure Functions v2**.

1. Zvolte **triggeru HTTP** jako šablona pro první funkce pomocí `HTTPTrigger` jako funkce pojmenujte a zvolte úroveň autorizace **funkce**.

    > [!NOTE]
    > **Funkce** vyžaduje úroveň autorizace [klíč funkce](functions-bindings-http-webhook.md#authorization-keys) hodnotu při volání koncového bodu funkce v Azure. To ztěžuje kdokoli volat vaši funkci.

1. Po zobrazení výzvy zvolte **Přidat do pracovního prostoru**.

Visual Studio Code vytvoří projekt aplikace funkcí Powershellu v nový pracovní prostor. Tento projekt obsahuje [host.json](functions-host-json.md) a [local.settings.json](functions-run-local.md#local-settings-file) konfigurační soubory, které platí pro všechny funkce v projektu. To [Powershellu projektu](functions-reference-powershell.md#folder-structure) je stejný jako aplikaci funkcí spuštěnou v Azure.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Nástroje Azure Functions Core se integruje se sadou Visual Studio Code vám umožní spouštět a ladit projekt Azure Functions místně.  

1. Chcete-li ladit vaši funkci, vložte volání [ `Wait-Debugger` ] rutiny v kódu funkce předtím, než se chcete připojit ladicí program, stiskněte klávesu F5 ke spuštění projektu aplikace funkcí a připojit ladicí program. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**.

1. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP.

    ![Místní výstup Azure](./media/functions-create-first-function-powershell/functions-vscode-f5.png)

1. Připojte řetězec dotazu `?name=<yourname>` na tuto adresu URL a pak použijte `Invoke-RestMethod` požadavek, proveďte takto:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Požadavek na získání můžete také spustit v prohlížeči.

    Při volání koncového bodu HttpTrigger bez předávání `name` parametrů jako parametr dotazu nebo v textu, funkce vrátí chybu 500. Při kontrole kódu v run.ps1 uvidíte, že k této chybě dochází záměrně.

1. Pokud chcete zastavit ladění, stiskněte Shift + F5.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

> [!NOTE]
> Nezapomeňte odebrat všechna volání do `Wait-Debugger` před publikováním vašich funkcí do Azure. 

> [!NOTE]
> Vytvoření aplikace Function App v Azure vyzve názvem aplikace Function App. AzureFunctions.advancedCreation nastavena na true, pokud chcete být vyzváni k zadání všech ostatních hodnot.

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test"></a>Spuštění funkce v Azure

Pokud chcete ověřit, že publikované funkce běží v Azure, spusťte následující příkaz prostředí PowerShell, nahrazení `Uri` parametr s adresou URL HTTPTrigger funkce z předchozího kroku. Stejně jako předtím, připojte řetězec dotazu `&name=<yourname>` na adresu URL, jako v následujícím příkladu:

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

## <a name="next-steps"></a>Další postup

Používáte Visual Studio Code k vytvoření aplikace funkcí Powershellu s jednoduchou funkcí aktivovanou protokolem HTTP. Můžete také zobrazit další informace o [ladění funkce Powershellu místně](functions-debug-powershell-local.md) pomocí nástrojů Azure Functions Core. Podívejte se [– Příručka pro vývojáře Azure Functions Powershellu](functions-reference-powershell.md).

> [!div class="nextstepaction"]
> [Povolit integraci modulu Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure portal]: https://portal.azure.com
[Azure Functions Core Tools]: functions-run-local.md
[Rozšíření Azure Functions pro Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[`Wait-Debugger`]: /powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6
