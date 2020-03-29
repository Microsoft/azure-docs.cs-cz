---
title: Vzorky zvyšují přesnost – LUIS
titleSuffix: Azure Cognitive Services
description: Přidejte šablony vzorů pro zlepšení přesnosti předpovědi v aplikacích jazyka porozumění (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 21afb12bf2464218119ebf52ebd980745e3d731d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76311712"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Jak přidat vzory pro zlepšení přesnosti predikce
Poté, co aplikace LUIS obdrží projevy koncového bodu, použijte [vzorek](luis-concept-patterns.md) ke zlepšení přesnosti předpovědi pro projevy, které odhalují vzor v pořadí slov a výběru slov. Vzorky používají specifickou [syntaxi](luis-concept-patterns.md#pattern-syntax) k označení umístění: [entit](luis-concept-entity-types.md), [rolí](luis-concept-roles.md)entit a volitelného textu.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> Vzorky zahrnují pouze počítačem učené entity, nikoli dílčí součásti.

## <a name="adding-example-utterances-as-pattern"></a>Přidání ukázkových promluv jako vzor

Pokud chcete přidat vzorek pro entitu, _nejjednodušší_ způsob je vytvořit vzorek ze stránky podrobnosti záměru. Tím zajistíte, že syntaxe odpovídá ukázkové utterance.

1. Na [portálu LUIS ve verzi Preview](https://preview.luis.ai)vyberte aplikaci na stránce Moje **aplikace.**
1. Na stránce seznamu **Záměry** vyberte název záměru ukázkové utterance, ze které chcete vytvořit projev šablony.
1. Na stránce Podrobnosti záměru vyberte řádek pro ukázkovou promluvu, kterou chcete použít jako utterance šablony, a pak z panelu nástrojů kontextu vyberte **+ Přidat jako vzorek.**

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s výběrem ukázkové utterance jako vzoru šablony na stránce Podrobnosti záměru.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. Ve vyskakovacím poli vyberte **Hotovo** na stránce **Potvrdit vzorky.** Není nutné definovat dílčí součásti entit, omezení nebo popisovače. Stačí pouze uvést stroj-naučil entity.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s potvrzením ukázkového utterance jako vzoru šablony na stránce Podrobnosti záměru](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Pokud potřebujete upravit šablonu, například výběr textu jako `[]` volitelného, pomocí (čtvercových) závorek, musíte tuto úpravu provést na stránce **Vzorky.**

1. Na navigačním panelu vyberte **Trénovat,** chcete-li aplikaci trénovat s novým vzorem.

## <a name="add-template-utterance-using-correct-syntax"></a>Přidání promluvy šablony pomocí správné syntaxe

1. Otevřete aplikaci tak, že na stránce **Moje aplikace** vyberete její název, a pak v levém panelu vyberte **Vzorky** v části **Zlepšení výkonu aplikace**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky se seznamem vzorů](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Vyberte správný záměr pro vzorek.

1. Do textového pole šablony zadejte utterance šablony a vyberte Enter. Pokud chcete zadat název entity, použijte správnou syntaxi entity vzoru. Začněte syntaxi `{`entity pomocí . Zobrazí se seznam entit. Vyberte správnou entitu.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky entity pro vzorek](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Pokud vaše entita obsahuje [roli](luis-concept-roles.md), označte roli s jedním dvojtečkou , `:`za názvem entity, například `{Location:Origin}`. Seznam rolí pro entity se zobrazí v seznamu. Vyberte roli a pak vyberte Enter.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky entity s rolí](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Po výběru správné entity dokončete zadávání vzoru a pak vyberte Enter. Až skončíte s zadáváním vzorů, [trénujte](luis-how-to-train.md) aplikaci.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky se zadaným vzorkem s oběma typy entit](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Trénování aplikace po změně modelu se vzory
Po přidání, úpravě, odebrání nebo opětovném přiřazení vzoru [trénování](luis-how-to-train.md) a [publikování](luis-how-to-publish-app.md) aplikace, aby změny ovlivnily dotazy koncových bodů.

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Použití kontextové panelu nástrojů

Kontextový panel nástrojů nad seznamem vzorů umožňuje:

* Hledání vzorů
* Úprava vzorku
* Přeřadit individuální vzor k jinému záměru
* Opětovné přiřazení několika vzorů k jinému záměru
* Odstranit jeden vzorek
* Odstranění několika vzorků
* Seznam vzorků filtru podle entity
* Filtr-vzor-list-by-intent
* Odebrat filtr entity nebo záměru
* Přidání vzoru z existující utterance na stránce záměru nebo entity

## <a name="next-steps"></a>Další kroky

* Naučte [se,](luis-tutorial-pattern.md) jak vytvořit vzor s pattern.any a role s kurzem.
* Přečtěte si, jak [aplikaci trénovat.](luis-how-to-train.md)
