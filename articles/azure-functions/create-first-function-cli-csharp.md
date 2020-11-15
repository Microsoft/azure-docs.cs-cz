---
title: Vytvoření funkce jazyka C# z příkazového řádku – Azure Functions
description: Naučte se vytvořit funkci jazyka C# z příkazového řádku a pak publikovat místní projekt na hostování bez serveru v Azure Functions.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
ms.openlocfilehash: b87408669dbb9268d6e3cd47b410b888ef1a19c2
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637145"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Rychlý Start: vytvoření funkce jazyka C# v Azure z příkazového řádku

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

V tomto článku pomocí nástrojů příkazového řádku vytvoříte funkci založenou na knihovně jazyka C#, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do prostředí Azure Functions bez serveru.

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

K dispozici je také [Visual Studio Codeá verze](create-first-function-vs-code-csharp.md) tohoto článku.

## <a name="configure-your-local-environment"></a>Konfigurace místního prostředí

Než začnete, musíte mít následující:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [.NET Core SDK 3,1](https://www.microsoft.com/net/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) verze 3. x.

+ Jeden z následujících nástrojů pro vytváření prostředků Azure:

    + [Azure CLI](/cli/azure/install-azure-cli) verze 2,4 nebo novější.

    + [Azure PowerShell](/powershell/azure/install-az-ps) verze 5,0 nebo novější.

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

Ověřte požadavky, které závisí na tom, jestli pro vytváření prostředků Azure používáte Azure CLI nebo Azure PowerShell:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ V terminálu nebo příkazovém okně spusťte příkaz `func --version` a ověřte, zda Azure Functions Core Tools mají verzi 3. x.

+ Spusťte `az --version` a ověřte, jestli je verze Azure CLI 2,4 nebo novější.

+ Spusťte `az login` , abyste se přihlásili do Azure a ověřili aktivní předplatné.

+ Spusťte `dotnet --list-sdks` , pokud chcete ověřit, že je nainstalovaná .NET Core SDK verze 3.1. x.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ V terminálu nebo příkazovém okně spusťte příkaz `func --version` a ověřte, zda Azure Functions Core Tools mají verzi 3. x.

+ Spusťte `(Get-Module -ListAvailable Az).Version` a ověřte verze 5,0 nebo novější. 

+ Spusťte `Connect-AzAccount` , abyste se přihlásili do Azure a ověřili aktivní předplatné.

+ Spusťte `dotnet --list-sdks` , pokud chcete ověřit, že je nainstalovaná .NET Core SDK verze 3.1. x.

---

## <a name="create-a-local-function-project"></a>Vytvoření projektu místní funkce

V Azure Functions je projekt funkce kontejnerem pro jednu nebo více jednotlivých funkcí, které každý reaguje na konkrétní Trigger. Všechny funkce v projektu sdílejí stejné místní konfigurace a konfigurace hostování. V této části vytvoříte projekt funkce, který obsahuje jedinou funkci.

1. Spusťte `func init` příkaz následujícím způsobem a vytvořte projekt Functions ve složce s názvem *LocalFunctionProj* se zadaným modulem runtime:  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. Přejděte do složky projektu:

    ```console
    cd LocalFunctionProj
    ```

    Tato složka obsahuje různé soubory pro projekt, včetně konfiguračních souborů s názvem [local.settings.jsv](functions-run-local.md#local-settings-file) a [host.jsna](functions-host-json.md). Vzhledem k tomu, že *local.settings.json* může obsahovat tajné kódy stažené z Azure, soubor je ve výchozím nastavení vyloučený ze správy zdrojového kódu v souboru *. gitignore* .

1. Do projektu přidejte funkci pomocí následujícího příkazu, kde `--name` argument je jedinečný název vaší funkce (HttpExample) a `--template` argument určuje aktivační událost funkce (http).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` Vytvoří soubor kódu HttpExample.cs.

### <a name="optional-examine-the-file-contents"></a>Volitelné Kontrola obsahu souboru

V případě potřeby můžete přeskočit, aby se [funkce spouštěla místně](#run-the-function-locally) , a prohlédnout si obsah souboru později.

#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* obsahuje `Run` metodu, která přijímá data požadavku v `req` proměnné, je [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) , který je upraven pomocí **HttpTriggerAttribute** , který definuje chování triggeru.

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Návratový objekt je [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) , který vrací zprávu odpovědi buď jako [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200), nebo jako [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Další informace najdete v tématu [Azure Functions triggerů http a vazeb](./functions-bindings-http-webhook.md?tabs=csharp).

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Vytvoření aplikace Function App v Azure:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Příkazem [AZ functionapp Create](/cli/azure/functionapp#az_functionapp_create) se vytvoří aplikace Function App v Azure. 
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    Rutina [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) vytvoří aplikaci Function App v Azure. 
    
    ---
    
    V předchozím příkladu nahraďte `<STORAGE_NAME>` názvem účtu, který jste použili v předchozím kroku, a nahraďte `<APP_NAME>` globálně jedinečným názvem vhodným pro vás. `<APP_NAME>` je také výchozí doména DNS pro aplikaci funkcí. 
    
    Tento příkaz vytvoří aplikaci Function App spuštěnou v zadaném jazykovém modulu runtime v rámci [plánu Azure Functions spotřeby](functions-scale.md#consumption-plan), který je zdarma pro množství využití, které se vám bude účtovat. Příkaz taky zřídí přidruženou instanci služby Azure Application Insights ve stejné skupině prostředků, pomocí které můžete monitorovat aplikaci Function App a zobrazovat protokoly. Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md). Instance nenese žádné náklady, dokud ji neaktivujete.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage]

[Připojení k frontě Azure Storage]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp
