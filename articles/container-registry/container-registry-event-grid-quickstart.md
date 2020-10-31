---
title: Rychlý Start – odeslání událostí do Event Grid
description: V tomto rychlém startu povolíte Event Grid události pro registr kontejnerů a potom zadáte události pro vložení a odstranění image kontejneru do ukázkové aplikace.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 6058fceb873e2b26da2d30dadba456e2a625f3f2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074212"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Rychlý Start: odeslání událostí z privátního registru kontejneru do Event Grid

Azure Event Grid je plně spravovaná služba Směrování událostí, která poskytuje jednotnou spotřebu událostí pomocí modelu publikování a odběru. V tomto rychlém startu pomocí Azure CLI vytvoříte registr kontejnerů, přihlásíte se k odběru událostí registru a pak nasadíte ukázkovou webovou aplikaci pro příjem událostí. Nakonec můžete aktivovat image kontejneru `push` a `delete` události a zobrazit datovou část události v ukázkové aplikaci.

Po dokončení kroků v tomto článku se v ukázkové webové aplikaci zobrazí události odeslané z registru kontejneru, které se Event Grid.

![Webový prohlížeč, který vykresluje ukázkovou webovou aplikaci se třemi přijatými událostmi][sample-app-01]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Příkazy rozhraní příkazového řádku Azure v tomto článku jsou formátované pro prostředí **bash** Shell. Pokud používáte jiné prostředí, jako je PowerShell nebo příkazový řádek, možná budete muset odpovídajícím způsobem upravit řádky pro pokračování řádku nebo přiřazení proměnných. V tomto článku se k minimalizaci velikosti požadovaných příkazů používají proměnné.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příkaz [AZ Group Create][az-group-create] vytvoří skupinu prostředků s názvem *myResourceGroup* v oblasti *eastus* . Pokud chcete pro skupinu prostředků použít jiný název, nastavte `RESOURCE_GROUP_NAME` na jinou hodnotu.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Vytvoření registru kontejneru

V dalším kroku nasaďte registr kontejneru do skupiny prostředků pomocí následujících příkazů. Před spuštěním příkazu [AZ ACR Create][az-acr-create] nastavte `ACR_NAME` na název svého registru. Název musí být v rámci Azure jedinečný a omezený na 5-50 alfanumerických znaků.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

Po vytvoření registru vrátí Azure CLI podobný výstup jako v následujícím příkladu:

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

V této části použijete šablonu Správce prostředků umístěnou v úložišti GitHub k nasazení předem připravené ukázkové webové aplikace do Azure App Service. Později se přihlásíte k odběru událostí Event Grid registru a tuto aplikaci určíte jako koncový bod, do kterého se události odesílají.

Pokud chcete ukázkovou aplikaci nasadit, nastavte `SITE_NAME` na jedinečný název vaší webové aplikace a spusťte následující příkazy. Název webu musí být v rámci Azure jedinečný, protože tvoří součást plně kvalifikovaného názvu domény (FQDN) webové aplikace. V pozdější části přejdete do plně kvalifikovaného názvu domény aplikace ve webovém prohlížeči a zobrazíte události v registru.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Po úspěšném nasazení (může to trvat několik minut) otevřete prohlížeč a přejděte do webové aplikace, abyste se ujistili, že je spuštěný:

`http://<your-site-name>.azurewebsites.net`

Měla by se zobrazit ukázková aplikace vykreslená bez zobrazených zpráv o událostech:

