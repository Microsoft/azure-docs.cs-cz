---
title: Osvědčené postupy pro výběr ID časových řad – Azure Time Series Insights | Microsoft Docs
description: Seznamte se s osvědčenými postupy při volbě ID časové řady v Azure Time Series Insights Gen2.
author: shipramishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: seodec18
ms.openlocfilehash: 8aefa6efa5f10dae8a28e7126b91b7b5f6ac77a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950735"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Osvědčené postupy při výběru ID časové řady

Tento článek shrnuje důležitost ID časových řad pro prostředí Azure Time Series Insights Gen2 a osvědčené postupy pro jejich výběr.

## <a name="choose-a-time-series-id"></a>Volba ID služby Time Series

Výběr vhodného ID časové řady je kritický. Výběr ID časové řady je například volba klíče oddílu pro databázi. Vyžaduje se při vytváření prostředí Azure Time Series Insights Gen2.

Podrobné vysvětlení ID časové řady najdete v kurzu zřizování prostředí. Zobrazí se dva různé příklady datové části telemetrie JSON a správný výběr ID časové řady pro každý z nich.</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]


> [!IMPORTANT]
> ID časových řad:
>
> * Vlastnost rozlišuje *velká a malá* písmena: písmena a znaky jsou používány v hledání, porovnávání, aktualizacích a při dělení.
> * *Neproměnlivá* vlastnost: po vytvoření ji nelze změnit.

> [!TIP]
> Pokud je zdrojem událostí centrum IoT, ID vaší časové řady bude pravděpodobně ***iothub-ID zařízení-připojení***.

Klíčové osvědčené postupy, které je potřeba provést, zahrnují:

* Vyberte klíč oddílu s velkým počtem jedinečných hodnot (například stovky nebo tisíce). V mnoha případech to může být ID zařízení, ID senzoru nebo ID značky ve formátu JSON.
* ID časové řady by mělo být jedinečné na úrovni uzlu na úrovni listu [modelu časové řady](./concepts-model-overview.md).
* Omezení počtu znaků pro řetězec názvu vlastnosti časové řady je 128. Pro hodnotu vlastnosti ID časové řady je limit znaků 1 024.
* Pokud hodnota jedinečné vlastnosti pro ID časové řady chybí, bude zpracována jako hodnota null a bude se jednat o stejné pravidlo omezení jedinečnosti.
* Pokud je vaše ID časové řady vnořené v rámci komplexního objektu JSON, nezapomeňte při zadávání názvu vlastnosti sledovat [pravidla sloučení](./concepts-json-flattening-escaping-rules.md) příchozích dat. Podívejte se na příklad [B](concepts-json-flattening-escaping-rules.md#example-b).
* Jako ID časové řady můžete také vybrat až *tři* vlastnosti klíče. Jejich kombinace bude složený klíč, který představuje ID časové řady.  
  > [!NOTE]
  > Vaše tři klíčové vlastnosti musí být řetězce.
  > Museli byste dotazovat na tento složený klíč místo na jednu vlastnost současně.

## <a name="select-more-than-one-key-property"></a>Vybrat více než jednu vlastnost klíče

Následující scénáře popisují výběr více než jedné vlastnosti klíče jako ID časové řady.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Příklad 1: ID časové řady s jedinečným klíčem

* Máte starší verze loďstva prostředků. Každá z nich má jedinečný klíč.
* Jeden vozový park je jednoznačně identifikovaný vlastností **deviceId**. V případě jiného loďstva je jedinečná vlastnost **objectID**. Žádná z těchto vlastností nezahrnuje jedinečné vlastnosti jiného loďstva. V tomto příkladu byste vybrali dva klíče, **deviceId** a **objectID** jako jedinečné klíče.
* Hodnoty null přijímáme a chybějící přítomnost vlastnosti v datové části události se počítá jako hodnota null. To je také vhodný způsob, jak zpracovávat odesílající data do dvou zdrojů událostí, kde data v každém zdroji událostí mají jedinečné ID časové řady.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Příklad 2: ID časové řady se složeným klíčem

* Vyžadujete, aby více vlastností bylo v rámci stejného loďstva prostředků jedinečné.
* Jste výrobcem inteligentních budov a nasadili snímače v každé místnosti. V každé místnosti jsou obvykle stejné hodnoty pro **sensorId**. Příklady jsou **sensor1**, **sensor2** a **sensor3**.
* Vaše budova má překrývající se čísla podlah a místností napříč lokalitami ve vlastnosti **flrRm**. Tato čísla mají hodnoty jako **1a**, **2b** a **3a**.
* Máte vlastnost, **umístění**, která obsahuje hodnoty, jako je například **Redmond**, **Barceloně** a **Tokio**. Chcete-li vytvořit jedinečnost, určete následující tři vlastnosti jako klíče ID časové řady: **sensorId**, **flrRm** a **Location**.

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

[![Nakonfigurujte ID časových řad pro prostředí.](media/v2-how-to-tsid/configure-environment-key.png)](media/v2-how-to-tsid/configure-environment-key.png#lightbox)

  > [!NOTE]
  > V Azure Portal nezadávejte názvy vlastností oddělené čárkami v jednom TexBox, jinak se bude považovat za jeden název vlastnosti, který obsahuje čárky.
  > Každý název vlastnosti zadejte do svého vlastního texboxu.

## <a name="next-steps"></a>Další kroky

* Přečtěte si [pravidla pro sloučení a uvozovací znaky JSON](./concepts-json-flattening-escaping-rules.md) , abyste pochopili, jak budou události uložené.

* Plánování [Azure Time Series Insightsho prostředí Gen2](./how-to-plan-your-environment.md).