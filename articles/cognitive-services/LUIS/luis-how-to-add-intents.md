---
title: Přidání záměrů – LUIS
titleSuffix: Azure Cognitive Services
description: Přidejte záměry do aplikace LUIS k identifikaci skupin otázek nebo příkazů, které mají stejné záměry.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73904318"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Přidání záměrů k určení záměru uživatele projevy

Přidejte [záměry](luis-concept-intent.md) do aplikace LUIS k identifikaci skupin otázek nebo příkazů, které mají stejný záměr. 

Záměry jsou spravovány z horní navigační panel **je sestavení** části, pak z **záměry**levého panelu . 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-intent"></a>Přidat záměr

1. Na [portálu náhledu LUIS](https://preview.luis.ai)vyberte **Sestavení,** chcete-li zobrazit záměry. 
1. Na stránce **Záměry** vyberte **+ Vytvořit**.
1. V dialogovém okně **Vytvořit nový záměr** zadejte `ModifyOrder`například název záměru a vyberte **Hotovo**.

    > [!div class="mx-imgBorder"]
    > ![Přidat záměr](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Záměr potřebuje příklad projevy.

## <a name="add-an-example-utterance"></a>Přidání příkladu utterance

Příklad y projevy jsou textové příklady uživatelských otázek nebo příkazů. Chcete-li učit porozumění jazykům (LUIS), kdy předpovědět tento záměr, je třeba přidat příklad projevy záměru. Služba LUIS potřebuje v rozsahu 15 až 30 příklad projevy začít chápat záměr. Nepřidávejte příklad projevy hromadně. Každý utterance by měl být pečlivě vybrán pro jak se liší od příkladů již v záměru. 

1. Na stránce podrobností záměru zadejte relevantní utterance, `Deliver a large cheese pizza` který očekáváte od uživatelů, například do textového pole pod názvem záměru, a stiskněte Enter.
 
    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky se stránkou podrobností záměrů se zvýrazněnou promluvou](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    Služba LUIS převede všechny projevy na malá písmena a přidá mezery kolem [tokenů,](luis-language-support.md#tokenization) jako jsou pomlčky.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Chyby předpovědi záměru 

Příklad utterance v záměru může mít záměr předpověď chyba mezi záměru vpříklad utterance je aktuálně v a záměr určený během tréninku. 

Chcete-li najít chyby předpovědi utterance a opravit je, použijte **možnosti filtru** nesprávné a nejasné v kombinaci s **view** možnost **podrobné zobrazení**. 

![Chcete-li najít chyby předpovědi utterance a opravit je, použijte filtr možnost.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Při použití filtry a zobrazení a existují příkladprojevy s chybami, seznam utterance příklad zobrazuje projevy a problémy.

> [!div class="mx-imgBorder"]
> ![! [Při použití filtrů a zobrazení a existují příklady projevy s chybami, seznam utterance příklad udává projevy a problémy.] (./media/luis-how-to-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Každý řádek zobrazuje aktuální skóre predikce tréninku pro ukázkový utterance, nejbližší skóre soupeře, což je rozdíl v těchto dvou skóre. 

### <a name="fixing-intents"></a>Oprava záměrů

Chcete-li se dozvědět, jak opravit chyby předpovědi záměru, použijte [souhrnný řídicí panel](luis-how-to-use-dashboard.md). Souhrnný řídicí panel poskytuje analýzu posledního školení aktivní verze a nabízí nejlepší návrhy pro opravu modelu.  

## <a name="using-the-contextual-toolbar"></a>Použití kontextového panelu nástrojů

Kontextový panel nástrojů obsahuje další akce:

* Upravit nebo odstranit ukázkový utterance
* Změnit přiřazení ukázkové utterance k jinému záměru
* Filtry a zobrazení: zobrazují pouze projevy obsahující filtrované entity nebo volitelné podrobnosti
* Prohledávat ukázkové projevy

## <a name="train-your-app-after-changing-model-with-intents"></a>Trénování aplikace po změně modelu se záměry

Po přidání, úpravě nebo odebrání záměrů [trénování](luis-how-to-train.md) a [publikování](luis-how-to-publish-app.md) aplikace tak, aby se změny použily na dotazy koncového bodu. Netrénujte po každé změně. Trénujte po skupině změn. 

## <a name="next-steps"></a>Další kroky

Další informace o přidávání [ukázkových projevů](luis-how-to-add-example-utterances.md) s entitami. 
