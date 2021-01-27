---
title: Vytvoření funkce Pythonu z příkazového řádku – Azure Functions
description: Naučte se vytvořit funkci Pythonu z příkazového řádku a pak publikovat místní projekt na hostování bez serveru v Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: 664a43dee635fa202f69927569fc1a5297bd1997
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880806"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Rychlý Start: vytvoření funkce Pythonu v Azure z příkazového řádku

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

V tomto článku pomocí nástrojů příkazového řádku vytvoříte funkci Pythonu, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do prostředí Azure Functions bez serveru.

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

K dispozici je také [Visual Studio Codeá verze](create-first-function-vs-code-python.md) tohoto článku.

## <a name="configure-your-local-environment"></a>Konfigurace místního prostředí

Než začnete, musíte mít následující:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) verze 3. x.
  
+ Jeden z následujících nástrojů pro vytváření prostředků Azure:

    + [Azure CLI](/cli/azure/install-azure-cli) verze 2,4 nebo novější.

    + [Azure PowerShell](/powershell/azure/install-az-ps) verze 5,0 nebo novější.

+ [Verze Pythonu, které jsou podporovány nástrojem Azure Functions](supported-languages.md#languages-by-runtime-version)

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

Ověřte požadavky, které závisí na tom, jestli pro vytváření prostředků Azure používáte Azure CLI nebo Azure PowerShell:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ V terminálu nebo příkazovém okně spusťte příkaz `func --version` a ověřte, zda Azure Functions Core Tools mají verzi 3. x.

+ Spusťte `az --version` a ověřte, jestli je verze Azure CLI 2,4 nebo novější.

+ Spusťte `az login` , abyste se přihlásili do Azure a ověřili aktivní předplatné.

+ Spusťte `python --version` (Linux/MacOS) nebo `py --version` (Windows), abyste zkontrolovali, jestli verze Pythonu jsou ve zprávách 3.8. x, 3.7. x nebo 3.6. x.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ V terminálu nebo příkazovém okně spusťte příkaz `func --version` a ověřte, zda Azure Functions Core Tools mají verzi 3. x.

+ Spusťte `(Get-Module -ListAvailable Az).Version` a ověřte verze 5,0 nebo novější. 

+ Spusťte `Connect-AzAccount` , abyste se přihlásili do Azure a ověřili aktivní předplatné.

+ Spusťte `python --version` (Linux/MacOS) nebo `py --version` (Windows), abyste zkontrolovali, jestli verze Pythonu jsou ve zprávách 3.8. x, 3.7. x nebo 3.6. x.

---

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Vytvoření a aktivace virtuálního prostředí

Ve vhodné složce spusťte následující příkazy, abyste vytvořili a aktivovali virtuální prostředí s názvem `.venv` . Ujistěte se, že používáte Python 3,8, 3,7 nebo 3,6, které podporuje Azure Functions.

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

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

V tomto aktivovaném virtuálním prostředí spustíte všechny následné příkazy. 

## <a name="create-a-local-function-project"></a>Vytvoření projektu místní funkce

V Azure Functions je projekt funkce kontejnerem pro jednu nebo více jednotlivých funkcí, které každý reaguje na konkrétní Trigger. Všechny funkce v projektu sdílejí stejné místní konfigurace a konfigurace hostování. V této části vytvoříte projekt funkce, který obsahuje jedinou funkci.

1. Spusťte `func init` příkaz následujícím způsobem a vytvořte projekt Functions ve složce s názvem *LocalFunctionProj* se zadaným modulem runtime:  

    ```console
    func init LocalFunctionProj --python
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

#### <a name="__init__py"></a>\_\_init \_ \_ . py

*\_ \_ init \_ \_ . py* obsahuje `main()` funkci Pythonu, která se aktivuje podle konfigurace v *function.js*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

U triggeru HTTP funkce přijímá data žádosti v proměnné `req` , jak je definováno v *function.js*. `req` je instancí [třídy Azure. Functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Návratový objekt definovaný jako `$return` in *function.json*, je instancí [třídy Azure. Functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Další informace najdete v tématu [Azure Functions triggerů http a vazeb](./functions-bindings-http-webhook.md?tabs=python).

#### <a name="functionjson"></a>function.json

*function.jsv* je konfigurační soubor, který definuje vstup a výstup `bindings` funkce, včetně typu triggeru.

`scriptFile`V případě potřeby můžete změnit na vyvolání jiného souboru Pythonu.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Každá vazba vyžaduje směr, typ a jedinečný název. Aktivační událost protokolu HTTP má vstupní vazbu typu [`httpTrigger`](functions-bindings-http-webhook-trigger.md) a výstupní vazby typu [`http`](functions-bindings-http-webhook-output.md) .

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Vytvoření podpory prostředků Azure pro vaši funkci

Než budete moct nasadit kód funkce do Azure, musíte vytvořit tři prostředky:

- Skupina prostředků, což je logický kontejner pro související prostředky.
- Účet úložiště, který uchovává stav a další informace o vašich projektech.
- Aplikace Function App, která poskytuje prostředí pro spuštění kódu vaší funkce. Aplikace Function App se mapuje na váš místní projekt funkce a umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

Tyto položky vytvoříte pomocí následujících příkazů. Podporují se rozhraní příkazového řádku Azure a PowerShellu.

1. Pokud jste to ještě neudělali, přihlaste se k Azure:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Příkaz [AZ Login](/cli/azure/reference-index#az-login) vás přihlásí k účtu Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Rutina [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) vás přihlásí k účtu Azure.

    ---

1. Vytvořte skupinu prostředků s názvem `AzureFunctionsQuickstart-rg` v `westeurope` oblasti. 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Pomocí příkazu [AZ Group Create](/cli/azure/group#az-group-create) se vytvoří skupina prostředků. Obecně vytvoříte skupinu prostředků a prostředky v oblasti blízko vás pomocí dostupné oblasti vrácené z `az account list-locations` příkazu.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Příkaz [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) vytvoří skupinu prostředků. Obecně vytvoříte skupinu prostředků a prostředky v oblasti blízko vás pomocí dostupné oblasti vrácené rutinou [Get-AzLocation](/powershell/module/az.resources/get-azlocation) .

    ---

    > [!NOTE]
    > Nemůžete hostovat aplikace pro Linux a Windows ve stejné skupině prostředků. Pokud máte existující skupinu prostředků s názvem `AzureFunctionsQuickstart-rg` aplikace funkcí Windows nebo webové aplikace, musíte použít jinou skupinu prostředků.

1. Vytvořte účet úložiště pro obecné účely ve vaší skupině prostředků a oblasti:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Příkaz [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) vytvoří účet úložiště. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Rutina [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) vytvoří účet úložiště.

    ---

    V předchozím příkladu nahraďte `<STORAGE_NAME>` názvem, který je vhodný pro vás a jedinečný v Azure Storage. Názvy musí obsahovat tři až 24 znaků a jenom malá písmena. `Standard_LRS` Určuje účet pro obecné účely, který je [podporován funkcemi](storage-considerations.md#storage-account-requirements).
    
    Účet úložiště pro tento rychlý Start vychází jenom z několika centů (USD).

1. Vytvoření aplikace Function App v Azure:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```
    
    Příkazem [AZ functionapp Create](/cli/azure/functionapp#az_functionapp_create) se vytvoří aplikace Function App v Azure. Pokud používáte Python 3,7 nebo 3,6, změňte `--runtime-version` na `3.7` nebo `3.6` v uvedeném pořadí.
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    Rutina [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) vytvoří aplikaci Function App v Azure. Pokud používáte Python 3,7 nebo 3,6, změňte `-RuntimeVersion` na `3.7` nebo `3.6` v uvedeném pořadí.

    ---
    
    V předchozím příkladu nahraďte `<STORAGE_NAME>` názvem účtu, který jste použili v předchozím kroku, a nahraďte `<APP_NAME>` globálně jedinečným názvem vhodným pro vás.  `<APP_NAME>` je také výchozí doména DNS pro aplikaci funkcí. 
    
    Tento příkaz vytvoří aplikaci Function App spuštěnou v zadaném jazykovém modulu runtime v rámci [plánu Azure Functions spotřeby](consumption-plan.md), který je zdarma pro množství využití, které se vám bude účtovat. Příkaz taky zřídí přidruženou instanci služby Azure Application Insights ve stejné skupině prostředků, pomocí které můžete monitorovat aplikaci Function App a zobrazovat protokoly. Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md). Instance nenese žádné náklady, dokud ji neaktivujete.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

Spuštěním následujícího příkazu zobrazte [protokoly streamování](functions-run-local.md#enable-streaming-logs) v reálném čase v Application Insights Azure Portal:

```console
func azure functionapp logstream <APP_NAME> --browser
```

V samostatném okně terminálu nebo v prohlížeči zavolejte vzdálenou funkci znovu. Podrobný protokol provádění funkce v Azure je zobrazený v terminálu. 

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/python-functions-qs-survey)