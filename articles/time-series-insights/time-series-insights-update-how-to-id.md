---
title: Osvědčené postupy pro výběr ID časových řad – Azure Time Series Insights | Microsoft Docs
description: Seznamte se s osvědčenými postupy při volbě ID časové řady v Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: a62c2460698408f6a2bfa51c6638bdeaf88bb31f
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083523"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Osvědčené postupy pro výběr ID řady času

Tento článek shrnuje důležitost ID časových řad pro prostředí Azure Time Series Insights Preview a osvědčené postupy pro jejich výběr.

## <a name="choose-a-time-series-id"></a>Volba ID služby Time Series

Výběr vhodného ID časové řady je kritický. Volba ID řady času je třeba zvolit klíč oddílu pro databázi. Vyžaduje se při vytváření prostředí Time Series Insights Preview. 

> [!IMPORTANT]
> ID časových řad:
> * Vlastnost rozlišuje *velká a malá* písmena: písmena a znaky jsou používány v hledání, porovnávání, aktualizacích a při dělení.
> * *Neproměnlivá* vlastnost: po vytvoření ji nelze změnit.

> [!TIP]
> Pokud je zdrojem událostí centrum IoT, ID vaší časové řady bude pravděpodobně ***iothub-ID zařízení-připojení***.

Klíčové osvědčené postupy, které je potřeba provést, zahrnují:

* Vyberte klíč oddílu s velkým počtem jedinečných hodnot (například stovky nebo tisíce). V mnoha případech to může být ID zařízení, ID senzoru nebo ID značky ve formátu JSON.
* ID časové řady by mělo být jedinečné na úrovni uzlu na úrovni listu [modelu časové řady](./time-series-insights-update-tsm.md).
* Omezení počtu znaků pro řetězec názvu vlastnosti časové řady je 128. Pro hodnotu vlastnosti ID časové řady je limit znaků 1 024.
* Pokud hodnota jedinečné vlastnosti pro ID časové řady chybí, bude zpracována jako hodnota null a bude se jednat o stejné pravidlo omezení jedinečnosti.
* Jako ID časové řady můžete také vybrat až *tři* vlastnosti klíče. Jejich kombinace bude složený klíč, který představuje ID časové řady.  
  > [!NOTE]
  > Vaše tři klíčové vlastnosti musí být řetězce.
  > Museli byste dotazovat na tento složený klíč místo na jednu vlastnost současně.

## <a name="select-more-than-one-key-property"></a>Vybrat více než jednu vlastnost klíče

Následující scénáře popisují výběr více než jedné vlastnosti klíče jako ID časové řady.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Příklad 1: ID časové řady s jedinečným klíčem

* Máte starší verze loďstva prostředků. Každá z nich má jedinečný klíč.
* Jeden vozový park je jednoznačně identifikovaný vlastností **deviceId**. V případě jiného loďstva je jedinečná vlastnost **objectID**. Žádná z těchto vlastností nezahrnuje jedinečné vlastnosti jiného loďstva. V tomto příkladu byste vybrali dva klíče, **deviceId** a **objectID**jako jedinečné klíče.
* Hodnoty null přijímáme a chybějící přítomnost vlastnosti v datové části události se počítá jako hodnota null. To je také vhodný způsob, jak zpracovávat odesílající data do dvou zdrojů událostí, kde data v každém zdroji událostí mají jedinečné ID časové řady.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Příklad 2: ID časové řady se složeným klíčem

* Budete potřebovat více vlastností být jedinečný v rámci stejného počtu prostředků. 
* Jste výrobcem inteligentních budov a nasadili snímače v každé místnosti. V každé místnosti jsou obvykle stejné hodnoty pro **sensorId**. Příklady jsou **sensor1**, **sensor2**a **sensor3**.
* Vaše budova má překrývající se čísla podlah a místností napříč lokalitami ve vlastnosti **flrRm**. Tato čísla mají hodnoty jako **1a**, **2b**a **3a**.
* Máte vlastnost, **umístění**, která obsahuje hodnoty, jako je například **Redmond**, **Barceloně**a **Tokio**. Chcete-li vytvořit jedinečnost, určete následující tři vlastnosti jako klíče ID časové řady: **sensorId**, **flrRm**a **Location**.

Příklad nezpracované události:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

V Azure Portal pak můžete složený klíč zadat následujícím způsobem: 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [modelování dat](./time-series-insights-update-tsm.md).

* Naplánujte si [prostředí pro Azure Time Series Insights Preview](./time-series-insights-update-plan.md).