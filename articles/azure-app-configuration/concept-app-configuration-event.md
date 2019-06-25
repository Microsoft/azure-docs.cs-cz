---
title: Reagující na události klíč hodnota konfigurace aplikace pro Azure | Dokumentace Microsoftu
description: Přihlášení k odběru událostí konfigurace aplikací pomocí služby Azure Event Grid.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 05/30/2019
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 601124aef37d2b285db71130f5c63b3620c7768f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735643"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reakce na události konfigurace aplikace pro Azure

Události konfigurace aplikace Azure umožňují aplikacím reagovat na změny hodnoty klíče. To se provádí bez nutnosti složité kódu nebo nákladné a neefektivní cyklického dotazování služby. Místo toho události se nasdílejí [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) pro předplatitele, jako [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), nebo dokonce vlastní naslouchací proces protokolu http vlastní a pouze Plaťte za to co používáte.

Události konfigurace aplikace služby Azure se odesílají do služby Azure Event Grid, která poskytuje spolehlivé doručování služeb do aplikací prostřednictvím bohaté opakování zásady a doručování onta nedoručených zpráv. Další informace najdete v tématu [doručování zpráv služby Event Grid a zkuste to znovu](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Mezi obvyklé scénáře událostí konfigurace aplikace patří aktualizace konfigurace aplikace, aktivuje nasazení nebo jakýkoli pracovní postup konfigurace objektově orientovaný. Pokud jsou málo časté změny, ale vaše situace vyžaduje okamžitou odezvu, může být zvláště efektivní architektury založené na události.

Podívejte se na [události konfigurace aplikace Azure trasu na vlastní web koncového bodu – rozhraní příkazového řádku](./howto-app-configuration-event.md) rychlý příklad. 

![Event Grid modelu](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>K dispozici události konfigurace aplikace Azure
Pomocí služby Event grid [odběry událostí](../event-grid/concepts.md#event-subscriptions) pro směrování zpráv událostí pro předplatitele. Azure odběry událostí konfigurace aplikace může obsahovat dva typy událostí:  

> |Název události|Popis|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Je aktivována při vytvoření nebo nahradit páru klíč hodnota|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Je aktivována při odstranění páru klíč hodnota|

## <a name="event-schema"></a>Schéma událostí
Události konfigurace aplikace služby Azure obsahovat všechny informace, které je potřeba reagovat na změny ve vašich datech. Události konfigurace aplikace můžete identifikovat, protože vlastnost Typ události začíná řetězcem "Microsoft.AppConfiguration". Další informace o použití vlastností událostí služby Event Grid je popsána v [schéma událostí služby Event Grid](../event-grid/event-schema.md).  

> |Vlastnost|Typ|Popis|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |topic|string|Konfigurace aplikací, který vysílá události úplné id Azure Resource Manageru.|
> |subject|string|Identifikátor URI klíč hodnota, která je předmětem události.|
> |eventTime|string|Datum/čas, která byla vygenerována událost ve formátu ISO 8601.|
> |eventType|string|"Microsoft.AppConfiguration.KeyValueModified" nebo "Microsoft.AppConfiguration.KeyValueDeleted".|
> |ID|string|Jedinečný identifikátor této události.|
> |dataVersion|string|Verze schématu datového objektu|
> |metadataVersion|string|Verze schématu vlastnosti nejvyšší úrovně.|
> |data|objekt|Shromažďování dat o určité události konfigurace aplikace pro Azure|
> |data.Key|string|Klíč klíč hodnota, která byla změněna nebo odstraněna.|
> |data.label|string|Popisek, pokud je libovolná z klíč hodnota, která byla změněna nebo odstraněna.|
> |data.etag|string|Pro `KeyValueModified` etag nový klíč hodnota. Pro `KeyValueDeleted` etag klíč hodnota, která byla odstraněna.|

Tady je příklad KeyValueModified události:
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

Další informace najdete v tématu [schématu událostí konfigurace aplikace pro Azure](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Postupy pro spotřebovávajících událostí
Aplikace, které zpracovávají události konfigurace služby aplikace postupujte podle několik doporučených postupů:
> [!div class="checklist"]
> * Směrování událostí na stejný ovladač událostí dá nakonfigurovat několik předplatných, je důležité se předpokládá, že události pocházejí z určitého zdroje, ale ke kontrole tématu zprávy, která se ujistěte, že pocházejí z konfigurace aplikace, které jste očekávali.
> * Podobně zkontrolujte, že typ události jsou připraveny k procesu, a Nepředpokládejte, že všechny události, které se zobrazí typy, které očekáváte, že bude.
> * Jak můžete doručování zpráv mimo pořadí a po určité prodlevě, použijte pole etag pochopit, pokud je stále aktuální informace o objektech.  Také použijte pole aplikace sequencer k vysvětlení pořadí událostí pro jakékoli konkrétní objekt.
> * Pomocí pole předmětu pro přístup k klíč hodnota, která byla změněna.


## <a name="next-steps"></a>Další postup

Další informace o službě Event Grid a vyzkoušejte události konfigurace aplikace Azure:

- [Informace o službě Event Grid](../event-grid/overview.md)
- [Směrování událostí služby konfigurace aplikací v Azure do vlastního webového koncového bodu](./howto-app-configuration-event.md)