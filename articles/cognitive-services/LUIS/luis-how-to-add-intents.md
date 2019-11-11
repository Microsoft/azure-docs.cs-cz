---
title: Přidat záměry – LUIS
titleSuffix: Azure Cognitive Services
description: Přidejte do aplikace LUIS záměry k identifikaci skupin otázek nebo příkazů, které mají stejné záměry.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 66a3350dee60772ce706af8995179dcd8c485b64
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904318"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Přidání záměrů k určení záměru uživatele projevy

Přidejte do aplikace LUIS [záměry](luis-concept-intent.md) k identifikaci skupin dotazů nebo příkazů, které mají stejný záměr. 

Záměry jsou spravovány v části **sestavení** v horním navigačním panelu a pak z **záměrů**v levém panelu. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-intent"></a>Přidat záměr

1. Na [portálu Luis Preview](https://preview.luis.ai)vyberte **sestavení** a zobrazte záměry. 
1. Na stránce **záměry** vyberte **+ vytvořit**.
1. V dialogovém okně **vytvořit nový záměr** zadejte název záměru, například `ModifyOrder`, a vyberte **Hotovo**.

    > [!div class="mx-imgBorder"]
    > ![přidání záměru](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Záměr vyžaduje příklad projevy.

## <a name="add-an-example-utterance"></a>Přidat příklad utterance

Příkladem projevy jsou textové příklady dotazů a příkazů uživatele. Pokud chcete naučit Language Understanding (LUIS), kdy se má záměr odhadnout, musíte do záměru přidat příklad projevy. LUIS potřebuje pro začátek porozumění záměru v rozsahu od 15 do 30 example projevy. Nepřidávejte do hromadného projevy příklad. Každá utterance by měla pečlivě vycházet z toho, jak se liší od příkladů již v záměru. 

1. Na stránce Podrobnosti záměru zadejte relevantní utterance od uživatelů, například `Deliver a large cheese pizza` do textového pole pod názvem záměru a potom stiskněte klávesu ENTER.
 
    > [!div class="mx-imgBorder"]
    > ![stránce s podrobnostmi o záměrech, se zvýrazněnou možností utterance](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS převede všechny projevy na malá písmena a přidá mezery kolem [tokenů](luis-language-support.md#tokenization) , jako jsou spojovníky.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Chyby předpovědi záměru 

Příklad utterance v záměru může mít chybu předpovědi záměru mezi záměrem, který je v současnosti v rámci, a záměrem určeným během školení. 

Chcete-li najít chyby předpovědi utterance a opravit je, použijte možnosti **filtru** nesprávné a nejasné v kombinaci s možností **zobrazení** **podrobného zobrazení**. 

![Chcete-li najít chyby předpovědi utterance a opravit je, použijte možnost filtr.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Pokud jsou filtry a zobrazení aplikovány a existují příklady projevy s chybami, zobrazí se v seznamu ukázkový utterance projevy a problémy.

> [!div class="mx-imgBorder"]
> ![! [Při použití filtrů a zobrazení se zobrazí příklad projevy s chybami. v seznamu příkladů utterance se zobrazí projevy a problémy.] (./Media/Luis-How-to-Add-intents/Find-Errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

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
