---
title: Vytvoření první funkce v Linuxu v Azure
description: Learn how to create your first function hosted on Linux in Azure using the command line tools, Azure Functions Core Tools and the Azure CLI.
ms.date: 03/12/2019
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 19abfee69db53c560dfa2696d85f8c1c3d770c09
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230814"
---
# <a name="quickstart-create-your-first-function-hosted-on-linux-using-command-line-tools"></a>Quickstart: Create your first function hosted on Linux using command line tools

Služba Azure Functions umožňuje spuštění kódu v [bezserverovém](https://azure.com/serverless) prostředí Linuxu, aniž byste nejprve museli vytvořit virtuální počítač nebo publikovat webovou aplikaci. Linux-hosting requires [the Functions 2.x runtime](functions-versions.md). Serverless functions run in the [Consumption plan](functions-scale.md#consumption-plan).

Tento článek Rychlý start vás provede použitím Azure CLI k vytvoření první aplikace funkcí spuštěné v Linuxu. Kód funkce se vytvoří místně a pak se nasadí do Azure pomocí [Azure Functions Core Tools](functions-run-local.md).

Následující kroky se podporují na počítačích se systémem Mac, Windows a Linux. Tento článek popisuje, jak vytvářet funkce v JavaScriptu nebo jazyce C#. To learn how to create Python functions, see [Create your first Python function using Core Tools and the Azure CLI](functions-create-first-function-python.md).

## <a name="prerequisites"></a>Předpoklady

Před spuštěním této ukázky musíte mít následující:

+ Install [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.6.666 or above.

+ Nainstalujte [rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli). Tento článek vyžaduje použití Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Můžete také použít [Azure Cloud Shell](https://shell.azure.com/bash).

+ Aktivní předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>Enable extension bundles

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Vytvoření linuxové aplikace funkcí v Azure

K hostování provádění funkcí v Linuxu musíte mít aplikaci funkcí. Aplikace funkcí poskytuje bezserverové prostředí pro provádění kódu funkcí. Umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. Aplikaci funkcí spuštěnou v Linuxu vytvoříte pomocí příkazu [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

V následujícím příkazu nahraďte zástupný text `<app_name>` jedinečným názvem aplikace funkcí a `<storage_name>` názvem účtu úložiště. `<app_name>` je také výchozí doména DNS pro aplikaci funkcí. Tento název musí být jedinečný mezi všemi aplikacemi v Azure. You should also set the `<language>` runtime for your function app, from `dotnet` (C#), `node` (JavaScript/TypeScript), or `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westus --os-type Linux \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

Po vytvoření aplikace funkcí se zobrazí následující zpráva:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Teď můžete do nové aplikace funkcí v Azure publikovat svůj projekt.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]