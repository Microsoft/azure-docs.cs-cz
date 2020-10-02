---
title: Podpora pro datový typ Long v Azure Time Series Insights Gen2 | Microsoft Docs
description: Podpora pro datový typ Long v Azure Time Series Insights Gen2.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: dpalled
ms.openlocfilehash: 2cf86ed4fd4305a37d27bf7a88e8493821ef085c
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629093"
---
# <a name="adding-support-for-long-data-type-in-azure-time-series-insights-gen2"></a>Přidání podpory pro typ Long data v Azure Time Series Insights Gen2

Přidání podpory pro dlouhodobý datový typ má vliv na to, jak ukládají a indexují číselná data v Azure Time Series Insights prostředích Gen2. Pokud máte prostředí Gen1, můžete tyto změny ignorovat.

Od 29. června 2020 v závislosti na vaší oblasti budou data indexována tak **dlouho** a **dvakrát**.  Pokud máte nějaké dotazy nebo obavy týkající se této změny, pošlete lístek podpory prostřednictvím Azure Portal a uveďte tuto komunikaci.

Pokud máte vliv na některý z následujících případů, proveďte doporučené změny:

- **Případ 1**: v tuto chvíli používáte proměnné modelu časové řady a v datech telemetrie můžete odesílat jenom integrální datové typy.
- **Případ 2**: aktuálně používáte proměnné modelu časové řady a v datech telemetrie můžete odesílat jak integrální, tak i neintegrální datové typy.
- **Případ 3**: použijete proměnné kategorií pro mapování celočíselných hodnot na kategorie.
- **Případ 4**: k vytvoření vlastní aplikace front-endu použijte sadu JavaScript SDK.
- **Případ 5**: blížíte se limitu názvů 1 000 a vlastností v teplém úložišti a odešlete jak integrální, tak i neintegrální data. Počet vlastností lze zobrazit jako metriku v [Azure Portal](https://portal.azure.com/).

V případě, že se na vás vztahují nějaké případy, proveďte změny v modelu. Aktualizujte výraz Time Series (TSX) v definici proměnné o doporučené změny. Aktualizovat obojí:

- Průzkumník Azure Time Series Insights
- Libovolný vlastní klient používající naše rozhraní API

V závislosti na vašem řešení a omezeních IoT možná nebudete mít přehled o datech, která se odesílají do prostředí Azure Time Series Insights Gen2. Pokud si nejste jistí, jestli jsou vaše data celočíselná nebo integrální i neintegrální, máte několik možností:

- Můžete počkat, až se funkce uvolní. Pak Prozkoumejte své nezpracované události v uživatelském rozhraní Průzkumníka, abyste pochopili, které vlastnosti jsou uloženy ve dvou samostatných sloupcích.
- Můžete proměnit doporučené změny pro všechny číselné značky.
- Můžete dočasně směrovat podmnožinu událostí do úložiště pro lepší pochopení a prozkoumání schématu.

Pokud chcete ukládat události, zapněte [zachytávání událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) pro Azure Event Hubs nebo [trasu](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-storage) z IoT Hub do úložiště objektů BLOB v Azure.

Data je také možné pozorovat prostřednictvím [Průzkumníka centra událostí](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)nebo pomocí nástroje [Event Processor Host](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send#receive-events).

Pokud používáte IoT Hub, přejděte k části [čtení zpráv ze zařízení do cloudu z integrovaného koncového bodu](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) pro přístup k integrovanému koncovému bodu.

> [!NOTE]
> Pokud neprovedete doporučené změny, může docházet k přerušení. Například ovlivněné proměnné Time Series Insights, ke kterým se dostanete prostřednictvím rozhraní API pro dotazy nebo Time Series Insights Explorer, vrátí **hodnotu null** (tj. v Průzkumníkovi nezobrazí žádná data).

## <a name="recommended-changes"></a>Doporučené změny

### <a name="case-1-using-time-series-model-variables-and-sending-only-integral-data-types-in-telemetry-data"></a>Případ 1: použití proměnných modelu časové řady a posílání pouze integrálních datových typů v datech telemetrie

Doporučené změny pro případ 1 jsou stejné jako pro případ 2. Postupujte podle pokynů v části pro případ 2.

### <a name="case-2-using-time-series-model-variables-and-sending-both-integral-and-nonintegral-types-in-telemetry-data"></a>Případ 2: použití proměnných modelu časové řady a odeslání integrálních i neintegrálních typů v datech telemetrie

Pokud aktuálně odesíláte celočíselná data telemetrie, vaše data budou rozdělena do dvou sloupců:

- **propertyValue_double**
- **propertyValue_long**

Vaše celočíselné zápisy dat do **propertyValue_long**. Dříve ingestovaná číselná data v **propertyValue_double** nebyla kopírována.

Pokud chcete zadat dotaz na data v těchto dvou sloupcích pro vlastnost **PropertyValue** , musíte v TSX použít skalární funkci **COALESCE ()** . Funkce přijímá argumenty stejného **datového typu** a vrací první hodnotu, která není null v seznamu argumentů. Další informace najdete v tématu [Azure Time Series Insights Gen2ch konceptů přístupu k datům](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions).

#### <a name="variable-definition-in-tsx---numeric"></a>Definice proměnné v TSX – numeric

*Definice předchozí proměnné:*

[![Snímek obrazovky se zobrazí dialogové okno Přidat novou proměnnou pro proměnnou PropertyValue, číselná.](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*Definice nové proměnné:*

[![Snímek obrazovky se zobrazí dialogové okno Přidat novou proměnnou pro proměnnou PropertyValue s vlastní hodnotou, která je číselná.](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

Jako [výraz vlastní časové řady](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)můžete také použít funkci **COALESCE ($Event. PropertyValue. Double, ToDouble – ($Event. PropertyValue. Long))** .

#### <a name="inline-variable-definition-using-tsx-query-apis---numeric"></a>Vložená definice proměnné s použitím rozhraní API pro dotazy TSX – numerický

*Definice předchozí proměnné:*

```JSON
"PropertyValueVariable": {

    "kind": "numeric",

    "value": {

        "tsx": "$event.propertyValue.Double"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

*Definice nové proměnné:*

```JSON
"PropertyValueVariable ": {

    "kind": "numeric",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

Jako [výraz vlastní časové řady](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)můžete také použít funkci **COALESCE ($Event. PropertyValue. Double, ToDouble – ($Event. PropertyValue. Long))** .

> [!NOTE]
> Tyto proměnné Doporučujeme aktualizovat na všech místech, která se můžou použít. Mezi tato místa patří model časových řad, uložené dotazy a dotazy konektoru Power BI.

### <a name="case-3-using-categorical-variables-to-map-integer-values-to-categories"></a>Případ 3: použití proměnných kategorií k mapování celočíselných hodnot na kategorie

Pokud aktuálně používáte proměnné kategorií, které mapují celočíselné hodnoty na kategorie, pravděpodobně použijete funkci **toLong** k převodu dat z **dvojitého** typu na typ **Long** . Stejně jako případy 1 a 2 je potřeba, abyste provedli sloupce **Long** **typu** **Double** a Long.

#### <a name="variable-definition-in-time-series-explorer---categorical"></a>Definice proměnné v Průzkumníkovi časových řad – kategorií

*Definice předchozí proměnné:*

[![Snímek obrazovky se zobrazí dialogové okno Přidat novou proměnnou pro proměnnou PropertyValue, kategorií.](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*Definice nové proměnné:*

[![Snímek obrazovky se zobrazí dialogové okno Přidat novou proměnnou pro proměnnou PropertyValue s vlastní hodnotou kategorií.](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

Jako [výraz vlastní časové řady](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)můžete také použít funkci **COALESCE ($Event. PropertyValue. Double, ToDouble – ($Event. PropertyValue. Long))** .

Proměnné kategorií stále vyžadují, aby byla hodnota typu Integer. **Datový typ** všech argumentů v **COALESCE ()** musí být typu **Long** ve [výrazu vlastní časové řady.](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)

#### <a name="inline-variable-definition-using-tsx-query-apis---categorical"></a>Vložená definice proměnné pomocí rozhraní API pro dotazy TSX – kategorií

*Definice předchozí proměnné:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "tolong($event.propertyValue.Double)"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

*Definice nové proměnné:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

Proměnné kategorií stále vyžadují, aby byla hodnota typu Integer. **Datový typ** všech argumentů v **COALESCE ()** musí být typu **Long** ve [výrazu vlastní časové řady](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax).

> [!NOTE]
> Tyto proměnné Doporučujeme aktualizovat na všech místech, která se můžou použít. Mezi tato místa patří model časových řad, uložené dotazy a dotazy konektoru Power BI.

### <a name="case-4-using-the-javascript-sdk-to-build-a-custom-front-end-application"></a>Případ 4: použití sady JavaScript SDK k vytvoření vlastní aplikace front-end

Pokud máte vliv na případy 1 až 3 a sestavíte si vlastní aplikace, musíte dotazy aktualizovat tak, aby používaly funkci **COALESCE ()** , jak je znázorněno v předchozích příkladech.

### <a name="case-5-nearing-warm-store-1000-property-limit"></a>Případ 5: blížící se limitu vlastností 1 000 úložiště

Pokud jste uživatel s vysokým počtem vlastností a domníváte se, že tato změna by nacházela do vašeho prostředí přes 1 000. limit vlastnosti služby teplého úložiště, odešlete lístek podpory prostřednictvím Azure Portal a uvedete tuto komunikaci.

## <a name="next-steps"></a>Další kroky

- Zobrazí úplný seznam [podporovaných datových typů](concepts-supported-data-types.md).
