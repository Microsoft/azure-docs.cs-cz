---
title: Plánování aplikace LUIS | Dokumentace Microsoftu
description: Popisují relevantní aplikaci záměry a entity a pak vytvořte plány aplikace v Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: DeniseMak
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 2ce202bbb1479db18fb88cfef4d510ae4cb39a78
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952096"
---
# <a name="plan-your-luis-app"></a>Plánování aplikace LUIS

Je důležité mít plán aplikace předtím, než začnete vytvářet v LUIS. Příprava osnovu nebo schématu je to možné záměry a entity, které jsou relevantní pro téma specifického pro doménu vaší aplikace.  

## <a name="identify-your-domain"></a>Identifikujte domény
Aplikace LUIS je zaměřená na téma specifického pro doménu.  Například může mít cestovní aplikaci provádějící rezervace lístky, lety, hotely a pronájem aut. Jiné aplikace mohou poskytnout obsah týkající se výkonu, sledování vhodnosti úsilí a nastavení cíle. 

> [!TIP]
> Služba LUIS nabízí [předem připravených domén](luis-how-to-use-prebuilt-domains.md) pro spoustu běžných scénářů.
> Zaškrtněte, pokud chcete zobrazit, pokud pro svou aplikaci můžete předem připravených domény jako výchozí bod.

## <a name="identify-your-intents"></a>Identifikujte vaše záměry
Představte si [záměry](luis-concept-intent.md) , které jsou důležité pro úlohy vaší aplikace. Vezměme si třeba cestovní aplikaci s funkcemi, které chcete rezervovat let a zkontrolovat, počasí v cílovém umístění uživatele. Můžete definovat příkazy "BookFlight" a "GetWeather" pro tyto akce. Ve složitější aplikaci s použitím dalších funkcí máte další záměry a byste měli definovat pečlivě proto není příliš konkrétní. Například "BookFlight" a "BookHotel" bude muset být samostatné příkazy, ale "BookInternationalFlight" a "BookDomesticFlight" může být příliš podobná.

> [!NOTE]
> Je vhodné používat jenom tolik záměrů, jako je třeba provést funkce vaší aplikace. Pokud definujete příliš mnoho záměrů, bude obtížnější pro LUIS ke klasifikaci projevy správně. Pokud definujete příliš málo, může se jednat o tak obecné tak, aby se překrývat.


## <a name="identify-your-entities"></a>Identifikace entit
Rezervovat letu, potřebujeme několik informací jako cíl, datum, letecká společnost, kategorie lístek a cestují třídy. Můžete přidat jako [entity](luis-concept-entity-types.md) vzhledem k tomu, že jsou důležité pro provedení záměru. 

Při zjišťování entit, které mají používat ve vaší aplikaci, mějte na paměti, že existují různé typy entit k zaznamenání relace mezi typy objektů. [Entity v LUIS](luis-concept-entity-types.md) poskytuje další podrobnosti o různých typech.

### <a name="simple-entity"></a>Jednoduchá entita
Jednoduché entity popisuje jeden koncept.

![jednoduché entity](./media/luis-plan-your-app/simple-entity.png)

Zobrazit [extrakce dat](luis-concept-data-extraction.md#simple-entity-data) Další informace o extrahování jednoduchou entitu z koncového bodu odpověď na dotaz JSON. Vyzkoušejte jednoduchou entitu [rychlý Start](luis-quickstart-primary-and-secondary-data.md) Další informace o tom, jak použít jednoduché entity.

### <a name="hierarchical-entity"></a>Hierarchická entita
Hierarchické entita je zvláštní druh **jednoduché** entity; definuje kategorie a její členy v podobě vztah nadřízenosti a podřízenosti.

![hierarchické entity](./media/luis-plan-your-app/hierarchical-entity.png)

Zobrazit [extrakce dat](luis-concept-data-extraction.md#hierarchical-entity-data) Další informace o extrahování hierarchické entity z koncového bodu odpověď na dotaz JSON. Zkuste hierarchické entity [rychlý Start](luis-quickstart-intent-and-hier-entity.md) získat další informace o tom, jak používat hierarchické entity.

### <a name="composite-entity"></a>složený entity
Složený entity je tvořena jinými entitami, které tvoří část celku. 

![složený entity](./media/luis-plan-your-app/composite-entity.png)

Zobrazit [extrakce dat](luis-concept-data-extraction.md#composite-entity-data) Další informace o extrahování entita složený z koncového bodu odpověď na dotaz JSON. Zkuste složený entity [kurzu](luis-tutorial-composite-entity.md) Další informace o tom, jak použít složené entity.

### <a name="prebuilt-entity"></a>Předem připravených entit
LUIS poskytuje [předem připravených entit](luis-prebuilt-entities.md) pro běžné typy, jako jsou `Number`, který můžete použít pro počet lístky v lístku pořadí.

![Číslo předem připravených entit](./media/luis-plan-your-app/number-entity.png)

Zobrazit [extrakce dat](luis-concept-data-extraction.md#prebuilt-entity-data) Další informace o extrahování entity regulárního výrazu z koncového bodu odpověď na dotaz JSON. 

### <a name="list-entity"></a>Entita seznamu 
Seznam entit je explicitně zadaného seznamu hodnot. Každá hodnota se skládá z jednoho nebo více synonyma. V cestovní aplikaci můžete vytvořit seznam entity představující letiště názvy.

![seznam entit](./media/luis-plan-your-app/list-entity.png)

Zobrazit [extrakce dat](luis-concept-data-extraction.md#list-entity-data) Další informace o extrahování seznam entit z koncového bodu odpověď na dotaz JSON. Zkuste [rychlý Start](luis-quickstart-intent-and-list-entity.md) získat další informace o tom, jak používat seznam entit.

### <a name="regular-expression-entity"></a>Regulární výraz entity
Entita regulárního výrazu umožňuje LUIS extrahovat data z utterance podle určitému regulárnímu výrazu.

![Regulární výraz entity](./media/luis-plan-your-app/regex-entity.png)

Zobrazit [extrakce dat](luis-concept-data-extraction.md#regular-expression-entity-data) Další informace o extrahování entity regulárního výrazu z koncového bodu odpověď na dotaz JSON. Zkuste [rychlý Start](luis-quickstart-intents-regex-entity.md) získat další informace o tom, jak používat entity regulární výraz.

## <a name="after-getting-endpoint-utterances"></a>Po získání projevy koncového bodu
Poté, co vaše aplikace získá projevy koncový bod, v úmyslu implementovat vylepšení predikce s [aktivně učit](luis-how-to-review-endoint-utt.md), [frázi seznamy](luis-concept-feature.md), a [vzory](luis-concept-patterns.md). 

### <a name="patternany-entity"></a>Pattern.Any entity
Patterns.any je zástupný symbol proměnné délky použít pouze ve [vzorku](luis-concept-patterns.md) utterance šablony k označení, ve kterém entita začíná a končí. Projevy šablony v souladu s [správnou syntaxi](luis-concept-patterns.md#pattern-syntax) k identifikaci entity a ignorable text.


## <a name="next-steps"></a>Další postup
* Zobrazit [vytvořit svoji první aplikaci Language Understanding Intelligent Services (LUIS)](luis-get-started-create-app.md) rychlý návod, jak vytvořit aplikaci LUIS.