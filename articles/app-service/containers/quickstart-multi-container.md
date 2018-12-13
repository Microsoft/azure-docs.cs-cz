---
title: Vytvoření vícekontejnerové aplikace pomocí Docker Compose - službě Azure App Service
description: Nasazení první vícekontejnerové aplikace ve službě Azure Web App for Containers během několika minut
keywords: azure app service, web app, linux, docker, compose, multicontainer, multi-container, web app for containers, multiple containers, container, kubernetes, wordpress, azure db for mysql, production database with containers
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 404be7a064e0e4b09178830b7802192e8775e121
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316570"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Vytvoření pomocí Docker Compose konfigurace aplikace s více kontejnerů (preview)

Služba [Web App for Containers](app-service-linux-intro.md) poskytuje flexibilní způsob, jak používat image Dockeru. Tento rychlý start ukazuje, jak ve službě [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) nasadit vícekontejnerovou aplikaci do služby Web App for Containers s použitím konfigurace Docker Compose. Informace pro Kubernetes a kompletní řešení s využitím Azure DB for MySQL najdete v [kurzu k vícekontejnerovým aplikacím](tutorial-multi-container-app.md).

Tento rychlý start absolvujete ve službě Cloud Shell, ale uvedené příkazy můžete spustit také místně v [Azure CLI](/cli/azure/install-azure-cli) (verze 2.0.32 nebo novější). 

![Ukázková vícekontejnerová aplikace ve službě Web App for Containers][1]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Stažení ukázky

Pro účely tohoto rychlého startu použijete soubor compose z [Dockeru](https://docs.docker.com/compose/wordpress/#define-the-project). Tento konfigurační soubor najdete mezi [ukázkami Azure](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

Ve službě Cloud Shell vytvořte adresář rychlého startu a přejděte do něj.

```bash
mkdir quickstart

cd quickstart
```

Potom spusťte následující příkaz, pomocí kterého do tohoto adresáře naklonujete úložiště ukázkové aplikace. Pak přejděte do adresáře `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Ve službě Cloud Shell vytvořte skupinu prostředků pomocí příkazu [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *Střed USA – jih*. Pokud chcete zobrazit všechna podporovaná umístění pro službu App Service v Linuxu na úrovni **Standard**, spusťte příkaz [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Obvykle budete svoji skupinu prostředků a prostředky vytvářet v oblasti, kterou máte blízko.

Po dokončení příkazu se ve výstupu JSON zobrazí vlastnosti skupiny prostředků.

## <a name="create-an-azure-app-service-plan"></a>Vytvoření plánu služby Azure App Service

Ve službě Cloud Shell vytvořte pomocí příkazu [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) plán služby App Service ve skupině prostředků.

Následující příklad vytvoří plán služby App Service s názvem `myAppServicePlan` v cenové úrovni **Standard** (`--sku S1`) a v kontejneru Linuxu (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Po vytvoření plánu služby App Service se v rozhraní příkazového řádku Azure zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="create-a-docker-compose-app"></a>Vytvoření aplikace Docker Compose

V terminálu Cloud Shell pomocí příkazu [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) vytvořte vícekontejnerovou [webovou aplikaci](app-service-linux-intro.md) v plánu služby App Service `myAppServicePlan`. Nezapomeňte nahradit _\<app_name>_ jedinečným názvem aplikace.

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Po vytvoření webové aplikace Azure CLI zobrazí výstup podobný následujícímu příkladu:

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Přechod do aplikace

Přejděte do nasazené aplikace na adrese `http://<app_name>.azurewebsites.net`. Načtení aplikace může pár minut trvat. Když se zobrazí chyba, ještě chvíli počkejte a pak aktualizujte prohlížeč.

![Ukázková vícekontejnerová aplikace ve službě Web App for Containers][1]

**Blahopřejeme**, vytvořili jste vícekontejnerovou aplikaci ve službě Web App for Containers.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření vícekontejnerové aplikace WordPress ve službě Web App for Containers](tutorial-multi-container-app.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png