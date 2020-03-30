---
title: Vytvoření funkce v Azure, která reaguje na požadavky HTTP
description: Zjistěte, jak vytvořit funkci z příkazového řádku a pak publikovat místní projekt na hostování bez serveru v Azure Functions.
ms.date: 01/28/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 89b6a9f31414cbaa9cc92c1a0d881a1354180990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282728"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Úvodní příručka: Vytvoření funkce v Azure, která reaguje na požadavky HTTP

V tomto článku pomocí nástrojů příkazového řádku vytvořit funkci, která reaguje na požadavky HTTP. Po testování kódu místně jej nasadíte do prostředí Azure Functions bez serveru. Dokončení tohoto rychlého startu bude mít na účtu Azure malé náklady na několik usd centů nebo méně.

K dispozici je také [visual studio verze tohoto článku.](functions-create-first-function-vs-code.md)

## <a name="configure-your-local-environment"></a>Konfigurace místního prostředí

Než začnete, musíte mít následující:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ [Nástroje Azure Functions Core verze](./functions-run-local.md#v2) 2.7.1846 nebo novější verze 2.x.
::: zone-end  
::: zone pivot="programming-language-python"
+ Python 3.6 a 3.7 vyžadují [nástroje Azure Functions Core Tools](./functions-run-local.md#v2) verze 2.7.1846 nebo novější verzi 2.x. Python 3.8 vyžaduje [verzi 3.x](./functions-run-local.md#v2) základních nástrojů.
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) verze 2.0.76 nebo novější. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Verze Node.js](https://nodejs.org/), Active LTS a Maintenance LTS (doporučeno 8.11.1 a 10.14.1).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8](https://www.python.org/downloads/release/python-382/), [Python 3.7](https://www.python.org/downloads/release/python-375/), [Python 3.6](https://www.python.org/downloads/release/python-368/), které jsou podporované funkcemi Azure (x64).
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ Sada [SDK 2.2 core .NET](https://www.microsoft.com/net/download)
::: zone-end

### <a name="check-your-environment"></a>Zkontrolujte své prostředí

+ V terminálu nebo příkazovém okně spusťte `func --version` a zkontrolujte, zda jsou základní nástroje Azure Functions verze 2.7.1846 nebo novější verze 2.x.

+ Spuštěním `az --version` zkontrolujte, zda je verze příkazového příkazového příkazu Azure 2.0.76 nebo novější.

+ Spusťte `az login` přihlášení k Azure a ověřte aktivní předplatné.

::: zone pivot="programming-language-javascript,programming-language-typescript"
+ Spuštěním `node --version` zkontrolujte, zda se v souboru Node.js verze sestavy 8.x nebo 10.x.
::: zone-end
::: zone pivot="programming-language-python"
+ Spuštěním `python --version` (Linux/MacOS) nebo `py --version` (Windows) zkontrolujte, zda se v Pythonu sestavy verzí 3.8.x, 3.7.x nebo 3.6.x.

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Vytvoření a aktivace virtuálního prostředí

Ve vhodné složce spusťte následující příkazy a `.venv`vytvořte a aktivujte virtuální prostředí s názvem . Nezapomeňte použít Python 3.8, 3.7 nebo 3.6, které jsou podporované funkcemi Azure.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Pokud Python nenainstaloval balíček venv do vaší distribuce Linuxu, spusťte následující příkaz:

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

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Spuštění všech následných příkazů v tomto aktivovaném virtuálním prostředí. (Chcete-li virtuální prostředí `deactivate`ukončit, spusťte .)

::: zone-end

## <a name="create-a-local-function-project"></a>Vytvoření projektu místní funkce

V Azure Functions je projekt funkce kontejner pro jednu nebo více jednotlivých funkcí, které každý reaguje na konkrétní aktivační událost. Všechny funkce v projektu sdílejí stejné místní a hostitelské konfigurace. V této části vytvoříte projekt funkce, který obsahuje jednu funkci.

1. Ve virtuálním prostředí `func init` spusťte příkaz a vytvořte projekt funkcí ve složce s názvem *LocalFunctionProj* se zadaným časem runtime:

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


    Tato složka obsahuje různé soubory pro projekt, včetně konfiguračních souborů s názvem [local.settings.json](functions-run-local.md#local-settings-file) a [host.json](functions-host-json.md). Vzhledem k tomu, *že local.settings.json* může obsahovat tajné klíče stažené z Azure, je soubor ve výchozím nastavení vyloučen ze správy zdrojového kódu v souboru *.gitignore.*

1. Přejděte do složky projektu:

    ```
    cd LocalFunctionProj
    ```
    
1. Přidejte funkci do projektu pomocí následujícího `--name` příkazu, kde argument je `--template` jedinečný název funkce a argument určuje aktivační událost funkce. 

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

    ::: zone pivot="programming-language-csharp"
    `func new`vytvoří soubor kódu HttpExample.cs.
    ::: zone-end
    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
    `func new`Vytvoří podsložku odpovídající názvu funkce, která obsahuje soubor kódu odpovídající zvolenému jazyku projektu a konfigurační soubor s názvem *function.json*.
    ::: zone-end

### <a name="optional-examine-the-file-contents"></a>(Nepovinné) Zkontrolujte obsah souboru

V případě potřeby můžete přeskočit na [Spustit funkci místně](#run-the-function-locally) a prozkoumat obsah souboru později.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* obsahuje `Run` metodu, která přijímá `req` data požadavku v proměnné je [HttpRequest,](/dotnet/api/microsoft.aspnetcore.http.httprequest) který je zdoben **HttpTriggerAttribute**, který definuje chování aktivační události. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Návratový objekt je [ActionResult,](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) který vrací zprávu odpovědi jako [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) nebo [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Další informace najdete v [tématu Azure Functions HTTP aktivační události a vazby](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* `main()` obsahuje funkci Pythonu, která se aktivuje podle konfigurace v *souboru function.json*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Pro aktivační událost HTTP funkce přijímá data `req` požadavku v proměnné, jak je definována v *function.json*. `req`je instancí [třídy azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Return Object, definované `$return` jako *function.json*, je instance [azure.functions.HttpResponse class](/python/api/azure-functions/azure.functions.httpresponse). Další informace najdete v [tématu Azure Functions HTTP aktivační události a vazby](/azure/azure-functions/functions-bindings-http-webhook?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index.js

*index.js* exportuje funkci, která je spuštěna podle konfigurace v *souboru function.json*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

Pro aktivační událost HTTP funkce přijímá data `req` požadavku v proměnné, jak je definována v *function.json*. Návratový objekt, `$return` definovaný jako *function.json*, je odpověď. Další informace najdete v [tématu Azure Functions HTTP aktivační události a vazby](/azure/azure-functions/functions-bindings-http-webhook?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index.ts

*index.ts* exportuje funkci, která je spuštěna podle konfigurace v *souboru function.json*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

Pro aktivační událost HTTP přijímá funkce data `req` požadavků v proměnné typu **HttpRequest,** jak je definováno v *function.json*. Návratový objekt, `$return` definovaný jako *function.json*, je odpověď. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run.ps1

*run.ps1* definuje skript funkce, který se aktivuje podle konfigurace v *souboru function.json*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

Pro aktivační událost HTTP funkce přijímá data `$Request` požadavku předaná param definované v *function.json*. Návratový objekt, `Response` definovaný jako *function.json*, `Push-OutputBinding` je předán rutině jako odpověď. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*function.json* je konfigurační soubor, `bindings` který definuje vstup a výstup pro funkci, včetně typu aktivační události. 
::: zone-end

::: zone pivot="programming-language-python"
V případě `scriptFile` potřeby můžete změnit vyvolání jiného souboru Pythonu.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Každá vazba vyžaduje směr, typ a jedinečný název. Aktivační událost HTTP má vstupní [`httpTrigger`](functions-bindings-http-webhook-trigger.md) vazbu typu [`http`](functions-bindings-http-webhook-output.md)a výstupní vazby typu .
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Vytvoření podpůrných prostředků Azure pro vaši funkci

Než budete moci nasadit kód funkce do Azure, musíte vytvořit tři prostředky:

- Skupina prostředků, která je logickým kontejnerem pro související prostředky.
- Účet úložiště, který udržuje stav a další informace o vašich projektech.
- Aplikace funkce, která poskytuje prostředí pro provádění kódu funkce. Aplikace funkcí se mapuje na projekt místních funkcí a umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazení a sdílení prostředků.

Pomocí následujících příkazů Azure CLI k vytvoření těchto položek. Každý příkaz poskytuje výstup JSON po dokončení.

1. Pokud jste tak ještě neučinili, přihlaste se do Azure pomocí příkazu [az login:](/cli/azure/reference-index#az-login)

    ```azurecli
    az login
    ```
    
1. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu `AzureFunctionsQuickstart-rg` prostředků `westeurope` s názvem v oblasti. (Skupinu prostředků a prostředky obvykle vytváříte v oblasti ve `az account list-locations` vašem okolí pomocí dostupné oblasti z příkazu.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    ::: zone pivot="programming-language-python"  
    > [!NOTE]
    > Linux a Windows nelze hostovat ve stejné skupině prostředků. Pokud máte existující skupinu `AzureFunctionsQuickstart-rg` prostředků pojmenovanou pomocí aplikace pro funkce Windows nebo webové aplikace, musíte použít jinou skupinu prostředků.
    ::: zone-end  
    
1. Vytvořte účet úložiště pro obecné účely ve skupině prostředků a oblasti pomocí příkazu [vytvořit účet úložiště az.](/cli/azure/storage/account#az-storage-account-create) V následujícím příkladu `<STORAGE_NAME>` nahraďte globálně jedinečný název, který je vám vhodný. Názvy musí obsahovat pouze tři až 24 písmen a malá písmena. `Standard_LRS`určuje účet pro obecné účely, který je [podporován funkcemi](storage-considerations.md#storage-account-requirements).

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    Účet úložiště vznikne pouze několik centů (USD) pro tento rychlý start.
    
1. Vytvořte aplikaci funkce pomocí příkazu [az functionapp create.](/cli/azure/functionapp#az-functionapp-create) V následujícím příkladu `<STORAGE_NAME>` nahraďte názvem účtu, který jste použili v předchozím kroku, a nahraďte `<APP_NAME>` globálně jedinečným názvem, který je vám vhodný. `<APP_NAME>` je také výchozí doména DNS pro aplikaci funkcí. 

    ::: zone pivot="programming-language-python"  
    Pokud používáte Python 3.8, `3.8` `--functions_version` změňte `--runtime-version` na a na `3`.
    
    Pokud používáte Python 3.6, změňte `--runtime-version` na `3.6`.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-javascript,programming-language-typescript"  
    Pokud používáte soubor Node.js `--runtime-version` 8, změňte také na `8`.

    
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-csharp"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  
    
    ::: zone pivot="programming-language-powershell"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    Tento příkaz vytvoří aplikaci funkcí spuštěnou v zadaném prostředí jazyka v rámci [plánu spotřeby funkcí Azure](functions-scale.md#consumption-plan), který je zdarma pro množství využití, které zde vznikne. Příkaz také zřídí přidruženou instanci Azure Application Insights ve stejné skupině prostředků, pomocí které můžete sledovat aplikaci funkcí a zobrazit protokoly. Další informace naleznete v [tématu Sledování funkcí Azure](functions-monitoring.md). Instance nevznikne žádné náklady, dokud ji neaktivujete.
    
## <a name="deploy-the-function-project-to-azure"></a>Nasazení projektu funkce do Azure

::: zone pivot="programming-language-typescript"  
Před použitím základních nástrojů k nasazení projektu do Azure vytvoříte sestavení javascriptových souborů připravené pro produkční prostředí ze zdrojových souborů Typu Script.

Následující příkaz připraví projekt jazyka TypeScript pro nasazení:

```
npm run build:production 
```
::: zone-end  

S potřebnými prostředky na místě, teď jste připraveni nasadit místní funkce projektu do aplikace funkce v Azure pomocí [func azure functionapp publikovat](functions-run-local.md#project-file-deployment) příkaz. V následujícím příkladu `<APP_NAME>` nahraďte názvem aplikace.

```
func azure functionapp publish <APP_NAME>
```

Pokud se zobrazí chyba "Nelze najít aplikaci s názvem ...", počkejte několik sekund a zkuste to znovu, protože Azure nemusí mít plně inicializována aplikace po předchozím `az functionapp create` příkazu.

Příkaz publikovat zobrazuje výsledky podobné následujícímu výstupu (zkráceno pro jednoduchost):

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

Vzhledem k tomu, že vaše funkce používá aktivační událost HTTP, vyvoláte ji tak, že vytvoříte požadavek HTTP na adresu URL v prohlížeči nebo pomocí nástroje, jako je curl. V obou případech `code` je parametr URL [jedinečným funkčním klíčem,](functions-bindings-http-webhook-trigger.md#authorization-keys) který autorizuje vyvolání koncového bodu funkce.

# <a name="browser"></a>[Prohlížeč](#tab/browser)

Zkopírujte úplnou **adresu URL invoke** zobrazenou ve výstupu příkazu publikovat `&name=Functions`do adresního řádku prohlížeče a přidejte parametr dotazu . Prohlížeč by měl zobrazit podobný výstup jako při spuštění funkce místně.

![Výstup funkce spuštěné v Azure v prohlížeči](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[Curl](#tab/curl)

Spustit [`curl`](https://curl.haxx.se/) s **adresou URL**invoke `&name=Functions`, připojit parametr . Výstupem příkazu by měl být text "Funkce Hello".

![Výstup funkce spuštěné v Azure pomocí curl](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Chcete-li zobrazit protokoly v reálném čase pro publikovanou aplikaci funkcí, použijte [datový proud živých metrik Application Insights](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kroku, [přidejte výstupní vazbu fronty Azure Storage](functions-add-output-binding-storage-queue-cli.md), udržujte všechny prostředky na místě, protože budete stavět na tom, co jste už udělali.

V opačném případě pomocí následujícího příkazu odstraňte skupinu prostředků a všechny její obsažené prostředky, abyste zabránili vzniku dalších nákladů.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage](functions-add-output-binding-storage-queue-cli.md)
