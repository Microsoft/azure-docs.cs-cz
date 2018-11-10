---
title: Vytvoření první funkce v Linuxu v Azure
description: Zjistěte, jak pomocí Azure Functions Core Tools a Azure CLI vytvořit první funkci hostovanou v Linuxu v Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 09/12/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: multiple
manager: jeconnoc
ms.openlocfilehash: 1045e0cc0d114bb8b35e6136a2054b3642eac7e8
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249863"
---
# <a name="create-your-first-function-hosted-on-linux-using-core-tools-and-the-azure-cli-preview"></a>Vytvoření první funkce hostované v Linuxu pomocí Core Tools a Azure CLI (Preview)

Služba Azure Functions umožňuje spuštění kódu v [bezserverovém](https://azure.microsoft.com/overview/serverless-computing/) prostředí Linuxu, aniž byste nejprve museli vytvořit virtuální počítač nebo publikovat webovou aplikaci. Hostování v Linuxu je aktuálně ve verzi Preview a vyžaduje [modul runtime služby Functions 2.0](functions-versions.md).

Tento článek Rychlý start vás provede použitím Azure CLI k vytvoření první aplikace funkcí spuštěné v Linuxu. Kód funkce se vytvoří místně a pak se nasadí do Azure pomocí [Azure Functions Core Tools](functions-run-local.md).

Následující kroky se podporují na počítačích se systémem Mac, Windows a Linux. Tento článek popisuje, jak vytvářet funkce v JavaScriptu nebo jazyce C#.

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

Po zobrazení výzvy použijte klávesy se šipkami a vyberte modul runtime pracovního procesu z následujících možností jazyků:

+ `dotnet`: vytvoří projekt knihovny třídy .NET (.csproj).
+ `node`: vytvoří projekt jazyka JavaScript.

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Vytvoření linuxové aplikace funkcí v Azure

K hostování provádění funkcí v Linuxu musíte mít aplikaci funkcí. Aplikace funkcí poskytuje bezserverové prostředí pro provádění kódu funkcí. Umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. Aplikaci funkcí spuštěnou v Linuxu vytvoříte pomocí příkazu [az functionapp create](/cli/azure/functionapp#az_functionapp_create).

V následujícím příkazu nahraďte zástupný text `<app_name>` jedinečným názvem aplikace funkcí a `<storage_name>` názvem účtu úložiště. `<app_name>` je také výchozí doména DNS pro aplikaci funkcí. Tento název musí být jedinečný mezi všemi aplikacemi v Azure.

```azurecli
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--location "westus" --is-linux
```

> [!NOTE]
> Pokud už máte existující skupinu prostředků `myResourceGroup`, která obsahuje aplikace App Service pro jiný systém než Linux, musíte použít jinou skupinu prostředků. Ve stejné skupině prostředků není možné hostovat aplikace pro Windows i Linux.  

Po vytvoření aplikace funkcí se zobrazí následující zpráva:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Teď můžete do nové aplikace funkcí v Azure publikovat svůj projekt.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak hostovat aplikaci funkcí ve výchozím kontejneru služby Azure App Service. Funkce v Linuxu můžete hostovat také ve vlastním kontejneru.

> [!div class="nextstepaction"] 
> [Vytvoření funkce v Linuxu pomocí vlastní image](functions-create-function-linux-custom-image.md)
