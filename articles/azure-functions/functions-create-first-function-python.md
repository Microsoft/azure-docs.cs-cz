---
title: Vytvoření funkce aktivované protokolem HTTP v Azure
description: Zjistěte, jak vytvořit první funkce Pythonu v Azure pomocí Azure Functions Core Tools a rozhraní příkazového řádku Azure.
author: ggailey777
ms.author: glenga
ms.date: 09/11/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: 03b8e12d63ba84b4e20d7263f1c2ecb8d912936d
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203148"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Vytvoření funkce aktivované protokolem HTTP v Azure

V tomto článku se dozvíte, jak pomocí nástrojů příkazového řádku vytvořit projekt v Pythonu, který běží v Azure Functions. Můžete také vytvořit funkci, která se aktivuje požadavkem HTTP. Nakonec publikujete projekt tak, aby běžel jako [funkce bez serveru](functions-scale.md#consumption-plan) v Azure.

Tento článek je první ze dvou rychlých startů Pythonu pro Azure Functions. Po dokončení tohoto rychlého startu můžete do své funkce [Přidat výstupní vazbu Azure Storage fronty](functions-add-output-binding-storage-queue-python.md) .

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte:

+ Nainstalujte [Python 3.6. x](https://www.python.org/downloads/).

+ Nainstalujte verzi [Azure Functions Core Tools](./functions-run-local.md#v2) 2.7.1575 nebo novější.

+ Nainstalujte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) verze 2. x nebo novější verzi.

+ Máte aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>Vytvoření a aktivace virtuálního prostředí (volitelné)

K místnímu vývoji funkcí Pythonu byste měli použít prostředí Python 3.6. x. Spusťte následující příkazy k vytvoření a aktivace do virtuálního prostředí s názvem `.venv`.

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

Teď, když jste aktivovali virtuální prostředí, spusťte zbývající příkazy. Pokud se chcete dostat z virtuálního prostředí, spusťte `deactivate`příkaz.

## <a name="create-a-local-functions-project"></a>Vytvoření projektu místní funkce

Projekt Functions je ekvivalentem aplikace Function App v Azure. Může mít několik funkcí, které všechny sdílí stejné místní a hostitelské konfigurace.

1. Ve virtuálním prostředí spusťte následující příkaz:

    ```console
    func init MyFunctionProj
    ```

1. Jako modul runtime pracovního procesu vyberte **Python** .

    Příkaz vytvoří složku _MyFunctionProj_ . Obsahuje tyto tři soubory:

    * *Local. Settings. JSON*: používá se k ukládání nastavení aplikace a připojovacích řetězců při místním spuštění. Tento soubor se nepublikuje do Azure.
    * *požadavky. txt*: obsahuje seznam balíčků, které bude systém instalovat při publikování do Azure.
    * *Host. JSON*: obsahuje možnosti globální konfigurace, které ovlivňují všechny funkce aplikace Function App. Tento soubor se publikuje do Azure.

1. Přejít do nové složky *MyFunctionProj* :

    ```console
    cd MyFunctionProj
    ```

## <a name="create-a-function"></a>Vytvoření funkce

Přidejte funkci do nového projektu.

1. Chcete-li do projektu přidat funkci, spusťte následující příkaz:

    ```console
    func new
    ```

1. Pomocí šipky dolů vyberte šablonu **triggeru http** .

1. Po zobrazení výzvy k zadání názvu funkce zadejte *HttpTrigger* a stiskněte klávesu ENTER.

Tyto příkazy vytvoří podsložku s názvem _HttpTrigger_. Obsahuje následující soubory:

* *Function. JSON*: konfigurační soubor, který definuje funkci, Trigger a další vazby. Všimněte si, že v tomto souboru hodnota `scriptFile` odkazuje na soubor, který obsahuje funkci, `bindings` a pole definuje Trigger a vazby volání.

    Každá vazba vyžaduje směr, typ a jedinečný název. Aktivační událost protokolu HTTP má vstupní vazbu [`httpTrigger`](functions-bindings-http-webhook.md#trigger) typu a výstupní vazby typu. [`http`](functions-bindings-http-webhook.md#output)

* *init.py\_: soubor skriptu, který je funkcí aktivovanými protokolem HTTP.\_ \_ \_* Všimněte si, že tento skript má `main()`výchozí hodnotu. Data protokolu HTTP z aktivační události přecházejí do funkce pomocí `req` pojmenovaného `binding parameter`. Třída, která je definována v Function. JSON, je instancí [třídy Azure. Functions. HttpRequest.](/python/api/azure-functions/azure.functions.httprequest) `req` 

    Návratový objekt definovaný jako `$return` v *Function. JSON*je instancí [třídy Azure. Functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Další informace najdete v tématu [Azure Functions triggerů http a vazeb](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Funkce se spouští místně pomocí modulu runtime Azure Functions.

1. Tento příkaz spustí aplikaci Function App:

    ```console
    func host start
    ```

    Když se spustí hostitel Azure Functions, zapíše něco podobného následujícímu výstupu. Tady se zkrátí, abyste si ho mohli lépe přečíst:

    ```output
    
                      %%%%%%
                     %%%%%%
                @   %%%%%%    @
              @@   %%%%%%      @@
           @@@    %%%%%%%%%%%    @@@
         @@      %%%%%%%%%%        @@
           @@         %%%%       @@
             @@      %%%       @@
               @@    %%      @@
                    %%
                    %
    
    ...
    
    Content root path: C:\functions\MyFunctionProj
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    ...
    
    Http Functions:
    
            HttpTrigger: http://localhost:7071/api/HttpTrigger
    
    [8/27/2018 10:38:27 PM] Host started (29486ms)
    [8/27/2018 10:38:27 PM] Job host started
    ```

1. Zkopírujte adresu URL vaší funkce `HttpTrigger` z výstupu modulu runtime a vložte do panelu Adresa vašeho prohlížeče.

1. K této adrese URL připojte řetězec dotazu `?name=<yourname>` a proveďte požadavek. Následující snímek obrazovky ukazuje odpověď na požadavek GET, který místní funkce vrátí do prohlížeče:

    ![Místní ověření v prohlížeči](./media/functions-create-first-function-python/function-test-local-browser.png)

1. Pro ukončení aplikace Function App vyberte CTRL + C.

Teď, když jste spustili funkci místně, můžete vytvořit aplikaci aplikace funkcí a ostatní požadované prostředky v Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Vytvoření aplikace Function App v Azure

Aplikace Function App poskytuje prostředí pro provádění kódu vaší funkce. Umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

Spusťte následující příkaz. Nahraďte `<APP_NAME>` jedinečným názvem aplikace Function App. Nahraďte `<STORAGE_NAME>` názvem účtu úložiště. `<APP_NAME>` je také výchozí doména DNS pro aplikaci funkcí. Tento název musí být jedinečný mezi všemi aplikacemi v Azure.

> [!NOTE]
> Nemůžete hostovat aplikace pro Linux a Windows ve stejné skupině prostředků. Pokud máte existující skupinu prostředků s názvem `myResourceGroup` aplikace funkcí Windows nebo webové aplikace, musíte použít jinou skupinu prostředků.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

Předchozí příkaz také zřídí přidruženou instanci služby Azure Application Insights ve stejné skupině prostředků. Tuto instanci můžete použít k monitorování aplikace Function App a zobrazení protokolů.

Teď jste připraveni publikovat projekt místních funkcí do aplikace Function App v Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Nasazení projektu aplikace funkcí do Azure

Po vytvoření aplikace Function App v Azure můžete pomocí příkazu [Func Azure functionapp Publish](functions-run-local.md#project-file-deployment) Core Tools nasadit kód projektu do Azure. V tomto příkladu nahraďte `<APP_NAME>` názvem vaší aplikace.

```console
func azure functionapp publish <APP_NAME> --build remote
```

`--build remote` Možnost umožňuje v Azure vzdáleně sestavit projekt Pythonu ze souborů v balíčku pro nasazení. 

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

Můžete zkopírovat `Invoke url` hodnotu pro svůj `HttpTrigger` a použít ji k ověření vaší funkce v Azure. Adresa URL obsahuje `code` hodnotu řetězce dotazu, který je klíčovou funkcí, což ztěžuje ostatním uživatelům volat koncový bod triggeru http v Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> Pokud chcete zobrazit protokoly téměř v reálném čase pro publikovanou aplikaci v Pythonu, použijte [Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs).

## <a name="next-steps"></a>Další kroky

Vytvořili jste projekt funkcí Pythonu s funkcí aktivovaným protokolem HTTP, spustíte ji na místním počítači a nasadíte ji do Azure. Nyní svou funkci rozšíříte do...

> [!div class="nextstepaction"]
> [Přidání výstupní vazby fronty Azure Storage](functions-add-output-binding-storage-queue-python.md)
