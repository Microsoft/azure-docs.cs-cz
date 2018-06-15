---
title: Přidat záměry v aplikacích LEOŠ | Microsoft Docs
description: Principy jazyka (LEOŠ) slouží k přidání záměry pomohou aplikace pochopení požadavků uživatele a reagovat na ně správně.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.service: cognitive-services
ms.openlocfilehash: 6e013e994a3bcb60c3104aa10cd7bad1535706f1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343825"
---
# <a name="manage-intents"></a>Spravovat záměry 
Přidat [záměry](luis-concept-intent.md) do vaší aplikace LEOŠ k identifikaci skupin dotazy nebo příkazy, které mají stejné záměry. 

Můžete přidat a spravovat vaše záměry z **záměry** stránka, k dispozici z **záměry** v LEOŠ na levém panelu. 

Následující postup ukazuje, jak přidat záměr "Bookflight" v aplikaci TravelAgent.

## <a name="add-intent"></a>Přidat záměru

1. Otevřete aplikaci (například TravelAgent) klepnutím na její název v **Moje aplikace** a pak klikněte na tlačítko **záměry** na levém panelu. 
2. Na **záměry** klikněte na tlačítko **vytvořit nové záměr**.

    ![Seznam tříd Intent](./media/luis-how-to-add-intents/IntentsList.png)
