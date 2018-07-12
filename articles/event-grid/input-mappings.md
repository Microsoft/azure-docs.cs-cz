---
title: Mapování vlastních polí do schématu služby Azure Event Grid
description: Popisuje, jak převést vaše vlastní schéma do schématu služby Azure Event Grid.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 32f93f383ec4044afb0696fcef1705c9ed65d673
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38578913"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mapování vlastních polí na schéma služby Event Grid

Pokud vaše data události neodpovídá očekávané [schéma služby Event Grid](event-schema.md), stále můžete služby Event Grid směrování události odběratelům. Tento článek popisuje způsob mapování schématu do schématu služby Event Grid.

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

Při vytváření vlastního tématu, určete, jak mapovat pole z původní události mřížka schématu událostí. Existují tři vlastnosti, které můžete použít k přizpůsobení mapování:

* `--input-schema` Parametr určuje typ schématu. Dostupné jsou následující možnosti *cloudeventv01schema*, *customeventschema*, a *eventgridschema*. Výchozí hodnota je eventgridschema. Při vytváření vlastních mapování mezi schéma a schéma tabulky událostí použijte customeventschema. Pokud jsou události ve schématu CloudEvents, použijte cloudeventv01schema.

* `--input-mapping-default-values` Parametr určuje výchozí hodnoty pro pole ve schématu služby Event Grid. Můžete nastavit výchozí hodnoty pro *subjektu*, *eventtype*, a *dataversion*. Obvykle použijete tento parametr, když vaše vlastní schéma nezahrnuje pole, které odpovídá jedné z těchto tří polí. Například můžete určit, že dataversion je vždycky nastavený na **1.0**.

* `--input-mapping-fields` Parametr mapuje pole z vašeho schématu do mřížky schématu událostí. Zadejte hodnoty do dvojic klíč/hodnota oddělených mezerami. Název klíče použijte název pole event grid. Pro hodnotu použijte název vlastního pole. Můžete použít názvy klíčů pro *id*, *tématu*, *čas události*, *subjektu*, *eventtype*a *dataversion*.

Následující příklad vytvoří vlastní téma s některými mapovat a výchozí pole:

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

## <a name="subscribe-to-event-grid-topic"></a>Přihlášení k odběru tématu event gridu

Když se přihlásíte k odběru vlastního tématu, zadejte schéma, které chcete použít pro příjem událostí. Můžete použít `--event-delivery-schema` parametr a nastavte ho na *cloudeventv01schema*, *eventgridschema*, nebo *inputeventschema*. Výchozí hodnota je eventgridschema.

Příklady v této části pomocí Queue storage pro obslužnou rutinu události. Další informace najdete v tématu [směrování vlastních událostí do úložiště Azure Queue storage](custom-event-to-queue-storage.md).

V následujícím příkladu odebírá téma event gridu a používá výchozí schéma tabulky událostí:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub1 \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

Následující příklad používá schéma vstupní události:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub2 \
  --event-delivery-schema inputeventschema \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

## <a name="publish-event-to-topic"></a>Publikování událostí do tématu

Nyní jste připraveni k odeslání události do vlastního tématu a zobrazit výsledek mapování. Následující skript pro událost v příspěvku [příkladu schématu](#original-event-schema):

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/mapeventfields.json)'")

curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Nyní podívejte se na Queue storage. Dva odběry doručení událostí v různých schémat.

První předplatné používá event grid schématu. Formát doručené událostí je:

```json
{
  "Id": "016b3d68-881f-4ea3-8a9c-ed9246582abe",
  "EventTime": "2018-05-01T20:00:25.2606434Z",
  "EventType": "Created",
  "DataVersion": "1.0",
  "MetadataVersion": "1",
  "Topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "Subject": "DefaultSubject",
  "Data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": { "someDataField1": "SomeDataFieldValue" } 
  }
}
```

Tato pole obsahují mapování z vlastního tématu. **myEventTypeField** je namapována na **EventType**. Výchozí hodnoty pro **DataVersion** a **subjektu** se používají. **Data** obsahuje původní pole schématu událostí.

Druhého předplatného používá schéma vstupních událostí. Formát doručené událostí je:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": { "someDataField1": "SomeDataFieldValue" }
}
```

Všimněte si, že byly dodány původního pole.

## <a name="next-steps"></a>Další postup

* Informace o doručování událostí a opakovaných pokusů [doručování zpráv služby Event Grid a zkuste to znovu](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat služby Event Grid, přečtěte si téma [vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md).
