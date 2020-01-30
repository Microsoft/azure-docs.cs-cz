---
title: Připojení funkcí k Azure Storage pomocí Visual Studio Code
description: Naučte se, jak přidat výstupní vazbu pro připojení vašich funkcí k Azure Storage frontě pomocí Visual Studio Code.
ms.date: 06/25/2019
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 5b7d7be7854a216b7cb7b610ea6d51fdc496a93f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845654"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio-code"></a>Připojení funkcí k Azure Storage pomocí Visual Studio Code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

V tomto článku se dozvíte, jak pomocí Visual Studio Code připojit funkci, kterou jste vytvořili v [předchozím článku rychlý Start](functions-create-first-function-vs-code.md) pro Azure Storage. Výstupní vazba, kterou do této funkce přidáte, zapisuje data z požadavku HTTP do zprávy ve frontě úložiště Azure Queue. 

Většina vazeb vyžaduje uložený připojovací řetězec, který funkce používá pro přístup k vázané službě. Pro snazší použití účtu úložiště, který jste vytvořili v aplikaci Function App. Připojení k tomuto účtu je již Uloženo v nastavení aplikace s názvem `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Požadavky

Než začnete tento článek, musíte splnit následující požadavky:

* Nainstalujte [Azure Storage rozšíření pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).

* Nainstalujte [Průzkumník služby Azure Storage](https://storageexplorer.com/). Průzkumník služby Storage je nástroj, který budete používat k prohlédnutí zpráv ve frontě generovaných výstupní vazbou. Průzkumník služby Storage se podporují v operačních systémech macOS, Windows a Linux.

::: zone pivot="programming-language-csharp"
* Instalace [.NET Core CLIch nástrojů](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x).
::: zone-end

* Proveďte kroky v [části 1 rychlého startu Visual Studio Code](functions-create-first-function-vs-code.md). 

V tomto článku se předpokládá, že jste už přihlášení k předplatnému Azure z Visual Studio Code. Můžete se přihlásit spuštěním `Azure: Sign In` z palety příkazů. 

## <a name="download-the-function-app-settings"></a>Stažení nastavení Function App

V [předchozím článku rychlý Start](functions-create-first-function-vs-code.md)jste vytvořili aplikaci funkcí v Azure spolu s požadovaným účtem úložiště. Připojovací řetězec pro tento účet je bezpečně uložený v nastavení aplikace v Azure. V tomto článku napíšete zprávy do fronty úložiště ve stejném účtu. Pokud se chcete připojit k účtu úložiště, když se funkce spouští místně, musíte si stáhnout nastavení aplikace do souboru Local. Settings. JSON. 

1. Stisknutím klávesy F1 otevřete paletu příkazů a pak vyhledejte a spusťte příkaz `Azure Functions: Download Remote Settings....`. 

1. Vyberte aplikaci funkcí, kterou jste vytvořili v předchozím článku. Vyberte možnost **Ano pro vše** , pokud chcete přepsat stávající místní nastavení. 

    > [!IMPORTANT]  
    > Protože obsahuje tajné kódy, soubor Local. Settings. JSON se nikdy nepublikuje a je vyloučený ze správy zdrojového kódu.

1. Zkopírujte hodnotu `AzureWebJobsStorage`, což je klíč k hodnotě připojovacího řetězce účtu úložiště. Pomocí tohoto připojení ověříte, zda výstupní vazba funguje podle očekávání.

## <a name="register-binding-extensions"></a>Registrace rozšíření vazeb

Vzhledem k tomu, že používáte výstupní vazbu úložiště front, musíte mít nainstalované rozšíření úložiště vazeb před spuštěním projektu. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

S výjimkou aktivačních událostí protokolu HTTP a časovače jsou vazby implementovány jako balíčky rozšíření. Spusťte následující příkaz [dotnet přidat balíček](/dotnet/core/tools/dotnet-add-package) v okně terminálu a přidejte do projektu balíček rozšíření úložiště.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

::: zone-end

Nyní můžete přidat výstupní vazbu úložiště do projektu.

## <a name="add-an-output-binding"></a>Přidání výstupní vazby

V rámci funkcí vyžaduje každý typ vazby `direction`, `type`a jedinečné `name`, které mají být definovány v souboru Function. JSON. Způsob, jakým definujete tyto atributy, závisí na jazyku aplikace Function App.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Přidání kódu, který používá výstupní vazbu

Po definování vazby můžete použít `name` vazby k přístupu jako atributu v signatuře funkce. Pomocí výstupní vazby nemusíte pro ověřování používat kód Azure Storage SDK, získat odkaz na frontu nebo zapisovat data. Úlohy za běhu functions a Queue výstupní vazby jsou za vás.

::: zone pivot="programming-language-javascript"

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

::: zone-end

::: zone pivot="programming-language-typescript"

Přidejte kód, který používá objekt výstupní vazby `msg` v `context.bindings` k vytvoření zprávy fronty. Přidejte tento kód před příkaz `context.res`.

```typescript
// Add a message to the Storage queue.
context.bindings.msg = "Name passed to the function: " + name;
```

V tomto okamžiku by měla funkce vypadat takto:

```javascript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));

    if (name) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " + name; 
        // Send a "hello" response.
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};

