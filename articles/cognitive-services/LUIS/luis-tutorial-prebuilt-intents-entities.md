---
title: Přidat předem záměry a entity extrahovat běžné data v znalosti jazyka - Azure | Microsoft Docs
description: Další informace o použití předem záměry a entity k extrakci různé typy dat entity.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 06/11/2018
ms.author: v-geberr
ms.openlocfilehash: 20950ced66497fb0dc96365975b37f244f677ce3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266375"
---
# <a name="use-prebuilt-intents-and-entities-to-handle-common-intents-and-data"></a>Použít předem záměry a entity pro zpracování běžné tříd Intent a dat
Přidejte předem záměry a entity rychle získat záměrné extrakce předpovědi a data v aplikaci rychlý start lidských zdrojů. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
* Přidat předem záměry 
* Přidat datetimeV2 předem entity a číslo
* Trénování a publikování
* Dotazování LEOŠ a obdrží odpověď předpovědi

## <a name="before-you-begin"></a>Než začnete
Pokud nemáte aplikaci lidských zdrojů [vlastní domény](luis-quickstart-intents-only.md) rychlý start, [importovat](create-new-app.md#import-new-app) JSON do nové aplikace ve [LEOŠ] [ LUIS] webu , z [LEOŠ-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json) úložiště Github.

Pokud chcete zachovat původní aplikace lidských zdrojů, klonování verze na [nastavení](luis-how-to-manage-versions.md#clone-a-version) stránky a pojmenujte ji `prebuilts`. Klonování je skvělým způsobem, jak přehrání s různými funkcemi LEOŠ bez vlivu na původní verzi. 

## <a name="add-prebuilt-intents"></a>Přidat předem záměry
LEOŠ poskytuje několik předem záměry usnadní běžné záměry uživatele.  

1. Ujistěte se, že vám aplikace zůstala viset **sestavení** části LEOŠ. Do této části můžete změnit tak, že vyberete **sestavení** v horní části pravým řádku nabídek. 

    [ ![Snímek obrazovky LEOŠ aplikace pomocí hightlighted sestavení v horní, pravé navigační panel](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png)](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png#lightbox)

2. Vyberte **přidat předem domény záměr**. 

    [ ![Snímek obrazovky záměry stránka s přidat předem domény záměrné tlačítkem](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. Vyhledejte `Utilities`. 

    [ ![Snímek obrazovky dialogového okna předem záměry s nástroje do vyhledávacího pole](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Vyberte následující tříd Intent a vyberte **provádí**: 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.Stop
    * Utilities.StartOver

## <a name="add-prebuilt-entities"></a>Přidat předem entity
LEOŠ poskytuje několik předem entity pro běžné extrakce data. 

1. Vyberte **entity** v levé navigační nabídce.

    [ ![Snímek obrazovky záměry seznam s zvýrazněných v levém navigačním entity](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. Vyberte **spravovat předem entity** tlačítko.

    [ ![Snímek obrazovky entit seznam s předem entity zvýrazněná Správa](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. Vyberte **číslo** a **datetimeV2** ze seznamu předem entit zvolte **provádí**.

    ![Snímek obrazovky číslo, vyberte v dialogovém okně předem entity](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>Trénování a publikování aplikace
1. V horní pravé straně LEOŠ webu, vyberte **Train** tlačítko. 

    ![Tlačítko Train](./media/luis-quickstart-intents-only/train-button.png)

    Školení je dokončena, když se zobrazí zelená stavového řádku v horní části webu potvrzení úspěšné.

    ![Vyškolení stavového řádku](./media/luis-quickstart-intents-only/trained.png)

2. V horní, pravé straně LEOŠ webu, vyberte **publikovat** tlačítko Otevřít stránku publikovat. Ve výchozím nastavení je vybraná produkční slot. Vyberte **publikovat** tlačítko volbou produkční slot. Publikování bylo dokončeno, když se zobrazí zelená stavového řádku v horní části webu potvrzení úspěšné.

    Nemáte k vytvoření LEOŠ klíče na portálu Azure, než můžete publikovat nebo před testovacího adresu URL koncového bodu. Každé LEOŠ aplikace má klíč volné starter pro vytváření obsahu. Nabízí neomezená vytváření obsahu a [několik přístupů koncový bod](luis-boundaries.md#key-limits). 

## <a name="query-endpoint-with-an-utterance"></a>Koncový bod dotazu s utterance
Na **publikovat** vyberte **koncový bod** odkaz ve spodní části stránky. Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v panelu Adresa. Přejděte na konec adresy URL v adrese a zadejte `I want to cancel on March 3`. Poslední parametr řetězce dotazu je `q`, utterance **dotazu**. 

Výsledek předpovědět záměr Utilities.Cancel a extrahovat data z března 3 a čísla 3. 

    ```
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

Klientská aplikace snadno a rychle přidáte předem záměry a entity, můžete přidat konverzace správy a extrahovat běžné datové typy. 

## <a name="next-steps"></a>Další postup

[Další informace o entitách](luis-concept-entity-types.md). 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
