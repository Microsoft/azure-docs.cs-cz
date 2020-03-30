---
title: Úvodní příručka – odeslání událostí do mřížky událostí
description: V tomto rychlém startu povolíte události event gridu pro registr kontejneru a potom odešlete nabízenou položku image kontejneru a odstraníte události do ukázkové aplikace.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18
ms.openlocfilehash: dbeba56820a520e3435eeb0c5c8dbc5aae981241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403232"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Úvodní příručka: Odeslání událostí z registru soukromého kontejneru do programu Event Grid

Azure Event Grid je plně spravovaná služba směrování událostí, která poskytuje jednotnou spotřebu událostí pomocí modelu publikování a odběru. V tomto rychlém startu použijete azure CLI k vytvoření registru kontejneru, přihlášení k odběru událostí registru a následném nasazení ukázkové webové aplikace pro příjem událostí. Nakonec aktivujete image `push` `delete` kontejneru a události a zobrazíte datovou část událostí v ukázkové aplikaci.

Po dokončení kroků v tomto článku se události odeslané z registru kontejneru do služby Event Grid zobrazí v ukázkové webové aplikaci:

![Vykreslování ukázkové webové aplikace pomocí tří přijatých událostí][sample-app-01]

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,][azure-account] než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Příkazy Azure CLI v tomto článku jsou formátovány pro prostředí **Bash.** Pokud používáte jiné prostředí, jako je PowerShell nebo Příkazový řádek, možná budete muset odpovídajícím způsobem upravit znaky pokračování řádku nebo řádky přiřazení proměnných. Tento článek používá proměnné k minimalizaci požadované velikosti příkazu úpravy.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém nasazujete a spravujete prostředky Azure. Následující příkaz [vytvoření skupiny az][az-group-create] vytvoří skupinu prostředků s názvem *myResourceGroup* v oblasti *eastus.* Pokud chcete pro skupinu prostředků použít jiný `RESOURCE_GROUP_NAME` název, nastavte jinou hodnotu.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

Dále nasaďte registr kontejnerů do skupiny prostředků pomocí následujících příkazů. Před spuštěním příkazu [az acr create][az-acr-create] nastavte `ACR_NAME` název registru. Název musí být jedinečný v rámci Azure a je omezen na 5-50 alfanumerických znaků.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

Po vytvoření registru azure CLI vrátí výstup podobný následující:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-08-16T20:02:46.569509+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}

```

## <a name="create-an-event-endpoint"></a>Vytvoření koncového bodu události

V této části použijete šablonu Správce prostředků umístěnou v úložišti GitHub k nasazení předem sestavené ukázkové webové aplikace do služby Azure App Service. Později se přihlásíte k odběru událostí mřížky událostí v registru a zadáte tuto aplikaci jako koncový bod, do kterého jsou události odesílány.

Chcete-li nasadit `SITE_NAME` ukázkovou aplikaci, nastavte jedinečný název webové aplikace a spusťte následující příkazy. Název webu musí být jedinečný v rámci Azure, protože je součástí plně kvalifikovaného názvu domény (FQDN) webové aplikace. V pozdější části přejdete do hlavního název stránky aplikace ve webovém prohlížeči a zobrazíte události registru.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Po úspěšném nasazení (může to trvat několik minut), otevřete prohlížeč a přejděte do webové aplikace a ujistěte se, že je spuštěná:

`http://<your-site-name>.azurewebsites.net`

Měli byste vidět ukázkovou aplikaci vykreslenou bez zobrazených zpráv o událostech:

