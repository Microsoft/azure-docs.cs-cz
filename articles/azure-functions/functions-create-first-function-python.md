---
title: Vytvoření funkce Python aktivované protokolem HTTP v Azure
description: Vytvářejte a nasaďte kód Python bez serveru do cloudu pomocí Azure Functions.
ms.date: 01/15/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 529a992178fae5566c8e315956388c4cd4b80257
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116213"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Rychlý Start: vytvoření funkce Python aktivované protokolem HTTP v Azure

V tomto článku pomocí nástrojů příkazového řádku vytvoříte funkci Pythonu, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do prostředí Azure Functions bez serveru. Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

K dispozici je také [Visual Studio Codeá verze](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-python) tohoto článku.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Verze [Azure Functions Core Tools](./functions-run-local.md#v2) 2.7.1846 nebo novější.
- Verze [Azure CLI](/cli/azure/install-azure-cli) 2.0.76 nebo novější. 
- [3.7.4 Pythonu – 64](https://www.python.org/downloads/release/python-374/). (3.7.4 Pythonu se ověřuje pomocí Azure Functions; Python 3,8 a novější verze se zatím nepodporují.)

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

1. V terminálu nebo příkazovém okně spusťte `func --version` a ověřte, že Azure Functions Core Tools jsou verze 2.7.1846 nebo novější.
1. Spusťte `az --version` a ověřte, že verze Azure CLI je 2.0.76 nebo novější.
1. Spusťte `az login`, abyste se přihlásili do Azure a ověřili aktivní předplatné.
1. Spusťte `python --version` (Linux/MacOS) nebo `py --version` (Windows), abyste zkontrolovali, jestli verze Pythonu nahlásí verzi 3.7. x.

## <a name="create-and-activate-a-virtual-environment"></a>Vytvoření a aktivace virtuální prostředí

Ve vhodné složce spusťte následující příkazy, abyste vytvořili a aktivovali virtuální prostředí s názvem `.venv`. Ujistěte se, že používáte Python 3,7, který podporuje Azure Functions.


# <a name="bashtabbash"></a>[bash](#tab/bash)

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

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmdtabcmd"></a>[Přepsat](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

V tomto aktivovaném virtuálním prostředí spustíte všechny následné příkazy. (Pokud chcete virtuální prostředí ukončit, spusťte `deactivate`.)

## <a name="create-a-local-function-project"></a>Vytvoření projektu místní funkce

V Azure Functions je projekt funkce kontejnerem pro jednu nebo více jednotlivých funkcí, které každý reaguje na konkrétní Trigger. Všechny funkce v projektu sdílejí stejné místní konfigurace a konfigurace hostování. V této části vytvoříte projekt funkce, který obsahuje jedinou funkci.

1. Ve virtuálním prostředí spuštěním příkazu `func init` vytvořte projekt Functions ve složce s názvem *LocalFunctionProj* se zadaným modulem runtime:

    ```
    func init LocalFunctionProj --python
    ```
    
    Tato složka obsahuje různé soubory pro projekt, včetně konfigurací souborů s názvem [Local. Settings. JSON](functions-run-local.md#local-settings-file) a [Host. JSON](functions-host-json.md). Protože *Local. Settings. JSON* může obsahovat tajné kódy stažené z Azure, soubor je ve výchozím nastavení vyloučený ze správy zdrojového kódu v souboru *. gitignore* .

    > [!TIP]
    > Vzhledem k tomu, že projekt funkce je svázán s konkrétním modulem runtime, všechny funkce v projektu musí být zapsány stejným jazykem.

1. Přejděte do složky projektu:

    ```
    cd LocalFunctionProj
    ```
    
1. Do projektu přidejte funkci pomocí následujícího příkazu, kde `--name` argument je jedinečný název vaší funkce a argument `--template` určuje Trigger funkce. `func new` vytvořit podsložku, která odpovídá názvu funkce, který obsahuje soubor kódu, který je vhodný pro zvolený jazyk projektu, a konfiguračního souboru s názvem *Function. JSON*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

### <a name="optional-examine-the-file-contents"></a>Volitelné Kontrola obsahu souboru

V případě potřeby můžete přeskočit, aby se [funkce spouštěla místně](#run-the-function-locally) , a prohlédnout si obsah souboru později.

#### <a name="__init__py"></a>\_\_init\_\_. py

*\_\_init\_\_. py* obsahuje `main()` funkci Pythonu, která se aktivuje podle konfigurace v *Function. JSON*.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

U triggeru HTTP funkce přijímá data žádosti v proměnné `req`, jak je definováno v *Function. JSON*. `req` je instance [třídy Azure. Functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Návratový objekt definovaný jako `$return` v *Function. JSON*je instancí [třídy Azure. Functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Další informace najdete v tématu [Azure Functions triggerů http a vazeb](functions-bindings-http-webhook.md).

#### <a name="functionjson"></a>function.json

*Function. JSON* je konfigurační soubor, který definuje vstupní a výstupní `bindings` pro funkci, včetně typu triggeru. V případě potřeby můžete změnit `scriptFile` k vyvolání jiného souboru Pythonu.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Každá vazba vyžaduje směr, typ a jedinečný název. Aktivační událost HTTP má vstupní vazbu typu [`httpTrigger`](functions-bindings-http-webhook.md#trigger) a výstupní vazba typu [`http`](functions-bindings-http-webhook.md#output).


## <a name="run-the-function-locally"></a>Místní spuštění funkce

Spusťte funkci spuštěním místního hostitele modulu runtime Azure Functions ve složce *LocalFunctionProj* :

```
func start
```

Měl by se zobrazit následující výstup. (Pokud se HttpExample nezobrazí, jak je uvedeno níže, pravděpodobně jste spustili hostitele ze složky *HttpExample* . V takovém případě pomocí **kombinace kláves Ctrl**+**C** zastavte hostitele, přejděte do nadřazené složky *LocalFunctionProj* a spusťte `func start` znovu.)

```output
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
```

Zkopírujte adresu URL funkce `HttpExample` z tohoto výstupu do prohlížeče a připojovat řetězec dotazu `?name=<your-name>`a provede úplnou adresu URL, jako je `http://localhost:7071/api/HttpExample?name=Functions`. V prohlížeči by se měla zobrazit zpráva, například `Hello Functions`:

![Výsledek funkce místně spuštěný v prohlížeči](./media/functions-create-first-function-python/function-test-local-browser.png)

Terminál, ve kterém jste spustili `func start`, zobrazuje také výstup protokolu při provádění požadavků.

Až budete připraveni, zastavte hostitele funkcí **stisknutím Ctrl**+**C** .

## <a name="create-supporting-azure-resources-for-your-function"></a>Vytvoření podpory prostředků Azure pro vaši funkci

Než budete moct nasadit kód funkce do Azure, musíte vytvořit tři prostředky:

- Skupina prostředků, což je logický kontejner pro související prostředky.
- Účet Azure Storage, který uchovává stav a další informace o vašich projektech.
- Aplikace Azure Functions, která poskytuje prostředí pro provádění kódu funkce. Aplikace Function App se mapuje na váš místní projekt funkce a umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

K vytvoření těchto položek použijete příkazy rozhraní příkazového řádku Azure. Každý příkaz poskytuje výstup JSON po dokončení.

1. Pokud jste to ještě neudělali, přihlaste se k Azure pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) :

    ```azurecli
    az login
    ```
    
1. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků s názvem `AzureFunctionsQuickstart-rg` v oblasti `westeurope`. (Obecně vytvoříte skupinu prostředků a prostředky v oblasti, která je blízko vás, pomocí dostupné oblasti z příkazu `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    
    > [!NOTE]
    > Nemůžete hostovat aplikace pro Linux a Windows ve stejné skupině prostředků. Pokud máte existující skupinu prostředků s názvem `AzureFunctionsQuickstart-rg` s aplikací Function App nebo webovou aplikací Windows, musíte použít jinou skupinu prostředků.
    
1. Pomocí příkazu [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) vytvořte účet úložiště pro obecné účely ve vaší skupině prostředků a oblasti. V následujícím příkladu nahraďte `<storage_name>` globálně jedinečným názvem vhodným pro vás. Názvy musí obsahovat tři až 24 znaků a jenom malá písmena. `Standard_LRS` určuje typický účet pro obecné účely.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    Účet úložiště pro tento rychlý Start vychází jenom z několika centů za USD.
    
1. Pomocí příkazu [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) vytvořte aplikaci Functions. V následujícím příkladu nahraďte `<storage_name>` názvem účtu, který jste použili v předchozím kroku, a nahraďte `<app_name>` globálně jedinečným názvem, který je pro vás vhodný. `<app_name>` je také výchozí doména DNS pro aplikaci funkcí.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --name <app_name> --storage-account <storage_name>
    ```
    
    Tento příkaz vytvoří aplikaci Function App, na které běží zadaný jazykový modul runtime v [plánu Azure Functions spotřeby](functions-scale.md#consumption-plan), který je zdarma pro množství využití, které se vám bude účtovat. Příkaz taky zřídí přidruženou instanci služby Azure Application Insights ve stejné skupině prostředků, pomocí které můžete monitorovat aplikaci Function App a zobrazovat protokoly. Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md). Instance nenese žádné náklady, dokud ji neaktivujete.
    
## <a name="deploy-the-function-project-to-azure"></a>Nasazení projektu funkce do Azure

V případě nezbytných prostředků teď budete připraveni nasadit projekt místní funkce do aplikace Function App v Azure pomocí příkazu [Func Azure functionapp Publish](functions-run-local.md#project-file-deployment) . V následujícím příkladu nahraďte `<app_name>` názvem vaší aplikace.

```
func azure functionapp publish <app_name>
```

Pokud se zobrazí chyba, "Nejde najít aplikaci s názvem...", počkejte pár sekund a zkuste to znovu, protože Azure nemusel aplikaci zcela inicializovat po předchozím `az functionapp create` příkazu.

Příkaz publikovat zobrazuje výsledky podobné následujícímu výstupu (zkrácený pro zjednodušení):

```output
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
```

## <a name="invoke-the-function-on-azure"></a>Vyvolání funkce v Azure

Vzhledem k tomu, že funkce používá Trigger HTTP, vyvoláte ji pomocí požadavku HTTP na adresu URL v prohlížeči nebo pomocí nástroje, jako je například kudrlinkou. V obou instancích je parametr adresy URL `code` jedinečný klíč funkce, který autorizuje vyvolání pomocí koncového bodu funkce.

# <a name="browsertabbrowser"></a>[Prohlížeee](#tab/browser)

Zkopírujte úplnou **adresu URL pro vyvolání** zobrazenou ve výstupu příkazu publikovat do adresního řádku prohlížeče a přidejte parametr dotazu `&name=Azure`. V prohlížeči by se měl zobrazit podobný výstup jako při spuštění funkce místně.

![Výstup funkce spuštěné v Azure v prohlížeči](./media/functions-create-first-function-python/function-test-cloud-browser.png)


# <a name="curltabcurl"></a>[Curl](#tab/curl)

Spusťte [kudrlinkou](https://curl.haxx.se/) s **adresou URL vyvolání**a připojením parametru `&name=Azure`. Výstupem příkazu by měl být text "Hello Azure".

![Výstup funkce spuštěný v Azure pomocí kudrlinkou](./media/functions-create-first-function-python/function-test-cloud-curl.png)

---

> [!TIP]
> Pokud chcete zobrazit protokoly téměř v reálném čase pro publikovanou aplikaci v Pythonu, použijte [Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kroku, [přidejte výstupní vazbu fronty Azure Storage](functions-add-output-binding-storage-queue-python.md), zachovejte všechny prostředky na místě, jak budete vytvářet informace o tom, co jste už provedli.

Jinak pomocí následujícího příkazu odstraňte skupinu prostředků a všechny její obsažené prostředky, abyste se vyhnuli vzniku dalších nákladů.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání výstupní vazby Azure Storage fronty](functions-add-output-binding-storage-queue-python.md)
