---
title: Plánování aplikací LEOŠ | Microsoft Docs
description: Popisují záměry příslušné aplikace a entity a pak vytvořte plány vaší aplikace v jazyce Principy inteligentního služby (LEOŠ).
services: cognitive-services
author: DeniseMak
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 7aec5d5b90ac7145ce9f337ec74c590b4b88c6b1
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266358"
---
# <a name="plan-your-luis-app"></a>Plánování LEOŠ aplikace

Je důležité, abyste před zahájením vytváření v LEOŠ plánování vaší aplikace. Příprava na obrys nebo schéma možné záměry a entitami, které jsou relevantní pro téma specifické pro doménu vaší aplikace.  

## <a name="identify-your-domain"></a>Identifikaci vaší doméně
LEOŠ aplikace je zaměřená na téma specifické pro doménu.  Například můžete mít cesta aplikace, který provádí rezervace lístků, lety, hotels a pronájem aut. Obsah týkající se výkonu, sledování úsilí vhodnosti a nastavení cíle mohou poskytnout jiné aplikaci. 

> [!TIP]
> Nabízí LEOŠ [předem domén](luis-how-to-use-prebuilt-domains.md) pro mnoho běžných scénářů.
> Zkontrolujte, pokud pro vaši aplikaci můžete předem domény jako výchozí bod.

## <a name="identify-your-intents"></a>Identifikovat vaše záměry
Zamyslete se nad [záměry](luis-concept-intent.md) , které jsou důležité úlohy vaší aplikace. Podívejme se na příklad, cesta aplikace, pomocí funkce chcete sešit letu a zkontrolovat, počasí v cílovém umístění uživatele. Můžete definovat záměry "BookFlight" a "GetWeather" pro tyto akce. Ve složitější aplikaci s další funkce máte více tříd Intent a byste měli pečlivě tak, aby se nesmí být příliš konkrétní definovat. Například "BookFlight" a "BookHotel" může musí být samostatné záměry, ale "BookInternationalFlight" a "BookDomesticFlight" může být příliš podobné.

> [!NOTE]
> Je vhodné používat jenom tolik záměry, jako je třeba provést funkce vaší aplikace. Pokud definujete příliš mnoho tříd Intent, bude těžší pro LEOŠ klasifikovat utterances správně. Pokud definujete příliš málo, mohou být proto Obecné, překrývat.


## <a name="identify-your-entities"></a>Identifikaci vaší entity
Chcete-li sešit letu, nutné určité informace jako cílový, datum, letecká společnost, kategorie lístku a cestují – třída. Můžete přidat jako [entity](luis-concept-entity-types.md) vzhledem k tomu, že jsou důležité k provedení záměrem. 

Jakmile určíte entit, které mají používat ve vaší aplikaci, mějte na paměti, že jsou různé typy entit pro zaznamenání vztahy mezi typy objektů. [Entity v LEOŠ](luis-concept-entity-types.md) poskytuje další podrobnosti o různých typech.

### <a name="simple-entity"></a>Jednoduché entity
Jednoduché entity popisuje jeden koncept.

![jednoduché entity](./media/luis-plan-your-app/simple-entity.png)

V tématu [extrakce dat](luis-concept-data-extraction.md#simple-entity-data) Další informace o extrahování jednoduché entity z koncového bodu odpověď na dotaz JSON. Zkuste jednoduché entity [rychlý Start](luis-quickstart-primary-and-secondary-data.md) Další informace o tom, jak používat jednoduché entity.

### <a name="hierarchical-entity"></a>Hierarchická entity
Hierarchická entita je zvláštní druh **jednoduché** entity; definování kategorii a její členy ve formě relaci nadřazený podřízený.

![Hierarchická entity](./media/luis-plan-your-app/hierarchical-entity.png)

V tématu [extrakce dat](luis-concept-data-extraction.md#hierarchical-entity-data) Další informace o extrahování hierarchické entity z koncového bodu odpověď na dotaz JSON. Zkuste hierarchické entity [rychlý Start](luis-quickstart-intent-and-hier-entity.md) Další informace o tom, jak používat hierarchické entity.

### <a name="composite-entity"></a>Složené entity
Složené entity se skládá z dalšími subjekty, které tvoří částí celek. 

![složené entity](./media/luis-plan-your-app/composite-entity.png)

V tématu [extrakce dat](luis-concept-data-extraction.md#composite-entity-data) Další informace o extrahování složené entity z koncového bodu odpověď na dotaz JSON. Zkuste složené entity [kurzu](luis-tutorial-composite-entity.md) Další informace o tom, jak použít složené entity.

### <a name="prebuilt-entity"></a>Předkompilované entity
Poskytuje LEOŠ [předem entity](Pre-builtEntities.md) pro běžné typy `Number`, který můžete použít pro počet lístků v lístku pořadí.

![Číslo předem entity](./media/luis-plan-your-app/number-entity.png)

V tématu [extrakce dat](luis-concept-data-extraction.md#prebuilt-entity-data) Další informace o extrahování regulární výraz entity z koncového bodu odpověď na dotaz JSON. 

### <a name="list-entity"></a>Seznam entity 
Seznam entity se explicitně určené seznam hodnot. Každá hodnota se skládá z jedné nebo více synonyma. V aplikaci cesta můžete vytvořit seznam entitu představují letiště názvy.

![seznam entity](./media/luis-plan-your-app/list-entity.png)

V tématu [extrakce dat](luis-concept-data-extraction.md#list-entity-data) Další informace o extrahování seznamu entity z koncového bodu odpověď na dotaz JSON. Zkuste [rychlý Start](luis-quickstart-intent-and-list-entity.md) Další informace o tom, jak používat seznamu entity.

### <a name="regular-expression-entity"></a>Regulární výraz entity
Regulární výraz entity umožňuje LEOŠ extrahovat data z utterance na základě výrazu regulární výraz.

![Regulární výraz entity](./media/luis-plan-your-app/regex-entity.png)

V tématu [extrakce dat](luis-concept-data-extraction.md#regular-expression-entity-data) Další informace o extrahování regulární výraz entity z koncového bodu odpověď na dotaz JSON. Zkuste [rychlý Start](luis-quickstart-intents-regex-entity.md) Další informace o tom, jak používat regulární výraz entity.

## <a name="after-getting-endpoint-utterances"></a>Po získání utterances koncového bodu
Po vaší aplikace získá utterances koncový bod, v plánu implementovat vylepšení předpovědi s [active learning](label-suggested-utterances.md), [fráze seznamy](luis-concept-feature.md), a [vzory](luis-concept-patterns.md). 

### <a name="patternany-entity"></a>Pattern.Any entity
Patterns.any je zástupný symbol proměnlivou délkou použít pouze v [na vzor](luis-concept-patterns.md) utterance šablony k označení, kde entity zahájení a ukončení. Šablona utterances v souladu s [správnou syntaxi](luis-concept-patterns.md#pattern-syntax) k identifikaci entity a Ignorovatelná text.


## <a name="next-steps"></a>Další postup
* V tématu [vytvoření první aplikace jazyk Principy inteligentního služby (LEOŠ)] [ luis-get-started-create-app] rychlé návod, jak vytvořit aplikaci LEOŠ.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app