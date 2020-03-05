---
title: Vytvoření funkce v Azure, která reaguje na požadavky HTTP
description: Naučte se vytvořit funkci z příkazového řádku a pak publikovat místní projekt na hostování bez serveru v Azure Functions.
ms.date: 01/28/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 2a02e1481d975f877508bde02948bc65561b9f13
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78272751"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Rychlý Start: vytvoření funkce v Azure, která reaguje na požadavky HTTP

V tomto článku použijete nástroje příkazového řádku k vytvoření funkce, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do prostředí Azure Functions bez serveru. Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

K dispozici je také [Visual Studio Codeá verze](functions-create-first-function-vs-code.md) tohoto článku.

## <a name="configure-your-local-environment"></a>Konfigurace místního prostředí

Než začnete, musíte mít následující:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Verze [Azure Functions Core Tools](./functions-run-local.md#v2) 2.7.1846 nebo novější verze 2. x.

+ Verze [Azure CLI](/cli/azure/install-azure-cli) 2.0.76 nebo novější. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ Verze [Node. js](https://nodejs.org/), Active LTS a Maintenance LTS (Doporučené 8.11.1 a 10.14.1).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3,7](https://www.python.org/downloads/release/python-375/) nebo [Python 3,6](https://www.python.org/downloads/release/python-368/), které jsou podporovány nástrojem Azure Functions. Python 3,8 a novější verze se ještě nepodporují. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [Jádro PowerShellu](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core SDK 2.2 +](https://www.microsoft.com/net/download)
::: zone-end

### <a name="check-your-environment"></a>Kontroly prostředí

+ V okně terminálu nebo příkazu spusťte `func --version` a ověřte, že Azure Functions Core Tools verze 2.7.1846 nebo novější verze 2. x.

+ Spusťte `az --version` a ověřte, že verze Azure CLI je 2.0.76 nebo novější.

+ Spusťte `az login`, abyste se přihlásili do Azure a ověřili aktivní předplatné.

::: zone pivot="programming-language-javascript,programming-language-typescript"
+ Spusťte `node --version` pro kontrolu verze Node. js sestavy 8. x nebo 10. x.
::: zone-end
::: zone pivot="programming-language-python"
+ Spusťte `python --version` (Linux/MacOS) nebo `py --version` (Windows), abyste zkontrolovali, jestli verze Pythonu oznamuje verzi 3.7. x nebo 3.6. x.

## <a name="create-venv"></a>Vytvoření a aktivace virtuálního prostředí

Ve vhodné složce spusťte následující příkazy, abyste vytvořili a aktivovali virtuální prostředí s názvem `.venv`. Ujistěte se, že používáte Python 3,7 nebo 3,6, který podporuje Azure Functions.


# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Pokud Python nenainstaloval balíček venv do distribuce systému Linux, spusťte následující příkaz:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Přepsat](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

V tomto aktivovaném virtuálním prostředí spustíte všechny následné příkazy. (Pokud chcete virtuální prostředí ukončit, spusťte `deactivate`.)

::: zone-end

## <a name="create-a-local-function-project"></a>Vytvoření projektu místní funkce

V Azure Functions je projekt funkce kontejnerem pro jednu nebo více jednotlivých funkcí, které každý reaguje na konkrétní Trigger. Všechny funkce v projektu sdílejí stejné místní konfigurace a konfigurace hostování. V této části vytvoříte projekt funkce, který obsahuje jedinou funkci.

1. Ve virtuálním prostředí spuštěním příkazu `func init` vytvořte projekt Functions ve složce s názvem *LocalFunctionProj* se zadaným modulem runtime:

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionProj --python
    ```
    ::: zone-end
    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionProj --dotnet
    ```
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionProj --javascript
    ```
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionProj --typescript
    ```
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionProj --powershell
    ```
    ::: zone-end


    Tato složka obsahuje různé soubory pro projekt, včetně konfigurací souborů s názvem [Local. Settings. JSON](functions-run-local.md#local-settings-file) a [Host. JSON](functions-host-json.md). Protože *Local. Settings. JSON* může obsahovat tajné kódy stažené z Azure, soubor je ve výchozím nastavení vyloučený ze správy zdrojového kódu v souboru *. gitignore* .

1. Přejděte do složky projektu:

    ```
    cd LocalFunctionProj
    ```
    
1. Do projektu přidejte funkci pomocí následujícího příkazu, kde `--name` argument je jedinečný název vaší funkce a argument `--template` určuje Trigger funkce. 

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

    ::: zone pivot="programming-language-csharp"
    `func new` vytvoří soubor kódu HttpExample.cs.
    ::: zone-end
    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
    `func new` vytvoří podsložku, která odpovídá názvu funkce, který obsahuje soubor kódu, který je vhodný pro zvolený jazyk projektu, a konfiguračního souboru s názvem *Function. JSON*.
    ::: zone-end

### <a name="optional-examine-the-file-contents"></a>Volitelné Kontrola obsahu souboru

V případě potřeby můžete přeskočit, aby se [funkce spouštěla místně](#run-the-function-locally) , a prohlédnout si obsah souboru později.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* obsahuje metodu `Run`, která přijímá data žádosti v proměnné `req` je [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) , který je upravený pomocí **HttpTriggerAttribute**, který definuje chování triggeru. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Návratový objekt je [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) , který vrací zprávu odpovědi buď jako [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200), nebo jako [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Další informace najdete v tématu [Azure Functions triggerů http a vazeb](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_. py

*\_\_init\_\_. py* obsahuje `main()` funkci Pythonu, která se aktivuje podle konfigurace v *Function. JSON*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

U triggeru HTTP funkce přijímá data žádosti v proměnné `req`, jak je definováno v *Function. JSON*. `req` je instance [třídy Azure. Functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Návratový objekt definovaný jako `$return` v *Function. JSON*je instancí [třídy Azure. Functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Další informace najdete v tématu [Azure Functions triggerů http a vazeb](/azure/azure-functions/functions-bindings-http-webhook?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index. js

*index. js* vyexportuje funkci, která se aktivuje podle konfigurace v *Function. JSON*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

U triggeru HTTP funkce přijímá data žádosti v proměnné `req`, jak je definováno v *Function. JSON*. Návratový objekt definovaný jako `$return` v *Function. JSON*je odpověď. Další informace najdete v tématu [Azure Functions triggerů http a vazeb](/azure/azure-functions/functions-bindings-http-webhook?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index. TS

*index. TS* exportuje funkci, která se aktivuje podle konfigurace v *Function. JSON*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

U triggeru HTTP funkce přijímá data žádosti v proměnné `req` typu **HttpRequest** , jak je definováno v *Function. JSON*. Návratový objekt definovaný jako `$return` v *Function. JSON*je odpověď. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>spustit. ps1

*příkaz run. ps1* definuje skript funkce, který se aktivuje podle konfigurace v *Function. JSON*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

U triggeru HTTP funkce přijímá data žádosti předaná `$Request`mu parametru definovanému v *Function. JSON*. Návratový objekt definovaný jako `Response` v *Function. JSON*je předán rutině `Push-OutputBinding` jako odpověď. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*Function. JSON* je konfigurační soubor, který definuje vstupní a výstupní `bindings` pro funkci, včetně typu triggeru. 
::: zone-end

::: zone pivot="programming-language-python"
V případě potřeby můžete změnit `scriptFile` k vyvolání jiného souboru Pythonu.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Každá vazba vyžaduje směr, typ a jedinečný název. Aktivační událost HTTP má vstupní vazbu typu [`httpTrigger`](functions-bindings-http-webhook-trigger.md) a výstupní vazba typu [`http`](functions-bindings-http-webhook-output.md).
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Vytvoření podpory prostředků Azure pro vaši funkci

Než budete moct nasadit kód funkce do Azure, musíte vytvořit tři prostředky:

- Skupina prostředků, což je logický kontejner pro související prostředky.
- Účet úložiště, který uchovává stav a další informace o vašich projektech.
- Aplikace Function App, která poskytuje prostředí pro spuštění kódu vaší funkce. Aplikace Function App se mapuje na váš místní projekt funkce a umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

Tyto položky vytvoříte pomocí následujících příkazů Azure CLI. Každý příkaz poskytuje výstup JSON po dokončení.

1. Pokud jste to ještě neudělali, přihlaste se k Azure pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) :

    ```azurecli
    az login
    ```
    
1. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků s názvem `AzureFunctionsQuickstart-rg` v oblasti `westeurope`. (Obecně vytvoříte skupinu prostředků a prostředky v oblasti, která je blízko vás, pomocí dostupné oblasti z příkazu `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    ::: zone pivot="programming-language-python"  
    > [!NOTE]
    > Nemůžete hostovat aplikace pro Linux a Windows ve stejné skupině prostředků. Pokud máte existující skupinu prostředků s názvem `AzureFunctionsQuickstart-rg` s aplikací Function App nebo webovou aplikací Windows, musíte použít jinou skupinu prostředků.
    ::: zone-end  
    
1. Pomocí příkazu [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) vytvořte účet úložiště pro obecné účely ve vaší skupině prostředků a oblasti. V následujícím příkladu nahraďte `<STORAGE_NAME>` globálně jedinečným názvem vhodným pro vás. Názvy musí obsahovat tři až 24 znaků a jenom malá písmena. `Standard_LRS` Určuje účet pro obecné účely, který [podporují funkce](storage-considerations.md#storage-account-requirements).

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    Účet úložiště pro tento rychlý Start vychází jenom z několika centů (USD).
    
1. Pomocí příkazu [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) vytvořte aplikaci Functions. V následujícím příkladu nahraďte `<STORAGE_NAME>` názvem účtu, který jste použili v předchozím kroku, a nahraďte `<APP_NAME>` globálně jedinečným názvem, který je pro vás vhodný. `<APP_NAME>` je také výchozí doména DNS pro aplikaci funkcí. 

    ::: zone pivot="programming-language-python"  
    Pokud používáte Python 3,6, změňte také `--runtime-version` na `3.6`.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-javascript,programming-language-typescript"  
    Pokud používáte Node. js 8, změňte také `--runtime-version` na `8`.

    
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-csharp"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  
    
    ::: zone pivot="programming-language-powershell"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    Tento příkaz vytvoří aplikaci Function App spuštěnou v zadaném jazykovém modulu runtime v rámci [plánu Azure Functions spotřeby](functions-scale.md#consumption-plan), který je zdarma pro množství využití, které se vám bude účtovat. Příkaz taky zřídí přidruženou instanci služby Azure Application Insights ve stejné skupině prostředků, pomocí které můžete monitorovat aplikaci Function App a zobrazovat protokoly. Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md). Instance nenese žádné náklady, dokud ji neaktivujete.
    
## <a name="deploy-the-function-project-to-azure"></a>Nasazení projektu funkce do Azure

::: zone pivot="programming-language-typescript"  
Před použitím základních nástrojů k nasazení projektu do Azure vytvoříte sestavení JavaScriptu připravené pro produkční soubory z zdrojových souborů TypeScriptu.

Následující příkaz připraví projekt TypeScript k nasazení:

```
npm run build:production 
```
::: zone-end  

V případě nezbytných prostředků teď budete připraveni nasadit projekt místní funkce do aplikace Function App v Azure pomocí příkazu [Func Azure functionapp Publish](functions-run-local.md#project-file-deployment) . V následujícím příkladu nahraďte `<APP_NAME>` názvem vaší aplikace.

```
func azure functionapp publish <APP_NAME>
```

Pokud se zobrazí chyba, "Nejde najít aplikaci s názvem...", počkejte pár sekund a zkuste to znovu, protože Azure nemusel aplikaci zcela inicializovat po předchozím `az functionapp create` příkazu.

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

## <a name="invoke-the-function-on-azure"></a>Vyvolání funkce v Azure

Vzhledem k tomu, že funkce používá Trigger HTTP, vyvoláte ji pomocí požadavku HTTP na adresu URL v prohlížeči nebo pomocí nástroje, jako je například kudrlinkou. V obou instancích je parametr adresy URL `code` jedinečný [klíč funkcí](functions-bindings-http-webhook-trigger.md#authorization-keys) , který autorizuje vyvolání koncového bodu funkce.

# <a name="browser"></a>[Prohlížeee](#tab/browser)

Zkopírujte úplnou **adresu URL pro vyvolání** zobrazenou ve výstupu příkazu publikovat do adresního řádku prohlížeče a přidejte parametr dotazu `&name=Functions`. V prohlížeči by se měl zobrazit podobný výstup jako při spuštění funkce místně.

![Výstup funkce spuštěné v Azure v prohlížeči](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[Curl](#tab/curl)

Spusťte [`curl`](https://curl.haxx.se/) s **adresou URL vyvolání**a připojením parametru `&name=Functions`. Výstupem příkazu by měl být text "Hello".

![Výstup funkce spuštěný v Azure pomocí kudrlinkou](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> K zobrazení protokolů téměř v reálném čase pro publikovanou aplikaci Function App použijte [Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kroku, [přidejte výstupní vazbu fronty Azure Storage](functions-add-output-binding-storage-queue-cli.md), zachovejte všechny prostředky na místě, jak budete vytvářet informace o tom, co jste už provedli.

Jinak pomocí následujícího příkazu odstraňte skupinu prostředků a všechny její obsažené prostředky, abyste se vyhnuli vzniku dalších nákladů.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage](functions-add-output-binding-storage-queue-cli.md)