export default httpTrigger;
```

::: zone-end

::: zone pivot="programming-language-powershell"

Přidejte kód, který používá rutinu `Push-OutputBinding` k zápisu textu do fronty pomocí výstupní vazby `msg`. Před nastavením stavu OK v příkazu `if` přidejte tento kód.

```powershell
# Write the $name value to the queue.
$outputMsg = "Name passed to the function: $name"
Push-OutputBinding -name msg -Value $outputMsg
```

V tomto okamžiku by měla funkce vypadat takto:

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

if ($name) {
    # Write the $name value to the queue.
    $outputMsg = "Name passed to the function: $name"
    Push-OutputBinding -name msg -Value $outputMsg

    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
else {
    $status = [HttpStatusCode]::BadRequest
    $body = "Please pass a name on the query string or in the request body."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

V účtu úložiště se vytvoří nová fronta s názvem **front** . modul runtime Functions při prvním použití výstupní vazby. Pomocí Průzkumník služby Storage ověříte, že se vytvořila fronta spolu s novou zprávou.

### <a name="connect-storage-explorer-to-your-account"></a>Propojení Průzkumníka služby Storage s vaším účtem

Pokud jste už Průzkumník služby Azure Storage nainstalovali a připojili ho k účtu Azure, přeskočte tuto část.

1. Spusťte nástroj [Azure Storage Explorer] , vyberte ikonu připojit na levé straně a vyberte **Přidat účet**.

    ![Přidat účet Azure do Průzkumník služby Microsoft Azure Storage](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. V dialogovém okně **připojit** zvolte **Přidat účet Azure**, zvolte **prostředí Azure**a pak vyberte **Přihlásit se...** . 

    ![Přihlášení k účtu Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Po úspěšném přihlášení ke svému účtu uvidíte všechna předplatná Azure přidružená k vašemu účtu.

### <a name="examine-the-output-queue"></a>Prozkoumání výstupní fronty

1. V Visual Studio Code stisknutím klávesy F1 otevřete paletu příkazů, potom vyhledejte a spusťte příkaz `Azure Storage: Open in Storage Explorer` a zvolte název svého účtu úložiště. Váš účet úložiště se otevře v Průzkumník služby Azure Storage.  

1. Rozbalte uzel **Fronty** a potom vyberte frontu s názvem **outqueue**. 

   Tato fronta obsahuje zprávu, kterou vytvořila výstupní vazba fronty při spuštění funkce aktivované protokolem HTTP. Pokud jste tuto funkci volali s výchozí hodnotou `name` (*Azure*), zpráva fronty je *Name passed to the function: Azure*.

    ![Zpráva fronty zobrazená v Průzkumník služby Azure Storage](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Znovu spusťte funkci, odešlete další žádost a ve frontě se zobrazí nová zpráva.  

Teď je čas na opětovné publikování aktualizované aplikace Function App do Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Opětovné nasazení a ověření aktualizované aplikace

1. V Visual Studio Code stisknutím klávesy F1 otevřete paletu příkazů. V paletě příkazů vyhledejte a vyberte `Azure Functions: Deploy to function app...`.

1. Vyberte aplikaci funkcí, kterou jste vytvořili v prvním článku. Vzhledem k tomu, že projekt znovu nasazujete do stejné aplikace, vyberte **nasadit** a zastavte tak upozornění týkající se přepsání souborů.

1. Po dokončení nasazení můžete znovu použít kudrlinkou nebo prohlížeč k otestování znovu nasazené funkce. Stejně jako dřív připojíte řetězec dotazu `&name=<yourname>` k adrese URL, jako v následujícím příkladu:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Opětovným [zobrazením zprávy ve frontě úložiště](#examine-the-output-queue) ověřte, zda výstupní vazba znovu generuje novou zprávu ve frontě.

## <a name="clean-up-resources"></a>Vyčištění prostředků

*Prostředky* v Azure odkazují na aplikace funkcí, funkce, účty úložiště atd. Jsou seskupené do *skupin prostředků*. Odstraněním skupiny odstraníte všechno, co v této skupině je.

Vytvořili jste prostředky k dokončení těchto rychlých startů. Tyto prostředky se vám mohou fakturovat, a to v závislosti na [stavu účtu](https://azure.microsoft.com/account/) a [cenách služeb](https://azure.microsoft.com/pricing/). Pokud prostředky už nepotřebujete, můžete k jejich odstranění použít tento postup:

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Další kroky

Aktualizovali jste funkci aktivovanou protokolem HTTP, která zapisuje data do fronty úložiště. Dále si můžete přečíst další informace o vývoji funkcí pomocí Visual Studio Code:

> [!div class="nextstepaction"]
> [Vývoj Azure Functions pomocí Visual Studio Code](functions-develop-vs-code.md)

[Azure Storage Explorer]: https://storageexplorer.com/