![Webový prohlížeč zobrazující ukázkovou webovou aplikaci bez zobrazených událostí][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Přihlásit se k odběru událostí registru

V Event Grid se přihlásíte k odběru *tématu,* abyste mu řekli, které události chcete sledovat a kam je chcete poslat. Následující příkaz [az eventgrid event-subscription create][az-eventgrid-event-subscription-create] se přihlásí k odběru registru kontejneru, který jste vytvořili, a určuje adresu URL webové aplikace jako koncový bod, do kterého by měl odesílat události. Proměnné prostředí, které jste naplnili v předchozích oddílech, jsou zde znovu použity, takže nejsou nutné žádné úpravy.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Po dokončení předplatného byste měli vidět výstup podobný následujícímu:

```json
{
  "destination": {
    "endpointBaseUrl": "https://eventgridviewer.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "All"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-acr",
  "labels": null,
  "name": "event-sub-acr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "topic": "/subscriptions/<Subscription ID>/resourceGroups/myresourcegroup/providers/microsoft.containerregistry/registries/myregistry",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Aktivační událost registru

Teď, když je ukázková aplikace v provozu a přihlásili jste se k odběru registru pomocí služby Event Grid, jste připraveni generovat některé události. V této části použijete úlohy ACR k vytvoření a nabízení bitové kopie kontejneru do registru. Úlohy ACR je funkce registru kontejnerů Azure, která umožňuje vytvářet ibi kontejnerů v cloudu, aniž byste museli nainstalovaný Docker Engine v místním počítači.

### <a name="build-and-push-image"></a>Vytváření a nabízení bitové kopie

Spusťte následující příkaz Azure CLI k vytvoření image kontejneru z obsahu úložiště GitHub. Ve výchozím nastavení úlohy ACR automaticky odešle úspěšně sestavenou `ImagePushed` bitovou kopii do registru, který událost generuje.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

Měli byste vidět výstup podobný následujícímu, zatímco acr úkoly sestaví a pak tlačí image. Následující ukázkový výstup byl zkrácen pro stručnost.

```output
Sending build context to ACR...
Queued a build with build ID: aa2
Waiting for build agent...
2018/08/16 22:19:38 Using acb_vol_27a2afa6-27dc-4ae4-9e52-6d6c8b7455b2 as the home volume
2018/08/16 22:19:38 Setting up Docker configuration...
2018/08/16 22:19:39 Successfully set up Docker configuration
2018/08/16 22:19:39 Logging in to registry: myregistry.azurecr.io
2018/08/16 22:19:55 Successfully logged in
Sending build context to Docker daemon  94.72kB
Step 1/5 : FROM node:9-alpine
...
```

Chcete-li ověřit, zda je vytvořená bitová kopie v registru, spusťte následující příkaz pro zobrazení značek v úložišti myimage:

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

Značka "v1" vytvořeného obrazu by se měla objevit ve výstupu podobně jako následující:

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>Odstranění obrázku

Nyní vygenerujte `ImageDeleted` událost odstraněním obrazu pomocí příkazu delete úložiště [az acr:][az-acr-repository-delete]

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Měli byste vidět výstup podobný následujícímu a požádat o potvrzení odstranění manifestu a přidružených obrázků:

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>Zobrazit události registru

Nyní jste do registru zasunuli bitovou kopii a poté ji smazali. Přejděte do webové aplikace Event Grid Viewer `ImageDeleted` `ImagePushed` a měli byste vidět jak události, tak události. Může se také zobrazit událost ověření předplatného generovaná spuštěním příkazu v části [Přihlásit se k odběru událostí registru.](#subscribe-to-registry-events)

Následující snímek obrazovky ukazuje ukázkovou aplikaci `ImageDeleted` se třemi událostmi a událost je rozbalena tak, aby zobrazovala její podrobnosti.

![Webový prohlížeč zobrazující ukázkovou aplikaci s událostmi ImagePushed a ImageDeleted][sample-app-03]

Blahopřejeme! Pokud vidíte `ImagePushed` události `ImageDeleted` a, váš registr odesílá události do aplikace Event Grid a Event Grid tyto události předává do koncového bodu webové aplikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi s prostředky, které jste vytvořili v tomto rychlém startu, můžete je odstranit všechny pomocí následujícího příkazu Azure CLI. Když odstraníte skupinu prostředků, všechny prostředky, které obsahuje, jsou trvale odstraněny.

**UPOZORNĚNÍ:** Tato operace je nevratná. Před spuštěním příkazu se ujistěte, že již nepotřebujete žádné prostředky ve skupině.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Schéma události Mřížka událostí

Odkaz na zprávu o události registru kontejnerů Azure najdete v dokumentaci k mřížce událostí:

[Schéma událostí sítě Azure pro registr kontejnerů](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili registr kontejnerů, vytvořili bitovou kopii s úkoly ACR, odstranili a spotřebovali události registru z event gridu pomocí ukázkové aplikace. Dále přejděte k kurzu Úlohy ACR, kde se dozvíte další informace o vytváření ibližových kopií kontejnerů v cloudu, včetně automatizovaných sestavení na základě aktualizace základní bitové kopie:

> [!div class="nextstepaction"]
> [Vytváření icloudových bitových kopií kontejnerů v cloudu pomocí úloh ACR](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
