---
title: Vytvoření funkce jazyka C# z příkazového řádku – Azure Functions
description: Naučte se vytvořit funkci jazyka C# z příkazového řádku a pak publikovat místní projekt na hostování bez serveru v Azure Functions.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
- devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 45173a74c0e3189c1f356aea2f8024ff15409f32
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866192"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Rychlý Start: vytvoření funkce jazyka C# v Azure z příkazového řádku

> [!div class="op_single_selector" title1="Vyberte jazyk funkce: "]
> - [C#](create-first-function-cli-csharp-ieux.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [Python](create-first-function-cli-python.md)
> - [TypeScript](create-first-function-cli-typescript.md)

V tomto článku pomocí nástrojů příkazového řádku vytvoříte funkci založenou na knihovně jazyka C#, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do <abbr title="Běhové výpočetní prostředí, ve kterém jsou všechny podrobnosti serveru transparentní pro vývojáře aplikací, což zjednodušuje proces nasazení a správy kódu.">Prostředí bez serveru</abbr> prostředí pro <abbr title="Služba Azure, která pro aplikace poskytuje výpočetní prostředí bez serveru s nízkými náklady.">Azure Functions</abbr>.

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

K dispozici je také [Visual Studio Codeá verze](create-first-function-vs-code-csharp.md) tohoto článku.

## <a name="1-prepare-your-environment"></a>1. Příprava prostředí

+ Získat Azure <abbr title="Profil, který uchovává fakturační informace pro využití Azure">account</abbr> s aktivní <abbr title="Základní organizační struktura, ve které spravujete prostředky v Azure, obvykle přidružené k jednotlivcům nebo oddělením v rámci organizace.">předplatné</abbr>. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Nainstalovat [sadu .NET Core 3,1 SDK](https://dotnet.microsoft.com/download)

+ Nainstalujte [Azure Functions Core Tools](functions-run-local.md#v2) verze 3. x.

+ Buď <abbr title="Sada nástrojů příkazového řádku pro různé platformy pro práci s prostředky Azure z místního vývojového počítače jako alternativa k použití Azure Portal.">Azure CLI</abbr> nebo <abbr title="Modul PowerShellu, který poskytuje příkazy pro práci s prostředky Azure z místního vývojového počítače, jako alternativu k použití Azure Portal.">Azure PowerShell</abbr> pro vytváření prostředků Azure:

    + [Azure CLI](/cli/azure/install-azure-cli) verze 2,4 nebo novější.

    + [Azure PowerShell](/powershell/azure/install-az-ps) verze 5,0 nebo novější.

---

### <a name="2-verify-prerequisites"></a>2. Ověření požadovaných součástí

Ověřte požadavky, které závisí na tom, jestli pro vytváření prostředků Azure používáte rozhraní příkazového řádku Azure CLI nebo Azure PowerShell:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ V terminálu nebo příkazovém okně spusťte příkaz `func --version` a ověřte, zda se <abbr title="Sada nástrojů příkazového řádku pro práci s Azure Functions v místním počítači.">Azure Functions Core Tools</abbr> jsou verze 3. x.

+ **Spustit příkaz** `az --version` Pokud chcete zjistit, jestli je verze Azure CLI 2,4 nebo novější.

+ **Spustit příkaz** `az login` Pokud se chcete přihlásit k Azure a ověřit aktivní předplatné.

+ **Spustit příkaz** `dotnet --list-sdks` ověření, že je nainstalovaná .NET Core SDK verze 3.1. x

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+**Spustit příkaz** `func --version` Chcete-li ověřit, zda Azure Functions Core Tools mají verzi 3. x.

+ **Spustit příkaz** `(Get-Module -ListAvailable Az).Version` a ověřte verze 5,0 nebo novější. 

+ **Spustit příkaz** `Connect-AzAccount` Pokud se chcete přihlásit k Azure a ověřit aktivní předplatné.

+ **Spustit příkaz** `dotnet --list-sdks` ověření, že je nainstalovaná .NET Core SDK verze 3.1. x

---

## <a name="3-create-a-local-function-project"></a>3. vytvoření projektu místní funkce

V této části vytvoříte místní <abbr title="Logický kontejner pro jednu nebo více jednotlivých funkcí, které lze nasadit a spravovat společně.">Azure Functions projekt</abbr> v jazyce C#. Každá funkce v projektu reaguje na konkrétní <abbr title="Událost, která vyvolá kód funkce, jako je například požadavek HTTP, zpráva fronty nebo určitý čas.">signálu</abbr>.

1. Spusťte `func init` příkaz pro vytvoření projektu Functions ve složce s názvem *LocalFunctionProj* se zadaným modulem runtime:  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. **Spustit příkaz** ' CD LocalFunctionProj ' pro přechod na <abbr title="Tato složka obsahuje různé soubory pro projekt, včetně konfiguračních souborů s názvem local.settings.jsv a host.jsna. Vzhledem k tomu, že local.settings.json může obsahovat tajné kódy stažené z Azure, soubor je ve výchozím nastavení vyloučený ze správy zdrojového kódu v souboru. gitignore.">Složka projektu</abbr>.

    ```console
    cd LocalFunctionProj
    ```
    <br/>

1. Do projektu přidejte funkci pomocí následujícího příkazu:
    
    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    `--name`Argument je jedinečný název vaší funkce (HttpExample).

    `--template`Argument určuje aktivační událost funkce (http).

    
    <br/>   
    <details>  
    <summary><strong>Volitelné: kód pro HttpExample. cs</strong></summary>  
    
    *HttpExample. cs* obsahuje `Run` metodu, která přijímá data žádosti v `req` proměnné je [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) , který je upravený pomocí **HttpTriggerAttribute**, který definuje chování triggeru.

    :::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::
        
    Návratový objekt je [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) , který vrací zprávu odpovědi buď jako [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200), nebo jako [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Další informace najdete v tématu [Azure Functions triggerů http a vazeb](./functions-bindings-http-webhook.md?tabs=csharp).  
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


1. Vyberte  <kbd>CTRL + C</kbd> a zvolte <kbd>y</kbd> a zastavte hostitele Functions.

<br/>

---
    
## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. vytvoření podpory prostředků Azure pro vaši funkci

Než budete moct nasadit kód funkce do Azure, musíte vytvořit <abbr title="Logický kontejner pro související prostředky Azure, které můžete spravovat jako jednotku.">skupina prostředků</abbr>, a <abbr title="Účet, který obsahuje všechny datové objekty služby Azure Storage. Účet úložiště poskytuje jedinečný obor názvů pro data úložiště.">účet úložiště</abbr>a a <abbr title="Cloudový prostředek, který hostuje funkce bez serveru v Azure, která poskytuje základní výpočetní prostředí, ve kterém se spouští funkce.">aplikace Function App</abbr> pomocí následujících příkazů:

1. Pokud jste to ještě neudělali, přihlaste se k Azure:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```


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


    ---

    Nemůžete hostovat aplikace pro Linux a Windows ve stejné skupině prostředků. Pokud máte existující skupinu prostředků s názvem `AzureFunctionsQuickstart-rg` aplikace funkcí Windows nebo webové aplikace, musíte použít jinou skupinu prostředků.
    
1. Vytvořte účet pro obecné účely Azure Storage ve vaší skupině prostředků a oblasti:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```


    ---

    Nahraďte `<STORAGE_NAME>` názvem, který je pro vás vhodný. <abbr title="Název musí být jedinečný ve všech účtech úložiště využívaných všemi zákazníky Azure globálně. Například můžete použít kombinaci svého osobního nebo firemního jména, názvu aplikace a číselného identifikátoru, jako v contosobizappstorage20">jedinečné v Azure Storage</abbr>. Názvy musí obsahovat tři až 24 znaků a jenom malá písmena. `Standard_LRS` Určuje účet pro obecné účely, který je [podporován funkcemi](storage-considerations.md#storage-account-requirements).


1. Vytvořte aplikaci Function App v Azure.
**Nahradit** ' <STORAGE_NAME> * * s názvem v předchozím kroku.
**Nahradit** ' <APP_NAME> ' globálně jedinečným názvem.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    
    ---
    
    Nahraďte `<STORAGE_NAME>` názvem účtu, který jste použili v předchozím kroku.

    `<APP_NAME>`Nahraďte <abbr title="Název, který musí být jedinečný ve všech zákaznících Azure globálně. Například můžete použít kombinaci názvu osobního nebo organizace, názvu aplikace a číselného identifikátoru, jako je například contoso-bizapp-Func-20.">jedinečný název</abbr>. `<APP_NAME>` je také výchozí doména DNS pro aplikaci funkcí. 

    <br/>
    <details>
    <summary><strong>Jaké jsou náklady na prostředky zřízené v Azure?</strong></summary>

    Tento příkaz vytvoří aplikaci Function App spuštěnou v zadaném jazykovém modulu runtime v rámci [plánu Azure Functions spotřeby](consumption-plan.md), který je zdarma pro množství využití, které se vám bude účtovat. Příkaz taky zřídí přidruženou instanci služby Azure Application Insights ve stejné skupině prostředků, pomocí které můžete monitorovat aplikaci Function App a zobrazovat protokoly. Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md). Instance nenese žádné náklady, dokud ji neaktivujete.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. nasazení projektu funkce do Azure


**Kopírovat** ' Func Azure funtionapp Publish <APP_NAME> do terminálu **nahraďte** `<APP_NAME>` názvem vaší aplikace.
**Spustit**

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

Zkopírujte úplnou **adresu URL pro vyvolání** zobrazenou ve výstupu `publish` příkazu do adresního řádku prohlížeče. **Přidejte** parametr dotazu **&Name = Functions**. 

![Výstup funkce spuštěné v Azure v prohlížeči](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

<br/>

---

## <a name="8-clean-up-resources"></a>8. vyčištění prostředků

Pokud budete pokračovat k [dalšímu kroku](#next-steps) a přidáte výstup Azure Storage Queue <abbr title="Deklarativní připojení mezi funkcí a dalšími prostředky. Vstupní vazba poskytuje data funkci. výstupní vazba poskytuje data z funkce do jiných prostředků.">vazba</abbr>, zachovejte všechny vaše prostředky na místě, jak budete vytvářet informace o tom, co jste už provedli.

Jinak pomocí následujícího příkazu odstraňte skupinu prostředků a všechny její obsažené prostředky, abyste se vyhnuli vzniku dalších nákladů.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

---

<br/>

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)
