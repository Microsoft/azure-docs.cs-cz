---
title: Rychlý start – události odeslání Azure Container Registry do služby Event Grid
description: V tomto rychlém startu povolte událostí služby Event Grid pro svůj registr kontejneru a odesílání nabízených oznámení image kontejneru a odstraňovat události k ukázkové aplikaci.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/23/2018
ms.author: danlep
ms.openlocfilehash: 60248da2bbdac751845a40720f8967203c562b4a
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085733"
---
# <a name="quickstart-send-container-registry-events-to-event-grid"></a>Rychlý start: Odeslání kontejneru událostí registru do služby Event Grid

Azure Event Grid je plně spravovaná služba Směrování událostí, která poskytuje jednotnou spotřebu událostí s využitím publikování-odběru modelu. V tomto rychlém startu použijete Azure CLI k vytvoření registru kontejnerů, přihlášení k odběru událostí registru a pak nasadit ukázkovou webovou aplikaci pro příjem událostí. Nakonec se aktivuje image kontejneru `push` a `delete` události a zobrazení datové části události v ukázkové aplikaci.

Po dokončení kroků v tomto článku, události odeslané z vašeho registru kontejneru do služby Event Grid se zobrazí v ukázkové webové aplikace:

![Webový prohlížeč vykreslování ukázkovou webovou aplikaci pomocí tří přijatých událostí][sample-app-01]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet][azure-account] před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Příkazy rozhraní příkazového řádku Azure v tomto článku jsou formátována pro **Bash** prostředí. Pokud používáte jiné prostředí, jako je PowerShell nebo příkazového řádku, budete muset upravit znaky pokračování řádku nebo řádky přiřazení proměnné odpovídajícím způsobem. Tento článek používá proměnné, chcete-li minimalizovat množství příkaz Úpravy vyžaduje.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém můžete nasadit a spravovat prostředky Azure. Následující [vytvořit skupiny az] [ az-group-create] příkaz vytvoří skupinu prostředků s názvem *myResourceGroup* v *eastus* oblasti. Pokud chcete použít jiný název skupiny prostředků, nastavte `RESOURCE_GROUP_NAME` na jinou hodnotu.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

V dalším kroku nasazení služby container registry do skupiny prostředků pomocí následujících příkazů. Před spuštěním [az acr vytvořit] [ az-acr-create] příkazu, nastavte `ACR_NAME` na název vašeho registru. Název musí být jedinečný v rámci Azure a je omezen na 5 až 50 alfanumerických znaků.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

Po vytvoření registru rozhraní Azure CLI vrátí výstup podobný následujícímu:

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

V této části pomocí šablony Resource Manageru se nachází v úložišti GitHub nasadit předem sestavené ukázkovou webovou aplikaci do služby Azure App Service. Později přihlášení k odběru událostí služby Event Grid vašeho registru a zadejte tuto aplikaci jako koncový bod, ke které se odesílají události.

Pokud chcete nasadit ukázkovou aplikaci, nastavte `SITE_NAME` jedinečný název pro vaši webovou aplikaci a spusťte následující příkazy. Název webu musí být v rámci Azure jedinečný, protože je součástí plně kvalifikovaného názvu domény (FQDN) webové aplikace. V další části přejděte do aplikace plně kvalifikovaný název domény ve webovém prohlížeči zobrazení událostí v registru.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Po úspěšném nasazení (může trvat několik minut), otevřete prohlížeč a přejděte do své webové aplikace a ujistěte se, zda je spuštěná:

`http://<your-site-name>.azurewebsites.net`

Měli byste vidět ukázkovou aplikaci vybarvením žádné zprávy o událostech zobrazí:

