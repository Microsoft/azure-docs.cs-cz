---
title: Vytvoření funkce Pythonu z příkazového řádku pro Azure Functions
description: Naučte se vytvořit funkci Pythonu z příkazového řádku a publikovat místní projekt na hostování bez serveru v Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 626cff867a336880689373c289087e2332a816ee
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787444"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Rychlý Start: vytvoření funkce Pythonu v Azure z příkazového řádku

> [!div class="op_single_selector" title1="Vyberte jazyk funkce: "]
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

V tomto článku pomocí nástrojů příkazového řádku vytvoříte funkci Pythonu, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do <abbr title="Běhové výpočetní prostředí, ve kterém jsou všechny podrobnosti serveru transparentní pro vývojáře aplikací, což zjednodušuje proces nasazení a správy kódu.">Prostředí bez serveru</abbr> prostředí pro <abbr title="Služba Azure, která pro aplikace poskytuje výpočetní prostředí bez serveru s nízkými náklady.">Azure Functions</abbr>.

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

K dispozici je také [Visual Studio Codeá verze](create-first-function-vs-code-python.md) tohoto článku.

## <a name="1-configure-your-environment"></a>1. konfigurace prostředí

Než začnete, musíte mít následující:

+ Azure <abbr title="Profil, který uchovává fakturační informace pro využití Azure">account</abbr> s aktivní <abbr title="Základní organizační struktura, ve které spravujete prostředky v Azure, obvykle přidružené k jednotlivcům nebo oddělením v rámci organizace.">předplatné</abbr>. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) verze 3. x. 
  
+ Buď <abbr title="Sada nástrojů příkazového řádku pro různé platformy pro práci s prostředky Azure z místního vývojového počítače jako alternativa k použití Azure Portal.">Azure CLI</abbr> nebo <abbr title="Modul PowerShellu, který poskytuje příkazy pro práci s prostředky Azure z místního vývojového počítače, jako alternativu k použití Azure Portal.">Azure PowerShell</abbr> pro vytváření prostředků Azure:

    + [Azure CLI](/cli/azure/install-azure-cli) verze 2,4 nebo novější.

    + [Azure PowerShell](/powershell/azure/install-az-ps) verze 5,0 nebo novější.

