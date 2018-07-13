---
title: Přidání předem připravených záměrů a entit pro extrakci běžných dat ve službě Language Understanding – Azure | Microsoft Docs
description: Zjistěte, jak pomocí předem připravených záměrů a entit extrahovat různé typy dat entit.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: v-geberr
ms.openlocfilehash: 075cb270641ca995eec95aa6aa8986c90077148a
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112183"
---
# <a name="tutorial-2-add-prebuilt-intents-and-entities"></a>Kurz: 2. Přidání předem připravených záměrů a entit
Přidejte do ukázkové aplikace pro lidské zdroje předem připravené záměry a entity a získejte tak rychle předpověď záměru a extrakci dat. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
* Přidání předem připravených záměrů 
* Přidání předem připravených entit datetimeV2 a čísla
* Trénování a publikování
* Dotazování služby LUIS a přijetí odpovědi s předpovědí

## <a name="before-you-begin"></a>Než začnete
Pokud nemáte aplikaci pro [lidské zdroje](luis-quickstart-intents-only.md) z předchozího kurzu, [naimportujte](create-new-app.md#import-new-app) na webu [LUIS](luis-reference-regions.md#luis-website) do nové aplikace JSON z úložiště [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json) na GitHubu.

Pokud chcete zachovat původní aplikaci pro lidské zdroje, naklonujte verzi na stránce [Settings](luis-how-to-manage-versions.md#clone-a-version) (Nastavení) a pojmenujte ji `prebuilts`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. 

## <a name="add-prebuilt-intents"></a>Přidání předem připravených záměrů
Služba LUIS poskytuje několik předem připravených záměrů, které vám pomůžou s běžnými záměry uživatelů.  

1. Ujistěte se, že je vaše aplikace uvedená v části **Build** (Sestavení) služby LUIS. Do této části můžete přejít výběrem možnosti **Build** (Sestavit) v pravém horním řádku nabídek. 

    [ ![Snímek obrazovky aplikace LUIS se zvýrazněnou možností Build (Sestavit) na pravém horním navigačním panelu](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png)](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png#lightbox)

2. Vyberte **Add prebuilt domain intent** (Přidat předem připravený záměr domény). 

    [ ![Snímek obrazovky se stránkou Intents (Záměry) a zvýrazněným tlačítkem Add prebuilt domain intent (Přidat předem připravený záměr domény)](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. Vyhledejte `Utilities`. 

    [ ![Snímek obrazovky s dialogovým oknem předem připravených záměrů s textem Utilities (Nástroje) ve vyhledávacím poli](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Vyberte následující záměry a pak vyberte **Done** (Hotovo): 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop


## <a name="add-prebuilt-entities"></a>Přidání předem připravených entit
Služba LUIS poskytuje několik předem připravených entit pro extrakci běžných dat. 

1. V levé navigační nabídce vyberte **Entities** (Entity).

    [ ![Snímek obrazovky se seznamem záměrů se zvýrazněnou možností Entities (Entity) na levém navigačním panelu](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. Vyberte tlačítko **Manage prebuilt entities** (Spravovat předem připravené entity).

    [ ![Snímek obrazovky se seznamem entit se zvýrazněným tlačítkem Manage prebuilt entities (Spravovat předem připravené entity)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. V seznamu předem připravených entit vyberte **number** (číslo) a **datetimeV2** a pak vyberte **Done** (Hotovo).

    ![Snímek obrazovky s vybranou možností number (číslo) v dialogovém okně s předem připravenými entitami](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>Trénování a publikování aplikace
1. V pravé horní části webu LUIS vyberte tlačítko **Train** (Trénovat). 

    ![Tlačítko Train (Trénovat)](./media/luis-quickstart-intents-only/train-button.png)

    Trénování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

    ![Stavový řádek trénování](./media/luis-quickstart-intents-only/trained.png)

2. V pravé horní části webu LUIS vyberte tlačítko **Publish** (Publikovat). Otevře se stránka Publish (Publikovat). 

3. Produkční slot je vybraný ve výchozím nastavení. Vedle volby produkčního slotu vyberte tlačítko **Publish** (Publikovat). Publikování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

    Před publikováním ani před testováním adresy URL koncového bodu není nutné na webu Azure Portal vytvářet klíč koncového bodu služby LUIS. Každá aplikace LUIS má bezplatný počáteční klíč pro vytváření obsahu. Nabízí neomezené vytváření obsahu a [několik požadavků přicházejících do koncového bodu](luis-boundaries.md#key-limits). 

## <a name="query-endpoint-with-an-utterance"></a>Odeslání dotazu na koncový bod s promluvou
V dolní části stránky **Publish** (Publikovat) vyberte odkaz na **koncový bod**. Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v adresním řádku. Na konec adresy URL zadejte `I want to cancel on March 3`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. 

Výsledek předpověděl záměr Utilities.Cancel a extrahoval datum March 3 (3. března) a číslo 3. 

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

Pro entitu March 3 (3. března) existují dvě hodnoty, protože promluva neuvádí, jestli se jedná o minulé nebo budoucí datum. Je na aplikaci volající službu LUIS, aby vytvořila předpoklad nebo v případě potřeby požádala o další informace. 

Díky rychlému a snadnému přidávání předem připravených záměrů a entit můžete do klientské aplikace přidat správu konverzací a extrahovat v ní běžné datové typy. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. Provedete to tak, že v nabídce vlevo nahoře vyberte **My apps** (Moje aplikace). Vyberte nabídku se třemi tečkami (...) vpravo od názvu aplikace v seznamu aplikací a vyberte **Delete** (Odstranit). V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání entity regulárního výrazu do aplikace](luis-quickstart-intents-regex-entity.md)

