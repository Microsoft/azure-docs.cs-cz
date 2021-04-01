---
title: Trasy událostí
titleSuffix: Azure Digital Twins
description: Naučte se směrovat události v rámci digitálních vláken Azure a dalších služeb Azure.
author: baanders
ms.author: baanders
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ea412b695c12f3ff7fdfa6250e2a474b618b8032
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102430917"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Směrování událostí v rámci digitálních vláken Azure a mimo ně

Digitální vlákna Azure využívá **trasy událostí** k posílání dat příjemcům mimo službu. 

Existují dva hlavní případy odeslání dat digitálních vláken Azure:
* Posílání dat z jednoho vlákna v grafu digitálních vláken Azure do jiného. Například pokud se u jedné z digitálních vláken změní vlastnost, můžete chtít odpovídajícím způsobem informovat a aktualizovat další digitální vlákna.
* Odesílání dat do služeb pro další úložiště nebo zpracování (označované také jako *výstup dat*). Příklad:
  - Nemocnice může chtít odeslat data události z digitálního vlákna Azure do [Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md)k zaznamenávání dat časových řad událostí souvisejících s handwashing pro hromadnou analýzu.
  - Společnost, která už používá [Azure Maps](../azure-maps/about-azure-maps.md) , může chtít použít digitální vlákna Azure k vylepšení jejich řešení. Můžou po nastavení digitálních vláken Azure rychle povolit mapu Azure, přenést entity mapy Azure do digitálních vláken Azure jako [digitálních vláken](concepts-twins-graph.md) ve dvojitých grafech nebo spouštět výkonné dotazy využívající jejich Azure Maps a data z digitálního vlákna Azure.

V obou těchto scénářích se používají trasy událostí.

## <a name="about-event-routes"></a>O trasách událostí

