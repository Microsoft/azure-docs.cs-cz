---
title: Posila Učení - Personalizátor
titleSuffix: Azure Cognitive Services
description: Personalizák používá informace o akcích a aktuálním kontextu k lepšímu hodnocení návrhů. Informace o těchto akcích a kontextu jsou atributy nebo vlastnosti, které jsou označovány jako funkce.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 36071cdee25cfa99fc54b0e5c0c0aa822cb5fe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68662841"
---
# <a name="what-is-reinforcement-learning"></a>Co je posilování učení?

Reinforcement Learning je přístup ke strojovému učení, který se učí chování tím, že získává zpětnou vazbu z jeho použití.
 
Posila Učení pracuje na:

* Poskytnutí příležitosti nebo stupně svobody k uzákonění chování - například rozhodování nebo rozhodování.
* Poskytování kontextových informací o prostředí a volbách.
* Poskytnutí zpětné vazby o tom, jak dobře chování dosahuje určitého cíle.

I když existuje mnoho podtypů a stylů posilování učení, to je, jak koncept funguje v Personalizace:

* Vaše aplikace poskytuje možnost zobrazit jednu část obsahu ze seznamu alternativ.
* Aplikace poskytuje informace o každé alternativě a kontextu uživatele.
* Vaše aplikace vypočítá _skóre odměny_.

Na rozdíl od některých přístupů k výztuže učení, Personalizátor nevyžaduje simulaci pracovat palců Jeho algoritmy učení jsou navrženy tak, aby reagovaly na vnější svět (versus jeho ovládání) a učily se z každého datového bodu s vědomím, že se jedná o jedinečnou příležitost, která stojí čas a peníze na vytvoření, a že existuje nenulová lítost (ztráta možné odměny), pokud dojde k neoptimálnímu výkonu.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Jaký typ algoritmů výztuže učení používá Personalizátor?

Aktuální verze Personalizace používá **kontextové bandity**, přístup k posílení učení, který je orámován kolem rozhodování nebo volby mezi diskrétní akce, v daném kontextu.

_Rozhodovací paměť_, model, který byl trénován zachytit nejlepší možné rozhodnutí, daný kontext, používá sadu lineárních modelů. Ty opakovaně ukázaly obchodní výsledky a jsou osvědčeným přístupem, částečně proto, že se mohou učit z reálného světa velmi rychle, aniž by potřebovali víceprůchodové školení, a částečně proto, že mohou doplňovat modely učení pod dohledem a hluboké nervové síťových modelů.

Zkoumání /využití přidělení provozu se provádí náhodně po procento stanovené pro průzkum a výchozí algoritmus pro průzkum je epsilon-chamtivý.

### <a name="history-of-contextual-bandits"></a>Dějiny kontextových banditů

John Langford vytvořil název Kontextové bandité (Langford a Zhang [2007]) popsat zvladatelné podmnožinu posílení učení a pracoval na půl tuctu dokumentů zlepšení našeho chápání toho, jak se učit v tomto paradigmatu:

* Beygelzimer et al. [2011]
* Dudík et al. [2011a,b]
* Agarwal et al. [2014, 2012]
* Beygelzimer a Langford [2009]
* Li et al. [2010]

John také poskytl několik výukových programů dříve na témata, jako je společná předpověď (ICML 2015), kontextová teorie banditů (NIPS 2013), aktivní učení (ICML 2009) a hranice složitosti vzorku (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Jaké rámce strojového učení personalizace používá?

Personalizátor v současné době používá [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) jako základ pro strojové učení. Tento rámec umožňuje maximální propustnost a nejnižší latenci při vytváření personalizace řadí a školení modelu se všemi událostmi.

## <a name="references"></a>Odkazy

* [Dělat kontextová rozhodnutí s nízkým technickým dluhem](https://arxiv.org/abs/1606.03966)
* [Přístup ke snížení spravedlivé klasifikace](https://arxiv.org/abs/1803.02453)
* [Efektivní kontextové bandité v nestacionárních světech](https://arxiv.org/abs/1708.01799)
* [Zbytková ztráta Predikce: Posílení: učení bez přírůstkové zpětné vazby](https://openreview.net/pdf?id=HJNMYceCW)
* [Mapování pokynů a vizuálních pozorování na akce s výztužným učením](https://arxiv.org/abs/1704.08795)
* [Naučte se hledat lépe než váš učitel](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>Další kroky

[Offline vyhodnocení](concepts-offline-evaluation.md) 