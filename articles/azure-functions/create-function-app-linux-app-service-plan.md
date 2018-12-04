---
title: Vytvoření aplikace funkcí v plánu služby Azure App Service v Linuxu
description: Zjistěte, jak vytvořit aplikaci function app, na kterém běží na Linuxu v plánu služby App Service pomocí Azure CLI.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: d9800ff3fc82636c5cae12167738667ec84326ee
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855898"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan-preview"></a>Vytvoření aplikace function app v Linuxu v plánu služby Azure App Service (preview)

Služba Azure Functions umožňuje hostovat funkce v Linuxu ve výchozím kontejneru Azure App Service. Tento článek vás provede jak používat rozhraní příkazového řádku Azure k vytvoření aplikace funkcí hostované v systému Linux v Azure, která běží v [plán služby App Service](functions-scale.md#app-service-plan). Můžete také [použít vlastní kontejner](functions-create-function-linux-custom-image.md). Hostování v Linuxu je aktuálně ve verzi preview.

V plánu služby App Service zodpovídáte za škálování vaší aplikace function app. Abyste mohli využívat funkce bez serveru služby Azure Functions, můžete také hostovat funkce v Linuxu v [plánu Consumption](functions-scale.md#consumption-plan).

Následující kroky můžete provést v počítačích se systémem Mac, Windows nebo Linux.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

+ Aktivní předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0.21 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Vytvoření plánu služby App Service pro Linux

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Vytvoření aplikace funkcí v Linuxu

K hostování provádění funkcí v Linuxu musíte mít aplikaci funkcí. Aplikace funkcí poskytuje prostředí pro provádění kódu funkce. Umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. Aplikaci funkcí vytvoříte pomocí příkazu [az functionapp create](/cli/azure/functionapp#az-functionapp-create) s použitím plánu služby App Service pro Linux.

V následujícím příkazu nahraďte zástupný symbol `<app_name>` jedinečným názvem vaší aplikace funkcí a `<storage_name>` názvem účtu úložiště. Jako výchozí doména DNS pro příslušnou aplikaci Function App se použije `<app_name>`, a proto musí být název mezi všemi aplikacemi v Azure jedinečný. Také byste měli nastavit `<language>` modul runtime pro aplikace function app, z `dotnet` (C#), `node` (JavaScript), nebo `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --plan myAppServicePlan \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

Po vytvoření a nasazení aplikace funkcí se v Azure CLI zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
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

Vzhledem k tomu, že `myAppServicePlan` je plán pro Linux, k vytvoření kontejneru, ve kterém se spouští aplikace funkcí v Linuxu, se použije integrovaná image Dockeru.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources-simple.md)]

## <a name="next-steps"></a>Další kroky

V tomto článku se dozvíte, jak vytvořit aplikaci function app Linux hostované v Azure. Teď můžete [nasazení projektu funkce](https://docs.microsoft.com/cli/azure/functionapp/deployment/source?view=azure-cli-latest) do této aplikace function app. Můžete použít Azure Functions Core Tools na [vytvoření projektu funkce](functions-run-local.md#create-a-local-functions-project) do místního počítače a nasaďte ji do nové aplikace funkce systému Linux.  

> [!div class="nextstepaction"] 
> [Místní psaní kódu a testování funkcí Azure Functions](functions-run-local.md)
