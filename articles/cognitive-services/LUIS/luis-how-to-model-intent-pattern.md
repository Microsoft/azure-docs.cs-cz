---
title: Přidat vzor šablon v aplikacích LEOŠ | Microsoft Docs
titleSuffix: Azure
description: Informace o postupu přidání vzor šablon v aplikacích jazyka Principy (LEOŠ) a zvyšte tak přesnost předpovědi.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: 68c0ea1fd3f2e60e0adec631f33c8bd09a3d9960
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35346002"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Postup přidání vzory a zvyšte tak přesnost předpovědi
Po LEOŠ aplikace obdrží utterances koncový bod, použijte [koncept](luis-concept-patterns.md) schémat a zvyšte tak přesnost předpovědi pro utterances, které se zobrazí v vzor v pořadí aplikace word a volba aplikace word. Vzory použití [entity](luis-concept-entity-types.md) a jejich rolí extrahovat data pomocí specifického vzoru syntaxe. 

## <a name="add-template-utterance-to-create-pattern"></a>Přidat utterance šablony pro vytvoření vzorku
1. Otevřete aplikaci tak, že vyberete svůj název na **Moje aplikace** a pak vyberte **vzory** v levém panelu klikněte v části **zlepšit výkon aplikace**.

    ![Snímek obrazovky seznamu vzory](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Vyberte správný záměr pro vzoru. 

    ![Vyberte záměr](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. Do textového pole šablony zadejte utterance šablony a Enter. Pokud chcete zadat název entity, použijte syntaxi správné vzor entity. Začněte syntaxe entity s `{`. Seznam zobrazí entity. Vyberte správný entity a pak vyberte Enter. 

    ![Snímek obrazovky entity pro vzor](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Pokud vaše entity zahrnuje roli, znamenat role s jedinou dvojtečku, `:`po název entity, jako například `{Location:Origin}`. Zobrazí seznam rolí pro entity v seznamu. Vyberte roli a pak vyberte Enter. 

    ![Snímek obrazovky entity s rolí](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Po výběru správné entity dokončit zadáním vzoru a Enter. Po dokončení vstup vzory [cvičení](luis-how-to-train.md) vaší aplikace.

    ![Snímek obrazovky zadat vzor s oběma typy entit](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="search-patterns"></a>Vzory pro vyhledávání
Hledání můžete najít vzorů, které obsahují daný text.  

1. Vyberte ikonu lupy.

    ![Snímek obrazovky vzory stránka se zvýrazněnou ikonu nástroj pro hledání](./media/luis-how-to-model-intent-pattern/search-icon.png)

    Zadejte hledaný text do pole Hledat v pravém horním rohu seznamu vzorů a Enter. Seznam vzorů se aktualizuje a zobrazí pouze vzory včetně hledaný text.

    ![Snímek obrazovky vzory stránka se hledaný text zvýrazněná vyhledávacího pole](./media/luis-how-to-model-intent-pattern/search-text.png)

    Chcete-li zrušit hledání a obnovit vaše úplný seznam vzorů, odstraňte hledaný text, který jste zadali.

<!-- TBD: should I be able to click on the magnifying glass again to close the search box? It doesn't reset the list. -->

## <a name="edit-a-pattern"></a>Upravit vzor
1. Chcete-li upravit vzor, vyberte ikonu tři tečky (...) na pravém konci řádku pro tento vzor a poté vyberte **upravit**. 

    ![Snímek obrazovky Upravit položku nabídky v řádku vzor](./media/luis-how-to-model-intent-pattern/patterns-three-dots.png) 

2. Zadejte všechny změny v textovém poli. Až skončíte, zadejte vyberte. Po dokončení úprav vzory [cvičení](luis-how-to-train.md) vaší aplikace.

    ![Snímek obrazovky úpravy vzor](./media/luis-how-to-model-intent-pattern/edit-pattern.png)

## <a name="reassign-individual-pattern-to-different-intent"></a>Přiřazení jednotlivých vzor různých záměrů

K přiřazení single vzor do různých záměrů, vyberte pole se seznamem záměrné vpravo od textu vzor a vyberte různých záměrů.

![Snímek obrazovky přeřazení jednotlivých vzor různých záměrů](./media/luis-how-to-model-intent-pattern/reassign-individual-pattern.png)

## <a name="reassign-several-patterns-to-different-intent"></a>Přiřadit několik vzorů pro různých záměrů

K přiřazení několik vzorů pro různých záměrů, zaškrtněte políčko nalevo od každého vzor nebo nejvyšší zaškrtávací políčko. **Přiřazení záměr** možnost se zobrazí na panelu nástrojů. Vyberte správný záměr pro vzory. 

![Snímek obrazovky přeřazení několik vzorů pro různých záměrů](./media/luis-how-to-model-intent-pattern/reassign-many-patterns.png)

## <a name="delete-a-single-pattern"></a>Odstranit single vzor

1. Pokud chcete odstranit vzor, vyberte ikonu tři tečky (...) na pravém konci řádku pro tento vzor, pak vyberte **odstranit**. 

    ![Snímek obrazovky odstranit utterance](./media/luis-how-to-model-intent-pattern/patterns-three-dots-ddl.png)

2. Vyberte **Ok** potvrďte odstranění.

    ![Snímek obrazovky odstranit potvrzení](./media/luis-how-to-model-intent-pattern/confirm-delete.png)

## <a name="delete-several-patterns"></a>Odstranit několik vzorů

1. Pokud chcete odstranit několik vzory, zaškrtněte políčko nalevo od každého vzor nebo nejvyšší zaškrtávací políčko. **Odstranit vzory (s)** možnost se zobrazí na panelu nástrojů. Vyberte **odstranit vzory (s)**.  

    ![Snímek obrazovky odstraňování několik vzorů](./media/luis-how-to-model-intent-pattern/delete-many-patterns.png)

2. **Odstranit vzory** se zobrazí dialogové okno potvrzení. Vyberte **Ok** dokončení odstranění.

    ![Snímek obrazovky odstraňování několik vzorů](./media/luis-how-to-model-intent-pattern/delete-many-patterns-confirmation.png)

## <a name="filter-pattern-list-by-entity"></a>Vzor seznam můžete filtrovat podle entity

Chcete-li filtrovat seznam vzorů podle konkrétní entitu, vyberte **Entity filtry** v panelu nástrojů nad vzory. 

![Snímek obrazovky filtrování vzory entita](./media/luis-how-to-model-intent-pattern/filter-entities-1.png)

Až se filtr použije, zobrazí se název entity níže panelu nástrojů. 

## <a name="filter-pattern-list-by-intent"></a>Vzor seznam můžete filtrovat podle záměru

Chcete-li filtrovat seznam vzorů podle konkrétní záměr, vyberte **záměr filtry** v panelu nástrojů nad vzory. 

![Snímek obrazovky filtrování vzory podle záměru](./media/luis-how-to-model-intent-pattern/filter-intents-1.png)

Až se filtr použije, se zobrazí název záměrné pod panelu nástrojů. 

## <a name="remove-entity-or-intent-filter"></a>Odeberte entity nebo záměrné filtru
Pokud vzor seznam je filtrovaný, entity nebo záměrné název se zobrazí pod panelu nástrojů. Chcete-li odebrat filtr, vyberte název.

![Snímek obrazovky filtrované vzory entita](./media/luis-how-to-model-intent-pattern/filter-entities-2.png)

Bude filtr odebrán a zobrazit všechny vzorky. 

## <a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>Přidat vzor z existující utterance na stránce záměr nebo entity
Vzor můžete vytvořit z existující utterance na buď **záměr** nebo **Entity** stránky. Všechny utterances na libovolné stránce záměr nebo entity se zobrazí v seznamu s pravém sloupci poskytování přístupu k úrovni utterance možnosti, jako **upravit**, **odstranit**, a **přidat jako vzor**.

1. Pro vybraný řádek utterance, vyberte se třemi tečkami (...) napravo od utterance a vyberte **přidat jako vzor**.

    [![](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png "Snímek obrazovky utterances tabulku s přidat vzor zvýrazněných v nabídce Možnosti")](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png)

2. Upravit podle vzoru [syntaxe pravidla](luis-concept-patterns.md#pattern-syntax). Pokud utterance, které jste vybrali označený verzí entity, jsou tyto entity již ve vzorku se správnou syntaxi.

    ![Snímek obrazovky filtrované vzory entita](./media/luis-how-to-model-intent-pattern/confirm-patterns-modal.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Cvičení aplikace po změně modelu pomocí vzorů
Po přidat, upravit, odebrat nebo změnit přiřazení vzor, [cvičení](luis-how-to-train.md) a [publikování](PublishApp.md) aplikace se změny ovlivňují dotazy koncový bod. 

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [sestavení vzor](luis-tutorial-pattern.md) s pattern.any a rolí.
* Zjistěte, jak [cvičení](luis-how-to-train.md) vaší aplikace.