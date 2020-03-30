---
title: Doporučené postupy pro výběr ID časové řady – Přehledy Azure Time Series | Dokumenty společnosti Microsoft
description: Přečtěte si o doporučených postupech při výběru ID časové řady ve verzi Azure Time Series Insights Preview.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083523"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Doporučené postupy pro výběr ID časové řady

Tento článek shrnuje důležitost ID časové řady pro prostředí Azure Time Series Insights Preview a osvědčené postupy pro výběr jednoho.

## <a name="choose-a-time-series-id"></a>Volba ID služby Time Series

Výběr vhodného ID časové řady je velmi důležitý. Výběr ID časové řady je jako výběr klíče oddílu pro databázi. Je vyžadována při vytváření prostředí Náhled přehledů časové řady. 

> [!IMPORTANT]
> ID časových řad jsou:
> * Vlastnost *rozlišující malá a* velká písmena: písmena a znaky se používají při hledání, porovnání, aktualizace a při dělení.
> * *Neměnná* vlastnost: po vytvoření ji nelze změnit.

> [!TIP]
> Pokud je zdrojem událostí centrum IoT, bude vaše ID časové řady pravděpodobně ***iothub-connection-device-id***.

Mezi klíčové osvědčené postupy, které je třeba dodržovat, patří:

* Vyberte klíč oddílu s mnoha odlišnými hodnotami (například stovky nebo tisíce). V mnoha případech se může jedná o ID zařízení, ID senzoru nebo ID značky v jsonu.
* ID časové řady by mělo být jedinečné na úrovni listu vašeho [modelu časové řady](./time-series-insights-update-tsm.md).
* Limit znaků pro řetězec názvu vlastnosti ID časové řady je 128. Pro hodnotu vlastnosti ID časové řady je limit znaků 1 024.
* Pokud chybí jedinečná hodnota vlastnosti pro ID časové řady, je považována za hodnotu null a řídí se stejným pravidlem omezení jedinečnosti.
* Jako ID časové řady můžete také vybrat až *tři* klíčové vlastnosti. Jejich kombinace bude složený klíč, který představuje ID časové řady.  
  > [!NOTE]
  > Tři klíčové vlastnosti musí být řetězce.
  > Budete muset dotaz proti tento složený klíč namísto jedné vlastnosti najednou.

## <a name="select-more-than-one-key-property"></a>Výběr více než jedné klíčové vlastnosti

Následující scénáře popisují výběr více než jedné klíčové vlastnosti jako ID časové řady.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Příklad 1: ID časové řady s jedinečným klíčem

* Máte starší flotily aktiv. Každý z nich má jedinečný klíč.
* Jeden vozový park je jednoznačně identifikován vlastností **deviceId**. Pro jiný vozový park, jedinečná vlastnost je **objectId**. Ani jeden z flotily neobsahuje jedinečný majetek druhé flotily. V tomto příkladu byste vybrali dvě klávesy, **deviceId** a **objectId**, jako jedinečné klíče.
* Přijímáme hodnoty null a nedostatek přítomnosti vlastnosti v datové části události se počítá jako hodnota null. Toto je také vhodný způsob zpracování odesílání dat do dvou zdrojů událostí, kde data v každém zdroji událostí má jedinečné ID časové řady.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Příklad 2: ID časové řady s složeným klíčem

* Chcete-li mít v rámci stejné flotily prostředků jedinečné více vlastností. 
* Jste výrobce chytrých budov a nasazujete senzory do každé místnosti. V každé místnosti obvykle máte stejné hodnoty pro **sensorId**. Příklady jsou **sensor1**, **sensor2**a **sensor3**.
* Vaše budova má překrývající se čísla podlaží a místností napříč místy v objektu **flrRm**. Tato čísla mají hodnoty jako **1a**, **2b**a **3a**.
* Máte vlastnost, **umístění**, která obsahuje hodnoty, jako je **Například Redmond**, **Barcelona**a **Tokio**. Chcete-li vytvořit jedinečnost, označte následující tři vlastnosti jako id tlačítka časové řady: **sensorId**, **flrRm**a **umístění**.

Příklad nezpracované události:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

Na webu Azure Portal pak můžete zadat složený klíč takto: 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [modelování dat](./time-series-insights-update-tsm.md).

* Naplánujte si [prostředí Azure Time Series Insights Preview](./time-series-insights-update-plan.md).