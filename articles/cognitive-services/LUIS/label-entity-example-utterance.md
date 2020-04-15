---
title: Utterance příklad entity popisku
description: Zjistěte, jak označit entitu načenou počítače podkomponentami v příkladu utterance na stránce podrobností záměru portálu LUIS.
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: ea5fbea902c9694d9a8a6a8a5bffcf5e7234bbbd
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382406"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Označit entitu naučil stroj v příkladu utterance

Označení entity v příkladu utterance poskytuje LUIS příklad toho, co entita je a kde se může zobrazit v utterance.

## <a name="labeling-machine-learned-entity"></a>Označení strojově naučené entity

Vezměme `hi, please I want a cheese pizza in 20 minutes`si frázi, .

1. Vyberte text zcela vlevo, pak vyberte text entity zcela vpravo a vyberte entitu, kterou chcete označit, v tomto případě Dokončeno objednávka. _Úplné pořadí_ je označeno na následujícím obrázku.

    > [!div class="mx-imgBorder"]
    > ![Kompletní entita s úplným popiskem](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Vyberte entitu z rozbalovacího okna. Označená kompletní pizza objednávka entita obsahuje všechna slova (zleva doprava v angličtině), které jsou označeny.

## <a name="review-labeled-text"></a>Zkontrolovat označený text

Po označení zkontrolujte příklad utterance a ujistěte se, že vybrané rozpětí textu bylo podtrženo s vybranou entitou. Plná čára označuje, že text byl označen.

> [!div class="mx-imgBorder"]
> ![Označená úplná strojově naváděná entita](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Potvrdit předpokládanou entitu

Pokud je tečkované-lemované pole kolem rozpětí textu a název entity je nad utterance, označuje, že text je předpovězen, ale _ještě není označen_. Chcete-li převést předpověď na popisek, vyberte řádek utterance a pak vyberte **Potvrdit předpovědi entit**.

> [!div class="mx-imgBorder"]
> ![Předpovědět úplnou entitu naučenou strojem](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

Případně můžete vybrat název entity nad textem a pak v nabídce, která se zobrazí, vyberte **Potvrdit předpověď.**

> [!div class="mx-imgBorder"]
> ![Předpovědět kompletní strojově naučenou entitu s nabídkou](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png)

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>Označit entitu popiskem malováním kurzorem palety entit

Paleta entit nabízí alternativu k předchozímu prostředí s popisky. Umožňuje vám štětinu přes text okamžitě označit s entitou.

1. Otevřete paletu entit výběrem na ikoně Zvýrazňovač v pravém horním rohu tabulky utterance.

    > [!div class="mx-imgBorder"]
    > ![Paleta entit pro entitu navoděnou strojem](media/label-utterances/example-1-label-machine-learned-entity-palette.png)

2. Vyberte komponentu entity. Tato akce je vizuálně označena novým kurzorem. Kurzor sleduje myš při pohybu na portálu.

    > [!div class="mx-imgBorder"]
    > ![Paleta entit pro entitu navoděnou strojem](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png)

3. V příkladu _utterance, malovat_ entitu s kurzorem.

    > [!div class="mx-imgBorder"]
    > ![Paleta entit pro entitu navoděnou strojem](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>Označení dílčích součástí počítače získané entity

Dílčí součásti v entitách jsou označeny přesně stejným způsobem jako entity nejvyšší úrovně. Při výběru textu jsou entity dostupné v rozbalovacím okně relativní vzhledem k kontextu, ve kterém se text zobrazuje. Například pokud máte entitu s 5 úrovní počítače a vybíráte text, který byl označen první a druhou úrovní (označeno názvem labeled entity pod ukázkovou utterance), entity dostupné v rozbalovacím okně jsou omezeny na kontext komponent třetí úrovně. Chcete-li označit text jinými entitami, vyberte **Popisovat jako jinou** možnost entity.

> [!div class="mx-imgBorder"]
> ![Paleta entit pro entitu navoděnou strojem](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png)

Dílčí součásti lze označit pouze v případě, že je označena také nadřazená součást.

## <a name="labeling-entity-roles"></a>Role entity popisky

Role entit jsou označeny paletou entit.

1. Na stránce Podrobnosti záměru vyberte **paletu entit** z panelu nástrojů kontextu.
1. Po otevření palety Entity vyberte entitu ze seznamu entit.
1. Přesuňte se do **inspektoru entity**a vyberte existující roli nebo vytvořte novou roli.
1. V textu utterance příklad uvězte text s rolí entity.

## <a name="labeling-for-punctuation"></a>Popisek pro interpunkci

Pro interpunkci není nutné označovat. Nastavení [aplikace](luis-reference-application-settings.md) slouží k řízení, pokud interpunkce ovlivňuje předpovědi utterance.

## <a name="unlabel-entities"></a>Zrušit označení entit

Chcete-li zrušit označení entity, vyberte název entity pod textem a vyberte **Zrušit popisek**. Pokud entita, kterou se pokoušíte zrušit popisek, má označené dílčí součásti, musí být dílčí součásti nejprve neoznačeny.

## <a name="editing-labels-using-the-entity-palette"></a>Úpravy popisků pomocí palety entit

Pokud při popisování uděláte chybu, paleta entit je snadný nástroj, který umožňuje rychlé úpravy. Pokud například popisek entity omylem zahrnuje další slovo a již má označené dílčí součásti, můžete pomocí palety entit vyčistit požadovaný kratší rozsah slov.

Příklad:

1. Pizza Typ podsložka zahrnuje "sýr pizza s", který obsahuje extra nesprávné slovo - "s"

    > [!div class="mx-imgBorder"]
    > ![Paleta entit pro entitu navoděnou strojem](media/label-utterances/edit-label-with-palette-1.png)

2. Použijte paletu entit k výběru Pizza Type a štětcem nad "sýrovou pizzou". Výsledkem je, že pouze sýr pizza je označen jako Pizza Type nyní.

    > [!div class="mx-imgBorder"]
    > ![Paleta entit pro entitu navoděnou strojem](media/label-utterances/edit-label-with-palette-2.png)

## <a name="labels-for-matching-text-entities"></a>Popisky pro odpovídající textové entity

Entity odpovídajícího textu zahrnují předem sestavené entity, entity regulárních výrazů, entity seznamu a entity pattern.any. Ty jsou automaticky označeny službou LUIS, takže je uživatelé nemusí ručně označovat.

## <a name="entity-prediction-errors"></a>Chyby předpovědi entity

Chyby předpovědi entit označují, že předpovídaná entita neodpovídá označené entitě. To je vizualizováns upozornění indikátor vedle utterance.

> [!div class="mx-imgBorder"]
> ![Paleta entit pro entitu navoděnou strojem](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Další kroky

Pomocí [řídicího panelu](luis-how-to-use-dashboard.md) a [zkontrolujte projevy koncového bodu](luis-how-to-review-endpoint-utterances.md) ke zlepšení kvality předpovědi vaší aplikace.