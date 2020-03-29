---
title: Plánování aplikace – LUIS
titleSuffix: Azure Cognitive Services
description: Načrtněte relevantní záměry a entity aplikací a pak vytvořte plány aplikací v inteligentních službách pro porozumění jazykům (LUIS).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74326780"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Plánování schématu aplikace LUIS s doménou subjektu a extrakcí dat

Schéma aplikace LUIS obsahuje [záměry](luis-glossary.md#intent) a [entity](luis-glossary.md#entity) relevantní pro vaši [doménu předmětu](luis-glossary.md#domain). Záměry klasifikovat [projevy](luis-glossary.md#utterance)uživatele a entity extrahovat data z projevy uživatele.

## <a name="identify-your-domain"></a>Identifikace domény

Aplikace LUIS je soustředěna kolem domény předmětu. Můžete mít například cestovní aplikaci, která zpracovává rezervaci letenek, letů, hotelů a pronajatých vozů. Jiná aplikace může poskytovat obsah související s cvičením, sledováním fitness úsilí a stanovením cílů. Identifikace domény vám pomůže najít slova nebo fráze, které jsou relevantní pro vaši doménu.

> [!TIP]
> Služba LUIS nabízí [předem vytvořené domény](luis-how-to-use-prebuilt-domains.md) pro mnoho běžných scénářů. Zkontrolujte, jestli můžete jako výchozí bod pro aplikaci použít předem připravenou doménu.

## <a name="identify-your-intents"></a>Identifikujte své záměry

Zamyslete se nad [záměry,](luis-concept-intent.md) které jsou důležité pro úlohu vaší aplikace.

Vezměme si příklad cestovní aplikace s funkcemi pro rezervaci letu a kontrolu počasí v cíli uživatele. Můžete definovat `BookFlight` a `GetWeather` záměry pro tyto akce.

Ve složitější aplikaci s více funkcemi máte více záměrů a měli byste je pečlivě definovat, aby záměry nebyly příliš specifické. Například `BookFlight` a `BookHotel` může být nutné samostatné `BookInternationalFlight` záměry, ale a `BookDomesticFlight` může být příliš podobné.

> [!NOTE]
> Je osvědčeným postupem používat pouze tolik záměrů, kolik potřebujete k provádění funkcí vaší aplikace. Pokud definujete příliš mnoho záměrů, bude pro LUIS obtížnější správně klasifikovat projevy. Pokud definujete příliš málo, mohou být tak obecné, že se překrývají.

Pokud nepotřebujete identifikovat celkový záměr uživatele, přidejte všechny ukázkové `None` projevy uživatele k záměru. Pokud se vaše aplikace zvětší a potřebuje více záměrů, můžete je vytvořit později.

## <a name="create-example-utterances-for-each-intent"></a>Vytvořit ukázkové projevy pro každý záměr

Chcete-li začít s, vyhněte se vytváření příliš mnoho projevy pro každý záměr. Jakmile jste určili záměry, vytvořte 15 až 30 příklad projevy na záměr. Každý utterance by se měla lišit od dříve zadaných projevy. Dobrá rozmanitost v projevech zahrnují celkový počet slov, volbu slova, slovesný čas a interpunkci.

Další informace naleznete [v tématu pochopení dobré projevy pro aplikace LUIS](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identifikujte své entity

V příkladu projevy identifikujte entity, které chcete extrahovat. Chcete-li rezervovat let, potřebujete informace, jako je cíl, datum, letecká společnost, kategorie letenek a cestovní třída. Vytvořte entity pro tyto datové typy a pak označte [entity](luis-concept-entity-types.md) v příkladu projevy. Entity jsou důležité pro dosažení záměru.

Při určování, které entity se mají ve vaší aplikaci používat, mějte na paměti, že existují různé typy entit pro zachycení vztahů mezi typy objektů. [Entity v LUIS](luis-concept-entity-types.md) poskytuje další podrobnosti o různých typech.

> [!TIP]
> Služba LUIS nabízí [předem vytvořené entity](luis-prebuilt-entities.md) pro běžné scénáře konverzačních uživatelů. Zvažte použití předem sestavené entity jako výchozí bod pro vývoj aplikací.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Učení luis vývoj lifecylce](luis-concept-app-iteration.md)

