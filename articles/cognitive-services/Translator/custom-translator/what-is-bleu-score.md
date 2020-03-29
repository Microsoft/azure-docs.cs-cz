---
title: Co je skóre BLEU? - Vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: BLEU je měření rozdílů mezi strojovým překladem a lidskými referenčními překlady stejné zdrojové věty.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: 85c4ee27a828a05c64ca6cbf84bff438535328be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647358"
---
# <a name="what-is-a-bleu-score"></a>Co je skóre BLEU?

[BLEU (Dvojjazyčné hodnocení Understudy)](https://en.wikipedia.org/wiki/BLEU) je měření rozdílů mezi automatickýpřeklad a jeden nebo více člověkem vytvořených referenčních překladů stejné zdrojové věty.

## <a name="scoring-process"></a>Proces hodnocení

Algoritmus BLEU porovnává po sobě jdoucí fráze automatického překladu s po sobě jdoucími frázemi, které najde v referenčním překladu, a váženě spočítá počet shod. Tyto zápasy jsou nezávislé na pozici. Vyšší stupeň shody označuje vyšší stupeň podobnosti s referenčním překladem a vyšší skóre. Srozumitelnost a gramatická správnost se neberou v úvahu.

## <a name="how-bleu-works"></a>Jak BLEU funguje?

BLEU síla je, že koreluje dobře s lidským úsudkem tím, že zprůměrování jednotlivých trest rozsudku chyby přes test korpusu, spíše než se snaží vymyslet přesný lidský úsudek pro každou větu.

Rozsáhlejší diskuse o skóre BLEU je [zde](https://youtu.be/-UqDljMymMg).

Výsledky BLEU silně závisí na šíři vaší domény, konzistenci testovacích dat s daty školení a ladění a na množství dat, která máte k dispozici pro trénování. Pokud vaše modely byly trénované na úzké doméně a vaše trénovací data jsou konzistentní s testovacími daty, můžete očekávat vysoké skóre BLEU.

>[!NOTE]
>Porovnání skóre BLEU je ospravedlnitelné pouze v případě, že jsou výsledky BLEU porovnány se stejnou testovací sadou, stejným jazykovým párem a stejným motorem MT. Skóre BLEU z jiné testovací sady se musí lišit.
