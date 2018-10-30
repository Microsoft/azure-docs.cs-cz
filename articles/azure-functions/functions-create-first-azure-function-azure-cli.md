---
title: Vytvoření první funkce pomocí Azure CLI
description: Naučíte se postup vytvoření první funkce Azure Function pro provádění pomocí Azure CLI bez serveru a Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 09/10/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 07a079e00963f1f5aff96369649e2e4fb248aae0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985994"
---
# <a name="create-your-first-function-from-the-command-line"></a>Vytvoření první funkce z příkazového řádku

Toto téma Rychlý start vás provede použitím příkazového řádku nebo terminálu k vytvoření první funkce. Pomocí Azure CLI vytvoříte aplikaci funkcí, což je infrastruktura [bez serveru](https://azure.microsoft.com/solutions/serverless/), která je hostitelem funkce. Projekt kódu funkce je generován ze šablony s použitím [Azure Functions Core Tools](functions-run-local.md), který se používá také k nasazení projektu funkcí aplikace do Azure.

Následující kroky můžete provést v počítačích se systémem Mac, Windows nebo Linux.

## <a name="prerequisites"></a>Požadavky

Před spuštěním této ukázky musíte mít následující:

+ Nainstalujte [Azure Core Tools verze 2.x](functions-run-local.md#v2).

+ Nainstalujte [rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli). Tento článek vyžaduje použití Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Můžete také použít [Azure Cloud Shell](https://shell.azure.com/bash).

+ Aktivní předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Vytvoření projektu funkce místní aplikace

Spuštěním následujícího příkazu z příkazového řádku vytvoříte projekt aplikace funkcí ve složce `MyFunctionProj` aktuálního místního adresáře. Úložiště GitHub se také vytvoří v `MyFunctionProj`.

```bash
func init MyFunctionProj
```

Po zobrazení výzvy vyberte modul runtime pracovního procesu z následujících možností jazyka:

+ `dotnet`: vytvoří projekt knihovny třídy .NET (.csproj).
+ `node`: vytvoří projekt jazyka JavaScript.

Při spuštění příkazu se zobrazí něco jako následující výstup:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

Pomocí následujícího příkazu přejděte do složky nového projektu `MyFunctionProj`.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Vytvoření Function App

K hostování provádění funkcí musíte mít aplikaci Function App. Function App poskytuje prostředí pro provádění kódu funkce bez serveru. Umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. Aplikaci Function App vytvoříte pomocí příkazu [az functionapp create](/cli/azure/functionapp#az-functionapp-create). 

V následujícím příkazu nahraďte zástupný symbol `<app_name>` jedinečným názvem vaší aplikace funkcí a `<storage_name>` názvem účtu úložiště. Jako výchozí doména DNS pro příslušnou aplikaci Function App se použije `<app_name>`, a proto musí být název mezi všemi aplikacemi v Azure jedinečný. Parametr _deployment-source-url_ představuje ukázkové úložiště na GitHubu, které obsahuje funkci Hello World aktivovanou protokolem HTTP.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <app_name> --storage-account  <storage_name>  
```

Nastavení parametru _consumption-plan-location_ znamená, že je aplikace funkcí hostovaná v plánu hostování Consumption. V tomto bezserverovém plánu bez se prostředky přidávají dynamicky podle potřeb vašich funkcí a vy platíte pouze za spuštěné funkce. Další informace najdete v tématu [Výběr správného plánu hostování](functions-scale.md).

Po vytvoření aplikace Function App se v Azure CLI zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

## <a name="configure-the-function-app"></a>Konfigurace aplikace Function App

Tools Core verze 2.x vytvoří projekty pomocí šablon pro modul runtime Azure Functions verze 2.x. Z tohoto důvodu se musíte ujistit, že se v Azure používá modul runtime verze 2.x. Nastavení aplikace `FUNCTIONS_WORKER_RUNTIME` na `~2` připne aplikaci funkcí k nejnovější verzi 2.x. Pomocí příkazu [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) nastavte nastavení aplikace.

V následujícím příkazu Azure CLI je <app_name> název vaší aplikace funkcí.

```azurecli-interactive
az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings FUNCTIONS_WORKER_RUNTIME=~2
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
