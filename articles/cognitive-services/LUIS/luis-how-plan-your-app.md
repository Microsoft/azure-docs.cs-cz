---
title: Plánování aplikací Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: Popisují relevantní aplikaci záměry a entity a pak vytvořte plány aplikace v Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: a7e137c864d216b6b936181c3482358bd10f175a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037265"
---
# <a name="plan-your-luis-app"></a>Plánování aplikace LUIS

Je důležité mít plán vaší aplikace. Identifikujte vaší domény, včetně možných záměry a entity, které jsou relevantní pro vaši aplikaci.  

## <a name="identify-your-domain"></a>Identifikujte domény
Aplikace LUIS je zaměřená na téma specifického pro doménu.  Například může mít cestovní aplikaci provádějící rezervace lístky, lety, hotely a pronájem aut. Jiné aplikace mohou poskytnout obsah týkající se výkonu, sledování vhodnosti úsilí a nastavení cíle. Identifikující doménu, vám pomůže najít slova nebo fráze, které jsou důležité pro vaši doménu.

> [!TIP]
> Služba LUIS nabízí [předem připravených domén](luis-how-to-use-prebuilt-domains.md) pro spoustu běžných scénářů.
> Zaškrtněte, pokud chcete zobrazit, pokud pro svou aplikaci můžete předem připravených domény jako výchozí bod.

## <a name="identify-your-intents"></a>Identifikujte vaše záměry
Představte si [záměry](luis-concept-intent.md) , které jsou důležité pro úlohy vaší aplikace. Vezměme si třeba cestovní aplikaci s funkcemi, které chcete rezervovat let a zkontrolovat, počasí v cílovém umístění uživatele. Můžete definovat příkazy "BookFlight" a "GetWeather" pro tyto akce. Ve složitější aplikaci s použitím dalších funkcí máte další záměry a byste měli definovat pečlivě proto není příliš konkrétní. Například "BookFlight" a "BookHotel" bude muset být samostatné příkazy, ale "BookInternationalFlight" a "BookDomesticFlight" může být příliš podobná.

> [!NOTE]
> Je vhodné používat jenom tolik záměrů, jako je třeba provést funkce vaší aplikace. Pokud definujete příliš mnoho záměrů, bude obtížnější pro LUIS ke klasifikaci projevy správně. Pokud definujete příliš málo, může se jednat o tak obecné tak, aby se překrývat.

## <a name="create-example-utterances-for-each-intent"></a>Vytvořit příklad projevy pro každý záměr
Po určení příkazů vytvořte příklad projevy 10 až 15 pro každý záměr. Než začneme mají méně než tento počet ani vytvořit mnoho projevy pro každý záměr. Každý utterance musí být odlišný od předchozího utterance. Dobré různých v projevy zahrnuje celkový počet slov, volba slov, příkaz čas a interpunkční znaménka. 

## <a name="identify-your-entities"></a>Identifikace entit
V příkladu projevy určete entity, které chcete, aby byl extrahován. Rezervovat letu, potřebujeme několik informací jako cíl, datum, letecká společnost, kategorie lístek a cestují třídy. Vytvořte entity pro tyto datové typy a označí [entity](luis-concept-entity-types.md) v příkladu projevy vzhledem k tomu, že jsou důležité pro provedení záměru. 

Při zjišťování entit, které mají používat ve vaší aplikaci, mějte na paměti, že existují různé typy entit k zaznamenání relace mezi typy objektů. [Entity v LUIS](luis-concept-entity-types.md) poskytuje další podrobnosti o různých typech.

### <a name="simple-entity"></a>Jednoduchá entita
Jednoduché entity popisuje jeden koncept.

![jednoduché entity](./media/luis-plan-your-app/simple-entity.png)