3. V **vytvořit nové záměr** dialogové okno, typ záměr název "BookFlight" a klikněte na tlačítko **provádí**.

    ![Přidat záměru](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Na stránce podrobností záměrné nově přidané záměru [přidat utterances](#add-an-utterance-on-intent-page).

## <a name="rename-intent"></a>Přejmenujte záměru

1. Na **záměr** klikněte na ikonu přejmenování ![přejmenovat záměr](./media/luis-how-to-add-intents/Rename-Intent-btn.png) vedle názvu záměrné. 

2. Na **záměr** stránky, v dialogovém okně se zobrazí aktuální název záměrné. Upravit název záměrné a stiskněte klávesu enter. Nový název uložit a zobrazit na stránce záměrné.

    ![Upravit záměru](./media/luis-how-to-add-intents/EditIntent-dialogbox.png)

## <a name="delete-intent"></a>Odstranit záměru
Při odstraňování záměrem než záměrné žádné, můžete přidat všechny utterances záměrné none. To je užitečné, pokud potřebujete přesunout utterances místo odstraněním.   

1. Na **záměr** klikněte na tlačítko **odstranit záměr** tlačítko vedle vpravo od názvu záměrné. 

    ![Odstranit záměrné tlačítko](./media/luis-how-to-add-intents/DeleteIntent.png)

2. Klikněte na tlačítko "Ok" v potvrzovacím dialogu.

<!--
    TBD: waiting for confirmation about which delete dialog is going to be in //BUILD

    ![Delete Intent Dialog](./media/luis-how-to-add-intents/DeleteIntent-Confirmation.png)
-->


## <a name="add-an-utterance-on-intent-page"></a>Přidat utterance na záměrné stránky

Na stránce záměrné zadejte příslušné utterance očekávat od uživatelů, jako například `book 2 adult business tickets to Paris tomorrow on Air France` do textového pole pod záměrné název a potom stiskněte klávesu Enter. 
 
>[!NOTE]
>LEOŠ převede všechny utterances na malá písmena.

![Stránka Podrobnosti snímek záměry, s utterance zvýrazněná](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

Utterances se přidají do seznamu utterances pro aktuální záměr. Po přidání utterance [popisku všechny entity](luis-how-to-add-example-utterances.md) v rámci utterances a [cvičení](luis-how-to-train.md) vaší aplikace. 

## <a name="create-a-pattern-from-an-utterance"></a>Vytvořit vzor z utterance
V tématu [přidat vzor z existující utterance na stránce záměr nebo entity](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="edit-an-utterance-on-intent-page"></a>Upravit utterance na záměrné stránce

Chcete-li upravit utterance, vyberte ikonu tři tečky (...) na pravém konci řádku pro tento utterance a pak vyberte **upravit**. Upravit text stiskněte klávesu Enter na klávesnici.

![Stránka Podrobnosti snímek záměry, se zvýrazněnou ikonou tři tečky](./media/luis-how-to-add-intents/edit-utterance.png) 

## <a name="reassign-utterances-on-intent-page"></a>Přiřazení utterances na záměrné stránce
Záměr jeden nebo více utterances můžete změnit změnou jejich přiřazení k jiné záměr. 

K opětovnému přiřazení jednoho utterance do různých záměrů, na pravém konci utterance na řádek, správné záměrné seznamu vyberte název **s názvem bez přípony záměr** sloupce. Utterance se odebral ze seznamu utterance aktuální záměr. 

![Snímek obrazovky BookFlight záměrné stránky se záměrem utterance pod vybraný sloupec záměrné Labeled](./media/luis-how-to-add-intents/reassign-1-utterance.png)

Chcete-li změnit záměr několik utterances, vyberte zaškrtávací políčka nalevo od utterances a pak vyberte **přiřazení záměr**. Vyberte správný záměr ze seznamu.

![Snímek obrazovky BookFlight záměrné stránka s utterance zaškrtnutí a tlačítko znovu přiřadit záměrné zvýrazněná](./media/luis-how-to-add-intents/delete-several-utterances.png) 

## <a name="delete-utterances-on-intent-page"></a>Odstranit utterances na záměrné stránce

Chcete-li odstranit utterance, vyberte ikonu tři tečky (...) na pravém konci řádku pro tento utterance a pak vyberte **odstranit**. Utterance se odebere ze seznamu a LEOŠ aplikace.

![Stránka Podrobnosti snímek záměry, se zvýrazněnou možností odstranit](./media/luis-how-to-add-intents/delete-utterance-ddl.png)

Chcete-li odstranit několik utterances:

1. Vyberte zaškrtávací políčka nalevo od utterances a pak vyberte **odstranit utterances (s)**. 

    ![Stránka Podrobnosti snímek záměry, s utterances zaškrtnutí a odstranění utterance(s) tlačítkem](./media/luis-how-to-add-intents/delete-several-utterances.png)

2. Vyberte **provádí** v **odstranit utterances?** automaticky otevíraná okna.

## <a name="search-in-utterances-on-intent-page"></a>Hledání v utterances na záměrné stránce
V záměrem můžete vyhledat utterances, které obsahují text (slova nebo fráze). Například může dojít k chybě, která zahrnuje určité slovo, a chcete najít všechny příklady, které zahrnují této konkrétní aplikace word. 

1. Vyberte ikonu lupy na panelu nástrojů.

    ![Snímek obrazovky záměry stránka s vyhledávání ikonu lupy zvýrazněná](./media/luis-how-to-add-intents/magnifying-glass.png)

2. Do textového pole hledání se zobrazí. Do pole Hledat v pravém horním rohu seznamu utterances zadejte slovo nebo frázi. Utterances seznam aktualizací, chcete-li zobrazit pouze utterances, které obsahují hledaný text. 

    ![Snímek obrazovky záměry stránka se zvýrazněnou pole hledání text](./media/luis-how-to-add-intents/search-textbox.png)

    Chcete-li zrušit hledání a obnovit vaše úplný seznam utterances, odstraňte hledaný text, který jste zadali. Zavřete textového pole hledání, vyberte ikonu lupy znovu na panelu nástrojů.

## <a name="prediction-discrepancy-errors-on-intent-page"></a>Chyby předpovědi nesoulad mezi databází na záměrné stránky
Utterance v záměrem může být v rozporu vybrané záměr a skóre předpovědi. LEOŠ označuje Tato nesrovnalost s červeným rámečkem kolem skóre. 

![Stránka záměr BookFlight – snímek obrazovky s skóre nesoulad mezi databází předpovědi zvýrazněná](./media/luis-how-to-add-intents/score-discrepancy.png) 

## <a name="filter-by-intent-prediction-discrepancy-errors-on-intent-page"></a>Filtrovat podle záměrné předpovědi nesoulad mezi databází chyby na stránce záměrné
Pro filtrování seznamu utterance pouze utterances s nesoulad mezi databází záměrné předpovědi, přepněte z **Zobrazit vše** k **pouze chyby** na panelu nástrojů. 

## <a name="filter-by-entity-type-on-intent-page"></a>Filtrovat podle typu entity na záměrné stránce
Použití **Entity filtry** rozevíracího seznamu na panelu nástrojů utterances filtrovat podle entity. 

![Snímek obrazovky záměry stránka se zvýrazněnou filtr typu entity](./media/luis-how-to-add-intents/filter-by-entities.png) 

Odebrat filtr, vyberte pole blue filtr s tímto slovo nebo frázi v panelu nástrojů.  
<!-- TBD: waiting for ux fix - bug in ux of prebuit entity number -- when filtering by it, it doesn't show the list -->

## <a name="switch-to-token-view-on-intent-page"></a>Umožňuje přepnout do zobrazení tokenu na záměrné stránce
Přepnutí **tokeny zobrazení** zobrazíte tokeny namísto názvů typu entity. Na klávesnici, můžete také použít **řízení + E** k přepnutí zobrazení. 

![Snímek obrazovky BookFlight záměr se zvýrazněnou tokenu zobrazení](./media/luis-how-to-add-intents/toggle-tokens-view.png)

## <a name="train-your-app-after-changing-model-with-intents"></a>Po změně modelu pomocí tříd Intent cvičení vaší aplikace
Po přidat, upravit nebo odebrat záměry, [cvičení](luis-how-to-train.md) a [publikování](PublishApp.md) aplikace se změny ovlivňují dotazy koncový bod. 

## <a name="next-steps"></a>Další postup

Po přidání záměry do vaší aplikace, je začít přidávat svůj další úkol [příklad utterances](luis-how-to-add-example-utterances.md) pro záměry jste přidali. 