+ [Python 3,8 (64-bit)](https://www.python.org/downloads/release/python-382/), [python 3,7 (64-bit)](https://www.python.org/downloads/release/python-375/), [Python 3,6 (64-bit)](https://www.python.org/downloads/release/python-368/), který je podporovaný verzí 3. x z Azure Functions.

### <a name="11-prerequisite-check"></a>Kontrola požadavků 1,1

Ověřte požadavky, které závisí na tom, jestli pro vytváření prostředků Azure používáte rozhraní příkazového řádku Azure CLI nebo Azure PowerShell:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ V terminálu nebo příkazovém okně spusťte příkaz `func --version` a ověřte, zda se <abbr title="Sada nástrojů příkazového řádku pro práci s Azure Functions v místním počítači.">Azure Functions Core Tools</abbr> jsou verze 3. x.

+ Spusťte `az --version` a ověřte, jestli je verze Azure CLI 2,4 nebo novější.

+ Spusťte `az login` , abyste se přihlásili do Azure a ověřili aktivní předplatné.

+ Spusťte `python --version` (Linux/MacOS) nebo `py --version` (Windows), abyste zkontrolovali, jestli verze Pythonu jsou ve zprávách 3.8. x, 3.7. x nebo 3.6. x.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ V terminálu nebo příkazovém okně spusťte příkaz `func --version` a ověřte, zda se <abbr title="Sada nástrojů příkazového řádku pro práci s Azure Functions v místním počítači.">Azure Functions Core Tools</abbr> jsou verze 3. x.

+ Spusťte `(Get-Module -ListAvailable Az).Version` a ověřte verze 5,0 nebo novější. 

+ Spusťte `Connect-AzAccount` , abyste se přihlásili do Azure a ověřili aktivní předplatné.

+ Spusťte `python --version` (Linux/MacOS) nebo `py --version` (Windows), abyste zkontrolovali, jestli verze Pythonu jsou ve zprávách 3.8. x, 3.7. x nebo 3.6. x.

---

<br/>

---

## <a name="2-create-and-activate-a-virtual-environment"></a>2. <a name="create-venv"></a> Vytvoření a aktivace virtuálního prostředí

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

<br/>

---

## <a name="3-create-a-local-function-project"></a>3. vytvoření projektu místní funkce

V této části vytvoříte místní <abbr title="Logický kontejner pro jednu nebo více jednotlivých funkcí, které lze nasadit a spravovat společně.">Azure Functions projekt</abbr> v Pythonu. Každá funkce v projektu reaguje na konkrétní <abbr title="Typ události, která vyvolá kód funkce, jako je například požadavek HTTP, zpráva fronty nebo určitý čas.">signálu</abbr>.

1. Spusťte `func init` příkaz pro vytvoření projektu Functions ve složce s názvem *LocalFunctionProj* se zadaným modulem runtime:  

    ```console
    func init LocalFunctionProj --python
    ```

1. Přejděte do složky projektu:

    ```console
    cd LocalFunctionProj
    ```
    
    <br/>
    <details>
    <summary><strong>Co se vytvořilo ve složce LocalFunctionProj?</strong></summary>
    
    Tato složka obsahuje různé soubory pro projekt, včetně konfiguračních souborů s názvem [local.settings.jsv](functions-run-local.md#local-settings-file) a [host.jsna](functions-host-json.md). Vzhledem k tomu, že *local.settings.json* může obsahovat tajné kódy stažené z Azure, soubor je ve výchozím nastavení vyloučený ze správy zdrojového kódu v souboru *. gitignore* .
    </details>

1. Do projektu přidejte funkci pomocí následujícího příkazu:

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    `--name`Argument je jedinečný název vaší funkce (HttpExample).

    `--template`Argument určuje aktivační událost funkce (http).
    
    `func new`vytvoří podsložku odpovídající názvu funkce, který obsahuje soubor *\_ \_ init \_ \_ . py* s kódem funkce a konfiguračním souborem s názvem *function.jsv*.

    <br/>    
    <details>
    <summary><strong>Kód pro __init__. py</strong></summary>
    
    *\_ \_ init \_ \_ . py* obsahuje `main()` funkci Pythonu, která se aktivuje podle konfigurace v *function.js*.
    
    :::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::
    
    U triggeru HTTP funkce přijímá data žádosti v proměnné `req` , jak je definováno v *function.js*. `req` je instancí [třídy Azure. Functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Návratový objekt definovaný jako `$return` in *function.json*, je instancí [třídy Azure. Functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Další informace najdete v tématu [Azure Functions triggerů http a vazeb](./functions-bindings-http-webhook.md?tabs=python).
    </details>

    <br/>
    <details>
    <summary><strong>Kód pro function.jsv</strong></summary>

    *function.jsv* je konfigurační soubor, který definuje <abbr title="Deklarativní připojení mezi funkcí a dalšími prostředky Vstupní vazba poskytuje data funkci. výstupní vazba poskytuje data z funkce do jiných prostředků.">vstupní a výstupní vazby</abbr> pro funkci, včetně typu triggeru.
    
    `scriptFile`V případě potřeby můžete změnit na vyvolání jiného souboru Pythonu.
    
    :::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
    
    Každá vazba vyžaduje směr, typ a jedinečný název. Aktivační událost protokolu HTTP má vstupní vazbu typu [`httpTrigger`](functions-bindings-http-webhook-trigger.md) a výstupní vazby typu [`http`](functions-bindings-http-webhook-output.md) .    
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4. Spusťte funkci místně.

1. Spusťte funkci spuštěním místního hostitele modulu runtime Azure Functions ze složky *LocalFunctionProj* :

    ```
    func start
    ```

    Ke konci výstupu by se měly zobrazit následující řádky: 
    
    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    <br/>
    <details>
    <summary><strong>Ve výstupu se nezobrazuje HttpExample</strong></summary>

    Pokud se HttpExample nezobrazí, pravděpodobně jste spustili hostitele mimo kořenovou složku projektu. V takovém případě pomocí <kbd>kombinace kláves CTRL + C</kbd> zastavte hostitele, přejděte do kořenové složky projektu a spusťte předchozí příkaz znovu.
    </details>

1. Zkopírujte adresu URL funkce **HttpExample** z tohoto výstupu do prohlížeče a přidejte řetězec dotazu **? název =<YOUR_NAME>**. tím se nastaví celá adresa URL **http://localhost:7071/api/HttpExample?name=Functions** . V prohlížeči by se měla zobrazit zpráva, například **funkce Hello**:

    ![Výsledek funkce místně spuštěný v prohlížeči](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. Terminál, ve kterém jste spustili projekt, také zobrazuje výstup protokolu při provádění požadavků.

1. Po dokončení použijte <kbd>kombinaci kláves CTRL + C</kbd> a kliknutím na <kbd>y</kbd> zastavte hostitele Functions.

<br/>

---

## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. vytvoření podpory prostředků Azure pro vaši funkci

Než budete moct nasadit kód funkce do Azure, musíte vytvořit <abbr title="Logický kontejner pro související prostředky Azure, které můžete spravovat jako jednotku.">skupina prostředků</abbr>, a <abbr title="Účet, který obsahuje všechny datové objekty služby Azure Storage. Účet úložiště poskytuje jedinečný obor názvů pro data úložiště.">účet úložiště</abbr>a a <abbr title="Cloudový prostředek, který hostuje funkce bez serveru v Azure, která poskytuje základní výpočetní prostředí, ve kterém se spouští funkce.">aplikace Function App</abbr> pomocí následujících příkazů:

1. Pokud jste to ještě neudělali, přihlaste se k Azure:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Příkaz [AZ Login](/cli/azure/reference-index#az_login) vás přihlásí k účtu Azure.

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
 
    Pomocí příkazu [AZ Group Create](/cli/azure/group#az_group_create) se vytvoří skupina prostředků. Obecně vytvoříte skupinu prostředků a prostředky v <abbr title="Zeměpisná reference na konkrétní datacentrum Azure, ve kterém jsou prostředky přiděleny.">oblast</abbr> poblíž vás pomocí dostupné oblasti vrácené z `az account list-locations` příkazu.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Příkaz [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) vytvoří skupinu prostředků. Obecně vytvoříte skupinu prostředků a prostředky v oblasti blízko vás pomocí dostupné oblasti vrácené rutinou [Get-AzLocation](/powershell/module/az.resources/get-azlocation) .

    ---

    Nemůžete hostovat aplikace pro Linux a Windows ve stejné skupině prostředků. Pokud máte existující skupinu prostředků s názvem `AzureFunctionsQuickstart-rg` aplikace funkcí Windows nebo webové aplikace, musíte použít jinou skupinu prostředků.

1. Vytvořte účet pro obecné účely Azure Storage ve vaší skupině prostředků a oblasti:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Příkaz [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) vytvoří účet úložiště. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Rutina [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) vytvoří účet úložiště.

    ---

    Nahraďte `<STORAGE_NAME>` názvem, který je pro vás vhodný. <abbr title="Název musí být jedinečný ve všech účtech úložiště využívaných všemi zákazníky Azure globálně. Například můžete použít kombinaci svého osobního nebo firemního jména, názvu aplikace a číselného identifikátoru, jako v contosobizappstorage20.">jedinečné v Azure Storage</abbr>. Názvy musí obsahovat tři až 24 znaků a jenom malá písmena. `Standard_LRS` Určuje účet pro obecné účely, který je [podporován funkcemi](storage-considerations.md#storage-account-requirements).
    
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
    
    Nahraďte `<STORAGE_NAME>` názvem účtu, který jste použili v předchozím kroku.

    `<APP_NAME>`Nahraďte <abbr title="Název, který musí být jedinečný ve všech zákaznících Azure globálně. Například můžete použít kombinaci názvu osobního nebo organizace, názvu aplikace a číselného identifikátoru, jako je například contoso-bizapp-Func-20.">globálně jedinečný název, který je pro vás vhodný</abbr>. `<APP_NAME>` je také výchozí doména DNS pro aplikaci funkcí. 
    
    <br/>
    <details>
    <summary><strong>Jaké jsou náklady na prostředky zřízené v Azure?</strong></summary>

    Tento příkaz vytvoří aplikaci Function App spuštěnou v zadaném jazykovém modulu runtime v rámci [plánu Azure Functions spotřeby](functions-scale.md#overview-of-plans), který je zdarma pro množství využití, které se vám bude účtovat. Příkaz taky zřídí přidruženou instanci služby Azure Application Insights ve stejné skupině prostředků, pomocí které můžete monitorovat aplikaci Function App a zobrazovat protokoly. Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md). Instance nenese žádné náklady, dokud ji neaktivujete.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. nasazení projektu funkce do Azure

Po úspěšném vytvoření aplikace Function App v Azure jste teď připraveni **nasadit svůj místní projekt Functions** pomocí příkazu [Func Azure functionapp Publish](functions-run-local.md#project-file-deployment) .  

V následujícím příkladu nahraďte `<APP_NAME>` názvem vaší aplikace.

```console
func azure functionapp publish <APP_NAME>
```

`publish`Příkaz zobrazuje výsledky podobné následujícímu výstupu (zkrácený pro zjednodušení):

<pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
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
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>

<br/>

---

## <a name="7-invoke-the-function-on-azure"></a>7. volání funkce v Azure

Vzhledem k tomu, že funkce používá Trigger HTTP, vyvoláte ji pomocí požadavku HTTP na jeho adresu URL v prohlížeči nebo pomocí nástroje jako <abbr title="Nástroj příkazového řádku pro generování požadavků HTTP na adresu URL; si https://curl.se/">curl</abbr>. 

# <a name="browser"></a>[Prohlížeč](#tab/browser)

Zkopírujte úplnou **adresu URL pro vyvolání** zobrazenou ve výstupu `publish` příkazu do adresního řádku prohlížeče a přidejte parametr dotazu **&Name = Functions**. V prohlížeči by se měl zobrazit podobný výstup jako při spuštění funkce místně.

![Výstup funkce spuštěné v Azure v prohlížeči](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Spusťte [`curl`](https://curl.haxx.se/) s **adresou URL vyvolání** a přidejte parametr **&Name = Functions**. Výstupem příkazu by měl být text "Hello".

![Výstup funkce spuštěný v Azure pomocí kudrlinkou](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

### <a name="71-view-real-time-streaming-logs"></a>7,1 zobrazení protokolů streamování v reálném čase

Spuštěním následujícího příkazu zobrazte [protokoly streamování](functions-run-local.md#enable-streaming-logs) v reálném čase v Application Insights Azure Portal:

```console
func azure functionapp logstream <APP_NAME> --browser
```

Nahraďte `<APP_NAME>` názvem vaší aplikace Function App.

V samostatném okně terminálu nebo v prohlížeči zavolejte vzdálenou funkci znovu. Podrobný protokol provádění funkce v Azure je zobrazený v terminálu. 

<br/>

---

## <a name="8-clean-up-resources"></a>8. vyčištění prostředků

Pokud budete pokračovat k [dalšímu kroku](#next-steps) a přidáte <abbr title="Způsob, jak přidružit funkci k frontě úložiště, aby mohla vytvářet zprávy ve frontě. ">Výstupní vazba fronty Azure Storage</abbr>, zachovejte všechny vaše prostředky na místě, jak budete vytvářet informace o tom, co jste už provedli.

Jinak pomocí následujícího příkazu odstraňte skupinu prostředků a všechny její obsažené prostředky, abyste se vyhnuli vzniku dalších nákladů.

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

<br/>

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/python-functions-qs-survey)
