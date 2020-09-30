---
title: Přidat záměry – LUIS
titleSuffix: Azure Cognitive Services
description: Přidejte do aplikace LUIS záměry k identifikaci skupin otázek nebo příkazů, které mají stejné záměry.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.service: cognitive-services
ms.openlocfilehash: c2802f1b41ed2842e12c808a8c380ebd646ffa03
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540927"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Přidání záměrů k určení záměru uživatele projevy

Přidejte do aplikace LUIS [záměry](luis-concept-intent.md) k identifikaci skupin dotazů nebo příkazů, které mají stejný záměr.

V portálu LUIS se záměry spravují z části **sestavení** v horním navigačním panelu a pak z **záměrů**v levém panelu.

## <a name="add-an-intent-to-your-app"></a>Přidání záměru do aplikace

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. Na stránce **záměry** vyberte **+ vytvořit**.
1. V dialogovém okně **vytvořit nový záměr** zadejte název záměru, například `ModifyOrder` a potom vyberte **Hotovo**.

    > [!div class="mx-imgBorder"]
    > ![Přidat záměr](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Záměr vyžaduje [příklad projevy](luis-concept-utterance.md) , aby bylo možné předpovědět projevy na publikovaném koncovém bodu předpovědi.

## <a name="add-an-example-utterance"></a>Přidat příklad utterance

Příkladem projevy jsou textové příklady dotazů a příkazů uživatele. Pokud chcete naučit Language Understanding (LUIS), kdy se má záměr odhadnout, musíte do záměru přidat příklad projevy. LUIS potřebuje pro začátek porozumění záměru v rozsahu od 15 do 30 example projevy. Nepřidávejte do hromadného projevy příklad. Každá utterance by měla pečlivě vycházet z toho, jak se liší od příkladů již v záměru.

1. Na stránce Podrobnosti záměru zadejte relevantní utterance od uživatelů, například `Deliver a large cheese pizza` do textového pole pod názvem záměr, a potom stiskněte klávesu ENTER.

    > [!div class="mx-imgBorder"]
    > ![Stránka s podrobnostmi o záměrech se zvýrazněným utterance](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png)

    LUIS převede všechny projevy na malá písmena a přidá mezery kolem [tokenů](luis-language-support.md#tokenization) , jako jsou spojovníky.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Chyby předpovědi záměru

V případě, že utterance není předpovězena výukovou aplikací pro záměr, je určena chyba předpovědi záměru.

1. Pokud chcete najít chyby předpovědi utterance a opravit je, použijte možnosti **filtru** nesprávného a nejasného.

    > [!div class="mx-imgBorder"]
    > ![Chcete-li najít chyby předpovědi utterance a opravit je, použijte možnost filtr.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

1. Chcete-li zobrazit hodnotu skóre na stránce s podrobnostmi záměru, v nabídce možnosti **zobrazení** vyberte možnost **Zobrazit výsledky záměru podrobností** .

    Pokud jsou filtry a zobrazení aplikovány a existují příklady projevy s chybami, zobrazí se v seznamu ukázkový utterance projevy a problémy.

V každém řádku se zobrazuje skóre předpovědi aktuálního školení pro příklad utterance, nejbližšího skóre rival, což je rozdíl v těchto dvou skórech.

### <a name="fixing-intents"></a>Opravení záměrů

Pokud se chcete dozvědět, jak opravit chyby předpovědi, použijte [řídicí panel souhrnu](luis-how-to-use-dashboard.md). Souhrnný řídicí panel poskytuje analýzu pro poslední školení aktivní verze a nabízí hlavní návrhy na opravu modelu.

## <a name="using-the-contextual-toolbar"></a>Použití kontextového panelu nástrojů

Panel nástrojů kontextu poskytuje další akce:

* Upravit nebo odstranit příklad utterance
* Opětovné přiřazení příkladu utterance k jinému záměru
* Filtry a zobrazení: zobrazit jenom projevy obsahující filtrované entity nebo zobrazit volitelné podrobnosti
* Hledání prostřednictvím příkladu projevy

## <a name="train-your-app-after-changing-model-with-intents"></a>Výuka aplikace po změně modelu s záměry

Po přidání, úpravě nebo odebrání záměrů, [školení](luis-how-to-train.md) a [publikování](luis-how-to-publish-app.md) aplikace, aby byly změny aplikovány na dotazy koncového bodu. Nevytvářejte vlaky po každé jedné změně. Naučit se po skupině změn.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o přidání [ukázkového projevyu](luis-how-to-add-example-utterances.md) s entitami.
