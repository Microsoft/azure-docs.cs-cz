---
title: Umístění oblasti prostorových analýz a umístění řádků
titleSuffix: Azure Cognitive Services
description: Naučte se nastavit zóny a řádky s prostorovou analýzou.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 7e2a64c14d7d7a1d20b64b746969aca1e60ab218
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "90936222"
---
# <a name="zone-and-line-placement-guide"></a>Průvodce umístěním zóny a linky

Tento článek poskytuje pokyny, jak definovat zóny a řádky pro operace prostorových analýz, aby bylo možné dosáhnout přesné analýzy pohybů obyvatel v prostoru. To platí pro všechny operace. 

Zóny a řádky jsou definované pomocí parametru SPACEANALYSIS_CONFIG JSON. Další informace najdete v článku o [operacích pro prostorovou analýzu](spatial-analysis-operations.md) .

## <a name="guidelines-for-drawing-zones"></a>Pokyny pro kreslení zón

Mějte na paměti, že všechny prostory se liší. polohu nebo velikost budete muset aktualizovat v závislosti na vašich potřebách.

Pokud chcete zobrazit konkrétní část zobrazení kamery, vytvořte největší zónu, kterou můžete pokrýt na konkrétní podlahovou oblast, která vás zajímá, ale nezahrnuje jiné oblasti, které vás zajímají. Tím se zvyšuje přesnost shromažďovaných dat a brání se falešně pozitivním hodnotám z oblastí, které nechcete sledovat. Buďte opatrní při umísťování rohů mnohoúhelníku a ujistěte se, že nejsou mimo oblast, kterou chcete sledovat.  

### <a name="example-of-a-well-shaped-zone"></a>Příklad zóny se správným tvarem

Zóna by měla být dostatečně velká, aby vyhovovala třem osobám, které jsou na jednotlivých hraničních zařízeních, a zaměřuje se na oblast zájmu. Prostorová analýza identifikuje lidi, jejichž nohy jsou umístěny v zóně, takže při kreslení zón 2D obrázku Představte zónu jako koberce na podlaze.

![Zóna ve správném tvaru](./media/spatial-analysis/zone-good-example.png)

### <a name="examples-of-zones-that-arent-well-shaped"></a>Příklady zón, které nejsou ve správném tvaru

Následující příklady znázorňují špatně navýšení zóny. V těchto příkladech je oblast zájmu místem, kde se zobrazuje *jeho herní čas* .

**Zóna není na podlaze.**

![Nesprávně natvarovaná zóna](./media/spatial-analysis/zone-not-on-floor.png) 

**Zóna je příliš malá.**

![zóna je příliš malá.](./media/spatial-analysis/zone-too-small.png)

**Zóna zcela nezachycuje oblast kolem zobrazení.**

![zóna zcela nezachytí oblast kolem konce zakončení.](./media/spatial-analysis/zone-bad-capture.png)

**Zóna je příliš blízko k okraji obrazu kamery a nezachycuje pravé zobrazení.**

![zóna je příliš blízko k okraji obrazu kamery a nezachycuje pravé zobrazení.](./media/spatial-analysis/zone-edge.png)

**Zóna je částečně blokována police, takže lidé a podlaha nejsou plně vidět.**

![zóna je částečně blokovaná, takže lidé nejsou plně vidět.](./media/spatial-analysis/zone-partially-blocked.png)

### <a name="example-of-a-well-shaped-line"></a>Příklad dobře tvarované čáry

Řádek by měl být dostatečně dlouhý na to, aby se vešel do celého vchodu. Prostorová analýza rozpoznala lidi, jejichž stopa protíná čáru, takže když kresby na 2D obrázku představí, tak, aby byly na podlaze. 

Pokud je to možné, rozšiřte řádek širší, než je aktuální vchod. Pokud to nevede k nadbytečnému překročení (jako na obrázku níže, když je řádek na zdi), pak ho rozšíříte.

![Dobře tvarované čáry](./media/spatial-analysis/zone-line-good-example.png)

### <a name="examples-of-lines-that-arent-well-shaped"></a>Příklady řádků, které nejsou ve správném tvaru

Následující příklady znázorňují špatně definované řádky.

**Řádek nepokrývá celou položku způsobem na podlaze.**

![Řádek nepokrývá celou položku způsobem na podlaze.](./media/spatial-analysis/zone-line-bad-coverage.png)

**Řádek je příliš vysoký a nepokrývá celé dveře.**

![Řádek je příliš vysoký a nepokrývá celé dveře.](./media/spatial-analysis/zone-line-too-high.png)

## <a name="next-steps"></a>Další kroky

* [Nasazení webové aplikace počítající lidi](spatial-analysis-web-app.md)
* [Konfigurace operací prostorových analýz](./spatial-analysis-operations.md)
* [Protokolování a řešení potíží](spatial-analysis-logging.md)
* [Průvodce umístěním kamery](spatial-analysis-camera-placement.md)
