---
title: Zpětnovazebnému učení – Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer používá informace o akcích a aktuální kontext a nabízí lepší návrhy hodnocení. Informace o těchto akce a kontextu se atributy nebo vlastnosti, které se označují jako funkce.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: b65073c0646db0cd0c27a71005bb4f74b091ae09
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506892"
---
# <a name="what-is-reinforcement-learning"></a>Co je Zpětnovazebnému učení?

Zpětnovazebnému učení je přístup k sadě machine learning, která se učí chování získáním zpětnou vazbu od jeho použití.
 
Zpětnovazebnému učení funguje tak, že:

* Poskytování příležitosti a stupeň volnosti upravovat chování – například provedení rozhodnutí nebo voleb.
* Poskytuje kontextové informace o prostředí a možnosti.
* Poskytování zpětné vazby o tom, jak dobře chování dosahuje určitého cíle.

Přestože existuje mnoho podtypy a styly zpětnovazební učení, jedná se o fungování koncept v Personalizer:

* Vaše aplikace poskytuje možnost zobrazit jednu část obsahu ze seznamu možností.
* Vaše aplikace obsahuje informace o všech možností a kontextu uživatele.
* Výpočetní prostředí vaší aplikace _oceňujte skóre_.

Na rozdíl od některých přístupy k zpětnovazební učení Personalizer nevyžaduje simulace pracovat v. Své algoritmy learning jsou navrženy pro react vnějším světem (a řídit ho) a Učte se od datových bodů seznámíte, že se jedná o jedinečnou příležitost, náklady, čas a peníze na vytváření, a má nenulovou lituje (ztráta možné reward) Pokud neoptimální výkonu se stane.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Jaký typ učení se supervizí zpětnovazebnému Personalizer použít?

Používá aktuální verzi Personalizer **kontextové bandits**, uvedeny přístup, který zpětnovazební učení, který je po provedení rozhodnutí nebo se nemusíte rozhodovat mezi diskrétní akce v daném kontextu.

_Rozhodnutí paměti_, modelu, která vyškolila zachycení nejlepší možné rozhodnutí zadaný kontext, používá sadu lineární modely. Tyto opakovaně ukázalo obchodních výsledků a jsou prověřené přístup částečně, protože se můžou učit z reálného světa velmi rychle bez nutnosti školení s více průchody a částečně protože doplňují pod dohledem, učení modely a hloubkových neurálních modely sítě.

Přidělení paměti prozkoumat/zneužití provoz se provádí náhodně následující procento nastavit pro zkoumání a je výchozí algoritmus pro zkoumání epsilon greedy.

### <a name="history-of-contextual-bandits"></a>Historie kontextové Bandits

Jan Langford poprvé použit název kontextové Bandits (Langford a Potokar [2007]) k popisu tractable podmnožinou zpětnovazebnému učení a pracoval na pět papírů zlepšit naši představu o tom se dozvíte v tomto paradigma:

* Beygelzimer et al. [2011]
* Dudík et al. [2011a,b]
* Agarwal et al. [2014, 2012]
* Beygelzimer a Langford [2009]
* Li et al. [2010]

John je také několik kurzy dříve uvedeného na témata, jako je společné Predikcí (ICML 2015), kontextové Bandit teorie (NIPS 2013), Active Learning (ICML 2009) a ukázkové složitost meze (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Jaké architektury strojového učení Personalizer použít?

Aktuálně používá personalizer [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) jako základ pro machine learning. Toto rozhraní umožňuje maximální propustnost a nejnižší latence při provádění přizpůsobení řadí a trénování modelu se všechny události.

## <a name="references"></a>Odkazy

* [Kontextové rozhodování s nízkou technického dluhu](https://arxiv.org/abs/1606.03966)
* [Snížení cen přístup ke korektní klasifikace](https://arxiv.org/abs/1803.02453)
* [Efektivní kontextové Bandits ve světě – stojícího vozidla](https://arxiv.org/abs/1708.01799)
* [Predikcí zbývající ztráta: Posílení: učení s ne přírůstkové zpětné vazby](https://openreview.net/pdf?id=HJNMYceCW)
* [Mapování pokyny a vizuální pozorování na akce s Zpětnovazebnému učení](https://arxiv.org/abs/1704.08795)
* [Naučte se vyhledávání lepší výsledky než vaše vyučující](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>Další postup

[Testování offline](concepts-offline-evaluation.md) 