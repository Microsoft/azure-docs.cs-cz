---
title: Mapování vlastních polí do schématu služby Azure Event Grid
description: Tento článek popisuje, jak převést vlastní schéma na Azure Event Grid schématu, když data události neodpovídají schématu Event Grid.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: e8077068a265d659cf6009eb7762188637c373d6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721655"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mapování vlastních polí na schéma Event Gridu

Pokud vaše data události neodpovídají očekávanému [schématu Event Grid](event-schema.md), můžete i nadále používat Event Grid ke směrování událostí odběratelům. Tento článek popisuje způsob mapování schématu do schématu služby Event Grid.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Nainstalujte funkci ve verzi preview

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Původní schéma událostí

Předpokládejme, že máte aplikaci, která odesílá události v následujícím formátu:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

I když tento formát neodpovídá požadovanému schématu, služby Event Grid umožňuje mapování polí na schéma. Nebo se zobrazí hodnoty v původní schématu.

## <a name="create-custom-topic-with-mapped-fields"></a>Vytvoření vlastního tématu pomocí namapovaná pole

Při vytváření vlastního tématu, určete, jak mapovat pole z původní události mřížka schématu událostí. Existují tři hodnoty, které můžete použít k přizpůsobení mapování:

* Hodnota **vstupního schématu** určuje typ schématu. Dostupné možnosti jsou schématu CloudEvents, vlastní událost schématu nebo schématu služby Event Grid. Výchozí hodnota je schéma služby Event Grid. Při vytváření vlastních mapování mezi schéma a schéma tabulky událostí použijte schéma vlastních událostí. Když události ve schématu CloudEvents, použití schématu Cloudevents.

* Vlastnost **mapování výchozích hodnot** určuje výchozí hodnoty pro pole ve schématu Event Grid. Můžete nastavit výchozí hodnoty pro `subject`, `eventtype`a `dataversion`. Obvykle použijete tento parametr, když vaše vlastní schéma neobsahuje pole, které odpovídá jedné z těchto tří polí. Například můžete určit, že verze dat je vždy nastavená na **1,0**.

* Hodnota **mapování polí** mapuje pole ze schématu do schématu Event Grid. Zadejte hodnoty do dvojic klíč/hodnota oddělených mezerami. Název klíče použijte název pole event grid. Pro hodnotu použijte název vlastního pole. Můžete použít názvy klíčů pro `id`, `topic`, `eventtime`, `subject`, `eventtype`a `dataversion`.

K vytvoření vlastního tématu pomocí Azure CLI, použijte:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema \
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName myResourceGroup `
  -Name demotopic `
  -Location eastus2 `
  -InputSchema CustomEventSchema `
  -InputMappingField @{eventType="myEventTypeField"} `
  -InputMappingDefaultValue @{subject="DefaultSubject"; dataVersion="1.0" }
```

## <a name="subscribe-to-event-grid-topic"></a>Přihlášení k odběru tématu event gridu

Když se přihlásíte k odběru vlastního tématu, zadejte schéma, které chcete použít pro příjem událostí. Zadáte schématu CloudEvents, vlastní událost schématu nebo schéma služby Event Grid. Výchozí hodnota je schéma služby Event Grid.

Následující příklad odebírá téma event gridu a používá schéma služby Event Grid. Pokud používáte Azure CLI, použijte:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

Následující příklad používá schéma vstupní události:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

Následující příklad odebírá téma event gridu a používá schéma služby Event Grid. Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

Následující příklad používá schéma vstupní události:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Publikování událostí do tématu

Teď jste připraveni k odeslání události do vlastního tématu a zobrazit výsledek mapování. Následující skript pro publikování události v [ukázkovém schématu](#original-event-schema):

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

event='[ { "myEventTypeField":"Created", "resource":"Users/example/Messages/1000", "resourceData":{"someDataField1":"SomeDataFieldValue"} } ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demotopic).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName myResourceGroup -Name demotopic

$htbody = @{
    myEventTypeField="Created"
    resource="Users/example/Messages/1000"
    resourceData= @{
        someDataField1="SomeDataFieldValue"
    }
}

$body = "["+(ConvertTo-Json $htbody)+"]"
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Nyní podívejte se na váš koncový bod Webhooku. Dva odběry doručení událostí v různých schémat.

První předplatné používá event grid schématu. Formát doručené událostí je:

```json
{
  "id": "aa5b8e2a-1235-4032-be8f-5223395b9eae",
  "eventTime": "2018-11-07T23:59:14.7997564Z",
  "eventType": "Created",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "subject": "DefaultSubject",
  "data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": {
      "someDataField1": "SomeDataFieldValue"
    }
  }
}
```

Tato pole obsahují mapování z vlastního tématu. **myEventTypeField** je mapován na **typ EventType**. Použijí se výchozí hodnoty **dataversion** a **Subject** . **Datový** objekt obsahuje pole původních schémat událostí.

Druhého předplatného používá schéma vstupních událostí. Formát doručené událostí je:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

Všimněte si, že byly dodány původního pole.

## <a name="next-steps"></a>Další kroky

* Pro informace o doručení a opakování události [Event Grid doručování zpráv a akci opakujte](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku o [Event Gridu](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte si téma [Vytvoření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).
