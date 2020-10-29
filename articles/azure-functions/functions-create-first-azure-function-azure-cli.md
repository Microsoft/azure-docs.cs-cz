---
title: Vytvoření funkce v Azure, která reaguje na požadavky HTTP
description: Naučte se vytvořit funkci z příkazového řádku a pak publikovat místní projekt na hostování bez serveru v Azure Functions.
ms.date: 03/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli, devx-track-azurepowershell
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: b7b46f2d280577f40f927a0d8eb6fcf2ed33e04a
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927426"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Rychlý Start: vytvoření funkce v Azure, která reaguje na požadavky HTTP

::: zone pivot="programming-language-csharp"  
V tomto článku pomocí nástrojů příkazového řádku vytvoříte funkci založenou na knihovně jazyka C#, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do prostředí Azure Functions bez serveru. 
::: zone-end  
::: zone pivot="programming-language-javascript"
V tomto článku vytvoříte pomocí nástrojů příkazového řádku funkci JavaScriptu, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do prostředí Azure Functions bez serveru. 
::: zone-end
::: zone pivot="programming-language-typescript"
V tomto článku vytvoříte pomocí nástrojů příkazového řádku funkci TypeScript, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do prostředí Azure Functions bez serveru. 
::: zone-end   
::: zone pivot="programming-language-powershell"
V tomto článku použijete nástroje příkazového řádku k vytvoření funkce PowerShellu, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do prostředí Azure Functions bez serveru. 
::: zone-end  
::: zone pivot="programming-language-python" 
V tomto článku pomocí nástrojů příkazového řádku vytvoříte funkci Pythonu, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do prostředí Azure Functions bez serveru. 
::: zone-end  
::: zone pivot="programming-language-java" 
V tomto článku použijete nástroje příkazového řádku k vytvoření funkce jazyka Java, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do prostředí Azure Functions bez serveru. 
::: zone-end

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
K dispozici je také [Visual Studio Codeá verze](functions-create-first-function-vs-code.md) tohoto článku.
::: zone-end  
::: zone pivot="programming-language-java"  
> [!NOTE]
> Pokud Maven není preferovaným vývojářským nástrojem, Projděte si podobné kurzy pro vývojáře v jazyce Java, které využívají [Gradle](./functions-create-first-java-gradle.md), [IntelliJ](/azure/developer/java/toolkit-for-intellij/quickstart-functions) a [Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-java).
::: zone-end  

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-a-local-function-project"></a>Vytvoření projektu místní funkce

V Azure Functions je projekt funkce kontejnerem pro jednu nebo více jednotlivých funkcí, které každý reaguje na konkrétní Trigger. Všechny funkce v projektu sdílejí stejné místní konfigurace a konfigurace hostování. V této části vytvoříte projekt funkce, který obsahuje jedinou funkci.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Spusťte `func init` příkaz následujícím způsobem a vytvořte projekt Functions ve složce s názvem *LocalFunctionProj* se zadaným modulem runtime:  
::: zone-end  
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
::: zone pivot="programming-language-java"  
Spuštěním následujícího příkazu v prázdné složce vygenerujte projekt Functions z [archetypu Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html). 

