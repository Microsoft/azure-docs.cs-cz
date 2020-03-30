---
title: Reakce na události hodnoty klíče konfigurace aplikace Azure
description: K odběru událostí konfigurace aplikací použijte Azure Event Grid.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: a4f61d147ba1abf73ada6360b8d0d965d8e063a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523794"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reakce na události konfigurace aplikací Azure

Události konfigurace aplikací Azure umožňují aplikacím reagovat na změny hodnot klíčů. To se provádí bez nutnosti složitého kódu nebo nákladné a neefektivní volební služby. Místo toho se události přessazují přes [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) předplatitelům, jako jsou Azure [Functions](https://azure.microsoft.com/services/functions/), Azure [Logic Apps](https://azure.microsoft.com/services/logic-apps/)nebo dokonce na vlastní http naslouchací proces. Kriticky platíte pouze za to, co používáte.

Události konfigurace aplikací Azure se odesílají do Azure Event Grid, která poskytuje spolehlivé doručovací služby vašim aplikacím prostřednictvím bohatých zásad opakování a doručování nedoručených dopisů. Další informace najdete v [tématu Doručování zpráv a opakování zprávy v mřížce událostí](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Mezi scénáře událostí běžné konfigurace aplikace patří aktualizace konfigurace aplikace, spuštění nasazení nebo jakýkoli pracovní postup orientovaný na konfiguraci. Pokud změny nejsou časté, ale váš scénář vyžaduje okamžitou odezvu, architektura založená na událostech může být obzvláště efektivní.

Podívejte se na [route Azure App Configuration události na vlastní webový koncový bod – CLI](./howto-app-configuration-event.md) pro rychlý příklad. 

![Model mřížky událostí](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Dostupné události konfigurace aplikací Azure
Mřížka událostí používá [odběry událostí](../event-grid/concepts.md#event-subscriptions) ke směrování zpráv událostí odběratelům. Předplatné událostí konfigurace aplikace Azure může zahrnovat dva typy událostí:  

> |Název události|Popis|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Aktivováno při vytvoření nebo nahrazení hodnoty klíče|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Aktivováno při odstranění hodnoty klíče|

## <a name="event-schema"></a>Schéma událostí
Události konfigurace aplikací Azure obsahují všechny informace, které potřebujete k reakci na změny v datech. Můžete identifikovat událost Konfigurace aplikace, protože vlastnost eventType začíná "Microsoft.AppConfiguration". Další informace o použití vlastností události Event Grid jsou popsány ve [schématu událostí Event Grid](../event-grid/event-schema.md).  

> |Vlastnost|Typ|Popis|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |téma|řetězec|Úplné ID Správce prostředků Azure konfigurace aplikace, která vyzařuje událost.|
> |Předmět|řetězec|Identifikátor URI hodnoty klíče, která je předmětem události.|
> |eventTime|řetězec|Datum a čas, kdy byla událost vygenerována ve formátu ISO 8601.|
> |Eventtype|řetězec|"Microsoft.AppConfiguration.KeyValueModified" nebo "Microsoft.AppConfiguration.KeyValueDeleted".|
> |ID|řetězec|Jedinečný identifikátor této události.|
> |dataVersion|řetězec|Verze schématu datového objektu.|
> |metadataVersion|řetězec|Verze schématu vlastností nejvyšší úrovně.|
> |data|objekt|Shromažďování dat událostí specifických pro konfiguraci aplikace Azure|
> |data.key|řetězec|Klíč hodnoty klíče, která byla změněna nebo odstraněna.|
> |data.label|řetězec|Popisek, pokud existuje, hodnoty klíče, která byla změněna nebo odstraněna.|
> |data.etag|řetězec|Pro `KeyValueModified` etag nové hodnoty klíče. Pro `KeyValueDeleted` etag klíč-hodnota, která byla odstraněna.|

Zde je příklad události KeyValueModified:
```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

Další informace najdete v [tématu Schéma událostí konfigurace aplikace Azure](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Postupy pro náročné události
Aplikace, které zpracovávají události konfigurace aplikace, by se měly řídit následujícími doporučenými postupy:
> [!div class="checklist"]
> * Více odběrů lze nakonfigurovat pro směrování událostí do stejné obslužné rutiny události, takže nepředpokládejte, že události pocházejí z určitého zdroje. Místo toho zkontrolujte téma zprávy a ujistěte se, že instance konfigurace aplikace odesílá událost.
> * Zkontrolujte eventType a nepředpokládejte, že všechny události, které obdržíte, budou typy, které očekáváte.
> * Pomocí polí etag porozumíte tomu, zda jsou informace o objektech stále aktuální.  
> * Pole sekvenceru slouží k pochopení pořadí událostí na libovolném konkrétním objektu.
> * Pole předmět uusejte ke změně hodnoty klíče.


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o Event Grid a vyzkoušejte události konfigurace aplikací Azure:

- [Informace o službě Event Grid](../event-grid/overview.md)
- [Směrování událostí konfigurace aplikací Azure do vlastního koncového bodu webu](./howto-app-configuration-event.md)