---
title: Mapování vlastního pole na Azure Event Grid schéma
description: Tento článek popisuje, jak převést vlastní schéma na Azure Event Grid schématu, když data události neodpovídají schématu Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 836e7b340c5c89100207e2f9409710b8dfa5e3bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86105519"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mapování vlastních polí na schéma Event Gridu

Pokud vaše data události neodpovídají očekávanému [schématu Event Grid](event-schema.md), můžete i nadále používat Event Grid ke směrování událostí odběratelům. Tento článek popisuje, jak namapovat schéma na Event Grid schématu.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Nainstalovat funkci Preview

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Původní schéma událostí

Řekněme, že máte aplikaci, která odesílá události v následujícím formátu:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

I když tento formát neodpovídá požadovanému schématu, Event Grid vám umožní mapovat pole na schéma. Nebo můžete získat hodnoty v původním schématu.

## <a name="create-custom-topic-with-mapped-fields"></a>Vytvoření vlastního tématu s mapovanými poli

Při vytváření vlastního tématu určete, jak se mají mapovat pole z původní události do schématu Event Grid. Existují tři hodnoty, které použijete k přizpůsobení mapování:

* Hodnota **vstupního schématu** určuje typ schématu. Dostupné možnosti jsou schéma CloudEvents, vlastní schéma události nebo schéma Event Grid. Výchozí hodnota je Event Grid schématu. Při vytváření vlastního mapování mezi schématem a schématem služby Event Grid použijte vlastní schéma událostí. Pokud jsou události ve schématu CloudEvents, použijte schéma Cloudevents.

* Vlastnost **mapování výchozích hodnot** určuje výchozí hodnoty pro pole ve schématu Event Grid. Můžete nastavit výchozí hodnoty pro `subject` , `eventtype` a `dataversion` . Tento parametr obvykle použijete, pokud vlastní schéma neobsahuje pole, které odpovídá jednomu z těchto tří polí. Například můžete určit, že verze dat je vždy nastavená na **1,0**.

* Hodnota **mapování polí** mapuje pole ze schématu do schématu Event Grid. Zadejte hodnoty do dvojice klíč/hodnota oddělené mezerou. Jako název klíče použijte název pole Event Grid. Pro tuto hodnotu použijte název pole. Můžete použít názvy klíčů pro `id` , `topic` , `eventtime` ,, a `subject` `eventtype` `dataversion` .

Pokud chcete vytvořit vlastní téma pomocí Azure CLI, použijte:

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

## <a name="subscribe-to-event-grid-topic"></a>Přihlášení k odběru Event gridu – téma

Při přihlášení k odběru vlastního tématu zadáte schéma, které chcete použít pro příjem událostí. Určíte schéma CloudEvents, vlastní schéma události nebo schéma Event Grid. Výchozí hodnota je Event Grid schématu.

V následujícím příkladu se přihlásí k odběru tématu služby Event Grid a použije se schéma Event Grid. Pokud používáte Azure CLI, použijte:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

Následující příklad používá vstupní schéma pro událost:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

V následujícím příkladu se přihlásí k odběru tématu služby Event Grid a použije se schéma Event Grid. Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

Následující příklad používá vstupní schéma pro událost:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Publikovat událost k tématu

Nyní jste připraveni odeslat událost do vlastního tématu a zobrazit výsledek mapování. Následující skript pro publikování události v [ukázkovém schématu](#original-event-schema):

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

Teď se podívejte na koncový bod Webhooku. Dvě předplatná přidávala události v různých schématech.

První předplatné používalo schéma Event gridu. Formát doručené události:

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

Druhé předplatné použilo schéma vstupní události. Formát doručené události:

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

* Pro informace o doručení a opakování události [Event Grid doručování zpráv a akci opakujte](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte si téma [Vytvoření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).
