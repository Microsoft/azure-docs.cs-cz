---
title: Plánování aplikace – LUIS
titleSuffix: Azure Cognitive Services
description: Vyosnovujte relevantní záměry aplikací a entity a pak vytvořte svoje plány aplikací v Language Understanding inteligentní služby (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b5e5df111b81cb60b6d194be190421bdb5ce2683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467698"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Plánování schématu aplikace LUIS s využitím domény a extrakce dat předmětu

Schéma aplikace LUIS obsahuje záměry a entity, které se vztahují k vaší doméně předmětu. Záměry klasifikují projevy uživatele a entity extrahují data z uživatelského projevy. 

## <a name="identify-your-domain"></a>Identifikace domény

Aplikace LUIS se na střed vycentruje v tématu specifickém pro doménu.  Můžete mít například cestovní aplikaci, která provádí rezervaci lístků, letů, hotelů a půjčovny vozidel. Jiná aplikace může poskytovat obsah týkající se výkonu, sledování úsilí o způsobilost a nastavování cílů. Identifikace domény vám pomůže najít slova nebo fráze, které jsou pro vaši doménu důležité.

> [!TIP]
> LUIS nabízí [předem připravené domény](luis-how-to-use-prebuilt-domains.md) pro mnoho běžných scénářů.
> Zkontrolujte, jestli můžete použít předem sestavenou doménu jako výchozí bod pro vaši aplikaci.

## <a name="identify-your-intents"></a>Identifikujte své záměry

Zamyslete se nad [záměry](luis-concept-intent.md) , které jsou důležité pro úlohu vaší aplikace. 

Podíváme se na příklad cestovní aplikace s funkcemi pro zarezervování letu a zkontrolujeme počasí v cíli uživatele. Pro tyto akce můžete definovat `BookFlight` a `GetWeather` záměry. 

Ve složitější aplikaci s více funkcemi máte více záměrů a měli byste je pečlivě definovat, aby záměry nebyly příliš specifické. Například `BookFlight` a `BookHotel` může být nutné oddělit záměry, ale `BookInternationalFlight` a `BookDomesticFlight` mohou být příliš podobné.

> [!NOTE]
> Osvědčeným postupem je použít jenom tolik záměrů, kolik potřebujete k provádění funkcí aplikace. Pokud definujete příliš mnoho záměrů, je LUIS pro projevy správně klasifikovat. Pokud definujete moc málo, můžou být tak obecné, aby se překrývaly.

Pokud nepotřebujete identifikovat celkový úmysl uživatele, přidejte do záměru None všechny ukázkové projevy uživatele. Pokud vaše aplikace roste na potřebu dalších záměrů, můžete je vytvořit později. 

## <a name="create-example-utterances-for-each-intent"></a>Vytvořit příklad projevy pro každý záměr

Jakmile určíte záměry, vytvořte pro každý záměr 15 až 30 příkladů projevy. Aby bylo možné začít používat, nemusíte mít méně než tento počet nebo pro každý záměr vytvořit příliš mnoho projevy. Každý utterance by měl být jiný než předchozí utterance. Dobrá odrůda v projevy zahrnuje celkový počet slov, volbu Word, sloveso vhodné a interpunkční znaménka. 

Další informace najdete v [projevy](luis-concept-utterance.md) .

## <a name="identify-your-entities"></a>Identifikace entit

V příkladu projevy Identifikujte entity, které mají být extrahovány. Pro zarezervování letu potřebujete informace, jako je cílová, datum, letecká kategorie a třída pro cestování. Vytvořte entity pro tyto datové typy a označte [entity](luis-concept-entity-types.md) v příkladu projevy, protože jsou důležité pro splnění záměru. 

Pokud určíte, které entity chcete v aplikaci použít, pamatujte na to, že existují různé typy entit pro záznam vztahů mezi typy objektů. [Entity v Luis](luis-concept-entity-types.md) poskytují další podrobnosti o různých typech.

## <a name="next-steps"></a>Další kroky

Přečtěte si o typických [cyklech vývoje](luis-concept-app-iteration.md).  