> [!IMPORTANT]
> + Použijte, pokud chcete, aby `-DjavaVersion=11` vaše funkce běžely na Java 11. Další informace najdete v tématu [verze Java](functions-reference-java.md#java-versions). 
> + `JAVA_HOME`Aby bylo možné tento článek dokončit, musí být proměnná prostředí nastavena na umístění instalace správné verze JDK.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
```
---

Maven vás vyzve k zadání hodnot potřebných k dokončení generování projektu při nasazení.   
Po zobrazení výzvy zadejte následující hodnoty:

| Výzva | Hodnota | Popis |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Hodnota, která jednoznačně identifikuje váš projekt napříč všemi projekty, podle [pravidel pro pojmenovávání balíčků](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) pro Java. |
| **artifactId** | `fabrikam-functions` | Hodnota, která představuje název jar bez čísla verze. |
| **znění** | `1.0-SNAPSHOT` | Vyberte výchozí hodnotu. |
| **balíček** | `com.fabrikam` | Hodnota, která je balíčkem Java pro vygenerovaný kód funkce. Použijte výchozí hodnotu. |

`Y`Potvrďte zadáním nebo stisknutím klávesy ENTER.

Maven vytvoří soubory projektu v nové složce s názvem _artifactId_ , který je v tomto příkladu `fabrikam-functions` . 

::: zone-end  
Přejděte do složky projektu:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionProj
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
Tato složka obsahuje různé soubory pro projekt, včetně konfiguračních souborů s názvem [local.settings.jsv](functions-run-local.md#local-settings-file) a [host.jsna](functions-host-json.md). Vzhledem k tomu, že *local.settings.json* může obsahovat tajné kódy stažené z Azure, soubor je ve výchozím nastavení vyloučený ze správy zdrojového kódu v souboru *. gitignore* .

[!INCLUDE [functions-cli-add-function](../../includes/functions-cli-add-function.md)]

### <a name="optional-examine-the-file-contents"></a>Volitelné Kontrola obsahu souboru

V případě potřeby můžete přeskočit, aby se [funkce spouštěla místně](#run-the-function-locally) , a prohlédnout si obsah souboru později.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* obsahuje `Run` metodu, která přijímá data požadavku v `req` proměnné, je [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) , který je upraven pomocí **HttpTriggerAttribute** , který definuje chování triggeru. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Návratový objekt je [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) , který vrací zprávu odpovědi buď jako [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200), nebo jako [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Další informace najdete v tématu [Azure Functions triggerů http a vazeb](./functions-bindings-http-webhook.md?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-java"
#### <a name="functionjava"></a>Function. Java
*Function. Java* obsahuje `run` metodu, která přijímá data žádosti v `request` proměnné je [zprávy HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) , který je upravený pomocí anotace [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) , která definuje chování triggeru. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Zpráva odpovědi vygeneruje rozhraní API [HttpResponseMessage. Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) .

#### <a name="pomxml"></a>pom.xml

Nastavení prostředků Azure vytvořených pro hostování vaší aplikace jsou definovaná v **konfiguračním** elementu modulu plug-in s **identifikátorem ID skupiny** `com.microsoft.azure` ve vygenerovaném souboru pom.xml. Například konfigurační prvek níže vydá pokyn nasazení založeného na Maven pro vytvoření aplikace Function App ve `java-functions-group` skupině prostředků v `westus` oblasti. Aplikace Function App se sama spustí ve Windows hostovaném v `java-functions-app-service-plan` plánu, což je ve výchozím nastavení plán spotřeby bez serveru.    

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-102":::

Tato nastavení můžete změnit, abyste mohli řídit, jak se v Azure vytvářejí prostředky, třeba změnou `runtime.os` z `windows` na `linux` před počátečním nasazením. Úplný seznam nastavení podporovaných modulem plug-in Maven najdete v [podrobnostech o konfiguraci](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).

#### <a name="functiontestjava"></a>FunctionTest. Java

Archetype také generuje test jednotek pro vaši funkci. Když změníte funkci pro přidání vazeb nebo přidání nových funkcí do projektu, budete také muset upravit testy v souboru *FunctionTest. Java* .
::: zone-end  
::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init \_ \_ . py

*\_ \_ init \_ \_ . py* obsahuje `main()` funkci Pythonu, která se aktivuje podle konfigurace v *function.js* .

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

U triggeru HTTP funkce přijímá data žádosti v proměnné `req` , jak je definováno v *function.js* . `req` je instancí [třídy Azure. Functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Návratový objekt definovaný jako `$return` in *function.json* , je instancí [třídy Azure. Functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Další informace najdete v tématu [Azure Functions triggerů http a vazeb](./functions-bindings-http-webhook.md?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index.js

*index.js* vyexportuje funkci, která se aktivuje podle konfigurace v *function.jsna* .

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

U triggeru HTTP funkce přijímá data žádosti v proměnné `req` , jak je definováno v *function.js* . Návratový objekt definovaný jako `$return` in *function.json* , je odpověď. Další informace najdete v tématu [Azure Functions triggerů http a vazeb](./functions-bindings-http-webhook.md?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index. TS

*index. TS* exportuje funkci, která se aktivuje podle konfigurace v *function.js* .

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

U triggeru HTTP funkce přijímá data žádosti v proměnné `req` typu **HttpRequest** , jak je definováno v *function.js* . Návratový objekt definovaný jako `$return` in *function.json* , je odpověď. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run.ps1

*run.ps1* definuje skript funkce, který se aktivuje podle konfigurace v *function.jsna* .

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

U triggeru HTTP funkce přijímá data žádosti předaná parametru `$Request` definovanému v *function.js* . Návratový objekt definovaný jako `Response` in *function.json* , se do rutiny předává `Push-OutputBinding` jako odpověď. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*function.jsv* je konfigurační soubor, který definuje vstup a výstup `bindings` funkce, včetně typu triggeru. 
::: zone-end

::: zone pivot="programming-language-python"
`scriptFile`V případě potřeby můžete změnit na vyvolání jiného souboru Pythonu.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Každá vazba vyžaduje směr, typ a jedinečný název. Aktivační událost protokolu HTTP má vstupní vazbu typu [`httpTrigger`](functions-bindings-http-webhook-trigger.md) a výstupní vazby typu [`http`](functions-bindings-http-webhook-output.md) .
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"    
## <a name="create-supporting-azure-resources-for-your-function"></a>Vytvoření podpory prostředků Azure pro vaši funkci

Než budete moct nasadit kód funkce do Azure, musíte vytvořit tři prostředky:

- Skupina prostředků, což je logický kontejner pro související prostředky.
- Účet úložiště, který uchovává stav a další informace o vašich projektech.
- Aplikace Function App, která poskytuje prostředí pro spuštění kódu vaší funkce. Aplikace Function App se mapuje na váš místní projekt funkce a umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

Tyto položky vytvoříte pomocí následujících příkazů Azure CLI. Každý příkaz poskytuje výstup JSON po dokončení.

Pokud jste to ještě neudělali, přihlaste se k Azure pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) :

```azurecli
az login
```
    
Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků s názvem `AzureFunctionsQuickstart-rg` v `westeurope` oblasti. (Obecně vytvoříte skupinu prostředků a prostředky v oblasti blízko vás pomocí dostupné oblasti z `az account list-locations` příkazu.)

```azurecli
az group create --name AzureFunctionsQuickstart-rg --location westeurope
```

> [!NOTE]
> Nemůžete hostovat aplikace pro Linux a Windows ve stejné skupině prostředků. Pokud máte existující skupinu prostředků s názvem `AzureFunctionsQuickstart-rg` aplikace funkcí Windows nebo webové aplikace, musíte použít jinou skupinu prostředků.
 
    
Pomocí příkazu [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) vytvořte účet úložiště pro obecné účely ve vaší skupině prostředků a oblasti. V následujícím příkladu nahraďte `<STORAGE_NAME>` globálně jedinečným názvem vhodným pro vás. Názvy musí obsahovat tři až 24 znaků a jenom malá písmena. `Standard_LRS` Určuje účet pro obecné účely, který je [podporován funkcemi](storage-considerations.md#storage-account-requirements).

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
```

Účet úložiště pro tento rychlý Start vychází jenom z několika centů (USD).
    
Pomocí příkazu [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) vytvořte aplikaci Function App. V následujícím příkladu nahraďte `<STORAGE_NAME>` názvem účtu, který jste použili v předchozím kroku, a nahraďte `<APP_NAME>` ho globálně jedinečným názvem, který je pro vás vhodný. `<APP_NAME>` je také výchozí doména DNS pro aplikaci funkcí. 
::: zone-end  

::: zone pivot="programming-language-python"  
Pokud používáte Python 3,8, změňte `--runtime-version` na `3.8` a `--functions_version` na `3` .

Pokud používáte Python 3,6, změňte `--runtime-version` na `3.6` .

```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Pokud používáte Node.js 8, změňte také `--runtime-version` na `8` .


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

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
Tento příkaz vytvoří aplikaci Function App spuštěnou v zadaném jazykovém modulu runtime v rámci [plánu Azure Functions spotřeby](functions-scale.md#consumption-plan), který je zdarma pro množství využití, které se vám bude účtovat. Příkaz taky zřídí přidruženou instanci služby Azure Application Insights ve stejné skupině prostředků, pomocí které můžete monitorovat aplikaci Function App a zobrazovat protokoly. Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md). Instance nenese žádné náklady, dokud ji neaktivujete.
    
## <a name="deploy-the-function-project-to-azure"></a>Nasazení projektu funkce do Azure
::: zone-end  

::: zone pivot="programming-language-typescript"  
Před použitím základních nástrojů k nasazení projektu do Azure vytvoříte sestavení JavaScriptu připravené pro produkční soubory z zdrojových souborů TypeScriptu.

Následující příkaz připraví projekt TypeScript k nasazení:

```
npm run build:production 
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
V případě nezbytných prostředků teď budete připraveni nasadit projekt místní funkce do aplikace Function App v Azure pomocí příkazu [Func Azure functionapp Publish](functions-run-local.md#project-file-deployment) . V následujícím příkladu nahraďte `<APP_NAME>` názvem vaší aplikace.

```
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

::: zone-end  
::: zone pivot="programming-language-java"  
## <a name="deploy-the-function-project-to-azure"></a>Nasazení projektu funkce do Azure

Aplikace Function App a související prostředky se vytvoří v Azure při prvním nasazení projektu Functions. Nastavení prostředků Azure vytvořených pro hostování vaší aplikace jsou definovaná v [ souborupom.xml](#pomxml). V tomto článku přijměte výchozí hodnoty.

> [!TIP]
> Chcete-li vytvořit aplikaci Function App běžící na systému Linux namísto systému Windows, změňte `runtime.os` prvek v souboru pom.xml z `windows` na `linux` . V [těchto oblastech](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions)se podporuje spuštění Linux v plánu spotřeby. Nemůžete mít aplikace spuštěné v systému Linux a aplikace, které běží ve Windows ve stejné skupině prostředků.

Než budete moct nasadit, přihlaste se k předplatnému Azure pomocí příkazu [AZ Login](/cli/azure/authenticate-azure-cli) Azure CLI. 

```azurecli
az login
```

K nasazení projektu do nové aplikace Function App použijte následující příkaz. 

```
mvn azure-functions:deploy
```

Tím se v Azure vytvoří následující prostředky:

+ Skupina prostředků. Název jako _Java-Functions-Group_ .
+ Účet úložiště. Požadováno funkcemi. Název se vygeneruje náhodně na základě požadavků na název účtu úložiště.
+ Plán hostování. Hostování bez serveru pro aplikaci Function App v oblasti _westus_ Název je _Java-Functions-App-Service-Plan_ .
+ Aplikace Function App Function App je jednotka pro nasazení a spouštění pro vaše funkce. Název se náhodně generuje na základě vašeho _artifactId_ a připojuje se k náhodně vygenerovanému číslu. 

Nasazení zabalí soubory projektu a nasadí je do nové aplikace Function App pomocí [nasazení zip](functions-deployment-technologies.md#zip-deploy). Kód se spouští z balíčku pro nasazení v Azure.
::: zone-end

## <a name="invoke-the-function-on-azure"></a>Vyvolání funkce v Azure

Vzhledem k tomu, že funkce používá Trigger HTTP, vyvoláte ji pomocí požadavku HTTP na adresu URL v prohlížeči nebo pomocí nástroje, jako je například kudrlinkou. V obou instancích `code` je parametr adresy URL jedinečný [klíč funkcí](functions-bindings-http-webhook-trigger.md#authorization-keys) , který autorizuje vyvolání koncového bodu funkce.

# <a name="browser"></a>[Prohlížeč](#tab/browser)

Zkopírujte úplnou **adresu URL pro vyvolání** zobrazenou ve výstupu příkazu publikovat do adresního řádku prohlížeče a přidejte parametr dotazu `&name=Functions` . V prohlížeči by se měl zobrazit podobný výstup jako při spuštění funkce místně.

![Výstup funkce spuštěné v Azure v prohlížeči](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[Curl](#tab/curl)

Spusťte [`curl`](https://curl.haxx.se/) s **adresou URL vyvolání** a přidejte parametr `&name=Functions` . Výstupem příkazu by měl být text "Hello".

![Výstup funkce spuštěný v Azure pomocí kudrlinkou](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> K zobrazení protokolů téměř v reálném čase pro publikovanou aplikaci Function App použijte [Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs).
>
> Spuštěním následujícího příkazu otevřete v prohlížeči aktivní datový proud metrik.
>   ```
>   func azure functionapp logstream <APP_NAME> --browser
>   ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kroku, [přidejte výstupní vazbu fronty Azure Storage](functions-add-output-binding-storage-queue-cli.md), zachovejte všechny prostředky na místě, jak budete vytvářet informace o tom, co jste už provedli.

Jinak pomocí následujícího příkazu odstraňte skupinu prostředků a všechny její obsažené prostředky, abyste se vyhnuli vzniku dalších nákladů.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end
::: zone pivot="programming-language-java"
```azurecli
az group delete --name java-functions-group
```
::: zone-end
::: zone pivot="programming-language-python"
Pokud chcete virtuální prostředí ukončit, spusťte příkaz `deactivate` .
::: zone-end

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage](functions-add-output-binding-storage-queue-cli.md)
 
