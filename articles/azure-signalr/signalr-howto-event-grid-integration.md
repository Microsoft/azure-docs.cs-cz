---
title: Jak odeslat události služby Azure SignalR do mřížky událostí
description: Průvodce, který vám ukáže, jak povolit události Event Grid pro službu SignalR, a pak odeslat připojené nebo odpojené události klienta do ukázkové aplikace.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: a76c9aaabf984723e2b60a7cd42425c9b29c916a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710828"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Odesílání událostí ze služby Azure SignalR Service do služby Event Grid

Azure Event Grid je plně spravovaná služba směrování událostí, která poskytuje jednotnou spotřebu událostí pomocí modelu pub-sub. V této příručce použijete azure cli k vytvoření služby Azure SignalR, přihlaste se k odběru událostí připojení a pak nasadíte ukázkovou webovou aplikaci pro příjem událostí. Nakonec se můžete připojit a odpojit a zobrazit datovou část události v ukázkové aplikaci.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,][azure-account] než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Příkazy Azure CLI v tomto článku jsou formátovány pro prostředí **Bash.** Pokud používáte jiné prostředí, jako je PowerShell nebo Příkazový řádek, možná budete muset odpovídajícím způsobem upravit znaky pokračování řádku nebo řádky přiřazení proměnných. Tento článek používá proměnné k minimalizaci požadované velikosti příkazu úpravy.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém nasazujete a spravujete prostředky Azure. Následující příkaz [vytvoření skupiny az][az-group-create] vytvoří skupinu prostředků s názvem *myResourceGroup* v oblasti *eastus.* Pokud chcete pro skupinu prostředků použít jiný `RESOURCE_GROUP_NAME` název, nastavte jinou hodnotu.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Vytvoření služby SignalR

Dále nasadit službu Azure Signalr do skupiny prostředků s následujícími příkazy.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

Po vytvoření služby SignalR vrátí azure CLI výstup podobný následujícímu:

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
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

Po úspěšném nasazení (může to trvat několik minut) otevřete prohlížeč a přejděte do webové aplikace a ujistěte se, že je spuštěná:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Přihlásit se k odběru událostí registru

V Event Grid se přihlásíte k odběru *tématu,* abyste mu řekli, které události chcete sledovat a kam je chcete poslat. Následující příkaz [vytvoření az eventgrid pro vytvoření][az-eventgrid-event-subscription-create] se přihlásí k odběru služby Azure SignalR, kterou jste vytvořili, a určuje adresu URL webové aplikace jako koncový bod, do kterého by měl odesílat události. Proměnné prostředí, které jste naplnili v předchozích oddílech, jsou zde znovu použity, takže nejsou nutné žádné úpravy.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Po dokončení předplatného byste měli vidět výstup podobný následujícímu:

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Aktivační událost registru

Přepněte do servisního režimu `Serverless Mode` a nastavte připojení klienta ke službě SignalR. Jako referenci můžete vzít [ukázku bez serveru.](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless)

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a separate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Zobrazit události registru

Nyní jste připojili klienta ke službě SignalR. Přejděte do webové aplikace Event Grid Viewer `ClientConnectionConnected` a měli byste vidět událost. Pokud klienta ukončíte, zobrazí `ClientConnectionDisconnected` se také událost.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
