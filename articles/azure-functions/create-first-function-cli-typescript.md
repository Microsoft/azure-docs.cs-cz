---
title: Vytvoření funkce TypeScript z příkazového řádku – Azure Functions
description: Naučte se vytvořit funkci TypeScript z příkazového řádku a pak publikovat místní projekt na hostování bez serveru v Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 488ef9fa3fd5b6c09ed435483dbf8f6fa3eb5bef
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937191"
---
# <a name="quickstart-create-a-typescript-function-in-azure-from-the-command-line"></a>Rychlý Start: vytvoření funkce TypeScript v Azure z příkazového řádku

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

V tomto článku vytvoříte pomocí nástrojů příkazového řádku funkci TypeScript, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do prostředí Azure Functions bez serveru.

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

K dispozici je také [Visual Studio Codeá verze](create-first-function-vs-code-typescript.md) tohoto článku.

## <a name="configure-your-local-environment"></a>Konfigurace místního prostředí

Než začnete, musíte mít následující:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) verze 3. x.

+ Jeden z následujících nástrojů pro vytváření prostředků Azure:

    + [Azure CLI](/cli/azure/install-azure-cli) verze 2,4 nebo novější.

    + [Azure PowerShell](/powershell/azure/install-az-ps) verze 5,0 nebo novější.

