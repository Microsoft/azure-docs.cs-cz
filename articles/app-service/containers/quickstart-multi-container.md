---
title: Vytvoření vícekontejnerové aplikace (Preview) ve službě Azure Web App for Containers s použitím konfigurace Docker Compose
description: Nasazení první vícekontejnerové aplikace ve službě Azure Web App for Containers během několika minut
keywords: azure app service, web app, linux, docker, compose, multicontainer, container, kubernetes
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
ms.custom: mvc
ms.openlocfilehash: ec5c92415668c925fe360c0c8887fd792a121842
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753709"
---
# <a name="create-a-multicontainer-preview-app-using-web-app-for-containers"></a>Vytvoření vícekontejnerové aplikace (Preview) pomocí služby Web App for Containers

Služba [Web App for Containers](app-service-linux-intro.md) poskytuje flexibilní způsob, jak používat image Dockeru. Tento rychlý start ukazuje, jak ve službě [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) nasadit vícekontejnerovou aplikaci do služby Web App for Containers s použitím konfigurace Docker Compose. Pokud používáte Kubernetes, postupujte podle kroků pro Kubernetes v [kurzu k vícekontejnerovým aplikacím](tutorial-multi-container-app.md).

Tento rychlý start absolvujete ve službě Cloud Shell, ale uvedené příkazy můžete spustit také místně v [Azure CLI](/cli/azure/install-azure-cli) (verze 2.0.32 nebo novější). V tomto rychlém startu se použije konfigurační soubor Docker Compose.

![Ukázková vícekontejnerová aplikace ve službě Web App for Containers][1]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Stažení ukázky

Pro účely tohoto rychlého startu použijete soubor compose z [Dockeru](https://docs.docker.com/compose/wordpress/#define-the-project), upravíte ho ale tak, aby zahrnoval Azure Database for MySQL, trvalé úložiště a Redis. Tento konfigurační soubor najdete mezi [ukázkami Azure](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

Ve službě Cloud Shell vytvořte adresář rychlého startu a přejděte do něj.

```bash
mkdir quickstart

cd quickstart
```

Potom spusťte následující příkaz, pomocí kterého do tohoto adresáře naklonujete úložiště ukázkové aplikace.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Ve službě Cloud Shell vytvořte skupinu prostředků pomocí příkazu [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *Střed USA – jih*. Pokud chcete zobrazit všechna podporovaná umístění pro službu App Service v Linuxu na úrovni **Standard**, spusťte příkaz [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Obvykle budete svoji skupinu prostředků a prostředky vytvářet v oblasti, kterou máte blízko.

Po dokončení příkazu se ve výstupu JSON zobrazí vlastnosti skupiny prostředků.

## <a name="create-an-azure-app-service-plan"></a>Vytvoření plánu služby Azure App Service

Ve službě Cloud Shell vytvořte pomocí příkazu [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) plán služby App Service ve skupině prostředků.

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

V terminálu Cloud Shell přejděte do adresáře `multicontainerwordpress`. Pomocí příkazu [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) vytvořte vícekontejnerovou [webovou aplikaci](app-service-linux-intro.md) v plánu služby App Service `myAppServicePlan`. Nezapomeňte nahradit _\<app_name>_ jedinečným názvem aplikace.

```bash
cd multicontainerwordpress

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření vícekontejnerové aplikace WordPress ve službě Web App for Containers](tutorial-multi-container-app.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png