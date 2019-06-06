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
ms.openlocfilehash: ea890a49fc7b6a153ece183b153f5bc2a49760b5
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496569"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Vytvoření funkce aktivované protokolem HTTP v Azure

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

V tomto článku se dozvíte, jak používat nástroje příkazového řádku k vytvoření projektu Pythonu, na kterém běží ve službě Azure Functions. Funkce, které vytvoříte se aktivuje požadavky HTTP. Nakonec publikování projektu pro spuštění jako [funkci bez serveru](functions-scale.md#consumption-plan) v Azure.

Tento článek je první ze dvou šablon rychlý start pro Azure Functions. Po dokončení tohoto článku můžete [přidání služby Azure Storage Výstupní vazba fronty](functions-add-output-binding-storage-queue-python.md) vaší funkce.

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte mít následující:

+ Nainstalujte [Python 3.6](https://www.python.org/downloads/).

+ Nainstalujte [nástrojů Azure Functions Core](./functions-run-local.md#v2) verze 2.6.666 nebo novější.

+ Nainstalujte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) verze 2.x nebo novější.

+ Aktivní předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Vytvoření a aktivace virtuální prostředí

Pro místní vývoj a testování funkce Pythonu, musí pracovat v prostředí Python 3.6. Spusťte následující příkazy k vytvoření a aktivace do virtuálního prostředí s názvem `.env`.

### <a name="bash-or-a-terminal-window"></a>Bash nebo okno terminálu:

```bash
python3.6 -m venv .env
source .env/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>Nebo příkazový řádek Windows Powershellu:

```powershell
py -3.6 -m venv .env
.env\scripts\activate
```

Zbývající příkazy jsou spuštěná ve virtuálním prostředí.

## <a name="create-a-local-functions-project"></a>Vytvořte projekt místní funkce

Projekt Functions je ekvivalentem aplikaci function app v Azure. Může mít více funkcí, které sdílejí stejnou konfiguraci místní a hostování.

Ve virtuálním prostředí, spusťte následující příkaz, výběr **python** jako váš modul runtime pracovního procesu.

```command
func init MyFunctionProj
```

Složka s názvem _MyFunctionProj_ se vytvoří, který obsahuje následující tři soubory:

* `local.settings.json` slouží k ukládání nastavení aplikace a připojovacích řetězců při místním spuštění. Tento soubor není publikován do Azure.
* `requirements.txt` obsahuje seznam balíčků, které mají být nainstalována na publikování na platformě Azure.
* `host.json` obsahuje možnosti globální konfigurace, které ovlivňují všechny funkce v aplikaci function app. Tento soubor je publikován do Azure.

Přejděte do nové složky MyFunctionProj:

```command
cd MyFunctionProj
```

Dál aktualizujte soubor host.json povolit rozšíření sady.  

## <a name="reference-bindings"></a>Odkaz na vazby

Rozšíření sady usnadňuje přidat rozšíření vazby dolů cestách. Je také eliminuje nutnost instalace .NET Core 2.x SDK. Rozšíření sady vyžaduje verzi 2.6.1071 základní nástroje nebo vyšší verze. 

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Teď můžete přidat funkce do vašeho projektu.

## <a name="create-a-function"></a>Vytvoření funkce

Přidání funkce do vašeho projektu, spusťte následující příkaz:

```command
func new
```

Zvolte **triggeru HTTP** šablony, zadejte `HttpTrigger` jako název pro tuto funkci, stiskněte klávesu Enter.

Podsložku s názvem _HttpTrigger_ se vytvoří, který obsahuje následující soubory:

* **Function.JSON**: konfigurační soubor, který definuje funkci, aktivační události a dalších vazeb. Přečtěte si tento soubor a Všimněte si, že hodnota `scriptFile` odkazuje na soubor obsahující tuto funkci při vyvolání aktivační události a vazby jsou definovány v `bindings` pole.

  Každá vazba vyžaduje směr, typ a jedinečný název. HTTP trigger má vstupní vazby typu [ `httpTrigger` ](functions-bindings-http-webhook.md#trigger) a výstupní vazby typu [ `http` ](functions-bindings-http-webhook.md#output).

* **\_\_init\_\_.py**: soubor skriptu, který je protokolu HTTP aktivované funkce. Tento skript zkontrolovat a zjistit, zda obsahuje výchozí `main()`. Data protokolu HTTP z triggeru je předán do této funkce pomocí `req` s názvem parametru vazby. Definované v function.json, `req` je instance [azure.functions.HttpRequest třídy](/python/api/azure-functions/azure.functions.httprequest). 

    Vrácený objekt definovaný jako `$return` v function.json, je instance [azure.functions.HttpResponse třídy](/python/api/azure-functions/azure.functions.httpresponse). Další informace najdete v tématu [aktivace protokolu HTTP služby Azure Functions a vazby](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Následující příkaz spustí aplikace function app, který se spouští místně s použitím stejného modulu runtime Azure Functions, který je v Azure.

```bash
func host start
```

Při spuštění hostitele funkce zapíše něco jako následující výstup, který byl zkrácen pro lepší čitelnost:

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

## <a name="create-a-function-app-in-azure"></a>Vytvoření aplikace function app v Azure

Aplikace function app poskytuje prostředí pro provádění kódu funkce. Umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

Spusťte následující příkaz s názvem aplikace jedinečné funkce místo `<APP_NAME>` zástupný text a účet úložiště název `<STORAGE_NAME>`. `<APP_NAME>` je také výchozí doména DNS pro aplikaci funkcí. Tento název musí být jedinečný mezi všemi aplikacemi v Azure.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

> [!NOTE]
> Azure Functions, plán Consumption pro Linux je aktuálně ve verzi preview a je k dispozici pouze v následujících oblastech: Západní USA, východní USA, západní Evropa, jihovýchodní Asie. Kromě toho aplikace pro Linux a Windows nemůže být hostovaná ve stejné skupině prostředků. Pokud máte existující skupinu prostředků s názvem `myResourceGroup` aplikace funkcí Windows nebo webové aplikace, musíte použít jinou skupinu prostředků.

Nyní jste připraveni publikovat projekt lokální funkce do aplikace function app v Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="next-steps"></a>Další postup

Jste vytvořili projekt functions Python s funkci aktivovanou protokolem HTTP, spusťte ho na místním počítači a nasadili ji do Azure. Teď rozšiřte vaše funkce...

> [!div class="nextstepaction"]
> [Přidání služby Azure Storage Výstupní vazba fronty](functions-add-output-binding-storage-queue-python.md)
