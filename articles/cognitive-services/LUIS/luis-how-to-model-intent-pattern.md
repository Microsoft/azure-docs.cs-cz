---
title: Vzory – přesnost přidávání – LUIS
titleSuffix: Azure Cognitive Services
description: Přidejte šablony vzorů, aby se zlepšila přesnost předpovědi v aplikacích Language Understanding (LUIS).
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
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311712"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Postup přidání vzorů pro zlepšení přesnosti předpovědi
Jakmile aplikace LUIS přijme koncový bod projevy, použijte [vzorek](luis-concept-patterns.md) , který vylepšit přesnost předpovědi pro projevy, která odhalí vzor v textovém pořadí a v textovém výběru. Vzory používají specifickou [syntaxi](luis-concept-patterns.md#pattern-syntax) k označení umístění: [entity](luis-concept-entity-types.md), [role](luis-concept-roles.md)entit a volitelného textu.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> Vzory zahrnují pouze nadřazené entity, které se strojově naučily, ne subsoučásti.

## <a name="adding-example-utterances-as-pattern"></a>Přidání příkladu projevy jako vzoru

Pokud chcete přidat vzor pro entitu, _nejjednodušší_ způsob je vytvořit vzor ze stránky s podrobnostmi záměru. Tím se zaručí, že syntaxe odpovídá příkladu utterance.

1. Na [portálu Luis Preview](https://preview.luis.ai)vyberte aplikaci ze stránky **Moje aplikace** .
1. Na stránce seznam **záměrů** vyberte název záměru ukázkového utteranceu, ze kterého chcete vytvořit šablonu utterance.
1. Na stránce Podrobnosti záměru vyberte řádek pro příklad utterance, který chcete použít jako šablonu utterance, a pak vyberte **+ Přidat jako vzorek** z panelu nástrojů kontextu.

    > [!div class="mx-imgBorder"]
    > ![snímek obrazovky s výběrem příkladu utterance jako vzoru šablony na stránce s podrobnostmi záměru.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. V místním okně vyberte na stránce **Potvrdit vzorce** možnost **Hotovo** . Nemusíte definovat dílčí komponenty, omezení nebo popisovače entit. Potřebujete jenom vypsat entitu získanou počítačem.

    > [!div class="mx-imgBorder"]
    > ![snímek obrazovky s potvrzením příkladu utterance jako vzoru šablony na stránce s podrobnostmi záměru.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Pokud potřebujete šablonu upravit, například vybrat text jako volitelnou, v závorkách `[]` (hranaté závorky) je potřeba tuto úpravu provést na stránce **vzory** .

1. V navigačním panelu vyberte možnost **výuka** pro výuku aplikace s novým vzorem.

## <a name="add-template-utterance-using-correct-syntax"></a>Přidat šablonu utterance pomocí správné syntaxe

1. Otevřete aplikaci tak, že vyberete jeho název na **Moje aplikace** stránce a pak vyberte **vzory** v levém panelu klikněte v části **zvýšit výkon aplikace**.

    > [!div class="mx-imgBorder"]
    > ![snímek obrazovky](./media/luis-how-to-model-intent-pattern/patterns-1.png) seznamu vzorů

1. Vyberte správný záměr pro vzor.

1. V textovém poli šablony zadejte utterance šablony a stiskněte Enter. Pokud chcete zadat název entity, použijte syntaxi správný model entity. Začněte syntaxe entity s `{`. Seznam entit zobrazí. Vyberte správnou entitu.

    > [!div class="mx-imgBorder"]
    > ![snímek entity pro](./media/luis-how-to-model-intent-pattern/patterns-3.png) vzorů

    Pokud vaše entita obsahuje [roli](luis-concept-roles.md), uveďte roli s jednou dvojtečkou, `:`za názvem entity, jako je například `{Location:Origin}`. Seznam rolí pro entity, které se zobrazí v seznamu. Vyberte roli a pak stiskněte Enter.

    > [!div class="mx-imgBorder"]
    > ![snímku entity s](./media/luis-how-to-model-intent-pattern/patterns-4.png) rolí

    Po výběru entity správné zadáte vzor a pak stiskněte Enter. Po dokončení zadávání vzorce [trénování](luis-how-to-train.md) vaší aplikace.

    > [!div class="mx-imgBorder"]
    > ![snímek obrazovky zadaného vzoru s oběma typy entit](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Po změně modelu s vzory trénování vaší aplikace
Po přidání, úpravy, odebrat nebo změnit přiřazení vzor, [trénování](luis-how-to-train.md) a [publikovat](luis-how-to-publish-app.md) vaší aplikace pro vaše změny ovlivnit dotazy koncový bod.

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

## <a name="use-contextual-toolbar"></a>Použít kontextový panel nástrojů

Kontextový panel nad seznamem vzorů vám umožní:

* Vyhledat vzory
* Úprava vzoru
* Změnit přiřazení jednotlivých vzor, který má jiný záměr
* Znovu přiřadit několik vzory na různých záměr
* Odstranění – jeden vzor
* Odstranit některé postupy
* Vzor seznam můžete filtrovat podle entity
* Filtr – vzor – seznam po záměru
* Odebrat entitu nebo záměru filtru
* Přidat model z existující utterance na stránce záměr nebo entity

## <a name="next-steps"></a>Další kroky

* Naučte se [vytvářet vzor](luis-tutorial-pattern.md) pomocí vzoru. jakékoli role a s kurzem.
* Zjistěte, jak [trénování](luis-how-to-train.md) vaší aplikace.
