---
title: Postup filtrování událostí pro Azure Event Grid
description: Tento článek ukazuje, jak filtrovat události (podle typu události, podle předmětu, podle operátorů a dat atd.) při vytváření předplatného Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 99fb00f99a055033ccfcd99e32a52d423878fb44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86105553"
---
# <a name="filter-events-for-event-grid"></a>Filtrovat události pro Event Grid

Tento článek popisuje, jak filtrovat události při vytváření předplatného Event Grid. Další informace o možnostech pro filtrování událostí najdete v tématu [Principy filtrování událostí pro Event Grid odběry](event-filtering.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>Filtrovat podle typu události

Při vytváření předplatného Event Grid můžete určit [typy událostí](event-schema.md) , které se mají odeslat do koncového bodu. Příklady v této části vytvářejí odběry událostí pro skupinu prostředků, ale omezují události, které jsou odesílány do `Microsoft.Resources.ResourceWriteFailure` a `Microsoft.Resources.ResourceWriteSuccess` . Pokud potřebujete větší flexibilitu při filtrování událostí podle typů událostí, přečtěte si téma filtrovat podle pokročilých operátorů a datových polí.

Pro prostředí PowerShell použijte `-IncludedEventType` parametr při vytváření předplatného.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

Pro rozhraní příkazového řádku Azure použijte `--included-event-types` parametr. Následující příklad používá Azure CLI v prostředí bash:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

Pro šablonu Správce prostředků použijte `includedEventTypes` vlastnost.

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

Události můžete filtrovat podle předmětu v datech události. Můžete zadat hodnotu, která se má shodovat s počátkem nebo koncem předmětu. Pokud při filtrování událostí podle předmětu potřebujete větší flexibilitu, přečtěte si téma filtrování podle pokročilých operátorů a datových polí.

V následujícím příkladu PowerShellu vytvoříte odběr událostí, který filtruje na začátku předmětu. Pomocí parametru můžete `-SubjectBeginsWith` omezit události na hodnoty pro konkrétní prostředek. Předáte ID prostředku pro skupinu zabezpečení sítě.

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

Následující příklad PowerShellu vytvoří předplatné pro úložiště objektů BLOB. Omezuje události na ty s předmětem, který končí `.jpg` .

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

V následujícím příkladu Azure CLI vytvoříte odběr událostí, který filtruje na začátku předmětu. Pomocí parametru můžete `--subject-begins-with` omezit události na hodnoty pro konkrétní prostředek. Předáte ID prostředku pro skupinu zabezpečení sítě.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

Další příklad Azure CLI vytvoří předplatné pro úložiště objektů BLOB. Omezuje události na ty s předmětem, který končí `.jpg` .

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

V následujícím příkladu šablony Správce prostředků vytvoříte odběr událostí, který filtruje na začátku předmětu. Pomocí vlastnosti můžete `subjectBeginsWith` omezit události na hodnoty pro konkrétní prostředek. Předáte ID prostředku pro skupinu zabezpečení sítě.

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

Následující příklad šablony Správce prostředků vytvoří předplatné pro úložiště objektů BLOB. Omezuje události na ty s předmětem, který končí `.jpg` .

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

## <a name="filter-by-operators-and-data"></a>Filtrovat podle operátorů a dat

Pro větší flexibilitu při filtrování lze pomocí operátorů a vlastností dat filtrovat události.

### <a name="subscribe-with-advanced-filters"></a>Přihlášení k odběru pomocí rozšířených filtrů

Další informace o operátorech a klíčích, které můžete použít pro rozšířené filtrování, najdete v tématu [Rozšířené filtrování](event-filtering.md#advanced-filtering).

Tyto příklady vytvoří vlastní téma. Přihlásí se k odběru vlastního tématu a filtrují podle hodnoty v datovém objektu. Do předplatného se odesílají události, které mají vlastnost Color nastavenou na modrou, červenou nebo zelenou.

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

### <a name="test-filter"></a>Filtr testů

Chcete-li filtr otestovat, odešlete událost s polem Color nastavenou na zelenou. Vzhledem k tomu, že zelená je jedna z hodnot ve filtru, je událost doručena do koncového bodu.

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

Chcete-li otestovat situaci, kdy událost není odeslána, odešlete událost s polem Color nastavenou na žlutou. Žlutá není jednou z hodnot uvedených v předplatném, takže se událost nedodá vašemu předplatnému.

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

* Informace o sledování doručení událostí najdete v tématu [monitorování Event Grid doručování zpráv](monitor-event-delivery.md).
* Další informace o ověřovacím klíči najdete v tématu [Event Grid Security and Authentication](security-authentication.md).
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).
