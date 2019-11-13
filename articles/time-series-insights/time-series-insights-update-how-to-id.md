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
ms.date: 10/22/2019
ms.custom: seodec18
ms.openlocfilehash: cf826c47c61e3714a05dd81fe3eea4e6ee0b03f4
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012501"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Osvědčené postupy pro výběr ID řady času

Tento článek shrnuje důležitost ID časových řad pro prostředí Azure Time Series Insights Preview a osvědčené postupy pro jejich výběr.

## <a name="choose-a-time-series-id"></a>Volba ID služby Time Series

Volba ID řady času je třeba zvolit klíč oddílu pro databázi. Je potřeba ji vybrat při vytváření prostředí Time Series Insights Preview. Jedná se o *neměnitelnou* vlastnost. To znamená, že po vytvoření prostředí Time Series Insights ve verzi Preview s ID časové řady už ho pro toto prostředí nemůžete změnit. 

> [!IMPORTANT]
> V ID časové řady se rozlišují malá a velká písmena.

Výběr vhodného ID časové řady je kritický. Tady jsou některé z osvědčených postupů, které můžete sledovat:

* Vyberte klíč oddílu s velkým počtem jedinečných hodnot (například stovky nebo tisíce). V mnoha případech to může být ID zařízení, ID senzoru nebo ID značky ve formátu JSON.
* ID řady čas musí být jedinečný na úrovni uzel typu list vaše [modelu časové řady](./time-series-insights-update-tsm.md).
* Pokud je zdrojem událostí centrum IoT, ID vaší časové řady bude pravděpodobně *iothub-ID zařízení-připojení*.
* Omezení počtu znaků pro řetězec názvu vlastnosti časové řady je 128. Pro hodnotu vlastnosti ID časové řady je limit znaků 1 024.
* Pokud hodnota jedinečné vlastnosti pro ID časové řady chybí, bude zpracována jako hodnota null a bude se jednat o stejné pravidlo omezení jedinečnosti.
* Jako ID časové řady můžete také vybrat až *tři* vlastnosti klíče. Jejich kombinace bude složený klíč, který představuje ID časové řady.  

  > [!NOTE]
  > Vaše tři klíčové vlastnosti musí být řetězce.
  > Museli byste dotazovat na tento složený klíč místo na jednu vlastnost současně.

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

V Azure Portal můžete tento složený klíč zadat jako: 

`[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]`

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [modelování dat](./time-series-insights-update-tsm.md).

* Naplánujte si [prostředí pro Azure Time Series Insights Preview](./time-series-insights-update-plan.md).