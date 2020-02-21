---
title: Reakce na události klíč-hodnota konfigurace aplikace Azure
description: Použijte Azure Event Grid k přihlášení k odběru událostí konfigurace aplikace.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: a4f61d147ba1abf73ada6360b8d0d965d8e063a5
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523794"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reakce na události konfigurace aplikace Azure

Události konfigurace aplikace Azure umožňují aplikacím reagovat na změny v klíčových hodnotách. Tato operace se provádí bez složitého kódu nebo nákladných a neefektivních služeb cyklického dotazování. Místo toho se události odesílají prostřednictvím [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) předplatitelům, jako je [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)nebo dokonce i vlastní naslouchací proces http. V kritickém případě platíte jenom za to, co využijete.

Události konfigurace aplikace Azure se odesílají do Azure Event Grid, které vašim aplikacím poskytuje spolehlivé služby doručování prostřednictvím zásad opakovaného opakování a doručování nedoručených zpráv. Další informace najdete v tématu [Event Grid doručování zpráv a zkuste to znovu](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Mezi běžné scénáře události konfigurace aplikací patří aktualizace konfigurace aplikace, aktivace nasazení nebo libovolný pracovní postup orientovaný na konfiguraci. Pokud jsou změny nečasté, ale scénář vyžaduje okamžitou odezvu, může být velmi efektivní architektura založená na událostech.

Podívejte se na stručný příklad [Směrování událostí konfigurace aplikace Azure do vlastního webového koncového bodu – rozhraní příkazového řádku](./howto-app-configuration-event.md) . 

![Model Event Grid](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Dostupné události konfigurace aplikace Azure
Event Grid používá [odběry událostí](../event-grid/concepts.md#event-subscriptions) ke směrování zpráv událostí odběratelům. Odběry událostí konfigurace aplikace Azure můžou zahrnovat dva typy událostí:  

> |Název události|Popis|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Je aktivována, když je vytvořena nebo nahrazena klíčová hodnota.|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Je aktivována, když se odstraní klíčová hodnota.|

## <a name="event-schema"></a>Schéma událostí
Události konfigurace aplikace Azure obsahují všechny informace, které potřebujete k reakci na změny ve vašich datech. Můžete identifikovat událost konfigurace aplikace, protože vlastnost eventType začíná na Microsoft. AppConfiguration. Další informace o použití vlastností události Event Grid najdete v části [Event Grid schéma událostí](../event-grid/event-schema.md).  

> |Vlastnost|Typ|Popis|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |topic|string|Úplné Azure Resource Manager ID konfigurace aplikace, která událost emituje.|
> |subject|string|Identifikátor URI klíč-hodnota, která je předmětem události.|
> |eventTime|string|Datum a čas generování události ve formátu ISO 8601.|
> |eventType|string|"Microsoft. AppConfiguration. KeyValueModified" nebo "Microsoft. AppConfiguration. KeyValueDeleted".|
> |ID|string|Jedinečný identifikátor této události.|
> |dataVersion|string|Verze schématu datového objektu.|
> |metadataVersion|string|Verze schématu vlastností nejvyšší úrovně.|
> |data|object|Kolekce dat událostí specifických pro konfiguraci aplikací Azure|
> |data. Key|string|Klíč hodnoty klíč-hodnota, která byla upravena nebo odstraněna.|
> |data. Label|string|Popisek (pokud existuje) hodnoty klíč-hodnota, která byla upravena nebo odstraněna.|
> |data.etag|string|Pro `KeyValueModified` značku nového klíč-hodnota. Pro `KeyValueDeleted` značku ETag hodnoty klíč-hodnota, která byla odstraněna.|

Tady je příklad události KeyValueModified:
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

Další informace najdete v tématu [schéma událostí konfigurace aplikace Azure](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Postupy pro náročné události
Aplikace, které zpracovávají události konfigurace aplikace, by měly dodržovat tyto Doporučené postupy:
> [!div class="checklist"]
> * Více předplatných lze nakonfigurovat pro směrování událostí do stejné obslužné rutiny události, takže nepředpokládá, že události jsou z konkrétního zdroje. Místo toho zkontrolujte téma zprávy a ujistěte se, že instance konfigurace aplikace posílá událost.
> * Ověřte typ eventType a Nepředpokládat, že všechny události, které obdržíte, budou typy, které jste očekávali.
> * Pomocí polí ETag Zjistěte, jestli jsou informace o objektech pořád aktuální.  
> * Pomocí polí Sequencer můžete pochopit pořadí událostí u libovolného konkrétního objektu.
> * Pro přístup k upravované hodnotě klíče použijte pole Subject.


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o Event Grid a poskytněte události konfigurace aplikací Azure a zkuste:

- [Informace o službě Event Grid](../event-grid/overview.md)
- [Směrování událostí konfigurace aplikace Azure do vlastního webového koncového bodu](./howto-app-configuration-event.md)