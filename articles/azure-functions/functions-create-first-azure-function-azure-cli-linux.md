---
title: Vytvoření první funkce v Linuxu v Azure
description: Zjistěte, jak pomocí Azure Functions Core Tools a Azure CLI vytvořit první funkci hostovanou v Linuxu v Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 03/12/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc, fasttrack-edit
ms.devlang: javascript
manager: jeconnoc
ms.openlocfilehash: 857646bb1b9b317f1e51218d258616e775056b43
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442255"
---
# <a name="create-your-first-function-hosted-on-linux-using-core-tools-and-the-azure-cli-preview"></a>Vytvoření první funkce hostované v Linuxu pomocí Core Tools a Azure CLI (Preview)

Služba Azure Functions umožňuje spuštění kódu v [bezserverovém](https://azure.com/serverless) prostředí Linuxu, aniž byste nejprve museli vytvořit virtuální počítač nebo publikovat webovou aplikaci. Hostování v Linuxu vyžaduje [modul runtime Functions 2.0](functions-versions.md). Podpora pro spuštění aplikace function app v Linuxu v bez serveru [plánu Consumption](functions-scale.md#consumption-plan) je aktuálně ve verzi preview. Další informace najdete v tématu [v tomto článku důležité informace o verzi preview](https://aka.ms/funclinux).

Tento článek Rychlý start vás provede použitím Azure CLI k vytvoření první aplikace funkcí spuštěné v Linuxu. Kód funkce se vytvoří místně a pak se nasadí do Azure pomocí [Azure Functions Core Tools](functions-run-local.md).

Následující kroky se podporují na počítačích se systémem Mac, Windows a Linux. Tento článek popisuje, jak vytvářet funkce v JavaScriptu nebo jazyce C#. Informace o vytváření funkcí Pythonu najdete v tématu [vytvoření první funkce Pythonu pomocí základní nástroje a rozhraní příkazového řádku Azure (preview)](functions-create-first-function-python.md).

## <a name="prerequisites"></a>Požadavky

Před spuštěním této ukázky musíte mít následující:

- Nainstalujte [nástrojů Azure Functions Core](./functions-run-local.md#v2) verze 2.6.666 nebo vyšší.

+ Nainstalujte [rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli). Tento článek vyžaduje použití Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Můžete také použít [Azure Cloud Shell](https://shell.azure.com/bash).

+ Aktivní předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Vytvoření projektu funkce místní aplikace

Spuštěním následujícího příkazu z příkazového řádku vytvoříte projekt aplikace funkcí ve složce `MyFunctionProj` aktuálního místního adresáře. Úložiště GitHub se také vytvoří v `MyFunctionProj`.

```bash
func init MyFunctionProj
```

Po zobrazení výzvy použijte klávesy se šipkami a vyberte modul runtime pracovního procesu z následujících možností jazyků:

+ `dotnet`: vytvoří projekt knihovny třídy .NET (.csproj).
+ `node`: vytvoří projekt jazyka JavaScript nebo TypeScript. Po zobrazení výzvy zvolte `JavaScript`.
+ `python`: vytvoří projekt Python. Funkce Pythonu najdete v článku [rychlý start Python](functions-create-first-function-python.md).

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

## <a name="enable-extension-bundles"></a>Povolení rozšíření sady

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Vytvoření linuxové aplikace funkcí v Azure

K hostování provádění funkcí v Linuxu musíte mít aplikaci funkcí. Aplikace funkcí poskytuje bezserverové prostředí pro provádění kódu funkcí. Umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. Aplikaci funkcí spuštěnou v Linuxu vytvoříte pomocí příkazu [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

V následujícím příkazu nahraďte zástupný text `<app_name>` jedinečným názvem aplikace funkcí a `<storage_name>` názvem účtu úložiště. `<app_name>` je také výchozí doména DNS pro aplikaci funkcí. Tento název musí být jedinečný mezi všemi aplikacemi v Azure. Také byste měli nastavit `<language>` modul runtime pro aplikace function app, z `dotnet` (C#), `node` (JavaScript/TypeScript), nebo `python`.

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