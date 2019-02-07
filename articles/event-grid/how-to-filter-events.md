---
title: Jak filtrovat události pro Azure Event Grid
description: Ukazuje, jak vytvářet předplatná Azure Event Grid, která umožňuje filtrovat události.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: spelluru
ms.openlocfilehash: 95a0d1b8afba71f6c8226dfe1ad5268d9e6f24e1
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816913"
---
# <a name="filter-events-for-event-grid"></a>Filtr událostí služby Event Grid

Tento článek ukazuje, jak filtrovat události při vytváření odběr služby Event Grid. Další informace o možnostech pro filtrování událostí, naleznete v tématu [porozumění události filtrování pro odběry služby Event Grid](event-filtering.md).

## <a name="filter-by-event-type"></a>Filtrovat podle typu události

Když vytváříte odběr Event gridu, určení [typy událostí](event-schema.md) odeslat ke koncovému bodu. Příklady v této části vytvořit odběry událostí pro skupinu prostředků, ale omezení událostí, které se pošlou `Microsoft.Resources.ResourceWriteFailure` a `Microsoft.Resources.ResourceWriteSuccess`. Pokud potřebujete větší flexibilitu při filtrování události podle typů událostí, naleznete v tématu filtrovat podle rozšířené operátory a datová pole.

Pokud používáte PowerShell, použijte `-IncludedEventType` parametr při vytváření odběru.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzureRmEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

Používáte Azure CLI, použijte `--included-event-types` parametru. Rozhraní příkazového řádku Azure v prostředí Bash v následujícím příkladu:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

Šablony Resource Manageru, použijte `includedEventTypes` vlastnost.

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

Můžete filtrovat události podle předmětu v datech události. Můžete určit hodnotu tak, aby odpovídaly začátku nebo konci předmět. Pokud potřebujete větší flexibilitu při filtrování událostí podle předmětu, najdete v článku filtrovat podle rozšířené operátory a datová pole.

V následujícím příkladu Powershellu vytvoříte odběr událostí, který filtruje podle začátku předmětu. Můžete použít `-SubjectBeginsWith` parametr, chcete-li omezit události, které pro určitý prostředek. Můžete předat ID prostředku, skupiny zabezpečení sítě.

```powershell
$resourceId = (Get-AzureRmResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzureRmEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

Následující příklad Powershellu vytvoří odběr pro úložiště objektů blob. Omezuje události, které s subjektem, který končí na `.jpg`.

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzureRmEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

Následující příklad rozhraní příkazového řádku Azure vytvoříte odběr událostí, který filtruje podle začátku předmětu. Můžete použít `--subject-begins-with` parametr, chcete-li omezit události, které pro určitý prostředek. Můžete předat ID prostředku, skupiny zabezpečení sítě.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

Následující příklad rozhraní příkazového řádku Azure vytvoří předplatné pro úložiště objektů blob. Omezuje události, které s subjektem, který končí na `.jpg`.

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

V následujícím příkladu šablony Resource Manageru vytvoříte odběr událostí, který filtruje podle začátku předmětu. Můžete použít `subjectBeginsWith` vlastnost omezit události, které konkrétní prostředek. Můžete předat ID prostředku, skupiny zabezpečení sítě.

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

Další příklad šablony Resource Manageru vytvoří odběr pro úložiště objektů blob. Omezuje události, které s subjektem, který končí na `.jpg`.

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

## <a name="filter-by-operators-and-data"></a>Filtrovat podle operátory a data

Pro větší flexibilitu při filtrování můžete použít operátory a vlastnosti dat můžete filtrovat události.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

### <a name="subscribe-with-advanced-filters"></a>K přihlášení k rozšířené filtry odběru

Další informace o operátorech a klíče, které můžete použít pro rozšířené filtrování, najdete v článku [rozšířené filtrování](event-filtering.md#advanced-filtering).

Tyto příklady vytvoření vlastního tématu. Umožňují přihlášení k odběru vlastního tématu a filtrování podle hodnoty datového objektu. Události, které mají nastavenou na modrou barvu vlastnost, červenou nebo zelenou se odesílají do předplatného.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
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

```azurepowershell-interactive
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzureRmResourceGroup -Name gridResourceGroup -Location eastus2
New-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="test-filter"></a>Test filtru

Filtr otestovat odeslání události s poli Barva nastavena na zelenou. Protože zelené je jedna z hodnot ve filtru, se doručí události do koncového bodu.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

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

K otestování scénář, kde nejsou odesílány události odeslání události s poli Barva nastavena na žlutou barvou. Žlutý není jeden z hodnoty zadané v daném předplatném, takže události se doručí do vašeho předplatného.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
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

## <a name="next-steps"></a>Další postup

* Informace o monitorování doručení událostí najdete v tématu [doručování zpráv služby Event Grid monitorování](monitor-event-delivery.md).
* Další informace o ověřovací klíč najdete v tématu [ověřování a zabezpečení služby Event Grid](security-authentication.md).
* Další informace o vytváření předplatného služby Azure Event Grid najdete v tématu [schéma předplatného služby Event Grid](subscription-creation-schema.md).
