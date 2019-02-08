---
title: Vzory přidat přesnost
titleSuffix: Language Understanding - Azure Cognitive Services
description: Zjistěte, jak přidat vzor šablon v aplikacích Language Understanding (LUIS) zvyšte přesnost předpovědi.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 9d3352017723f5beac318d461a537820b6593bef
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881668"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Jak přidat vzorce, a zvyšte přesnost předpovědi
Po aplikaci LUIS obdrží koncový bod projevy, použijte [koncept](luis-concept-patterns.md) vzorů zvyšte přesnost předpovědi pro projevy, které se zjistí vzor v pořadí slov a požadované aplikace word. Vzory používání [entity](luis-concept-entity-types.md) a jejich rolí k extrakci dat pomocí specifického vzoru syntaxe. 

## <a name="add-template-utterance-to-create-pattern"></a>Přidat utterance šablony k vytvoření vzorku
1. Otevřete aplikaci tak, že vyberete jeho název na **Moje aplikace** stránce a pak vyberte **vzory** v levém panelu klikněte v části **zvýšit výkon aplikace**.

    ![Snímek obrazovky seznamu vzorů](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Vyberte správný záměr pro vzor. 

    ![Vyberte záměr](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. V textovém poli šablony zadejte utterance šablony a stiskněte Enter. Pokud chcete zadat název entity, použijte syntaxi správný model entity. Začněte syntaxe entity s `{`. Seznam entit zobrazí. Vyberte správný entitu a pak stiskněte Enter. 

    ![Snímek obrazovky pro model entity](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Pokud entita obsahuje roli, naznačit roli s jedinou dvojtečku `:`po název entity, jako `{Location:Origin}`. Seznam rolí pro entity, které se zobrazí v seznamu. Vyberte roli a pak stiskněte Enter. 

    ![Snímek obrazovky entity s rolí](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Po výběru entity správné zadáte vzor a pak stiskněte Enter. Po dokončení zadávání vzorce [trénování](luis-how-to-train.md) vaší aplikace.

    ![Snímek obrazovky zadaný vzor s oběma typy entit](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="search-patterns"></a>Vyhledávání vzorků
Vyhledávání umožňuje najít vzory, které obsahují daný text.  

1. Vyberte ikonu lupy.

    ![Vzory snímek obrazovky stránky s ikonou nástroj hledání zvýrazněnou](./media/luis-how-to-model-intent-pattern/search-icon.png)

    Zadejte hledaný text do pole Hledat v pravém horním rohu seznam vzorů a stiskněte Enter. Seznam vzorů se aktualizuje a zobrazí jenom ty vzory, včetně hledaný text.

    ![Snímek obrazovky vzory stránky pomocí vyhledávacího pole zvýrazní hledaný text](./media/luis-how-to-model-intent-pattern/search-text.png)

    Chcete-li zrušit hledání a obnovit váš úplný seznam vzorů, odstraňte hledaný text, který jste zadali.

<!-- TBD: should I be able to click on the magnifying glass again to close the search box? It doesn't reset the list. -->

## <a name="edit-a-pattern"></a>Úprava vzoru
1. Chcete-li upravit vzor, vyberte tři tečky (***...*** ) tlačítko na pravém konci řádku pro tento model a pak vyberte **upravit**. 

    ![Snímek obrazovky Upravit položku nabídky na řádku vzor](./media/luis-how-to-model-intent-pattern/patterns-three-dots.png) 

2. Zadejte všechny změny v textovém poli. Jakmile budete hotovi, vyberte možnost zadat. Po dokončení úprav vzory [trénování](luis-how-to-train.md) vaší aplikace.

    ![Snímek obrazovky úpravy vzor](./media/luis-how-to-model-intent-pattern/edit-pattern.png)

## <a name="reassign-individual-pattern-to-different-intent"></a>Změnit přiřazení jednotlivých vzor, který má jiný záměr

Jeden vzor na různých záměr přiřazení, vyberte záměru seznamu vpravo od textu vzor a vyberte jiný záměr.

![Snímek obrazovky přiřazení jednotlivých vzor, který má jiný záměr](./media/luis-how-to-model-intent-pattern/reassign-individual-pattern.png)

## <a name="reassign-several-patterns-to-different-intent"></a>Znovu přiřadit několik vzory na různých záměr

Změnit některé postupy na různých záměr přiřazení, vyberte zaškrtávací políčko nalevo od každý vzorek nebo horní políčko zaškrtněte, pokud. **Záměr přiřazení** možnost se zobrazí na panelu nástrojů. Vyberte správný záměr pro tyto vzory se dají. 

![Snímek obrazovky přiřazení několika vzory na různých záměr](./media/luis-how-to-model-intent-pattern/reassign-many-patterns.png)

## <a name="delete-a-single-pattern"></a>Odstranit jeden vzor

1. Pokud chcete odstranit vzor, vyberte tři tečky (***...*** ) tlačítko na pravém konci řádku pro tento model a pak vyberte **odstranit**. 

    ![Snímek obrazovky odstranit utterance](./media/luis-how-to-model-intent-pattern/patterns-three-dots-ddl.png)

2. Vyberte **Ok** potvrďte odstranění.

    ![Snímek obrazovky odstranit potvrzení](./media/luis-how-to-model-intent-pattern/confirm-delete.png)

## <a name="delete-several-patterns"></a>Odstranit některé postupy

1. Pokud chcete odstranit některé postupy, vyberte zaškrtávací políčko nalevo od každý vzorek nebo horní políčko zaškrtněte, pokud. **Odstranit způsoby (s)** možnost se zobrazí na panelu nástrojů. Vyberte **odstranit způsoby (s)**.  

    ![Snímek obrazovky s odstraněním několik vzory](./media/luis-how-to-model-intent-pattern/delete-many-patterns.png)

2. **Odstranit vzory** se zobrazí dialogové okno potvrzení. Vyberte **Ok** na dokončení odstranění.

    ![Snímek obrazovky s odstraněním několik pole pro potvrzení vzory](./media/luis-how-to-model-intent-pattern/delete-many-patterns-confirmation.png)

## <a name="filter-pattern-list-by-entity"></a>Vzor seznam můžete filtrovat podle entity

Chcete-li filtrovat seznam vzorů podle konkrétní entitu, vyberte **Entity filtry** na panelu nástrojů nad vzory. 

![Snímek obrazovky vzory pomocí filtrování podle entity](./media/luis-how-to-model-intent-pattern/filter-entities-1.png)

Po použití filtru název entity se zobrazí pod panelu nástrojů. 

## <a name="filter-pattern-list-by-intent"></a>Vzor seznam můžete filtrovat podle záměr

Chcete-li filtrovat seznam vzorů podle konkrétní záměr, vyberte **záměr filtry** na panelu nástrojů nad vzory. 

![Snímek obrazovky vzory pomocí filtrování podle záměr](./media/luis-how-to-model-intent-pattern/filter-intents-1.png)

Po použití filtru záměru název se zobrazí pod panelu nástrojů. 

## <a name="remove-entity-or-intent-filter"></a>Odebrat entitu nebo záměru filtru
Pokud vzor seznam je filtrovaný, entity nebo záměru název se zobrazí pod panelem nástrojů. Chcete-li odebrat filtr, vyberte název.

![Snímek obrazovky z entity odebrat filtr](./media/luis-how-to-model-intent-pattern/filter-entities-2.png)

Bude filtr odebrán a zobrazit všechny vzorky. 

## <a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>Přidat model z existující utterance na stránce záměr nebo entity
Vzoru můžete vytvořit ze stávající utterance buď **záměr** nebo **Entity** stránky. Všechny projevy na libovolné stránce záměr nebo entity se zobrazí v seznamu se v pravém sloupci, jako poskytuje přístup k možnosti na úrovni utterance **upravit**, **odstranit**, a **přidat jako vzor**.

1. Na vybraný řádek utterance, vyberte tři tečky (***...*** ) tlačítko vpravo utterance a vybrat **přidat jako vzor**.

    [![Snímek obrazovky projevy tabulky se zvýrazněnou možností nabídky vzorem přidat](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png "projevy tabulky se zvýrazněnou možností nabídky vzorem přidat snímek obrazovky")](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png)

2. Upravit model podle [syntaktickými pravidly](luis-concept-patterns.md#pattern-syntax). Pokud s entitami označený utterance, kterou jste vybrali, tyto entity jsou již ve vzoru s správnou syntaxi.

    ![Snímek obrazovky filtrované vzory entity](./media/luis-how-to-model-intent-pattern/confirm-patterns-modal.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Po změně modelu s vzory trénování vaší aplikace
Po přidání, úpravy, odebrat nebo změnit přiřazení vzor, [trénování](luis-how-to-train.md) a [publikovat](luis-how-to-publish-app.md) vaší aplikace pro vaše změny ovlivnit dotazy koncový bod. 

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [sestavení vzor](luis-tutorial-pattern.md) pattern.any a rolí.
* Zjistěte, jak [trénování](luis-how-to-train.md) vaší aplikace.
