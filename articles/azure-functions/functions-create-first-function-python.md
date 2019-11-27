---
title: Vytvoření funkce Python aktivované protokolem HTTP v Azure
description: Zjistěte, jak vytvořit první funkce Pythonu v Azure pomocí Azure Functions Core Tools a rozhraní příkazového řádku Azure.
ms.date: 11/07/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 18ae1ed000ffe61ce1ea9ff5c18aae98a0ffae65
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227195"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Rychlý Start: vytvoření funkce Python aktivované protokolem HTTP v Azure

V tomto článku se dozvíte, jak pomocí nástrojů příkazového řádku vytvořit projekt v Pythonu, který běží v Azure Functions. Můžete také vytvořit funkci, která se aktivuje požadavkem HTTP. Po spuštění místně publikujete projekt, aby se spouštěl jako [funkce bez serveru](functions-scale.md#consumption-plan) v Azure. 

Tento článek je první ze dvou rychlých startů Pythonu pro Azure Functions. Po dokončení tohoto rychlého startu můžete do své funkce [Přidat výstupní vazbu Azure Storage fronty](functions-add-output-binding-storage-queue-python.md) .

K dispozici je také [Visual Studio Codeá verze](/azure/python/tutorial-vs-code-serverless-python-01) tohoto článku.

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte:

+ Nainstalujte [Python 3.7.4](https://www.python.org/downloads/). Tato verze Pythonu se ověřuje pomocí funkcí. Python 3,8 a novější verze se zatím nepodporují.

+ Nainstalujte verzi [Azure Functions Core Tools](./functions-run-local.md#v2) 2.7.1846 nebo novější.

+ Nainstalujte verzi [Azure CLI](/cli/azure/install-azure-cli) 2.0.76 nebo novější.

+ Máte aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Vytvoření a aktivace virtuální prostředí

Prostředí Python 3,7 byste měli použít k místnímu vývoji funkcí Pythonu. Spuštěním následujících příkazů vytvořte a aktivujte virtuální prostředí s názvem `.venv`.

> [!NOTE]
> Pokud Python nenainstaloval venv do distribuce pro Linux, můžete ho nainstalovat pomocí následujícího příkazu:
> ```command
> sudo apt-get install python3-venv

### <a name="bash"></a>Bash

```bash
python -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell nebo příkazový řádek systému Windows:

```powershell
py -m venv .venv
.venv\scripts\activate
```

Teď, když jste aktivovali virtuální prostředí, spusťte zbývající příkazy. Pokud se chcete dostat z virtuálního prostředí, spusťte `deactivate`.

## <a name="create-a-local-functions-project"></a>Vytvoření projektu místní funkce

Projekt Functions může mít více funkcí, které mají všechny sdílet stejné místní a hostitelské konfigurace.

Ve virtuálním prostředí spusťte následující příkazy:

```console
func init MyFunctionProj --python
cd MyFunctionProj
```

Příkaz `func init` vytvoří složku _MyFunctionProj_ . Projekt Pythonu v této složce ještě nemá žádné funkce. Přidáte je dál.

## <a name="create-a-function"></a>Vytvoření funkce

Chcete-li do projektu přidat funkci, spusťte následující příkaz:

```console
func new --name HttpTrigger --template "HTTP trigger"
```

Tento příkaz vytvoří podsložku s názvem _HttpTrigger_, která obsahuje následující soubory:

* *Function. JSON*: konfigurační soubor, který definuje funkci, Trigger a další vazby. Všimněte si, že v tomto souboru hodnota `scriptFile` odkazuje na soubor obsahující funkci a pole `bindings` definuje Trigger vyvolání a vazby.

    Každá vazba vyžaduje směr, typ a jedinečný název. Aktivační událost HTTP má vstupní vazbu typu [`httpTrigger`](functions-bindings-http-webhook.md#trigger) a výstupní vazba typu [`http`](functions-bindings-http-webhook.md#output).

* *\_\_init\_\_. py*: soubor skriptu, který je funkcí AKTIVOVANÝmi protokolem HTTP. Všimněte si, že tento skript má výchozí `main()`. Data protokolu HTTP z aktivační události přecházejí do funkce pomocí `req` s názvem `binding parameter`. `req`, která je definována v Function. JSON, je instancí [třídy Azure. Functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    Návratový objekt definovaný jako `$return` v *Function. JSON*je instancí [třídy Azure. Functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Další informace najdete v tématu [Azure Functions triggerů http a vazeb](functions-bindings-http-webhook.md).

Nyní můžete spustit novou funkci na místním počítači.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Tento příkaz spustí aplikaci Function App s použitím modulu Azure Functions runtime (Func. exe):

```console
func host start
```

Měli byste vidět následující informace zapsané do výstupu:

```output
Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger    
```

Zkopírujte z tohoto výstupu adresu URL funkce `HttpTrigger` a vložte ji do adresního řádku prohlížeče. K této adrese URL připojte řetězec dotazu `?name=<yourname>` a proveďte požadavek. Následující snímek obrazovky ukazuje odpověď na požadavek GET, který místní funkce vrátí do prohlížeče:

![Místní ověření v prohlížeči](./media/functions-create-first-function-python/function-test-local-browser.png)

Pro vypnutí provádění aplikace Function App použijte kombinaci kláves CTRL + C.

Teď, když máte funkci spuštěnou místně, můžete do Azure nasadit kód vaší funkce.  
Než budete moct aplikaci nasadit, budete muset vytvořit nějaké prostředky Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Vytvoření aplikace Function App v Azure

Aplikace Function App poskytuje prostředí pro provádění kódu vaší funkce. Umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. 

Spusťte následující příkaz. Nahraďte `<APP_NAME>` jedinečným názvem aplikace Function App. Nahraďte `<STORAGE_NAME>` názvem účtu úložiště. `<APP_NAME>` je také výchozí doména DNS pro aplikaci funkcí. Tento název musí být jedinečný mezi všemi aplikacemi v Azure.

> [!NOTE]
> Nemůžete hostovat aplikace pro Linux a Windows ve stejné skupině prostředků. Pokud máte existující skupinu prostředků s názvem `myResourceGroup` s aplikací Function App nebo webovou aplikací Windows, musíte použít jinou skupinu prostředků.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python --runtime-version 3.7 \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

Předchozí příkaz vytvoří aplikaci funkcí běžící v Pythonu 3.7.4. Také zřídí přidruženou instanci služby Azure Application Insights ve stejné skupině prostředků. Tuto instanci můžete použít k monitorování aplikace Function App a zobrazení protokolů. 

Teď jste připraveni publikovat projekt místních funkcí do aplikace Function App v Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Nasazení projektu aplikace funkcí do Azure

Po vytvoření aplikace Function App v Azure můžete pomocí příkazu [Func Azure functionapp Publish](functions-run-local.md#project-file-deployment) Core Tools nasadit kód projektu do Azure. V tomto příkladu nahraďte `<APP_NAME>` názvem vaší aplikace.

```console
func azure functionapp publish <APP_NAME> --build remote
```

Možnost `--build remote` sestaví projekt Python vzdáleně v Azure ze souborů v balíčku pro nasazení, který se doporučuje. 

Zobrazí se výstup podobný následující zprávě. Tady se zkrátí, abyste si ho mohli lépe přečíst:

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Můžete zkopírovat `Invoke url` hodnotu pro `HttpTrigger` a použít ji k ověření vaší funkce v Azure. Adresa URL obsahuje `code` hodnotu řetězce dotazu, který je klíčovou funkcí, což ztěžuje ostatním uživatelům volat koncový bod triggeru HTTP v Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> Pokud chcete zobrazit protokoly téměř v reálném čase pro publikovanou aplikaci v Pythonu, použijte [Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs).

## <a name="next-steps"></a>Další kroky

Vytvořili jste projekt funkcí Pythonu s funkcí aktivovaným protokolem HTTP, spustíte ji na místním počítači a nasadíte ji do Azure. Nyní svou funkci rozšíříte do...

> [!div class="nextstepaction"]
> [Přidání výstupní vazby fronty Azure Storage](functions-add-output-binding-storage-queue-python.md)
