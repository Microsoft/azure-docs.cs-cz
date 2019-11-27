---
title: Plánování aplikace – LUIS
titleSuffix: Azure Cognitive Services
description: Popisují relevantní aplikaci záměry a entity a pak vytvořte plány aplikace v Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 6a155f4c43da03ccdc40d289742918973aa6da7b
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326780"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Plánování schématu aplikace LUIS s využitím domény a extrakce dat předmětu

Schéma aplikace LUIS obsahuje [záměry](luis-glossary.md#intent) a [entity](luis-glossary.md#entity) , které se vztahují k vaší [doméně](luis-glossary.md#domain)předmětu. Záměry klasifikují [projevy](luis-glossary.md#utterance)uživatele a entity extrahují data z uživatelského projevy.

## <a name="identify-your-domain"></a>Identifikujte domény

Aplikace LUIS se zacentruje kolem domény předmětu. Můžete mít například cestovní aplikaci, která zpracovává rezervace lístků, letů, hotelů a půjčovny vozidel. Jiné aplikace mohou poskytnout obsah týkající se výkonu, sledování vhodnosti úsilí a nastavení cíle. Identifikace domény vám pomůže najít slova nebo fráze, které jsou relevantní pro vaši doménu.

> [!TIP]
> LUIS nabízí [předem připravené domény](luis-how-to-use-prebuilt-domains.md) pro mnoho běžných scénářů. Zaškrtněte, pokud chcete zobrazit, pokud pro svou aplikaci můžete předem připravených domény jako výchozí bod.

## <a name="identify-your-intents"></a>Identifikujte vaše záměry

Zamyslete se nad [záměry](luis-concept-intent.md) , které jsou důležité pro úlohu vaší aplikace.

Vezměme si třeba cestovní aplikaci s funkcemi, které chcete rezervovat let a zkontrolovat, počasí v cílovém umístění uživatele. Pro tyto akce můžete definovat `BookFlight` a `GetWeather` záměry.

Ve složitější aplikaci s více funkcemi máte více záměrů a měli byste je pečlivě definovat, aby záměry nebyly příliš specifické. Například `BookFlight` a `BookHotel` může být nutné oddělit záměry, ale `BookInternationalFlight` a `BookDomesticFlight` mohou být příliš podobné.

> [!NOTE]
> Je vhodné používat jenom tolik záměrů, jako je třeba provést funkce vaší aplikace. Pokud definujete příliš mnoho záměrů, bude obtížnější pro LUIS ke klasifikaci projevy správně. Pokud definujete moc málo, můžou být tak obecné, aby se překrývaly.

Pokud nepotřebujete identifikovat celkový uživatelský záměr, přidejte do `None`ho záměru všechny příklady uživatelů projevy. Pokud vaše aplikace roste na potřebu dalších záměrů, můžete je vytvořit později.

## <a name="create-example-utterances-for-each-intent"></a>Vytvořit příklad projevy pro každý záměr

Chcete-li začít, vyhněte se vytváření příliš velkého počtu projevy pro každý záměr. Po určení záměrů vytvořte 15 až 30 příkladů projevy na záměr. Každý utterance by se měl lišit od dříve poskytnuté projevy. Dobrá odrůda v projevy zahrnuje celkový počet slov, volbu Word, sloveso vhodné a interpunkční znaménka.

Další informace najdete v tématu [porozumění dobrým projevyům pro aplikace Luis](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identifikace entit

V příkladu projevy určete entity, které chcete, aby byl extrahován. Pro zarezervování letu potřebujete informace, jako je cílová, datum, letecká kategorie a třída pro cestování. Vytvořte entity pro tyto datové typy a označte [entity](luis-concept-entity-types.md) v příkladu projevy. Entity jsou důležité pro splnění záměru.

Při určování entit pro použití ve vaší aplikaci Pamatujte na to, že existují různé typy entit pro zachycení vztahů mezi typy objektů. [Entity v Luis](luis-concept-entity-types.md) poskytují další podrobnosti o různých typech.

> [!TIP]
> LUIS nabízí [předem připravené entity](luis-prebuilt-entities.md) pro běžné scénáře uživatelů v konverzaci. Zvažte použití předem připravených entit jako výchozího bodu pro vývoj aplikací.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Učení lifecylce vývoje LUIS](luis-concept-app-iteration.md)

