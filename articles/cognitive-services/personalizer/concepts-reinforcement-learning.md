---
title: Posílení učení – přizpůsobené přizpůsobování
titleSuffix: Azure Cognitive Services
description: Přizpůsobení používá informace o akcích a aktuálním kontextu k zajištění lepšího hodnocení návrhů. Informace o těchto akcích a kontextu jsou atributy nebo vlastnosti, které jsou označovány jako funkce.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: 8b97221de4921e06ddfab610618f37683b990181
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132734"
---
# <a name="what-is-reinforcement-learning"></a>Co je posílení učení?

Posílení učení je přístup ke strojovém učení, který se učí chování díky získání zpětné vazby od jejího používání.
 
Posílení učení prostřednictvím:

* Poskytnutí příležitosti nebo stupně volnosti k vystavení chování, například rozhodování nebo výběru.
* Poskytování kontextových informací o prostředí a volbách.
* Poskytnutí zpětné vazby o tom, jak dobře má chování dosáhnout určitého cíle.

I když existuje spousta podtypů a stylů pro posílení učení, jedná se o to, jak koncept funguje v přizpůsobování:

* Vaše aplikace nabízí možnost zobrazit jednu část obsahu ze seznamu alternativ.
* Vaše aplikace poskytuje informace o každé Alternative a kontextu uživatele.
* Vaše aplikace počítá _skóre odměňování_.

Na rozdíl od některých přístupů k posílení učení, přizpůsobování nevyžaduje simulaci pro práci v. Jeho výukové algoritmy jsou navržené tak, aby reagovaly na vnější svět (nad ním), a Naučte se od každého datového bodu vědět, že se jedná o jedinečnou příležitost, kterou je potřeba vytvořit a který je nenulový (ztrátou případné odměny), pokud dojde k dosažení optimálního výkonu.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Jaký typ sledovacích algoritmů pro posílení využití přizpůsobuje použití?

Aktuální verze přizpůsobeného doplňku používá **kontext Bandits**, přístup k posílení učení, které se v rámci daného kontextu rozhodne při rozhodování nebo volbách mezi diskrétními akcemi.

_Rozhodovací paměť_, model, který byl vyškolený k zaznamenání nejlepšího možného rozhodnutí, s ohledem na kontext, používá sadu lineárních modelů. Tyto informace se opakovaně ukázaly jako obchodní výsledky a jsou osvědčeným přístupem, které se dají rychle učit z reálného světa, aniž byste museli postupovat na více průchodech a částečně, protože můžou doplňovat školicí modely a modely rozsáhlých neuronové sítí.

Přidělení provozu prozkoumat/zneužít se provádí náhodně za procento nastavené pro průzkum a výchozí algoritmus pro zkoumání je Epsilon – hladec.

### <a name="history-of-contextual-bandits"></a>Historie kontextových Bandits

Jan Langford mince kontext Bandits (Langford and Zhang [2007]), aby popsal odvolávatelné podmnožiny pro posílení učení a pracovali na pololetních materiálech, které zlepšují pochopení toho, jak se v tomto paradigmatu naučíte:

* Beygelzimer et al. [2011]
* Dudík et al. [2011 a, b]
* Agarwal et al. [2014, 2012]
* Beygelzimer a Langford [2009]
* Li et al. [2010]

Jan taky pro témata, jako je například společná předpověď (ICML 2015), provedl několik kurzů, kontextová Bandit teorie (NIPS 2013), Active Learning (ICML 2009) a meze složitosti vzorku (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Jaké architektury machine learningu používá přizpůsobování?

Přizpůsobování aktuálně používá [pro dostupné](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) jako základ pro strojové učení. Toto rozhraní umožňuje maximální propustnost a nejnižší latenci při provádění pořadí přizpůsobení a školení modelu se všemi událostmi.

## <a name="references"></a>Reference

* [Provádění kontextových rozhodnutí s nízkým technickým dluhem](https://arxiv.org/abs/1606.03966)
* [Snížení přístupu k korektní klasifikaci](https://arxiv.org/abs/1803.02453)
* [Efektivní kontextová Bandits v nestacionárních světů](https://arxiv.org/abs/1708.01799)
* [Předpověď reziduální ztráty: posílení: učení bez přírůstkové zpětné vazby](https://openreview.net/pdf?id=HJNMYceCW)
* [Mapování pokynů a vizuálních pozorování na akce s posílením učení](https://arxiv.org/abs/1704.08795)
* [Výuka pro hledání lépe než učitelů](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>Další kroky

[Offline vyhodnocení](concepts-offline-evaluation.md) 