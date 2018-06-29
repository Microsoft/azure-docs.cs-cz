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
ms.openlocfilehash: 37d67bef7712012a95543041744706b240b16e2d
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085493"
---
# <a name="tutorial-2-add-prebuilt-intents-and-entities"></a>Kurz: 2. Přidání předem připravených záměrů a entit
Přidejte předem záměry a entity rychle získat záměrné extrakce předpovědi a data v aplikaci rychlý start lidských zdrojů. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
* Přidat předem záměry 
* Přidat datetimeV2 předem entity a číslo
* Trénování a publikování
* Dotazování LEOŠ a obdrží odpověď předpovědi

## <a name="before-you-begin"></a>Než začnete
Pokud nemáte [lidských zdrojů](luis-quickstart-intents-only.md) aplikace z předchozí kurzu [importovat](create-new-app.md#import-new-app) JSON do nové aplikace v [LEOŠ](luis-reference-regions.md#luis-website) webu, z [LEOŠ – ukázky ](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json) Úložiště Github.

Pokud chcete zachovat původní aplikaci pro lidské zdroje, naklonujte verzi na stránce [Settings](luis-how-to-manage-versions.md#clone-a-version) (Nastavení) a pojmenujte ji `prebuilts`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. 

## <a name="add-prebuilt-intents"></a>Přidat předem záměry
LEOŠ poskytuje několik předem záměry usnadní běžné záměry uživatele.  

1. Ujistěte se, že vám aplikace zůstala viset **sestavení** části LEOŠ. Do této části můžete přejít výběrem možnosti **Build** (Sestavit) v pravém horním řádku nabídek. 

    [ ![Snímek obrazovky aplikace LUIS se zvýrazněnou možností Build (Sestavit) na pravém horním navigačním panelu](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png)](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png#lightbox)

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
1. V pravé horní části webu LUIS vyberte tlačítko **Train** (Trénovat). 

    ![Tlačítko Train](./media/luis-quickstart-intents-only/train-button.png)

    Trénování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

    ![Vyškolení stavového řádku](./media/luis-quickstart-intents-only/trained.png)

2. V horní, pravé straně LEOŠ webu, vyberte **publikovat** tlačítko Otevřít stránku publikovat. Ve výchozím nastavení je vybraná produkční slot. Vyberte **publikovat** tlačítko volbou produkční slot. Publikování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

    Nemáte k vytvoření LEOŠ klíče na portálu Azure, než můžete publikovat nebo před testovacího adresu URL koncového bodu. Každé LEOŠ aplikace má klíč volné starter pro vytváření obsahu. Nabízí neomezená vytváření obsahu a [několik přístupů koncový bod](luis-boundaries.md#key-limits). 

## <a name="query-endpoint-with-an-utterance"></a>Koncový bod dotazu s utterance
V dolní části stránky **Publish** (Publikovat) vyberte odkaz na **koncový bod**. Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v adresním řádku. Na konec adresy URL zadejte `I want to cancel on March 3`. Poslední parametr řetězce dotazu je `q`, utterance **dotazu**. 

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání entity regulární výraz k aplikaci](luis-quickstart-intents-regex-entity.md)

