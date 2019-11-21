---
title: Create your first PowerShell function in Azure
description: Learn how to create your first PowerShell function in Azure using Visual Studio Code.
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
# <a name="create-your-first-powershell-function-in-azure"></a>Create your first PowerShell function in Azure

This quickstart article walks you through how to create your first [serverless](https://azure.com/serverless) PowerShell function using Visual Studio Code.

![Azure Functions code in a Visual Studio Code project](./media/functions-create-first-function-powershell/powershell-project-first-function.png)

You use the [Rozšíření Azure Functions pro Visual Studio Code] to create a PowerShell function locally and then deployed it to a new function app in Azure. Rozšíření je v současné době ve verzi Preview. Další informace najdete na stránce rozšíření [Rozšíření Azure Functions pro Visual Studio Code].

> [!NOTE]  
> PowerShell support for the [Azure Functions extension][rozšíření azure functions pro visual studio code] is currently disabled by default. Enabling PowerShell support is one of the steps in this article.

The following steps are supported on macOS, Windows, and Linux-based operating systems.

## <a name="prerequisites"></a>Předpoklady

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* Install [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/) na jedné z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 

* Install [PowerShell extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).

* Install [.NET Core SDK 2.2+](https://www.microsoft.com/net/download) (required by Azure Functions Core Tools and available on all supported platforms).

* Install version 2.x of the [Azure Functions Core Tools](functions-run-local.md#v2).

* You also need an active Azure subscription.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)] 

## <a name="create-a-function-app-project"></a>Vytvoření projektu aplikace funkcí

Šablona projektu Azure Functions ve Visual Studio Code vytvoří projekt, který jde publikovat do aplikace funkcí v Azure. Aplikace funkcí umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. 

1. Ve Visual Studio Code vyberte logo Azure, aby se zobrazila oblast **Azure: Functions**, pak vyberte ikonu Vytvořit nový projekt.

    ![Vytvoření projektu aplikace funkcí](./media/functions-create-first-function-powershell/create-function-app-project.png)

1. Choose a location for your Functions project workspace and choose **Select**.

    > [!NOTE]
    > Tento článek je navržený k dokončení mimo pracovní prostor. V tomto případě nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Select the **Powershell** as the language for your function app project and then **Azure Functions v2**.

1. Choose **HTTP Trigger** as the template for your first function, use `HTTPTrigger` as the function name, and choose an authorization level of **Function**.

    > [!NOTE]
    > The **Function** authorization level requires a [function key](functions-bindings-http-webhook.md#authorization-keys) value when calling the function endpoint in Azure. This makes it harder for just anyone to call your function.

1. Po zobrazení výzvy zvolte **Přidat do pracovního prostoru**.

Visual Studio Code creates the PowerShell function app project in a new workspace. This project contains the [host.json](functions-host-json.md) and [local.settings.json](functions-run-local.md#local-settings-file) configuration files, which apply to all function in the project. This [PowerShell project](functions-reference-powershell.md#folder-structure) is the same as a function app running in Azure.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Azure Functions Core Tools integrates with Visual Studio Code to let you run and debug an Azure Functions project locally.  

1. To debug your function, insert a call to the [`Wait-Debugger`] cmdlet in the function code before you want to attach the debugger, then press F5 to start the function app project and attach the debugger. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**.

1. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP.

    ![Místní výstup Azure](./media/functions-create-first-function-powershell/functions-vscode-f5.png)

1. Append the query string `?name=<yourname>` to this URL, and then use `Invoke-RestMethod` to execute the request, as follows:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    You can also execute the GET request from a browser.

    When you call the HttpTrigger endpoint without passing a `name` parameter either as a query parameter or in the body, the function returns a [HttpStatusCode]::BadRequest error. When you review the code in run.ps1, you see that this error occurs by design.

1. Pokud chcete zastavit ladění, stiskněte Shift + F5.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

> [!NOTE]
> Remember to remove any calls to `Wait-Debugger` before you publish your functions to Azure. 
>
> Creating a function app in Azure only prompts for your function app name. Other values are defined for you.
> Set `azureFunctions.advancedCreation` to `true` to be prompted for all other values.

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test"></a>Run the function in Azure

To verify that your published function runs in Azure, execute the following PowerShell command, replacing the `Uri` parameter with the URL of the HTTPTrigger function from the previous step. As before, append the query string `&name=<yourname>` to the URL, as in the following example:

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

You have used Visual Studio Code to create a PowerShell function app with a simple HTTP-triggered function. You may also want to learn more about [debugging a PowerShell function locally](functions-debug-powershell-local.md) using the Azure Functions Core Tools. Check out the [Azure Functions PowerShell developer guide](functions-reference-powershell.md).

> [!div class="nextstepaction"]
> [Povolení integrace Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure portal]: https://portal.azure.com
[Azure Functions Core Tools]: functions-run-local.md
[Rozšíření Azure Functions pro Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[`Wait-Debugger`]: /powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6
