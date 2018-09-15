---
title: Přidání záměrů aplikace LUIS
titleSuffix: Azure Cognitive Services
description: Přidání záměrů do vaší aplikace LUIS k identifikaci skupin dotazy nebo příkazy, které mají stejné záměry.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 687cbad9d1e493e07fc5f813fdd7c52dee3b97d4
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45634621"
---
# <a name="manage-intents"></a>Správa záměrů 
Přidat [záměry](luis-concept-intent.md) do vaší aplikace LUIS k identifikaci skupin dotazy nebo příkazy, které mají stejné záměry. 

Záměry spravují **sestavení** části v horním panelu nástrojů. Přidávat a spravovat vaše záměry z **záměry** stránky k dispozici na levém panelu. 

Následující postup ukazuje, jak přidat "Bookflight" záměr v aplikaci TravelAgent.

## <a name="add-intent"></a>Přidat záměr

1. Otevřete aplikaci (například TravelAgent) kliknutím na název na **Moje aplikace** stránce a potom klikněte na tlačítko **záměry** na levém panelu. 
2. Na **záměry** klikněte na **vytvořit nové záměr**.

3. V **vytvořit nové záměr** dialogové okno, zadejte název "BookFlight" záměr a klikněte na **provádí**.

    ![Přidat záměr](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Na stránce s podrobnostmi záměru nově přidané záměru [přidání projevů](#add-an-utterance-on-intent-page).

## <a name="rename-intent"></a>Přejmenovat záměr

1. Na **záměr** klikněte na ikonu přejmenovat ![přejmenovat záměr](./media/luis-how-to-add-intents/Rename-Intent-btn.png) vedle názvu záměru. 

2. Na **záměr** stránky, záměru aktuální název se zobrazí v dialogovém okně. Upravit záměru název a stiskněte klávesu enter. Nový název uložit a zobrazit na stránce záměru.

    ![Upravit záměr](./media/luis-how-to-add-intents/EditIntent-dialogbox.png)

## <a name="delete-intent"></a>Odstranit záměr
Při odstraňování záměru než záměru None, můžete přidat všechny projevy na hodnotu None záměru. To je užitečné, pokud potřebujete přesunout projevy místo jejich odstranění.   

1. Na **záměr** stránky, klikněte na tlačítko **odstranit záměr** tlačítko vedle vpravo od názvu záměru. 

    ![Záměru tlačítko Odstranit](./media/luis-how-to-add-intents/DeleteIntent.png)

2. Klikněte na tlačítko "Ok" v dialogovém okně potvrzení.

<!--
    TBD: waiting for confirmation about which delete dialog is going to be in //BUILD

    ![Delete Intent Dialog](./media/luis-how-to-add-intents/DeleteIntent-Confirmation.png)
-->


## <a name="add-an-utterance-on-intent-page"></a>Přidat utterance na stránce záměru

Na stránce záměru zadejte relevantní utterance očekáváte, že od uživatelů, jako například `book 2 adult business tickets to Paris tomorrow on Air France` do textového pole pod název záměru a potom stiskněte klávesu Enter. 
 
>[!NOTE]
>Služba LUIS převede všechny projevy na malá písmena.

![Stránce s podrobnostmi o snímek obrazovky záměrů, se zvýrazněným utterance](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

Projevy se přidají do seznamu projevy pro aktuální záměr. Po přidání utterance [popisek žádné entity](luis-how-to-add-example-utterances.md) v rámci projevy a [trénování](luis-how-to-train.md) vaší aplikace. 

## <a name="create-a-pattern-from-an-utterance"></a>Ze utterance společně tvoří masku
Zobrazit [přidat vzorek z existující utterance na stránce záměr nebo entity](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="edit-an-utterance-on-intent-page"></a>Upravit utterance na stránce záměru

Upravit utterance, vyberte tři tečky (***...*** ) na pravém konci řádku pro tento utterance tlačítko a pak vyberte **upravit**. Upravit text a stiskněte klávesu Enter na klávesnici.

![Stránce s podrobnostmi o snímek obrazovky záměrů, se zvýrazněným tlačítkem tlačítko se třemi tečkami](./media/luis-how-to-add-intents/edit-utterance.png) 

## <a name="reassign-utterances-on-intent-page"></a>Změnit přiřazení projevy na stránce záměru
Záměr projevů jeden nebo více můžete změnit změnou přiřazení na jiné záměr. 

K opětovnému přiřazení jednoho utterance na různých záměr, na pravém konci řádku utterance správné záměru seznamu vyberte název **označené záměr** sloupce. Utterance je odebrat ze seznamu utterance aktuální záměr. 

![Snímek obrazovky BookFlight záměru stránka s záměr utterance pod Labeled záměru vybraný sloupec](./media/luis-how-to-add-intents/reassign-1-utterance.png)

Chcete-li změnit záměr projevů několik, zaškrtněte políčka nalevo od projevy a pak vyberte **záměr přiřazení**. Ze seznamu vyberte správné záměr.

![Snímek obrazovky BookFlight záměru stránku utterance zaškrtnuto a zvýrazněným tlačítkem záměru opětovného přiřazení](./media/luis-how-to-add-intents/delete-several-utterances.png) 

## <a name="delete-utterances-on-intent-page"></a>Odstranit projevy na stránce záměru

Pokud chcete odstranit utterance, vyberte tři tečky (***...*** ) na pravém konci řádku pro tento utterance tlačítko a pak vyberte **odstranit**. Utterance se odebere ze seznamu a aplikaci LUIS.

![Stránce s podrobnostmi o snímek obrazovky záměrů, se zvýrazněnou možností odstranit](./media/luis-how-to-add-intents/delete-utterance-ddl.png)

Pokud chcete odstranit několik projevy:

1. Zaškrtněte políčka nalevo od projevy a pak vyberte **odstranit projevy (s)**. 

    ![Stránce s podrobnostmi o snímek obrazovky záměrů, s projevy zkontrolovat a odstranit zvýrazněným tlačítkem utterance(s)](./media/luis-how-to-add-intents/delete-several-utterances.png)

2. Vyberte **provádí** v **odstranit projevy?** automaticky otevíraná okna.

## <a name="search-in-utterances-on-intent-page"></a>Hledat v projevy na stránce záměru
Projevy, které obsahují text (slova nebo fráze) můžete vyhledat záměr utterance seznamu certifikačních autorit. Například můžete si všimnout chybu, která zahrnuje určité slovo a chcete najít všechny příklady, které zahrnují toto konkrétní slovo. 

1. V panelu nástrojů vyberte ikonu lupy.

    ![Snímek obrazovky záměry stránku pro hledání ikonu lupy zvýrazněnou](./media/luis-how-to-add-intents/magnifying-glass.png)

2. Zobrazí se textové pole pro hledání. Zadejte slovo nebo frázi v pole Hledat v pravém horním rohu projevy seznamu. Projevy seznam aktualizací, chcete-li zobrazit pouze projevy, které obsahují hledaný text. 

    ![Snímek obrazovky záměry stránky se zvýrazněnou textové pole pro hledání](./media/luis-how-to-add-intents/search-textbox.png)

    Chcete-li zrušit hledání a obnovit váš seznam všech projevy, odstraňte hledaný text, který jste zadali. Zavřete textové pole pro hledání, vyberte ikonu lupy znovu na panelu nástrojů.

## <a name="prediction-discrepancy-errors-on-intent-page"></a>Predikce nesrovnalosti chyby na stránce záměru
Utterance v záměru pravděpodobně relevanci vybrané záměr a skóre předpovědi. Služba LUIS označuje této nesrovnalosti s červeným rámečkem okolo položky skóre. 

![Stránka záměr BookFlight snímek obrazovky se zvýrazněnou skóre nesrovnalosti predikcí](./media/luis-how-to-add-intents/score-discrepancy.png) 

## <a name="filter-by-intent-prediction-discrepancy-errors-on-intent-page"></a>Filtrovat podle záměru předpovědi nesrovnalosti chyby na stránce záměru
K filtrování seznamu utterance pouze projevy s záměru předpovědi nesrovnalosti, přepněte z **Zobrazit vše** k **pouze chyby** na panelu nástrojů. 

## <a name="filter-by-entity-type-on-intent-page"></a>Filtrovat podle typu entity na stránce záměru
Použití **Entity filtry** rozevíracího seznamu na panelu nástrojů můžete filtrovat projevy podle entity. 

![Snímek obrazovky záměry stránky se zvýrazněnou filtr typu entity](./media/luis-how-to-add-intents/filter-by-entities.png) 

Odebrat filtr, vyberte pole modré filtr s tímto slova nebo fráze ve skupinovém rámečku panelu nástrojů.  
<!-- TBD: waiting for ux fix - bug in ux of prebuit entity number -- when filtering by it, it doesn't show the list -->

## <a name="switch-to-token-view-on-intent-page"></a>Přepnout na token zobrazení na stránce záměru
Přepnout **tokeny zobrazení** zobrazíte tokeny namísto názvů typu entity. Na klávesnici, můžete také použít **ovládací prvek + E** přepnete zobrazení. 

![Záměr BookFlight snímek obrazovky se zvýrazněnou Token zobrazení](./media/luis-how-to-add-intents/toggle-tokens-view.png)

## <a name="train-your-app-after-changing-model-with-intents"></a>Po změně modelu pomocí tříd Intent trénování vaší aplikace
Po přidání, úprava nebo odebrání záměrů, [trénování](luis-how-to-train.md) a [publikovat](luis-how-to-publish-app.md) vaší aplikace pro vaše změny ovlivnit dotazy koncový bod. 

## <a name="next-steps"></a>Další postup

Po přidání záměrů do vaší aplikace, dalším krokem je začít přidávat [příklad projevy](luis-how-to-add-example-utterances.md) pro záměrů, které jste přidali. 
