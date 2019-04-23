---
title: Vzory přidat přesnost
titleSuffix: Language Understanding - Azure Cognitive Services
description: Přidejte vzor šablony zvyšte přesnost předpovědi v aplikacích Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 202b9632b7a7faaf955874a0300edbe5134b7fa1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60196661"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Jak přidat vzorce, a zvyšte přesnost předpovědi
Po aplikaci LUIS obdrží koncový bod projevy, použijte [vzor](luis-concept-patterns.md) zvyšte přesnost předpovědi pro projevy, které se zjistí vzor v pořadí slov a požadované aplikace word. Vzory použít konkrétní [syntaxe](luis-concept-patterns.md#pattern-syntax) označující umístění: [entity](luis-concept-entity-types.md), entity [role](luis-concept-roles.md)a volitelný text, který.

## <a name="add-template-utterance-to-create-pattern"></a>Přidat utterance šablony k vytvoření vzorku
1. Otevřete aplikaci tak, že vyberete jeho název na **Moje aplikace** stránce a pak vyberte **vzory** v levém panelu klikněte v části **zvýšit výkon aplikace**.

    ![Snímek obrazovky seznamu vzorů](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Vyberte správný záměr pro vzor. 

    ![Vyberte záměr](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. V textovém poli šablony zadejte utterance šablony a stiskněte Enter. Pokud chcete zadat název entity, použijte syntaxi správný model entity. Začněte syntaxe entity s `{`. Seznam entit zobrazí. Vyberte správný entitu a pak stiskněte Enter. 

    ![Snímek obrazovky pro model entity](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Pokud entita obsahuje [role](luis-concept-roles.md), naznačit roli s jedinou dvojtečku `:`po název entity, jako `{Location:Origin}`. Seznam rolí pro entity, které se zobrazí v seznamu. Vyberte roli a pak stiskněte Enter. 

    ![Snímek obrazovky entity s rolí](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Po výběru entity správné zadáte vzor a pak stiskněte Enter. Po dokončení zadávání vzorce [trénování](luis-how-to-train.md) vaší aplikace.

    ![Snímek obrazovky zadaný vzor s oběma typy entit](./media/luis-how-to-model-intent-pattern/patterns-5.png)

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

## <a name="use-contextual-toolbar"></a>Použít kontextové panel nástrojů

Kontextové nástrojů nad seznamem vzory vám umožní:

* Hledat vzorce
* Úprava vzoru
* Změnit přiřazení jednotlivých vzor, který má jiný záměr
* Znovu přiřadit několik vzory na různých záměr
* Odstranění jeden vzor
* Odstranit některé postupy
* Vzor seznam můžete filtrovat podle entity
* Filtr vzor – seznam – podle záměr
* Odebrat entitu nebo záměru filtru
* Přidat model z existující utterance na stránce záměr nebo entity

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [sestavení vzor](luis-tutorial-pattern.md) se pattern.any a rolemi kurzu.
* Zjistěte, jak [trénování](luis-how-to-train.md) vaší aplikace.
