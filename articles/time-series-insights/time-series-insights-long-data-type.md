---
title: Přidání podpory pro datový typ Long | Microsoft Docs
description: Podpora pro datový typ Long
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: dpalled
ms.openlocfilehash: a0b53749a32e79d690cf4412fdac82b18dfe2f2e
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86183626"
---
# <a name="adding-support-for-long-data-type"></a>Přidání podpory pro datový typ Long

Tyto změny se použijí jenom v prostředích Preview (PAYG). Máte-li standardní prostředí TSI SKU, tyto změny můžete ignorovat.

Provádíme změny, jak ukládáme a indexuje číselná data v Azure Time Series Insights Preview, což by mohlo mít vliv na vás. Pokud máte vliv na některý z následujících případů, proveďte co nejdříve nezbytné změny. Vaše data se začnou indexovat tak dlouho a v závislosti na vaší oblasti se budou zdvojnásobit od 29. června do 30. června 2020. Pokud máte nějaké dotazy nebo obavy týkající se této změny, pošlete lístek podpory prostřednictvím Azure Portal a uveďte tuto komunikaci.

Tato změna se týká následujících případů:

1. Pokud aktuálně používáte proměnné modelu časové řady a v datech telemetrie odesíláte pouze integrální datové typy.
1. Pokud aktuálně používáte proměnné modelu časové řady a v datech telemetrie odesíláte jak datové typy integrální, tak i neintegrální.
1. Použijete-li proměnné kategorií pro mapování celočíselných hodnot na kategorie.
1. Použijete-li sadu JavaScript SDK k vytvoření vlastní aplikace front-endu.
1. Pokud se blížíte limitu 1 000 pro název vlastnosti v úložišti pro uchování dat (WS) a zadáte jak integrální, tak i neintegrální data, počet vlastností se dá zobrazit jako metrika v [Azure Portal](https://portal.azure.com/).

Pokud se vám pro vás uplatní některý z výše uvedených případů, budete muset provést změny v modelu, aby se tato změna vešla. Aktualizujte výraz časové řady v definici proměnné v Průzkumníkovi TSI a v jakémkoli vlastním klientovi pomocí našich rozhraní API s doporučenými změnami. Podrobnosti najdete níže.

V závislosti na vašem řešení a omezeních IoT možná nebudete mít přehled o datech odesílaných do PAYG prostředí TSI. Pokud si nejste jistí, jestli jsou vaše data celočíselná nebo integrální i neintegrální, máte k dispozici několik možností. Můžete počkat na vydání funkce a pak prozkoumat nezpracované události v uživatelském rozhraní Průzkumníka, abyste pochopili, které vlastnosti byly uloženy ve dvou samostatných sloupcích. Tyto změny můžete deaktivovat pro všechny číselné značky nebo dočasně směrovat podmnožinu událostí do úložiště, abyste lépe pochopili a prozkoumali vaše schéma. Pokud chcete ukládat události, zapněte [zachytávání událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) pro Event Hubs nebo [trasu](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-storage) z IoT Hub do Azure Blob Storage. Data je také možné pozorovat prostřednictvím [Průzkumníka centra událostí](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)nebo pomocí nástroje [Event Processor Host](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send#receive-events). Pokud používáte IoT Hub, přečtěte [si dokumentaci k tomuto](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) předdefinovanému koncovému bodu.

Pamatujte na to, že pokud jste tyto změny ovlivnili a nemůžete je učinit pomocí výše uvedených dat, může dojít k přerušení, kdy se ovlivněné proměnné časové řady, ke kterým se přistupoval prostřednictvím rozhraní API pro dotazy, nebo Time Series Insights Explorer vrátí *hodnotu null* (tj. nezobrazí žádná data v Průzkumníkovi).

## <a name="recommended-changes"></a>Doporučené změny

Případ 1 & 2: **Použití proměnných modelu časové řady a odesílání pouze integrálních datových typů nebo posílání integrálních i neintegrálních typů v datech telemetrie.**

Pokud aktuálně posíláte data o celočíselných telemetriech, data se rozdělí do dvou sloupců: "propertyValue_double" a "propertyValue_long".

Vaše celočíselná data budou zapsána do "propertyValue_long", pokud se změny projeví a dříve ingestované (a budoucí) číselná data v "propertyValue_double" nebudou kopírovány.

Pokud chcete zadat dotaz na data v těchto dvou sloupcích pro vlastnost "propertyValue", budete muset v TSX použít skalární funkci *COALESCE ()* . Funkce přijímá argumenty stejného datového typu a v seznamu argumentů vrátí první hodnotu, která není null (Další informace o použití [najdete tady](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions)).

### <a name="variable-definition-in-time-series-explorer---numeric"></a>Definice proměnné v Průzkumníkovi časových řad – numerický

*Definice předchozí proměnné:*

[![Definice předchozí proměnné](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*Definice nové proměnné:*

[![Definice nové proměnné](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

Jako [výraz vlastní časové řady](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) můžete také použít *"COALESCE ($Event. PropertyValue. Double, ToDouble – ($Event. PropertyValue. Long))"* .

### <a name="inline-variable-definition-using-time-series-query-apis---numeric"></a>Vložená definice proměnné s použitím rozhraní API pro dotazy Time Series – numerický

*Definice předchozí proměnné:*

```tsx
"PropertyValueVariable": {

    "kind": "numeric",

    "value": {

        "tsx": "$event.propertyValue.Double"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
```

*Definice nové proměnné:*

```tsx
"PropertyValueVariable ": {

    "kind": "numeric",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
```

Jako [výraz vlastní časové řady](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) můžete také použít *"COALESCE ($Event. PropertyValue. Double, ToDouble – ($Event. PropertyValue. Long))"* .

> [!NOTE]
> Tyto proměnné Doporučujeme aktualizovat na všech místech, která se můžou používat (model časových řad, uložené dotazy, dotazy Power BI konektoru).

Případ 3: **Použití proměnných kategorií k mapování celočíselných hodnot na kategorie**

Pokud aktuálně používáte proměnné kategorií, které mapují celočíselné hodnoty na kategorie, pravděpodobně použijete funkci toLong k převodu dat z dvojitého typu na typ Long. Stejně jako v případech uvedených výše je potřeba, abyste provedli sloupce se dvěma a dlouhými datovými sloupci.

### <a name="variable-definition-in-time-series-explorer---categorical"></a>Definice proměnné v Průzkumníkovi časových řad – kategorií

*Definice předchozí proměnné:*

[![Definice předchozí proměnné](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*Definice nové proměnné:*

[![Definice nové proměnné](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

Jako [výraz vlastní časové řady](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) můžete také použít *"COALESCE ($Event. PropertyValue. Double, ToDouble – ($Event. PropertyValue. Long))"* .

Proměnné kategorií stále vyžadují, aby byla hodnota typu Integer. Datový typ všech argumentů v COALESCE () musí být typu Long ve [výrazu vlastní časové řady.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="inline-variable-definition-using-time-series-query-apis---categorical"></a>Vložená definice proměnné pomocí rozhraní API pro dotazy Time Series – kategorií

*Definice předchozí proměnné:*

```tsx
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

```tsx
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

Proměnné kategorií stále vyžadují, aby byla hodnota typu Integer. Datový typ všech argumentů v COALESCE () musí být typu Long ve [výrazu vlastní časové řady.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

> [!NOTE]
> Tyto proměnné Doporučujeme aktualizovat na všech místech, která se můžou používat (model časových řad, uložené dotazy, dotazy Power BI konektoru).

Případ 4: **použití sady JavaScript SDK k vytvoření vlastní aplikace front-end**

Pokud máte vliv na případy 1-3 výše a sestavíte si vlastní aplikace, musíte dotazy aktualizovat tak, aby používaly funkci *COALESCE ()* , jak je znázorněno v předchozích příkladech.

Případ 5: **blížící se limitu vlastností 1 000 úložiště**

Pokud jste uživatelem s vysokým počtem vlastností a domníváte se, že tato změna by mohla nabízet vaše prostředí přes 1 000 WS – omezení názvu, odešle lístek podpory prostřednictvím Azure Portal a uvede tuto komunikaci.

## <a name="next-steps"></a>Další kroky

* V [tomto](concepts-supported-data-types.md) článku se zobrazí úplný seznam podporovaných datových typů.
