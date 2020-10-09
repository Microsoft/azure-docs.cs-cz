---
title: Odeslání událostí služby signalizace Azure do Event Grid
description: Průvodce, který vám ukáže, jak povolit události Event Grid pro službu Signaler, a pak odeslat události připojené/odpojené klientovi k ukázkové aplikaci.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: a76c9aaabf984723e2b60a7cd42425c9b29c916a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76710828"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Odesílání událostí ze služby Azure SignalR Service do služby Event Grid

Azure Event Grid je plně spravovaná služba Směrování událostí, která poskytuje jednotnou spotřebu událostí pomocí modelu Pub-sub. V tomto průvodci pomocí Azure CLI vytvoříte službu signalizace Azure, přihlásíte se k odběru událostí připojení a pak nasadíte ukázkovou webovou aplikaci, abyste přijímali události. Nakonec se můžete připojit a odpojit a zobrazit datovou část události v ukázkové aplikaci.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet][azure-account].

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Příkazy rozhraní příkazového řádku Azure v tomto článku jsou formátované pro prostředí **bash** Shell. Pokud používáte jiné prostředí, jako je PowerShell nebo příkazový řádek, možná budete muset odpovídajícím způsobem upravit řádky pro pokračování řádku nebo přiřazení proměnných. V tomto článku se k minimalizaci velikosti požadovaných příkazů používají proměnné.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příkaz [AZ Group Create][az-group-create] vytvoří skupinu prostředků s názvem *myResourceGroup* v oblasti *eastus* . Pokud chcete pro skupinu prostředků použít jiný název, nastavte `RESOURCE_GROUP_NAME` na jinou hodnotu.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Vytvoření služby SignalR

V dalším kroku nasaďte službu Azure Signal Service do skupiny prostředků pomocí následujících příkazů.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

Po vytvoření služby signalizace Azure CLI vrátí výstup podobný následujícímu:

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

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Přihlášení k odběru událostí registru

V Event Grid se přihlásíte k odběru *tématu* a sdělte mu, které události chcete sledovat a kam je odeslat. Následující příkaz [AZ eventgrid Event-Subscription Create][az-eventgrid-event-subscription-create] se přihlásí k odběru služby signalizace Azure, kterou jste vytvořili, a určí adresu URL vaší webové aplikace jako koncový bod, do kterého by měla posílat události. Zde se znovu použijí proměnné prostředí, které jste nastavili v předchozích částech, takže se nevyžadují žádné úpravy.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Po dokončení odběru by se měl zobrazit výstup podobný následujícímu:

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

## <a name="trigger-registry-events"></a>Aktivovat události registru

Přepněte do režimu služby `Serverless Mode` a nastavte připojení klienta ke službě Signal. Jako referenci můžete mít [ukázku bez serveru](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) .

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

## <a name="view-registry-events"></a>Zobrazení událostí registru

Nyní jste připojili klienta ke službě signalizace. Přejděte do webové aplikace v prohlížeči Event Grid a měla by se zobrazit `ClientConnectionConnected` událost. Pokud ukončíte klienta nástroje, zobrazí se také `ClientConnectionDisconnected` událost.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
