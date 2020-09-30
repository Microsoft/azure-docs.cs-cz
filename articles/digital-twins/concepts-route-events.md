---
title: Trasy událostí
titleSuffix: Azure Digital Twins
description: Naučte se směrovat události v rámci digitálních vláken Azure a dalších služeb Azure.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d41518b1fc0d8cdda3ded1e8036bd29e24e2b34a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541352"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Směrování událostí v rámci digitálních vláken Azure a mimo ně

Digitální vlákna Azure využívá **trasy událostí** k posílání dat příjemcům mimo službu. 

Během období Preview existují dva hlavní případy odeslání dat digitálních vláken Azure:
* Posílání dat z jednoho vlákna v grafu digitálních vláken Azure do jiného. Například pokud se u jedné z digitálních vláken změní vlastnost, můžete chtít odpovídajícím způsobem informovat a aktualizovat další digitální vlákna.
* Odesílání dat do služeb pro další úložiště nebo zpracování (označované také jako *výstup dat*). Příklad:
  - Nemocnice může chtít odeslat data události z digitálního vlákna Azure do [Time Series Insights (TSI)](../time-series-insights/time-series-insights-update-overview.md)k zaznamenávání dat časových řad událostí souvisejících s handwashing pro hromadnou analýzu.
  - Společnost, která už používá [Azure Maps](../azure-maps/about-azure-maps.md) , může chtít použít digitální vlákna Azure k vylepšení jejich řešení. Můžou po nastavení digitálních vláken Azure rychle povolit mapu Azure, přenést entity mapy Azure do digitálních vláken Azure jako [digitálních vláken](concepts-twins-graph.md) ve dvojitých grafech nebo spouštět výkonné dotazy využívající jejich Azure Maps a data z digitálního vlákna Azure.

V obou těchto scénářích se používají trasy událostí.

## <a name="about-event-routes"></a>O trasách událostí

Trasa události umožňuje odesílat data událostí z digitálních vláken v Azure do vlastních koncových bodů ve vašich předplatných. Pro koncové body jsou aktuálně podporované tři služby Azure: [centrum událostí](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)a [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Každá z těchto služeb Azure se může připojit k jiným službám a funguje jako střední, odesílá data do konečných míst, jako je například TSI nebo Azure Maps pro jakékoli zpracování, které potřebujete.

Následující diagram znázorňuje tok dat událostí prostřednictvím většího řešení IoT s aspektem digitálních vláken Azure:

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="Služba Azure Digital prochází směrování dat prostřednictvím koncových bodů až po několik služeb pro příjem dat." border="false":::

Typickými podřízenými cíli pro trasy událostí jsou prostředky, jako jsou TSI, Azure Maps, úložiště a analytická řešení.

### <a name="event-routes-for-internal-digital-twin-events"></a>Trasy událostí pro interní digitální události s dvojitou událostí

Během aktuální verze Preview se pro zpracování událostí v grafu s dvojitou přesností používají i trasy událostí a odesílají data z digitálního vlákna do digitálního vlákna. To se provádí připojením směrování událostí prostřednictvím Event Grid k výpočetním prostředkům, jako je například [Azure Functions](../azure-functions/functions-overview.md). Tyto funkce pak definují, jak by vlákna měly přijímat a reagovat na události. 

Pokud chce výpočetní prostředek upravit vydaný graf na základě události, kterou získala prostřednictvím postupu události, je užitečné, aby věděl, který z nich chce změnit na předem. 

Alternativně zpráva o události obsahuje také ID zdroje dat, který zprávu odeslal, takže výpočetní prostředek může pomocí dotazů nebo průchodů najít cílový objekt vláken pro požadovanou operaci. 

Výpočetní prostředek musí také navázat oprávnění zabezpečení a přístup nezávisle.

Informace o postupu při nastavení funkce Azure pro zpracování digitálních dvojitých událostí najdete v tématu [*How to: set a Azure Function for Data Processing*](how-to-create-azure-function.md).

## <a name="create-an-endpoint"></a>Vytvoření koncového bodu

Aby bylo možné definovat trasu události, vývojáři musí nejprve definovat koncové body. **Koncový bod** je cíl mimo digitální vlákna Azure, které podporuje připojení ke směrování. Podporované cíle v aktuální verzi Preview jsou:
* Event Grid vlastní témata
* Centrum událostí
* Service Bus

Pokud chcete vytvořit koncový bod, můžete použít [**rozhraní API plochy ovládacího prvku**](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins)Azure Digital vlákna, [**příkazy CLI**](how-to-manage-routes-apis-cli.md#manage-endpoints-and-routes-with-cli)nebo [**Azure Portal**](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins). 

Při definování koncového bodu je potřeba zadat:
* Název koncového bodu
* Typ koncového bodu (Event Grid, centrum událostí nebo Service Bus)
* Primární připojovací řetězec a sekundární připojovací řetězec, který se má ověřit 
* Cesta k tématu koncového bodu, například *Your-topic.westus2.eventgrid.Azure.NET*

Rozhraní API koncového bodu, která jsou k dispozici v řídicí rovině, jsou:
* Vytvořit koncový bod
* Získá seznam koncových bodů.
* Získat koncový bod podle názvu
* Odstranit koncový bod podle názvu

## <a name="create-an-event-route"></a>Vytvoření trasy události
 
Pokud chcete vytvořit trasu události, můžete použít [**rozhraní API roviny dat**](how-to-manage-routes-apis-cli.md#create-an-event-route)digitálních vláken Azure, [**příkazy CLI**](how-to-manage-routes-apis-cli.md#manage-endpoints-and-routes-with-cli)nebo [**Azure Portal**](how-to-manage-routes-portal.md#create-an-event-route). 

Tady je příklad vytvoření trasy události v rámci klientské aplikace pomocí `CreateEventRoute` volání [rozhraní .NET (C#) SDK](how-to-use-apis-sdks.md) : 

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

1. Nejprve `EventRoute` je vytvořen objekt a konstruktor převezme název koncového bodu. Toto `endpointName` pole označuje koncový bod, jako je například centrum událostí, Event Grid nebo Service Bus. Tyto koncové body je potřeba vytvořit v předplatném a připojit se k digitálním plochám Azure pomocí rozhraní API řídicích rovin před provedením tohoto volání registrace.

2. Objekt směrování událostí má také pole [**filtru**](./how-to-manage-routes-apis-cli.md#filter-events) , pomocí kterého lze omezit typy událostí, které následují po této trase. Filtr `true` umožňuje trasu bez dalšího filtrování (filtr `false` zakáže trasu). 

3. Tento objekt směrování události je pak předán `CreateEventRoute` spolu s názvem trasy.

> [!TIP]
> Všechny funkce sady SDK přicházejí v synchronních a asynchronních verzích.

Trasy je také možné vytvořit pomocí rozhraní příkazového [řádku Azure Digital zdvojené](how-to-use-cli.md).

### <a name="types-of-event-messages"></a>Typy zpráv událostí

Různé typy událostí v IoT Hub a digitálních Vlákenách Azure vytváří různé typy oznamovacích zpráv, jak je popsáno níže.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak nastavit a spravovat postup události:
* [*Postupy: Správa koncových bodů a tras*](how-to-manage-routes-apis-cli.md)

Nebo si přečtěte téma jak použít Azure Functions ke směrování událostí v rámci digitálních vláken Azure:
* [*Postupy: nastavení funkce Azure pro zpracování dat*](how-to-create-azure-function.md)