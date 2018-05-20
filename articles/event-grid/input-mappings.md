---
title: Mapování vlastních polí na schéma Azure událostí mřížky
description: Popisuje, jak převést váš vlastní schéma na schéma mřížky událostí Azure.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 32f93f383ec4044afb0696fcef1705c9ed65d673
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Vlastní pole mapy událostí mřížky schématu

Pokud vaše data události neodpovídá očekávanému [událostí mřížky schématu](event-schema.md), stále můžete mřížky událostí pro událost trasy odběratelům. Tento článek popisuje způsob namapování vašeho schématu do schématu událostí mřížky.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Původní schématu události

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

I když tento formát se neshoduje se požadované schéma, mřížky událostí umožňuje vaše pole mapovány na schéma. Nebo se může zobrazit hodnoty v původní schématu.

## <a name="create-custom-topic-with-mapped-fields"></a>Vytvořit vlastní téma s namapovanou pole

Při vytváření vlastní téma, určete, jak mapování polí z vašeho původního události schématu mřížky událostí. Existují tři vlastnosti, které můžete použít k přizpůsobení mapování:

* `--input-schema` Parametr určuje typ schématu. Jsou dostupné možnosti *cloudeventv01schema*, *customeventschema*, a *eventgridschema*. Výchozí hodnota je eventgridschema. Při vytváření vlastní mapování mezi schéma a schéma událostí mřížky, použijte customeventschema. Když se události CloudEvents schéma, použijte cloudeventv01schema.

* `--input-mapping-default-values` Parametr určuje výchozí hodnoty pro pole ve schématu událostí mřížky. Můžete nastavit výchozí hodnoty pro *subjektu*, *eventtype*, a *dataversion*. Tento parametr se obvykle používají při vlastní schéma neobsahuje pole, které odpovídá jednomu z těchto tří polí. Například můžete zadat, že dataversion je vždycky nastavený na **1.0**.

* `--input-mapping-fields` Parametr mapuje pole z vašeho schématu do schématu mřížky událostí. Zadejte hodnoty do páry klíč – hodnota oddělených mezerami. Název klíče použijte název pole událostí mřížky. Hodnota použijte název pole. Můžete použít názvy klíčů pro *id*, *tématu*, *eventtime*, *subjektu*, *eventtype*a *dataversion*.

Následující příklad vytvoří vlastní téma s některými namapované a výchozí pole:

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

## <a name="subscribe-to-event-grid-topic"></a>Přihlášení k odběru událostí mřížky tématu

Když se přihlásíte k odběru vlastní tématu, určíte schéma, které chcete použít pro příjem událostí. Můžete použít `--event-delivery-schema` parametr a nastavte ji na *cloudeventv01schema*, *eventgridschema*, nebo *inputeventschema*. Výchozí hodnota je eventgridschema.

Příklady v této části použijte Queue storage pro obslužné rutiny události. Další informace najdete v tématu [směrovat vlastních událostí Azure Queue storage](custom-event-to-queue-storage.md).

V následujícím příkladu jako odběratel u mřížky téma události a používá výchozí schéma mřížky událostí:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub1 \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

Další příklad používá vstupní schéma události:

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

Nyní jste připraveni k odeslání události do vlastní tématu a zobrazí výsledek mapování. Následující skript pro událost v příspěvku [příklad schématu](#original-event-schema):

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/mapeventfields.json)'")

curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Nyní podívejte se na Queue storage. Obě předplatná událostí doručit v různých schématech.

První předplatné použít schématu mřížky události. Formát doručené události je:

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

Tato pole obsahují mapování z vlastní heslo. **myEventTypeField** je namapována na **EventType**. Výchozí hodnoty pro **DataVersion** a **subjektu** se používají. **Data** objekt obsahuje pole původní schématu událostí.

Druhého předplatného používá schéma vstupní událost. Formát doručené události je:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": { "someDataField1": "SomeDataFieldValue" }
}
```

Všimněte si, že byly dodány původní pole.

## <a name="next-steps"></a>Další postup

* Informace o události doručení a opakovaných pokusů [doručení zpráv událostí mřížky a zkuste to znovu](delivery-and-retry.md).
* Úvod k mřížce událostí, naleznete v části [o mřížky událostí](overview.md).
* Chcete-li rychle začít používat událostí mřížky, přečtěte si téma [vytvořit a směrování vlastních událostí s Azure událostí mřížky](custom-event-quickstart.md).
