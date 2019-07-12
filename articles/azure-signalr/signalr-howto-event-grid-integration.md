---
title: Jak odesílat události služby Azure SignalR do služby Event Grid
description: Tento průvodce ukazují, jak povolit událostí služby Event Grid pro služby SignalR odešlete připojení klienta Připojení/odpojení události k ukázkové aplikaci.
services: azure-signalr
author: chenyl
ms.service: azure-signalr
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: chenyl
ms.openlocfilehash: 2d782306938136ce6d21a331185f591316f58a29
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789173"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Jak odesílat události ze služby Azure SignalR do služby Event Grid

Azure Event Grid je plně spravovaná služba Směrování událostí, která poskytuje jednotnou spotřebu událostí s využitím modelu pub-sub. V této příručce použijte rozhraní příkazového řádku Azure k vytvoření služby Azure SignalR, přihlášení k odběru událostí připojení a pak nasadit ukázkovou webovou aplikaci pro příjem událostí. Nakonec můžete připojit a odpojit a zobrazit datovou část události v ukázkové aplikaci.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet][azure-account] před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Příkazy rozhraní příkazového řádku Azure v tomto článku jsou formátována pro **Bash** prostředí. Pokud používáte jiné prostředí, jako je PowerShell nebo příkazového řádku, budete muset upravit znaky pokračování řádku nebo řádky přiřazení proměnné odpovídajícím způsobem. Tento článek používá proměnné, chcete-li minimalizovat množství příkaz Úpravy vyžaduje.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém můžete nasadit a spravovat prostředky Azure. Následující [vytvořit skupiny az][az-group-create] příkaz vytvoří skupinu prostředků s názvem *myResourceGroup* v *eastus* oblasti. Pokud chcete použít jiný název skupiny prostředků, nastavte `RESOURCE_GROUP_NAME` na jinou hodnotu.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Vytvoření služby SignalR

V dalším kroku nasazení služby Azure Signalr do skupiny prostředků pomocí následujících příkazů.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

Po vytvoření služby SignalR, rozhraní příkazového řádku Azure CLI vrátí výstup podobný následujícímu:

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

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Přihlášení k odběru událostí registru

Ve službě Event Grid se přihlásíte k odběru *tématu* mu sdělíte, které události chcete sledovat a kam poslat platbu je. Následující [az eventgrid-odběr události vytvoření][az-eventgrid-event-subscription-create] příkaz přihlásí ke službě Azure SignalR jste vytvořili a určuje adresu URL webové aplikace jako koncový bod, ke kterému se má odeslat události. Proměnné prostředí, které vyplní v předchozích částech jsou zde, znovu tak, aby byly požadované žádné úpravy.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Pokud předplatné bylo dokončeno, byste měli vidět výstup podobný následujícímu:

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

## <a name="trigger-registry-events"></a>Aktivační události registru

Přepnout do režimu servisu na `Serverless Mode` a nastavení připojení klienta ke službě SignalR. Můžete využít [bez serveru ukázka](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) jako odkaz.

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a seperate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Zobrazení událostí modulu registru

Jste teď připojení klienta pro služby SignalR. Přejděte do prohlížeče událostí mřížky webové aplikace a měli byste vidět `ClientConnectionConnected` událostí. Pokud je ukončit klienta, zobrazí se také `ClientConnectionDisconnected` událostí.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