Trasa události umožňuje odesílat data událostí z digitálních vláken v Azure do vlastních koncových bodů ve vašich předplatných. Pro koncové body jsou aktuálně podporované tři služby Azure: [centrum událostí](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)a [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Každá z těchto služeb Azure se může připojit k jiným službám a funguje jako střední, odesílá data do konečných míst, jako je například TSI nebo Azure Maps pro jakékoli zpracování, které potřebujete.

Následující diagram znázorňuje tok dat událostí prostřednictvím většího řešení IoT s aspektem digitálních vláken Azure:

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="Služba Azure Digital prochází směrování dat prostřednictvím koncových bodů až po několik služeb pro příjem dat." border="false":::

Typickými podřízenými cíli pro trasy událostí jsou prostředky, jako jsou TSI, Azure Maps, úložiště a analytická řešení.

### <a name="event-routes-for-internal-digital-twin-events"></a>Trasy událostí pro interní digitální události s dvojitou událostí

Trasy událostí slouží také ke zpracování událostí v rámci cyklického grafu a k odesílání dat z digitálního vlákna do digitálního vlákna. To se provádí připojením směrování událostí prostřednictvím Event Grid k výpočetním prostředkům, jako je například [Azure Functions](../azure-functions/functions-overview.md). Tyto funkce pak definují, jak by vlákna měly přijímat a reagovat na události. 

Pokud chce výpočetní prostředek upravit vydaný graf na základě události, kterou získala prostřednictvím postupu události, je užitečné, aby věděl, který z nich chce změnit na předem. 

Alternativně zpráva o události obsahuje také ID zdroje dat, který zprávu odeslal, takže výpočetní prostředek může pomocí dotazů nebo průchodů najít cílový objekt vláken pro požadovanou operaci. 

Výpočetní prostředek musí také navázat oprávnění zabezpečení a přístup nezávisle.

Informace o postupu při nastavení funkce Azure pro zpracování digitálních dvojitých událostí najdete v tématu [*How to: set a Azure Function for Data Processing*](how-to-create-azure-function.md).

## <a name="create-an-endpoint"></a>Vytvoření koncového bodu

Aby mohli vývojáři definovat trasu události, musí nejdřív definovat koncové body. **Koncový bod** je cíl mimo digitální vlákna Azure, které podporuje připojení ke směrování. Mezi podporované cíle patří:
* Event Grid vlastní témata
* Centrum událostí
* Service Bus

Pokud chcete vytvořit koncový bod, můžete použít rozhraní REST API pro digitální vlákna Azure [, příkazy CLI](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins)nebo [Azure Portal](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins).

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
 
Pokud chcete vytvořit trasu události, můžete použít rozhraní REST API služby Azure Digital revláken [, příkazy CLI](how-to-manage-routes-apis-cli.md#create-an-event-route)nebo [Azure Portal](how-to-manage-routes-portal.md#create-an-event-route).

Tady je příklad vytvoření trasy události v rámci klientské aplikace pomocí `CreateOrReplaceEventRouteAsync` volání [rozhraní .NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client) : 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::

1. Nejprve `DigitalTwinsEventRoute` je vytvořen objekt a konstruktor převezme název koncového bodu. Toto `endpointName` pole označuje koncový bod, jako je například centrum událostí, Event Grid nebo Service Bus. Tyto koncové body je potřeba vytvořit v předplatném a připojit se k digitálním plochám Azure pomocí rozhraní API řídicích rovin před provedením tohoto volání registrace.

2. Objekt směrování událostí má také pole [**filtru**](how-to-manage-routes-apis-cli.md#filter-events) , pomocí kterého lze omezit typy událostí, které následují po této trase. Filtr `true` umožňuje trasu bez dalšího filtrování (filtr `false` zakáže trasu). 

3. Tento objekt směrování události je pak předán `CreateOrReplaceEventRouteAsync` spolu s názvem trasy.

> [!TIP]
> Všechny funkce sady SDK přicházejí v synchronních a asynchronních verzích.

## <a name="dead-letter-events"></a>Nedoručené události

Když koncový bod nemůže doručovat událost v určitém časovém období nebo po pokusu o doručení události v určitém počtu opakování, může odeslat nedoručenou událost do účtu úložiště. Tento proces se označuje jako **nedoručené**. Pokud je splněna **jedna z následujících** podmínek, digitální vlákna Azure bude událost nedoručena. 

* Událost se nedoručuje do období TTL (Time to Live).
* Počet pokusů o doručení události překročil limit.

Je-li splněna některá z podmínek, událost je vyřazena nebo byla nedoručena. Ve výchozím nastavení **každý koncový bod** nezapne nedoručené písmeno. Pokud ho chcete povolit, musíte při vytváření koncového bodu zadat účet úložiště, který bude obsahovat nedoručené události. Pak můžete vyžádat události z tohoto účtu úložiště a vyřešit tak doručení.

Před nastavením umístění nedoručených zpráv musíte mít účet úložiště s kontejnerem. Při vytváření koncového bodu zadejte adresu URL tohoto kontejneru. Nedoručené písmeno je k dispozici jako adresa URL kontejneru s tokenem SAS. Tento token potřebuje `write` oprávnění pouze pro cílový kontejner v rámci účtu úložiště. Plně vytvořená adresa URL bude ve formátu: `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>`

Další informace o tokenech SAS najdete v tématu: [ *udělení omezeného přístupu k Azure Storage prostředkům pomocí sdílených přístupových podpisů (SAS)*](../storage/common/storage-sas-overview.md)

Informace o tom, jak nastavit koncový bod pomocí nedoručených zpráv, najdete v tématu [*Postupy: Správa koncových bodů a tras v digitálních událostech Azure (rozhraní API a rozhraní příkazového řádku)*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering).

### <a name="types-of-event-messages"></a>Typy zpráv událostí

Různé typy událostí v IoT Hub a digitálních Vlákenách Azure vytváří různé typy oznamovacích zpráv, jak je popsáno níže.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak nastavit a spravovat postup události:
* [*Postupy: Správa koncových bodů a tras*](how-to-manage-routes-apis-cli.md)

Nebo si přečtěte téma jak použít Azure Functions ke směrování událostí v rámci digitálních vláken Azure:
* [*Postupy: nastavení funkce Azure pro zpracování dat*](how-to-create-azure-function.md)