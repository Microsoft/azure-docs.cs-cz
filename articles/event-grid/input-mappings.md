---
title: Mapování vlastního pole na schéma Sítě událostí Azure
description: Tento článek popisuje, jak převést vlastní schéma do schématu Azure Event Grid, když vaše data událostí neodpovídá schématu Mřížka událostí.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: e8077068a265d659cf6009eb7762188637c373d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721655"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mapování vlastních polí na schéma Event Gridu

Pokud data událostí neodpovídají očekávanému [schématu Event Grid](event-schema.md), můžete událost směrovat odběratelům pomocí služby Event Grid. Tento článek popisuje, jak namapovat schéma na schéma mřížky událostí.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalace funkce náhledu

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Původní schéma události

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

I když tento formát neodpovídá požadovanému schématu, funkce Event Grid umožňuje mapovat pole na schéma. Nebo můžete přijímat hodnoty v původním schématu.

## <a name="create-custom-topic-with-mapped-fields"></a>Vytvoření vlastního tématu s namapovanými poli

Při vytváření vlastního tématu určete, jak mapovat pole z původní události do schématu mřížky událostí. Mapování lze použít třemi hodnotami:

* Hodnota **vstupního schématu** určuje typ schématu. Dostupné možnosti jsou Schéma CloudEvents, vlastní schéma událostí nebo schéma mřížky událostí. Výchozí hodnota je schéma Mřížky událostí. Při vytváření vlastního mapování mezi schématem a schématem mřížky událostí použijte vlastní schéma událostí. Pokud jsou události ve schématu CloudEvents, použijte cloududálosti schéma.

* Vlastnost **výchozí hodnoty mapování** určuje výchozí hodnoty polí ve schématu Mřížka událostí. Můžete nastavit výchozí `subject`hodnoty `eventtype`pro `dataversion`aplikace , a . Tento parametr obvykle použijete, pokud vlastní schéma neobsahuje pole, které odpovídá jednomu z těchto tří polí. Můžete například určit, že verze dat je vždy nastavena na **hodnotu 1.0**.

* Hodnota **mapování polí** mapuje pole ze schématu do schématu mřížky událostí. Hodnoty zadáváte v dvojicích klíč/hodnota oddělených prostory. Pro název klíče použijte název pole mřížky událostí. Pro hodnotu použijte název pole. Můžete použít názvy `id` `topic`klíčů `eventtime` `subject`pro `eventtype`, `dataversion`, , , a .

Pokud chcete vytvořit vlastní téma pomocí azure cli, použijte:

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

## <a name="subscribe-to-event-grid-topic"></a>Přihlásit se k odběru tématu mřížky událostí

Při přihlášení k odběru vlastního tématu zadáte schéma, které chcete použít pro příjem událostí. Zadáte schéma CloudEvents, vlastní schéma událostí nebo schéma mřížky událostí. Výchozí hodnota je schéma Mřížky událostí.

Následující příklad se přihlásí k odběru tématu mřížky událostí a použije schéma Mřížky událostí. Pokud používáte Azure CLI, použijte:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

Další příklad používá vstupní schéma události:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

Následující příklad se přihlásí k odběru tématu mřížky událostí a použije schéma Mřížky událostí. Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

Další příklad používá vstupní schéma události:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Publikovat událost do tématu

Nyní jste připraveni odeslat událost do vlastního tématu a zobrazit výsledek mapování. Následující skript pro zaúčtování události v [ukázkovém schématu](#original-event-schema):

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

Nyní se podívejte na koncový bod WebHooku. Dvě předplatná doručovala události v různých schématech.

První předplatné používá schéma mřížky událostí. Formát dodané události je:

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

Tato pole obsahují mapování z vlastního tématu. **pole myEventTypeField** je mapováno na **eventtype**. Použijí se výchozí hodnoty pro **DataVersion** a **Subject.** Objekt **Data** obsahuje původní pole schématu událostí.

Druhé předplatné používá schéma vstupní události. Formát dodané události je:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

Všimněte si, že původní pole byla doručena.

## <a name="next-steps"></a>Další kroky

* Informace o doručení události a opakování zpráv [event grid u zpráv a opakování](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte [si tématu Vytváření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).
