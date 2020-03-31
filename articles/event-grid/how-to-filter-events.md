---
title: Jak filtrovat události pro Azure Event Grid
description: Tento článek ukazuje, jak filtrovat události (podle typu události, podle předmětu, operátorů a dat atd.) při vytváření předplatného event gridu.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 63a5cdbff79af52d9f96cf410a820c6cfc530066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454019"
---
# <a name="filter-events-for-event-grid"></a>Filtrování událostí pro mřížku událostí

Tento článek ukazuje, jak filtrovat události při vytváření předplatného mřížky událostí. Další informace o možnostech filtrování událostí najdete v [tématu Principy filtrování událostí pro odběry služby Event Grid](event-filtering.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>Filtrovat podle typu události

Při vytváření předplatného event gridu můžete určit, [které typy událostí](event-schema.md) mají být odeslány do koncového bodu. Příklady v této části vytvořit odběry událostí pro skupinu prostředků, ale omezit události, které jsou odesílány do `Microsoft.Resources.ResourceWriteFailure` a `Microsoft.Resources.ResourceWriteSuccess`. Pokud potřebujete větší flexibilitu při filtrování událostí podle typů událostí, přečtěte si informace o filtru podle pokročilých operátorů a datových polí.

Pro PowerShell použijte `-IncludedEventType` parametr při vytváření předplatného.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

Pro rozhraní příkazového `--included-event-types` řádku Azure použijte parametr. Následující příklad používá Azure CLI v prostředí Bash:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

Pro šablonu Správce prostředků `includedEventTypes` použijte vlastnost.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

## <a name="filter-by-subject"></a>Filtrovat podle předmětu

Události můžete filtrovat podle předmětu v datech události. Můžete zadat hodnotu, která se bude shodovat s začátkem nebo koncem předmětu. Pokud potřebujete větší flexibilitu při filtrování událostí podle předmětu, přečtěte si téma Filtrovat podle pokročilých operátorů a datových polí.

V následujícím příkladu prostředí PowerShell vytvoříte odběr událostí, který filtruje na začátku předmětu. `-SubjectBeginsWith` Parametr slouží k omezení událostí na události pro konkrétní prostředek. Předáte ID prostředku skupiny zabezpečení sítě.

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

Další příklad Prostředí PowerShell vytvoří předplatné pro úložiště objektů blob. Omezuje události na ty s předmětem, který končí v `.jpg`.

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

V následujícím příkladu Azure CLI vytvoříte odběr událostí, který filtruje na začátku předmětu. `--subject-begins-with` Parametr slouží k omezení událostí na události pro konkrétní prostředek. Předáte ID prostředku skupiny zabezpečení sítě.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

Další příklad Azure CLI vytvoří předplatné pro úložiště objektů blob. Omezuje události na ty s předmětem, který končí v `.jpg`.

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

V následujícím příkladu šablony Správce prostředků vytvoříte odběr událostí, který filtruje podle začátku předmětu. `subjectBeginsWith` Vlastnost slouží k omezení událostí na události pro konkrétní prostředek. Předáte ID prostředku skupiny zabezpečení sítě.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

Další příklad šablony Správce prostředků vytvoří předplatné pro úložiště objektů blob. Omezuje události na ty s předmětem, který končí v `.jpg`.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

## <a name="filter-by-operators-and-data"></a>Filtrování podle operátorů a dat

Pro větší flexibilitu při filtrování můžete k filtrování událostí použít operátory a vlastnosti dat.

### <a name="subscribe-with-advanced-filters"></a>Přihlásit se pomocí pokročilých filtrů

Informace o operátorech a klíčích, které lze použít pro pokročilé filtrování, naleznete v [tématu Rozšířené filtrování](event-filtering.md#advanced-filtering).

Tyto příklady vytvořit vlastní téma. Přihlásí se k odběru vlastního tématu a filtrují podle hodnoty v datovém objektu. Události, které mají vlastnost color nastavenou na modrou, červenou nebo zelenou, jsou odeslány do předplatného.

Pokud používáte Azure CLI, použijte:

```azurecli
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

Všimněte si, že je nastavené [datum vypršení platnosti](concepts.md#event-subscription-expiration) odběru.

Pokud používáte PowerShell, použijte:

```powershell
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="test-filter"></a>Testovací filtr

Chcete-li filtr otestovat, odešlete událost s barevným polem nastaveným na zelenou. Vzhledem k tomu, že zelená je jednou z hodnot ve filtru, událost je doručena do koncového bodu.

Pokud používáte Azure CLI, použijte:

```azurecli
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Pokud používáte PowerShell, použijte:

```powershell
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Chcete-li otestovat scénář, ve kterém není událost odeslána, odešlete událost s barevným polem nastaveným na žlutou. Žlutá není jednou z hodnot zadaných v předplatném, takže událost se do vašeho předplatného nedoručuje.

Pokud používáte Azure CLI, použijte:

```azurecli
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
Pokud používáte PowerShell, použijte:

```powershell
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

## <a name="next-steps"></a>Další kroky

* Informace o dodávkách událostí monitorování naleznete v [tématu Sledování doručování zpráv v programu Event Grid](monitor-event-delivery.md).
* Další informace o ověřovacím klíči naleznete v tématu [Zabezpečení a ověřování mřížky událostí](security-authentication.md).
* Další informace o vytvoření předplatného Služby Azure Event Grid najdete v [tématu schéma předplatného služby Event Grid](subscription-creation-schema.md).