![Webové prohlížeče zobrazující ukázkovou webovou aplikaci s se zobrazí žádné události][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Přihlášení k odběru událostí registru

Ve službě Event Grid se přihlásíte k odběru *tématu* mu sdělíte, které události chcete sledovat a kam poslat platbu je. Následující [az eventgrid-odběr události vytvoření] [ az-eventgrid-event-subscription-create] příkaz přihlásí k registru kontejneru, které jste vytvořili a určuje adresu URL webové aplikace jako koncový bod, ke kterému se má odeslat události. Proměnné prostředí, které vyplní v předchozích částech jsou zde, znovu tak, aby byly požadované žádné úpravy.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Pokud předplatné bylo dokončeno, byste měli vidět výstup podobný následujícímu:

```JSON
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

## <a name="trigger-registry-events"></a>Aktivační události registru

Teď, když je ukázková aplikace v provozu a spuštěný a vy jste odběru do vašeho registru pomocí služby Event Grid, jste připraveni vygenerovat některé události. V této části použijete sestavíte a odešlete image kontejneru do registru ACR úlohy. Úlohy služby ACR je funkce ve službě Azure Container Registry, která vám umožní vytvářet Image kontejneru v cloudu, bez nutnosti modul Docker v místním počítači byly nainstalovány.

### <a name="build-and-push-image"></a>Vytvoření a nahrání image

Spusťte následující příkaz rozhraní příkazového řádku Azure k sestavení image kontejneru z obsahu úložiště GitHub. Ve výchozím nastavení, ACR úlohy automaticky nasdílí úspěšně sestavenou image do vašeho registru, který generuje `ImagePushed` událostí.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

Zatímco ACR úlohy sestavení a předá image byste měli vidět výstup podobný následujícímu. Následující ukázkový výstup byl zkrácen pro zkrácení.

```console
$ az acr build -r $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
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

Pokud chcete ověřit, že sestavenou image se v registru, spusťte následující příkaz k zobrazení značky v úložišti "myimage":

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

Značka "v1" image, kterou jste vytvořili by se měla zobrazit v výstup podobný následujícímu:

```console
$ az acr repository show-tags --name $ACR_NAME --repository myimage
[
  "v1"
]
```

### <a name="delete-the-image"></a>Odstranit image

Nyní, generovat `ImageDeleted` události tak, že odstraníte bitová kopie se [az acr úložiště odstranit] [ az-acr-repository-delete] příkaz:

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Byste měli vidět výstup podobný následujícímu, s výzvou k potvrzení odstranění manifestu a související obrázky:

```console
$ az acr repository delete --name $ACR_NAME --image myimage:v1
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): y
```

## <a name="view-registry-events"></a>Zobrazení událostí modulu registru

Jste nyní obrázek nahrány do vašeho registru a byl odstraněn. Přejděte do webové aplikace mřížky Prohlížeč událostí a byste měli vidět oba `ImageDeleted` a `ImagePushed` události. Můžete si také prohlédnout vygenerované pomocí provádí se příkaz v událost ověření předplatného [přihlášení k odběru událostí registru](#subscribe-to-registry-events) oddílu.

Následující snímek obrazovky ukazuje ukázkovou aplikaci s tři události a `ImageDeleted` události rozbalen a zobrazuje její podrobnosti.

![Webový prohlížeč zobrazující ukázkovou aplikaci s událostmi ImagePushed a ImageDeleted][sample-app-03]

Blahopřejeme! Pokud se zobrazí `ImagePushed` a `ImageDeleted` události, váš registr je odesílání událostí do služby Event Grid a předává tyto události do vašeho koncového bodu aplikace webové služby Event Grid.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete hotovi s prostředky, které jste vytvořili v rámci tohoto rychlého startu, můžete je odstranit všechny pomocí následujícího příkazu rozhraní příkazového řádku Azure. Když odstraníte skupinu prostředků, všechny prostředky, které obsahuje se trvale odstraní.

**UPOZORNĚNÍ**: Tato operace je nevratná operace. Ujistěte se, že už nepotřebujete žádné prostředky ve skupině před spuštěním příkazu.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Schéma událostí mřížka událostí

Odkaz na schéma zprávu události Azure Container Registry najdete v dokumentaci služby Event Grid:

[Schéma událostí Azure Event Grid pro registr kontejneru](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Další postup

V tomto rychlém startu nasazení registru kontejneru, vytvořená image s úlohami služby ACR, byl odstraněn a spotřebovaly vašeho registru události ze služby Event Grid s ukázkovou aplikací. V dalším kroku podívat na kurz služby ACR úlohy Další informace o vytváření imagí kontejnerů v cloudu, včetně automatizované buildy při aktualizaci základní image:

> [!div class="nextstepaction"]
> [Sestavování imagí kontejneru v cloudu s využitím ACR úlohy](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository#az-acr-create
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
