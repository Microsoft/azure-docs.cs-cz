---
title: Co je skóre BLEU? – Vlastní Translator
titleSuffix: Azure Cognitive Services
description: BLEU je měření rozdílů mezi automatický překlad a jeden nebo několik převodů lidských vytvořen odkaz z jedné větě zdroje. Algoritmus BLEU porovnává po sobě jdoucích fráze automatický překlad po sobě jdoucích možnosti najde v překladu odkazu a vrátí počet shod vážený způsobem.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-rada
ms.openlocfilehash: 354c26e1d7290a6296b80eabcd8e63ff304e180f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730167"
---
# <a name="what-is-a-bleu-score"></a>Co je skóre BLEU?

[BLEU (Understudy dvojjazyčné vyhodnocení)](https://en.wikipedia.org/wiki/BLEU) je měření rozdílů mezi automatický překlad a jeden nebo několik převodů lidských vytvořen odkaz z jedné větě zdroje.

## <a name="scoring-process"></a>Procesu určování skóre

Algoritmus BLEU porovnává po sobě jdoucích fráze automatický překlad po sobě jdoucích možnosti najde v překladu odkazu a vrátí počet shod vážený způsobem. Tyto shody jsou nezávislé na umístění. Vyšší míra shody znamená vyšší stupeň podobnosti s překlad odkazu a vyšší ohodnocení. Srozumitelnost a gramatické správnosti neberou v úvahu.

## <a name="how-bleu-works"></a>Jak funguje BLEU?

BLEU pro šifrování je, že ho koreluje s lidskou rozhodnutí odstávkou věty rozhodnutí chyby přes svátek testu, spíše než pokusu navrhnout přesné lidské rozhodnutí pro každé větě.

Je rozsáhlejší informace o BLEU skóre [tady](https://youtu.be/-UqDljMymMg).

Výsledky BLEU silně závisí na škálu vaší domény, konzistenci dat testu pomocí školení a optimalizace dat, kolik dat máte k dispozici pro trénování. Pokud mají byla vašich modelů trénovaných na úzký domény a trénovacích dat je konzistentní s testovací data, můžete očekávat vysoké skóre BLEU.

>[!NOTE]
>Porovnání mezi službou BLEU skóre je oprávněné, pouze pokud BLEU výsledky jsou porovnávány s stejnou sadu testů, stejného páru jazyka a stejný stroj MT. Skóre BLEU z množiny různými testovacími je vázán na lišit.
