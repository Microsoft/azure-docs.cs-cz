---
title: Analýza mínění
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvořte aplikaci, která ukazuje, jak získat z projevy kladná, záporná a neutrální mínění. Mínění se určuje z celé promluvy.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: ee50907d7965a66d09dc57113e87edecb1932083
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754284"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>Kurz:  Získat mínění utterance

V tomto kurzu vytvořte aplikaci, která ukazuje, jak určit kladná, záporná a neutrální mínění v projevy. Mínění se určuje z celé promluvy.

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Vytvoření nové aplikace
> * Přidání analýzy mínění jako nastavení publikování
> * Vytrénovat aplikaci
> * Publikování aplikace
> * Získání mínění promluvy z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Analýza subjektivního hodnocení se nastavení publikování

Následující promluvy ukazují příklady mínění:

|Mínění|Skóre|Promluva|
|:--|:--|:--|
|pozitivní|0,91 |John W. Smith did a great job on the presentation in Paris (John W. Smith udělal skvělou práci na prezentaci v Paříži).|
|pozitivní|0,84 |Technici Seattle nebyla slavných práci na prodejní prvotního Parker.|

Analýza mínění je nastavení publikování, které se vztahuje na všechny promluvy. Není nutné slova označující mínění v utterance a označte je. 

Jedná se o nastavení publikování, takže se nezobrazuje na stránkách záměrů a entit. Zobrazuje se v podokně [interaktivního testu](luis-interactive-test.md#view-sentiment-results) nebo při testování na adrese URL koncového bodu. 


## <a name="create-a-new-app"></a>Vytvoření nové aplikace

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>Přidat PersonName předem připravených entit 


1. V levé navigační nabídce vyberte **Entities** (Entity).

1. Vyberte tlačítko **Add prebuilt entity** (Přidat předem připravenou entitu).

1. Vyberte ze seznamu předem připravených entit následující entity a pak vyberte **provádí**:

    * **[PersonName](luis-reference-prebuilt-person.md)** 

    ![Snímek obrazovky s vybranou možností number (číslo) v dialogovém okně s předem připravenými entitami](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>Vytvoření záměru určit zaměstnance zpětné vazby

Přidejte nový záměr, který bude zachycovat zpětnou vazbu o zaměstnancích od členů společnosti. 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Vyberte **Create new intent** (Vytvořit nový záměr).

3. Nový záměr pojmenujte `EmployeeFeedback`.

    ![Dialogové okno Create new intent (Vytvořit nový záměr) s názvem EmployeeFeedback (Zpětná vazba o zaměstnancích)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Přidejte několik promluv, které budou indikovat, že zaměstnanec udělal něco dobře, nebo nějakou oblast, ve které se potřebuje zlepšit:

    |Projevy|
    |--|
    |Jan Macek členkou nebyla odvádí dobrou práci při uvítacímu zpět z mateřská|
    |Skvělá práce z comforting kolegou v ušetřil čas grief se nespustil Jill Jones.|
    |Bob Barnes nebyly k dispozici všechny požadované faktury doklady.|
    |Tomáš TODD zapnuté v požadované formuláře měsíc pozdní bez podpisu|
    |Katherine Kelly nevytvořili důležité marketingové schůzku mimo pracoviště.|
    |Jan Dillard nenalezlo schůzky u revizí dne.|
    |Označit Mathews rocked prodejní prvotního na Harvard|
    |Walter Williams nebyla Skvělá práce na prezentaci v informačních technologií|

    [ ![Snímek obrazovky aplikace LUIS s příklady promluv v záměru EmployeeFeedback (Zpětná vazba o zaměstnancích)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>Přidání projevů příklad na hodnotu None záměru 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trénování aplikace, takže můžete otestovat změny k příslušnému záměru 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurace aplikace pro zahrnutí analýzy mínění

1. Na pravém horním navigačním panelu vyberte **Manage** (Správa) a pak v levé nabídce vyberte **Publish settings** (Nastavení publikování).

1. Vyberte **analýzu subjektivního hodnocení** k povolení tohoto nastavení. 

    ![Zapnout analýzu subjektivního hodnocení jako nastavení publikování](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikování aplikace, tedy dotazovatelné z koncového bodu trénovaného modelu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Získat mínění utterance z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Na konec adresy URL zadejte `Jill Jones work with the media team on the public portal was amazing`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `EmployeeFeedback` s extrahovanou analýzou mínění.
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.9616192
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.9616192
        },
        {
          "intent": "None",
          "score": 0.09347677
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 0,
          "endIndex": 9
        }
      ],
      "sentimentAnalysis": {
        "label": "positive",
        "score": 0.8694164
      }
    }
    ```

    SentimentAnalysis je kladný se skóre 86 %. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Související informace

* Analýza subjektivního hodnocení poskytuje služby Cognitive Services [rozhraní Text Analytics](../Text-Analytics/index.yml). Tato funkce je omezen na rozhraní Text Analytics [podporované jazyky](luis-language-support.md##languages-supported).
* [Trénování](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování v portálu služby LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Další postup
V tomto kurzu se přidá analýza mínění jako nastavení publikování tak, aby se extrahovaly hodnoty mínění z promluvy jako celku.

> [!div class="nextstepaction"] 
> [Kontrola promluv koncového bodu v aplikaci HR](luis-tutorial-review-endpoint-utterances.md) 

