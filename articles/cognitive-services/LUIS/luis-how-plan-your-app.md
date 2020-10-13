---
title: Plánování aplikace – LUIS
description: Vyosnovujte relevantní záměry aplikací a entity a pak vytvořte svoje plány aplikací v Language Understanding inteligentní služby (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 69f2048da0b4690d9de72e96e296edbd55c4cd9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91309451"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Plánování schématu aplikace LUIS s využitím domény a extrakce dat předmětu

Schéma aplikace LUIS obsahuje [záměry](luis-glossary.md#intent) a [entity](luis-glossary.md#entity) , které se vztahují k vaší [doméně](luis-glossary.md#domain)předmětu. Záměry klasifikují [projevy](luis-glossary.md#utterance)uživatele a entity extrahují data z uživatelského projevy.

## <a name="identify-your-domain"></a>Identifikace domény

Aplikace LUIS se zacentruje kolem domény předmětu. Můžete mít například cestovní aplikaci, která zpracovává rezervace lístků, letů, hotelů a půjčovny vozidel. Jiná aplikace může poskytovat obsah týkající se výkonu, sledování úsilí o způsobilost a nastavování cílů. Identifikace domény vám pomůže najít slova nebo fráze, které jsou relevantní pro vaši doménu.

> [!TIP]
> LUIS nabízí [předem připravené domény](luis-how-to-use-prebuilt-domains.md) pro mnoho běžných scénářů. Zkontrolujte, jestli můžete použít předem sestavenou doménu jako výchozí bod pro vaši aplikaci.

## <a name="identify-your-intents"></a>Identifikujte své záměry

Zamyslete se nad [záměry](luis-concept-intent.md) , které jsou důležité pro úlohu vaší aplikace.

Podíváme se na příklad cestovní aplikace s funkcemi pro zarezervování letu a zkontrolujeme počasí v cíli uživatele. `BookFlight` `GetWeather` Pro tyto akce můžete definovat záměry a.

Ve složitější aplikaci s více funkcemi máte více záměrů a měli byste je pečlivě definovat, aby záměry nebyly příliš specifické. Například `BookFlight` a `BookHotel` může být potřeba oddělit záměry, ale `BookInternationalFlight` `BookDomesticFlight` může být příliš podobný.

> [!NOTE]
> Osvědčeným postupem je použít jenom tolik záměrů, kolik potřebujete k provádění funkcí aplikace. Pokud definujete příliš mnoho záměrů, je LUIS pro projevy správně klasifikovat. Pokud definujete moc málo, můžou být tak obecné, aby se překrývaly.

Pokud nepotřebujete identifikovat celkový úmysl uživatele, přidejte do záměru všechny ukázkové projevy uživatele `None` . Pokud vaše aplikace roste na potřebu dalších záměrů, můžete je vytvořit později.

## <a name="create-example-utterances-for-each-intent"></a>Vytvořit příklad projevy pro každý záměr

Chcete-li začít, vyhněte se vytváření příliš velkého počtu projevy pro každý záměr. Po určení záměrů vytvořte 15 až 30 příkladů projevy na záměr. Každý utterance by se měl lišit od dříve poskytnuté projevy. Dobrá odrůda v projevy zahrnuje celkový počet slov, volbu Word, sloveso vhodné a [interpunkční znaménka](luis-reference-application-settings.md#punctuation-normalization).

Další informace najdete v tématu [porozumění dobrým projevyům pro aplikace Luis](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identifikace entit

V příkladu projevy Identifikujte entity, které mají být extrahovány. Pro zarezervování letu potřebujete informace, jako je cílová, datum, letecká kategorie a třída pro cestování. Vytvořte entity pro tyto datové typy a označte [entity](luis-concept-entity-types.md) v příkladu projevy. Entity jsou důležité pro splnění záměru.

Při určování entit pro použití ve vaší aplikaci Pamatujte na to, že existují různé typy entit pro zachycení vztahů mezi typy objektů. [Entity v Luis](luis-concept-entity-types.md) poskytují další podrobnosti o různých typech.

> [!TIP]
> LUIS nabízí [předem připravené entity](luis-prebuilt-entities.md) pro běžné scénáře uživatelů v konverzaci. Zvažte použití předem připravených entit jako výchozího bodu pro vývoj aplikací.

## <a name="resolution-with-intent-or-entity"></a>Řešení s záměrem nebo entitou?

V mnoha případech, zejména při práci s přirozenými konverzacemi, uživatelé poskytují utterance, který může obsahovat více než jednu funkci nebo záměr. Pro vyřešení tohoto obecného pravidla poznáte, že reprezentace výstupu se dá dělat jak v záměrech, tak i v entitách. Tato reprezentace by se měla mapovat na akce klientských aplikací a nemusí být omezena na záměry.

**Int--li-vazby** je koncept, který akce (obvykle porozumění jako záměry) mohou být zachyceny jako entity a v tomto formuláři se spoléhaly na výstupní JSON, kde je lze namapovat na určitou akci. _Negace_ je běžné využití, které využívá tuto závislost na záměru i entitě pro úplnou extrakci.

Vezměte v úvahu následující dva projevyy, které se velmi blíží výběru slov, ale mají různé výsledky:

|Promluva|
|--|
|`Please schedule my flight from Cairo to Seattle`|
|`Cancel my flight from Cairo to Seattle`|

Místo toho, abyste měli dva samostatné záměry, vytvořte jeden záměr s `FlightAction` entitou strojového učení. Entita strojového učení by měla extrahovat podrobnosti o akci pro plánování i zrušení žádosti, a to i v případě původu nebo cílového umístění.

`FlightAction`Entita by byla strukturovaná v následujícím pseudo schématu entity Machine Learning a subentity:

* FlightAction
    * Akce
    * Zdroj
    * Cíl

Pro usnadnění extrakce přidejte funkce do subentit. Vaše funkce zvolíte na základě slovníku, který očekáváte, abyste viděli v projevy uživatelů a hodnoty, které chcete vrátit v odpovědi předpovědi.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Učení lifecylce vývoje LUIS](luis-concept-app-iteration.md)

