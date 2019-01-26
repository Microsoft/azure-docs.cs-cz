---
title: Vytvoření první funkce Pythonu v Azure
description: Zjistěte, jak vytvořit první funkce Pythonu v Azure pomocí Azure Functions Core Tools a rozhraní příkazového řádku Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 08/29/2018
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 631fc4e613311df9386084408ead625f6d018151
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54911886"
---
# <a name="create-your-first-python-function-in-azure-preview"></a>Vytvoření první funkce Pythonu v Azure (preview)

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

Tento článek Rychlý start vás provede jak používat rozhraní příkazového řádku Azure k vytvoření vaší první [bez serveru](https://azure.com/serverless) aplikace běžící na Linuxu funkce Pythonu. Kód funkce se vytvoří místně a pak se nasadí do Azure pomocí [Azure Functions Core Tools](functions-run-local.md). Další informace o aspektech týkajících se ve verzi preview pro spuštění vaší aplikace function App v Linuxu najdete v tématu [to funguje v systému Linux článku](https://aka.ms/funclinux).

Následující kroky se podporují na počítačích se systémem Mac, Windows a Linux.

## <a name="prerequisites"></a>Požadavky

Pro vytváření a testování místně, budete muset:

+ Nainstalujte [Python 3.6](https://www.python.org/downloads/).

+ Nainstalujte [nástrojů Azure Functions Core](functions-run-local.md#v2) verze 2.2.70 nebo novější (vyžaduje .NET Core 2.x SDK).

Publikovat a spustit v Azure:

+ Nainstalujte [rozhraní příkazového řádku Azure]( /cli/azure/install-azure-cli) verze 2.x nebo novější.

+ Budete potřebovat aktivní předplatné Azure.
  [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Vytvoření a aktivace virtuální prostředí

Vytvoření projektu funkce, je vyžadován, které pracují ve virtuálním prostředí Pythonu 3.6. Spusťte následující příkazy k vytvoření a aktivace do virtuálního prostředí s názvem `.env`.

```bash
# In Bash
python3.6 -m venv .env
source .env/bin/activate

# In PowerShell
py -3.6 -m venv .env
.env\scripts\activate
```

## <a name="create-a-local-functions-project"></a>Vytvořte projekt místní funkce

Nyní můžete vytvořit místní projekt Functions. Tento adresář je ekvivalentem aplikaci Function App v Azure. Může obsahovat více funkcí, které sdílejí stejnou konfiguraci místní a hostování.

V okně terminálu nebo z příkazového řádku spusťte následující příkaz:

```bash
func init MyFunctionProj
```

Vyberte si **python** jako požadovaný modul runtime.

```output
Select a worker runtime:
1. dotnet
2. node
3. python
```

Zobrazit něco jako následující výstup.

```output
Installing wheel package
Installing azure-functions package
Installing azure-functions-worker package
Running pip freeze
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing /MyFunctionProj/.vscode/extensions.json
```

Nová složka s názvem _MyFunctionProj_ se vytvoří. Chcete-li pokračovat, přejděte do této složky.

```bash
cd MyFunctionProj
```

## <a name="create-a-function"></a>Vytvoření funkce

Vytvořit funkci, spusťte následující příkaz:

```bash
func new
```

Zvolte `HTTP Trigger` jako šablonu a zadejte **název funkce** z `HttpTrigger`.

```output
Select a template:
1. Blob trigger
2. Cosmos DB trigger
3. Event Grid trigger
4. Event Hub trigger
5. HTTP trigger
6. Queue trigger
7. Service Bus Queue trigger
8. Service Bus Topic trigger
9. Timer trigger

Choose option: 5
Function name: HttpTrigger
```

Zobrazit něco jako následující výstup.

```output
Writing /MyFunctionProj/HttpTrigger/sample.dat
Writing /MyFunctionProj/HttpTrigger/__init__.py
Writing /MyFunctionProj/HttpTrigger/function.json
The function "HttpTrigger" was created successfully from the "HTTP trigger" template.
```

Dílčí složku s názvem _HttpTrigger_ se vytvoří. Tato položka obsahuje `__init__.py` souboru primární skriptu a `function.json` souboru, který popisuje aktivační událost a vazby používá funkci. Další informace o tomto programovacím modelu, můžete se podívat do [– Příručka pro vývojáře Azure Functions Python](functions-reference-python.md).

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Následujícím příkazem místně spusťte funkce hostitele.

```bash
func host start
```

Při spuštění funkce hostitele, vypíše adresu URL vaší funkce aktivované protokolem HTTP. (Všimněte si, že byl zkrácen celý výstup pro lepší čitelnost.)

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
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.
...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger
```

Zkopírujte adresu URL vaší funkce z výstupu a vložte do panelu Adresa prohlížeče. K této adrese URL připojte řetězec dotazu `?name=<yourname>` a proveďte požadavek.

    http://localhost:7071/api/HttpTrigger?name=<yourname>

Následující snímek obrazovky ukazuje neodpověděla funkce, když se aktivuje v prohlížeči:

![test](./media/functions-create-first-function-python/function-test-local-browser.png)

Nyní jste připraveni k vytvoření aplikace Function App a ostatní požadované prostředky pro publikování na platformě Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Vytvoření linuxové aplikace funkcí v Azure

Aplikace function app poskytuje prostředí pro provádění kódu funkce. Umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. Vytvoření **funkce v Linuxu spuštěnou aplikaci Python** pomocí [az functionapp vytvořit](/cli/azure/functionapp#az_functionapp_create) příkazu.

Spusťte následující příkaz s názvem aplikace jedinečné funkce místo `<app_name>` zástupný text a účet úložiště název `<storage_name>`. `<app_name>` je také výchozí doména DNS pro aplikaci funkcí. Tento název musí být jedinečný mezi všemi aplikacemi v Azure.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <app_name> --storage-account  <storage_name>
```

> [!NOTE]
> Pokud už máte existující skupinu prostředků `myResourceGroup`, která obsahuje aplikace App Service pro jiný systém než Linux, musíte použít jinou skupinu prostředků. Ve stejné skupině prostředků není možné hostovat aplikace pro Windows i Linux.  

Po vytvoření aplikace function app, zobrazí se následující zpráva:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Nyní jste připraveni publikovat projekt lokální funkce do aplikace Function App v Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Nasazení projektu aplikace funkcí do Azure

Pomocí nástrojů Azure Functions Core, spusťte následující příkaz. Nahraďte `<app_name>` s názvem vaší aplikace z předchozího kroku.

```bash
func azure functionapp publish <app_name>
```

Zobrazí se něco jako následující výstup, který byl zkrácen pro lepší čitelnost.

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Další postup

Další informace o vývoji funkcí Azure Functions pomocí Pythonu.

> [!div class="nextstepaction"]
> [Příručka pro vývojáře Azure Functions Python](functions-reference-python.md)
> [aktivační události Azure Functions a vazby](functions-triggers-bindings.md)
