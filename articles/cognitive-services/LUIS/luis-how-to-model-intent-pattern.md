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
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: f0e801afde240d8b2d6e0798e6894b2e83f9c5cb
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560442"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Postup přidání vzorů pro zlepšení přesnosti předpovědi
Jakmile aplikace LUIS přijme koncový bod projevy, použijte [vzorek](luis-concept-patterns.md) , který vylepšit přesnost předpovědi pro projevy, která odhalí vzor v textovém pořadí a v textovém výběru. Vzory používají specifickou [syntaxi](luis-concept-patterns.md#pattern-syntax) k označení umístění: [entity](luis-concept-entity-types.md), [role](luis-concept-roles.md)entit a volitelného textu.

## <a name="add-template-utterance-to-create-pattern"></a>Přidat utterance šablony k vytvoření vzorku
1. Otevřete aplikaci tak, že vyberete jeho název na **Moje aplikace** stránce a pak vyberte **vzory** v levém panelu klikněte v části **zvýšit výkon aplikace**.

    ![Snímek obrazovky seznamu vzorů](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Vyberte správný záměr pro vzor. 

    ![Vyberte záměr](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. V textovém poli šablony zadejte utterance šablony a stiskněte Enter. Pokud chcete zadat název entity, použijte syntaxi správný model entity. Začněte syntaxe entity s `{`. Seznam entit zobrazí. Vyberte správný entitu a pak stiskněte Enter. 

    ![Snímek obrazovky pro model entity](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Pokud vaše entita obsahuje [roli](luis-concept-roles.md), uveďte roli s jednou dvojtečkou `:`, za `{Location:Origin}`názvem entity, jako je například. Seznam rolí pro entity, které se zobrazí v seznamu. Vyberte roli a pak stiskněte Enter. 

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

## <a name="next-steps"></a>Další postup

* Naučte se [vytvářet vzor](luis-tutorial-pattern.md) pomocí vzoru. jakékoli role a s kurzem.
* Zjistěte, jak [trénování](luis-how-to-train.md) vaší aplikace.