![Webový prohlížeč, ve kterém se zobrazuje Ukázková webová aplikace bez zobrazených událostí][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Přihlášení k odběru událostí registru

V Event Grid se přihlásíte k odběru *tématu* a sdělte mu, které události chcete sledovat a kam je odeslat. Následující příkaz [AZ eventgrid Event-Subscription Create][az-eventgrid-event-subscription-create] se přihlásí k odběru registru kontejneru, který jste vytvořili, a určí adresu URL vaší webové aplikace jako koncový bod, na který by měla posílat události. Zde se znovu použijí proměnné prostředí, které jste nastavili v předchozích částech, takže se nevyžadují žádné úpravy.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Po dokončení odběru by se měl zobrazit výstup podobný následujícímu:

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

## <a name="trigger-registry-events"></a>Aktivovat události registru

Teď, když je ukázková aplikace spuštěná a že jste se k odběru svého registru přihlásili Event Grid, jste připraveni vygenerovat nějaké události. V této části použijete úlohy ACR k sestavení a vložení image kontejneru do registru. ACR úkoly jsou funkce Azure Container Registry, která umožňuje vytvářet image kontejnerů v cloudu, aniž by bylo potřeba modul Docker nainstalovaný na místním počítači.

### <a name="build-and-push-image"></a>Sestavit a vložit obrázek

Spuštěním následujícího příkazu Azure CLI sestavíte image kontejneru z obsahu úložiště GitHub. Ve výchozím nastavení ACR úlohy automaticky do registru vloží úspěšně sestavenou image, která událost vygeneruje `ImagePushed` .

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

Měl by se zobrazit výstup podobný následujícímu při sestavování úloh ACR a následném vložení image. Následující vzorový výstup byl zkrácen pro zkrácení.

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

Pokud chcete ověřit, jestli je vytvořená image ve vašem registru, spusťte následující příkaz, který zobrazí značky v úložišti "MyImage":

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

Značka "v1" obrázku, kterou jste vytvořili, by se měla zobrazit ve výstupu, podobně jako v následujícím příkladu:

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>Odstranění obrázku

Nyní vygenerujte `ImageDeleted` událost odstraněním bitové kopie pomocí příkazu [AZ ACR úložiště Delete][az-acr-repository-delete] :

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Měl by se zobrazit výstup podobný následujícímu, který žádá o potvrzení o odstranění manifestu a přidružených imagí:

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>Zobrazení událostí registru

Nyní jste do svého registru vložili image a pak jste ji odstranili. Přejděte do webové aplikace v prohlížeči Event Grid a měli byste vidět `ImageDeleted` `ImagePushed` události a. Můžete také zobrazit událost ověření odběru vygenerovanou spuštěním příkazu v části [přihlášení k odběru událostí registru](#subscribe-to-registry-events) .

Na následujícím snímku obrazovky vidíte ukázkovou aplikaci se třemi událostmi a `ImageDeleted` událost je rozbalená tak, aby zobrazovala její podrobnosti.

![Webový prohlížeč, který zobrazuje ukázkovou aplikaci s ImagePushed a ImageDeletedmi událostmi][sample-app-03]

Gratulujeme! Pokud se zobrazí `ImagePushed` události a `ImageDeleted` , registr odesílá události do Event Grid a Event Grid předává tyto události do koncového bodu webové aplikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi s prostředky, které jste vytvořili v rámci tohoto rychlého startu, můžete je odstranit pomocí následujícího příkazu rozhraní příkazového řádku Azure CLI. Když odstraníte skupinu prostředků, všechny prostředky, které obsahuje, se trvale odstraní.

**Upozornění** : Tato operace je nevratná. Před spuštěním příkazu se ujistěte, že už ve skupině nepotřebujete žádné prostředky.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Schéma událostí služby Event Grid

Odkaz na schéma zpráv Azure Container Registry události najdete v dokumentaci k Event Grid:

[Azure Event Grid schéma událostí pro Container Registry](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili registr kontejneru, vytvořili jste image s ACR úkoly, odstranili jsme ji a využili jste události registru z Event Grid s ukázkovou aplikací. Potom přejděte k kurzu ACR úkoly, kde se dozvíte víc o vytváření imagí kontejnerů v cloudu, včetně automatizovaných sestavení při aktualizaci základní Image:

> [!div class="nextstepaction"]
> [Vytváření imagí kontejnerů v cloudu s ACR úkoly](container-registry-tutorial-quick-task.md)

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
