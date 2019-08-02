---
title: Vytvoření funkce aktivované protokolem HTTP v Azure
description: Zjistěte, jak vytvořit první funkce Pythonu v Azure pomocí Azure Functions Core Tools a rozhraní příkazového řádku Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 58f5cfd3718720cafc922bbd7b974a353e0d9d02
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722785"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Vytvoření funkce aktivované protokolem HTTP v Azure

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

V tomto článku se dozvíte, jak pomocí nástrojů příkazového řádku vytvořit projekt v Pythonu, který běží v Azure Functions. Vytvořená funkce je aktivována požadavky HTTP. Nakonec publikujete projekt tak, aby běžel jako [funkce bez serveru](functions-scale.md#consumption-plan) v Azure.

Tento článek je první ze dvou rychlých startů pro Azure Functions. Po dokončení tohoto článku přidáte do své funkce [výstupní vazbu Azure Storage fronty](functions-add-output-binding-storage-queue-python.md) .

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte mít následující:

+ Nainstalujte [Python 3,6](https://www.python.org/downloads/).

+ Nainstalujte verzi [Azure Functions Core Tools](./functions-run-local.md#v2) 2.6.1071 nebo novější.

+ Nainstalujte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) verze 2. x nebo novější verzi.

+ Aktivní předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Vytvoření a aktivace virtuální prostředí

Aby bylo možné místně vyvíjet a testovat funkce Pythonu, je nutné pracovat v prostředí Python 3,6. Spusťte následující příkazy k vytvoření a aktivace do virtuálního prostředí s názvem `.venv`.

### <a name="bash"></a>Bash

```bash
python3.6 -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell nebo příkazový řádek systému Windows:

```powershell
py -3.6 -m venv .venv
.venv\scripts\activate
```

Zbývající příkazy se spustí ve virtuálním prostředí.

## <a name="create-a-local-functions-project"></a>Vytvořte projekt místní funkce

Projekt Functions je ekvivalentem aplikace Function App v Azure. Může mít několik funkcí, které všechny sdílí stejné místní a hostitelské konfigurace.

Ve virtuálním prostředí spusťte následující příkaz a jako modul runtime pracovního procesu zvolte **Python** .

```console
func init MyFunctionProj
```

Vytvoří se složka s názvem _MyFunctionProj_ , která obsahuje následující tři soubory:

* `local.settings.json`slouží k ukládání nastavení aplikace a připojovacích řetězců při místním spuštění. Tento soubor se nepublikuje do Azure.
* `requirements.txt`obsahuje seznam balíčků, které mají být nainstalovány při publikování do Azure.
* `host.json`obsahuje možnosti globální konfigurace, které ovlivňují všechny funkce aplikace Function App. Tento soubor se publikuje do Azure.

Přejděte do nové složky MyFunctionProj:

```console
cd MyFunctionProj
```

V dalším kroku aktualizujete soubor host. JSON, aby se povolily sady rozšíření.  

## <a name="create-a-function"></a>Vytvoření funkce

Chcete-li do projektu přidat funkci, spusťte následující příkaz:

```console
func new
```

Zvolte šablonu **triggeru http** , zadejte `HttpTrigger` název funkce a potom stiskněte klávesu ENTER.

Vytvoří se podsložka s názvem _HttpTrigger_ , která obsahuje následující soubory:

* **Function. JSON**: konfigurační soubor, který definuje funkci, Trigger a další vazby. Zkontrolujte tento soubor a podívejte se, že hodnota `scriptFile` odkazuje na soubor, který obsahuje funkci, zatímco Trigger a vazby volání jsou definované `bindings` v poli.

  Každá vazba vyžaduje směr, typ a jedinečný název. Aktivační událost protokolu HTTP má vstupní vazbu [`httpTrigger`](functions-bindings-http-webhook.md#trigger) typu a výstupní vazby typu. [`http`](functions-bindings-http-webhook.md#output)

* **init.py\_: soubor skriptu, který je funkcí aktivovanými protokolem HTTP.\_ \_ \_** Zkontrolujte tento skript a podívejte se, že obsahuje výchozí `main()`hodnotu. Data protokolu HTTP z triggeru jsou předána této funkci pomocí `req` pojmenovaného parametru vazby. Definováno v Function. JSON `req` je instance [třídy Azure. Functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    Návratový objekt definovaný jako `$return` v Function. JSON je instancí [třídy Azure. Functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Další informace najdete v tématu [Azure Functions triggerů http a vazeb](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Následující příkaz spustí aplikaci Function App, která se spouští místně pomocí stejného Azure Functions modulu runtime, který je v Azure.

```bash
func host start
```

Jakmile se hostitel funkce spustí, zapíše něco podobného následujícímu výstupu, který byl zkrácen pro čitelnost:

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

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Zkopírujte adresu URL vaší funkce `HttpTrigger` z výstupu modulu runtime a vložte do panelu Adresa vašeho prohlížeče. K této adrese URL připojte řetězec dotazu `?name=<yourname>` a proveďte požadavek. Následuje ukázka odezvy na požadavek GET vrácené místní funkcí v prohlížeči:

![Místní test v prohlížeči](./media/functions-create-first-function-python/function-test-local-browser.png)

Teď, když jste spustili funkci místně, můžete vytvořit aplikaci aplikace funkcí a ostatní požadované prostředky v Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Vytvoření aplikace Function App v Azure

Aplikace Function App poskytuje prostředí pro provádění kódu vaší funkce. Umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

Spusťte následující příkaz s názvem aplikace jedinečné funkce místo `<APP_NAME>` zástupný text a účet úložiště název `<STORAGE_NAME>`. `<APP_NAME>` je také výchozí doména DNS pro aplikaci funkcí. Tento název musí být jedinečný mezi všemi aplikacemi v Azure.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

> [!NOTE]
> Azure Functions, plán spotřeby pro Linux je teď ve verzi Preview a dostupný jenom v následujících oblastech: Západní USA, Východní USA, Západní Evropa Východní Asie. Aplikace pro Linux a Windows se navíc nedají hostovat ve stejné skupině prostředků. Pokud máte existující skupinu prostředků s názvem `myResourceGroup` aplikace funkcí Windows nebo webové aplikace, musíte použít jinou skupinu prostředků.

Teď jste připraveni publikovat projekt místních funkcí do aplikace Function App v Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="next-steps"></a>Další postup

Vytvořili jste projekt funkcí Pythonu s funkcí aktivovaným protokolem HTTP, spustíte ji na místním počítači a nasadíte ji do Azure. Nyní svou funkci rozšíříte do...

> [!div class="nextstepaction"]
> [Přidání výstupní vazby fronty Azure Storage](functions-add-output-binding-storage-queue-python.md)
