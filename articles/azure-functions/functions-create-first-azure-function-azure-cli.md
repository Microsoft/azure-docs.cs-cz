---
title: Vytvoření první funkce pomocí Azure CLI
description: Naučíte se postup vytvoření první funkce Azure Function pro provádění pomocí Azure CLI bez serveru a Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/13/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 7ff57519cfbb99fa705aff6c970951730c501f3e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52846449"
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

V následujícím příkazu nahraďte zástupný symbol `<app_name>` jedinečným názvem vaší aplikace funkcí a `<storage_name>` názvem účtu úložiště. Jako výchozí doména DNS pro příslušnou aplikaci Function App se použije `<app_name>`, a proto musí být název mezi všemi aplikacemi v Azure jedinečný. Také byste měli nastavit `<language>` modul runtime pro aplikace function app, z `dotnet` (C#) nebo `node` (JavaScript).

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <app_name> --storage-account  <storage_name> --runtime <language> 
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

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