+ [Node.js](https://nodejs.org/), aktivní verze LTS pro LTS a údržbu (Doporučené 8.11.1 a 10.14.1).

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

Ověřte požadavky, které závisí na tom, jestli pro vytváření prostředků Azure používáte Azure CLI nebo Azure PowerShell:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ V terminálu nebo příkazovém okně spusťte příkaz `func --version` a ověřte, zda Azure Functions Core Tools mají verzi 3. x.

+ Spusťte `az --version` a ověřte, jestli je verze Azure CLI 2,4 nebo novější.

+ Spusťte `az login` , abyste se přihlásili do Azure a ověřili aktivní předplatné.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ V terminálu nebo příkazovém okně spusťte příkaz `func --version` a ověřte, zda Azure Functions Core Tools mají verzi 3. x.

+ Spusťte `(Get-Module -ListAvailable Az).Version` a ověřte verze 5,0 nebo novější. 

+ Spusťte `Connect-AzAccount` , abyste se přihlásili do Azure a ověřili aktivní předplatné.

---

## <a name="create-a-local-function-project"></a>Vytvoření projektu místní funkce

V Azure Functions je projekt funkce kontejnerem pro jednu nebo více jednotlivých funkcí, které každý reaguje na konkrétní Trigger. Všechny funkce v projektu sdílejí stejné místní konfigurace a konfigurace hostování. V této části vytvoříte projekt funkce, který obsahuje jedinou funkci.

1. Spusťte `func init` příkaz následujícím způsobem a vytvořte projekt Functions ve složce s názvem *LocalFunctionProj* se zadaným modulem runtime:  

    ```console
    func init LocalFunctionProj --typescript
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
    
    `func new` vytvoří podsložku, která odpovídá názvu funkce, který obsahuje soubor kódu, který je vhodný pro zvolený jazyk projektu, a konfiguračního souboru s názvem *function.js*.

### <a name="optional-examine-the-file-contents"></a>Volitelné Kontrola obsahu souboru

V případě potřeby můžete přeskočit, aby se [funkce spouštěla místně](#run-the-function-locally) , a prohlédnout si obsah souboru později.

#### <a name="indexts"></a>index. TS

*index. TS* exportuje funkci, která se aktivuje podle konfigurace v *function.js*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

U triggeru HTTP funkce přijímá data žádosti v proměnné `req` typu **HttpRequest** , jak je definováno v *function.js*. Návratový objekt definovaný jako `$return` in *function.json*, je odpověď. 

#### <a name="functionjson"></a>function.json

*function.jsv* je konfigurační soubor, který definuje vstup a výstup `bindings` funkce, včetně typu triggeru. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::

Každá vazba vyžaduje směr, typ a jedinečný název. Aktivační událost protokolu HTTP má vstupní vazbu typu [`httpTrigger`](functions-bindings-http-webhook-trigger.md) a výstupní vazby typu [`http`](functions-bindings-http-webhook-output.md) .

## <a name="run-the-function-locally"></a>Místní spuštění funkce

1. Spusťte funkci spuštěním místního hostitele modulu runtime Azure Functions ze složky *LocalFunctionProj* :

    ```console
    npm install
    npm start
    ```
    
    Ke konci výstupu by se měly zobrazit následující řádky:
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Pokud se HttpExample nezobrazí, jak je uvedeno níže, pravděpodobně jste spustili hostitele mimo kořenovou složku projektu. V takovém případě pomocí **kombinace kláves CTRL +** + **C** zastavte hostitele, přejděte do kořenové složky projektu a spusťte předchozí příkaz znovu.

1. Zkopírujte adresu URL vaší `HttpExample` funkce z tohoto výstupu do prohlížeče a přidejte řetězec dotazu `?name=<your-name>` , jako je třeba celá adresa URL `http://localhost:7071/api/HttpExample?name=Functions` . V prohlížeči by se měla zobrazit zpráva jako `Hello Functions` :

    ![Výsledek funkce místně spuštěný v prohlížeči](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    Terminál, ve kterém jste spustili projekt, také zobrazuje výstup protokolu při provádění požadavků.

1. Až budete připraveni, použijte **CTRL** + **C** a vyberte možnost `y` zastavit hostitele funkcí.

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Vytvoření aplikace Function App v Azure:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 12 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Příkazem [AZ functionapp Create](/cli/azure/functionapp#az_functionapp_create) se vytvoří aplikace Function App v Azure. Pokud používáte Node.js 10, změňte také `--runtime-version` na `10` .
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime node -RuntimeVersion 12 -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    Rutina [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) vytvoří aplikaci Function App v Azure. Pokud používáte Node.js 10, změňte `-RuntimeVersion` na `10` .

    ---
        
    V předchozím příkladu nahraďte `<STORAGE_NAME>` názvem účtu, který jste použili v předchozím kroku, a nahraďte `<APP_NAME>` globálně jedinečným názvem vhodným pro vás. `<APP_NAME>` je také výchozí doména DNS pro aplikaci funkcí. 
    
    Tento příkaz vytvoří aplikaci Function App spuštěnou v zadaném jazykovém modulu runtime v rámci [plánu Azure Functions spotřeby](consumption-plan.md), který je zdarma pro množství využití, které se vám bude účtovat. Příkaz taky zřídí přidruženou instanci služby Azure Application Insights ve stejné skupině prostředků, pomocí které můžete monitorovat aplikaci Function App a zobrazovat protokoly. Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md). Instance nenese žádné náklady, dokud ji neaktivujete.

## <a name="deploy-the-function-project-to-azure"></a>Nasazení projektu funkce do Azure

Před použitím základních nástrojů k nasazení projektu do Azure vytvoříte sestavení JavaScriptu připravené pro produkční soubory z zdrojových souborů TypeScriptu.

1. K přípravě projektu TypeScript pro nasazení použijte následující příkaz:

    ```console
    npm run build:production 
    ```

1. V případě nezbytných prostředků teď budete připraveni nasadit projekt místní funkce do aplikace Function App v Azure pomocí příkazu [Func Azure functionapp Publish](functions-run-local.md#project-file-deployment) . V následujícím příkladu nahraďte `<APP_NAME>` názvem vaší aplikace.

    ```console
    func azure functionapp publish <APP_NAME>
    ```
    
    Pokud se zobrazí chyba, "Nejde najít aplikaci s názvem...", počkejte pár sekund a zkuste to znovu, protože Azure nemusel aplikaci zcela inicializovat za předchozím `az functionapp create` příkazem.
    
    Příkaz publikovat zobrazuje výsledky podobné následujícímu výstupu (zkrácený pro zjednodušení):
    
    <pre>
    ...
    
    Getting site publishing info...
    Creating archive for current directory...
    Performing remote build for functions project.
    
    ...
    
    Deployment successful.
    Remote build succeeded!
    Syncing triggers...
    Functions in msdocs-azurefunctions-qs:
        HttpExample - [httpTrigger]
            Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
    </pre>

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-typescript)
