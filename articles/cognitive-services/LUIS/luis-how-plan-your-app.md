---
title: Plánování aplikace
titleSuffix: Language Understanding - Azure Cognitive Services
description: Popisují relevantní aplikaci záměry a entity a pak vytvořte plány aplikace v Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: d0c5ba41bc636479407e7580fa686fe1bba91612
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873185"
---
# <a name="plan-your-luis-app-with-subject-domain-intents-and-entities"></a>Plánování aplikace LUIS s využitím domény předmětu, záměry a entity

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

## <a name="next-steps"></a>Další postup

Poté, co vaše aplikace se trénuje publikována a získá projevy koncový bod, v úmyslu implementovat vylepšení predikce s [aktivně učit](luis-how-to-review-endoint-utt.md), [frázi seznamy](luis-concept-feature.md), a [vzory](luis-concept-patterns.md). 


* Zobrazit [vytvořit svoji první aplikaci Language Understanding Intelligent Services (LUIS)](luis-get-started-create-app.md) rychlý návod, jak vytvořit aplikaci LUIS.