Zobrazit [extrakce dat](luis-concept-data-extraction.md#simple-entity-data) Další informace o extrahování jednoduchou entitu z koncového bodu odpověď na dotaz JSON. Vyzkoušejte to [rychlý Start](luis-quickstart-primary-and-secondary-data.md) Další informace o tom, jak použít jednoduché entity.

### <a name="hierarchical-entity"></a>Hierarchická entita
Hierarchické entita je zvláštní druh **jednoduché** entity; definuje kategorie a její členy v podobě vztah nadřízenosti a podřízenosti. Relace se určuje podle kontextu z v rámci utterance. Podřízené položky hierarchické entity jsou také jednoduché entity.

![hierarchické entity](./media/luis-plan-your-app/hierarchical-entity.png)

Zobrazit [extrakce dat](luis-concept-data-extraction.md#hierarchical-entity-data) Další informace o extrahování hierarchické entity z koncového bodu odpověď na dotaz JSON. Vyzkoušejte to [rychlý Start](luis-quickstart-intent-and-hier-entity.md) získat další informace o tom, jak používat hierarchické entity.

### <a name="composite-entity"></a>Složený entity
Složený entity je tvořena jinými entitami, které tvoří část celku. Složený entita obsahuje celou řadu typů entit.

![Složený entity](./media/luis-plan-your-app/composite-entity.png)

Zobrazit [extrakce dat](luis-concept-data-extraction.md#composite-entity-data) Další informace o extrahování entita složený z koncového bodu odpověď na dotaz JSON. Vyzkoušejte to [kurzu](luis-tutorial-composite-entity.md) Další informace o tom, jak použít složené entity.

### <a name="prebuilt-entity"></a>Předem připravených entit
LUIS poskytuje [předem připravených entit](luis-prebuilt-entities.md) pro běžné typy dat, jako je číslo, data, e-mailovou adresu a adresu URL. Číslo předem připravených entit můžete použít pro počet lístky v lístku pořadí.

![Číslo předem připravených entit](./media/luis-plan-your-app/number-entity.png)

Zobrazit [extrakce dat](luis-concept-data-extraction.md#prebuilt-entity-data) Další informace o extrahování předem připravených entit z koncového bodu odpověď na dotaz JSON. 

### <a name="list-entity"></a>Entita seznamu 
Seznam entit je explicitně zadaného seznamu hodnot. Každá hodnota se skládá z jednoho nebo více synonyma. V cestovní aplikaci můžete vytvořit seznam entity představující letiště názvy.

![seznam entit](./media/luis-plan-your-app/list-entity.png)

Zobrazit [extrakce dat](luis-concept-data-extraction.md#list-entity-data) Další informace o extrahování seznam entit z koncového bodu odpověď na dotaz JSON. Vyzkoušejte to [rychlý Start](luis-quickstart-intent-and-list-entity.md) získat další informace o tom, jak používat seznam entit.

### <a name="regular-expression-entity"></a>Regulární výraz entity
Entita regulárního výrazu umožňuje LUIS se extrahovat správně formátovaná data ze utterance založené na regulární výraz.

![Regulární výraz entity](./media/luis-plan-your-app/regex-entity.png)

Zobrazit [extrakce dat](luis-concept-data-extraction.md#regular-expression-entity-data) Další informace o extrahování entity regulárního výrazu z koncového bodu odpověď na dotaz JSON. Zkuste [rychlý Start](luis-quickstart-intents-regex-entity.md) získat další informace o tom, jak používat entity regulární výraz.

## <a name="next-steps"></a>Další postup
Poté, co vaše aplikace se trénuje publikována a získá projevy koncový bod, v úmyslu implementovat vylepšení predikce s [aktivně učit](luis-how-to-review-endoint-utt.md), [frázi seznamy](luis-concept-feature.md), a [vzory](luis-concept-patterns.md). 


* Zobrazit [vytvořit svoji první aplikaci Language Understanding Intelligent Services (LUIS)](luis-get-started-create-app.md) rychlý návod, jak vytvořit aplikaci LUIS.
