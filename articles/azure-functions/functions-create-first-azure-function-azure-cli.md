---
title: Vytvoření první funkce pomocí Azure CLI
description: Naučíte se postup vytvoření první funkce Azure Function pro provádění pomocí Azure CLI bez serveru a Azure Functions Core Tools.
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/13/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
manager: gwallace
ms.openlocfilehash: 1d0c6edf250e49fe966388253617181ed2b3dff1
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329641"
---
# <a name="quickstart-create-your-first-function-from-the-command-line-using-azure-cli"></a>Rychlý Start: Vytvoření první funkce z příkazového řádku pomocí Azure CLI

Toto téma Rychlý start vás provede použitím příkazového řádku nebo terminálu k vytvoření první funkce. Pomocí Azure CLI vytvoříte aplikaci funkcí, což je infrastruktura [bez serveru](https://azure.microsoft.com/solutions/serverless/), která je hostitelem funkce. Projekt kódu funkce je generován ze šablony s použitím [Azure Functions Core Tools](functions-run-local.md), který se používá také k nasazení projektu funkcí aplikace do Azure.

Následující kroky můžete provést v počítačích se systémem Mac, Windows nebo Linux.

## <a name="prerequisites"></a>Předpoklady

Před spuštěním této ukázky musíte mít následující:

+ Nainstalujte [Azure Functions Core Tools](./functions-run-local.md#v2) verze 2.6.666 nebo novější.

+ Nainstalujte [rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). Tento článek vyžaduje Azure CLI verze 2,0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Můžete také použít [Azure Cloud Shell](https://shell.azure.com/bash).

+ Aktivní předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>Povolit sady rozšíření

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Vytvoření Function App

K hostování provádění funkcí musíte mít aplikaci Function App. Function App poskytuje prostředí pro provádění kódu funkce bez serveru. Umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. Aplikaci Function App vytvoříte pomocí příkazu [az functionapp create](/cli/azure/functionapp#az-functionapp-create). 

V následujícím příkazu nahraďte zástupný symbol `<APP_NAME>` jedinečným názvem vaší aplikace funkcí a `<STORAGE_NAME>` názvem účtu úložiště. Jako výchozí doména DNS pro příslušnou aplikaci Function App se použije `<APP_NAME>`, a proto musí být název mezi všemi aplikacemi v Azure jedinečný. Měli byste také nastavit modul runtime `<language>` pro aplikaci Function App, od `dotnet` (C#) nebo `node` (JavaScript).

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <APP_NAME> --storage-account  <STORAGE_NAME> --runtime <language>
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

