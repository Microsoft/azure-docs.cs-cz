---
title: Popisek entity příkladu utterance
description: Naučte se, jak označit entitu strojového učení s podentitami na příkladu utterance na stránce s podrobnostmi záměru na portálu LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ffbaa2e40d5924ba61e548398e63295cf7dba2b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019731"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>Označení entity strojového učení v příkladu utterance

Označení entity v příkladu utterance poskytuje LUIS příklad, co je entita a kde se entita může zobrazit v utterance.

Můžete označovat prvky, které se naučily počítač, a subentity.

Vzhledem k tomu, že nemůžete označit regulární výraz, seznam nebo předem sestavené entity, vytvořte entitu nebo subentitu a přidejte tyto entity jako funkce, pokud jsou k dispozici, na entitu nebo subentitu.

## <a name="label-example-utterances-from-the-intent-detail-page"></a>Příklad popisku projevy na stránce s podrobnostmi záměru

Chcete-li popsat příklady entit v rámci utterance, vyberte záměr utterance.

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. Vyberte záměr s příkladem projevy, který chcete označit pro extrakci s entitou.
1. Vyberte text, který chcete označit, a pak vyberte entitu.

## <a name="two-techniques-to-label-entities"></a>Dva postupy pro označení entit

Na stránce s podrobnostmi záměru jsou podporovány dvě techniky označování.
* Vyberte entitu nebo podentitu z [palety entit](#label-with-the-entity-palette-visible) a pak vyberte v příkladu utterance text. To je doporučený postup, protože můžete vizuálně ověřit, že pracujete se správnou entitou nebo podentitou podle vašeho schématu.
* Nejprve vyberte v příkladu utterance text. Když to uděláte, zobrazí se místní nabídka [možností označování](#how-to-label-entity-from-in-place-menu) .

## <a name="label-with-the-entity-palette-visible"></a>Popisek s viditelnou paletou entit

Po [Naplánování schématu pomocí entit](luis-how-plan-your-app.md)nechejte při označování zobrazovat **paletu entit** . **Paleta entit** představuje připomenutí, které entity jste si naplánovali k extrakci.

Pokud chcete získat přístup k **paletě entit**, vyberte **@** symbol na panelu nástrojů nad ukázkovým seznamem utterance.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky se stránkou s podrobnostmi o záměru v paletě entit](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="how-to-label-entity-from-entity-palette"></a>Jak označit entitu z palety entit

Paleta entit nabízí alternativu k předchozímu prostředí označování. Umožňuje přenášet text na text, abyste ho mohli hned označit entitou.

1. Otevřete paletu entity tak, že vyberete **@** symbol v pravém horním rohu tabulky utterance.

2. Vyberte entitu z palety, kterou chcete označit. Tato akce je vizuálně označena novým kurzorem. Po přesunutí na portálu LUIS se ukazatel myši sleduje.

3. V příkladu utterance _malujte_ entitu pomocí kurzoru.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky ukazuje, že se entita vykresluje se kurzorem.](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="adding-entity-as-a-feature-from-the-entity-palette"></a>Přidání entity jako funkce z palety entit

V části paleta v paletě entit lze přidat funkce k aktuálně vybrané entitě. Můžete vybrat ze všech existujících entit a seznamů frází nebo vytvořit nový seznam frází.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s paletou entit s entitou jako funkcí](media/label-utterances/entity-palette-entity-as-a-feature.png)

## <a name="labeling-entity-roles"></a>Označování rolí entit

Role entit jsou označené pomocí **palety entit**.

1. Na stránce s podrobnostmi záměru vyberte z panelu nástrojů kontext **paletu entity** .
1. Po otevření palety entit vyberte ze seznamu entit entitu.
1. Pod seznamem entit vyberte existující roli.
1. V příkladu utterance text označte text pomocí role entity.

## <a name="how-to-label-entity-from-in-place-menu"></a>Jak označit entitu v místní nabídce

Označování na místě umožňuje rychle vybrat text v rámci utterance a označit ho jako popisek. Můžete také vytvořit entitu služby Machine Learning nebo seznam entit z textu s popiskem.

Vezměte v úvahu příklad utterance, `hi, please I want a cheese pizza in 20 minutes` .

Vyberte text na nejvyšší úrovni, vyberte nejvíce vpravo text entity a potom v místní nabídce vyberte entitu, se kterou chcete popisek označit.

> [!div class="mx-imgBorder"]
> ![Popisek dokončení počítače – studijní entita](media/label-utterances/label-steps-in-place-menu.png)

## <a name="review-labeled-text"></a>Zkontrolovat text s popiskem

Po označení se podívejte na příklad utterance a ujistěte se, že vybraný rozsah textu byl podtržený zvolenou entitou. Plná čára indikuje, že text byl označený.

> [!div class="mx-imgBorder"]
> ![Označení dokončená entita strojového učení](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Potvrdit předpovězenou entitu

Pokud se kolem rozsahu textu zobrazuje tečkovaná čárkou, indikuje to, že text je předpovězený, ale _ještě není označený_. Pokud chcete tuto předpověď převést na popisek, vyberte řádek utterance a pak na kontextovém panelu nástrojů vyberte **Potvrdit entity** .

## <a name="relabeling-over-existing-entities"></a>Přeznačení u stávajících entit

Pokud Přepište text, který je už označený, LUIS může rozdělit nebo sloučit existující popisky.

## <a name="labeling-for-punctuation"></a>Označení pro interpunkční znaménka

Nemusíte označovat interpunkční znaménka. Pomocí [nastavení aplikace](luis-reference-application-settings.md) můžete řídit, jak interpunkce ovlivňuje utterance předpovědi.

## <a name="unlabel-entities"></a>Odznačení entit

> [!NOTE]
> U některých entit strojového počítače se dají bez označení označit. Nemůžete označovat nebo odoznačovat entity regulárních výrazů, seznam entit nebo předem sestavených entit.

Chcete-li zrušit označení entity, vyberte entitu a v místní nabídce vyberte možnost zrušit **označení** .

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky znázorňující entitu s odznačením](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>Automatické označování pro nadřazené a podřízené entity

Pokud přiřadíte nadřazenou entitu, bude mít každá podentita, která se dá předpovědět, na základě aktuálně proučené verze, bude označená.

Pokud přidáváte popisek pro podentitu, bude nadřazená položka automaticky označena.

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>Automatické označování pro entity, které nejsou učené počítačem

Mezi nestrojové entity, které patří mezi počítače, patří předem připravené entity, entity regulárních výrazů, seznam entit a vzor. jakékoli entity. Tyto jsou automaticky označeny pomocí LUIS, takže není nutné je ručně popsat podle uživatelů.

## <a name="intent-prediction-errors"></a>Chyby předpovědi záměru

Chyba předpovědi záměru indikuje, že příklad utterance, s ohledem na aktuálně vyškolenou aplikaci, se pro záměr nepředpokládá.

Přečtěte si, jak [Zobrazit tyto chyby](luis-how-to-add-intents.md#intent-prediction-errors) na stránce s podrobnostmi záměru.

## <a name="entity-prediction-errors"></a>Chyby předpovědi entity

Chyby předpovědi entit označují, že předpovězená entita neodpovídá označené entitě. Toto je vizuálně s indikátorem upozornění vedle utterance.

> [!div class="mx-imgBorder"]
> ![Paleta entit pro entitu strojového učení](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Další kroky

K vylepšení kvality předpovědi vaší aplikace použijte [řídicí panel](luis-how-to-use-dashboard.md) a [Prohlédněte si projevy koncového bodu](luis-how-to-review-endpoint-utterances.md) .
