---
title: 'Kurz: Analýza mínění – LUIS'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte aplikaci, která ukazuje, jak získat pozitivní, negativní a neutrální mínění z projevy. Mínění se určuje z celé promluvy.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: ed403e3d761b32c6837eb8e72edef3f3e6380217
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307574"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>Kurz:  Získat mínění z utterance

V tomto kurzu vytvoříte aplikaci, která ukazuje, jak určit kladné, záporné a neutrální mínění z projevy. Mínění se určuje z celé promluvy.

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Vytvoření nové aplikace
> * Přidání analýzy mínění jako nastavení publikování
> * Vytrénovat aplikaci
> * Publikování aplikace
> * Získání mínění promluvy z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Analýza mínění je nastavení publikování

Následující promluvy ukazují příklady mínění:

|Mínění|Skóre|Promluva|
|:--|:--|:--|
|pozitivní|0,91 |John W. Smith did a great job on the presentation in Paris (John W. Smith udělal skvělou práci na prezentaci v Paříži).|
|pozitivní|0,84 |Technici v Seattlu slavná práci na rozteči Parker Sales.|

Analýza mínění je nastavení publikování, které se vztahuje na všechny promluvy. Nemusíte hledat slova označující mínění v utterance a označit je. 

Jedná se o nastavení publikování, takže se nezobrazuje na stránkách záměrů a entit. Zobrazuje se v podokně [interaktivního testu](luis-interactive-test.md#view-sentiment-results) nebo při testování na adrese URL koncového bodu. 


## <a name="create-a-new-app"></a>Vytvoření nové aplikace

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>Přidat předem vytvořenou entitu Person 

1. V navigační nabídce vyberte **sestavení** .

1. V levé navigační nabídce vyberte **Entities** (Entity).

1. Vyberte tlačítko **Add prebuilt entity** (Přidat předem připravenou entitu).

1. V seznamu předem vytvořených entit vyberte následující entitu a potom vyberte **Hotovo**:

   * **[PersonName](luis-reference-prebuilt-person.md)** 

     ![Snímek obrazovky s vybranou možností number (číslo) v dialogovém okně s předem připravenými entitami](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>Vytvoření záměru k určení názoru zaměstnanců

Přidejte nový záměr, který bude zachycovat zpětnou vazbu o zaměstnancích od členů společnosti. 

1. Na levém panelu vyberte **Intents** (Záměry).

1. Vyberte **Create new intent** (Vytvořit nový záměr).

1. Nový záměr pojmenujte `EmployeeFeedback`.

    ![Dialogové okno Create new intent (Vytvořit nový záměr) s názvem EmployeeFeedback (Zpětná vazba o zaměstnancích)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Přidejte několik promluv, které budou indikovat, že zaměstnanec udělal něco dobře, nebo nějakou oblast, ve které se potřebuje zlepšit:

    |Projevy|
    |--|
    |Jan Novák měl dobrý úkol vítání spolupracovníka z mateřského pozůstatku.|
    |Jill Novotný dostal skvělou úlohu, která by v čase Grief pohodlí spolupracovníka.|
    |Bob Barnes nemá všechny požadované faktury pro paperwork.|
    |Todd Tomáš se zapnul do požadovaných forem za měsíc pozdě bez signatur|
    |Katherine Kelly neudělala tuto důležitou marketingovou schůzku mimo pracoviště.|
    |Denise Dillard nenalezl schůzku pro recenze z června.|
    |Označení Mathews Rock Sales rozteč na Harvard|
    |Waltera Williams v prezentaci Skvělé úlohy na Stanfordu|

    Vyberte **Možnosti zobrazení**, vyberte **Zobrazit hodnoty entit** pro zobrazení názvů.

    [![Snímek obrazovky aplikace LUIS s příkladem projevy v EmployeeFeedback záměru](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>Přidat příklad projevy k záměru None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Výuka aplikace, aby se mohly testovat změny záměru 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurace aplikace pro zahrnutí analýzy mínění

1. Na pravém horním navigačním panelu vyberte **Manage** (Správa) a pak v levé nabídce vyberte **Publish settings** (Nastavení publikování).

1. Vyberte možnost **použít analýzu mínění k určení, zda je utterance uživatele kladné, záporné nebo neutrální.** pro povolení tohoto nastavení. 

    ![Zapnout analýzu subjektivního hodnocení jako nastavení publikování](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikujte aplikaci, aby se Queryable z koncového bodu vyškolený model.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Získat mínění utterance z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Přejděte na konec adresy URL v adrese a zadejte následující utterance:

    `Jill Jones work with the media team on the public portal was amazing` 

    Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `EmployeeFeedback` s extrahovanou analýzou mínění.
    
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

    SentimentAnalysis je pozitivní s skóre 86%. 

    Zkuste jiné utterance odebráním hodnoty pro `q` v adresním řádku prohlížeče: `William Jones did a terrible job presenting his ideas.`Skóre mínění označuje negativní mínění vrácením nízkého skóre `0.18597582`.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Související informace

* Mínění je k dispozici pomocí [Analýza textu](../Text-Analytics/index.yml)služby pro rozpoznávání. Tato funkce je omezená, aby Analýza textu [podporované jazyky](luis-language-support.md##languages-supported).
* [Postup výuky](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování na portálu LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Další postup
V tomto kurzu se přidá analýza mínění jako nastavení publikování tak, aby se extrahovaly hodnoty mínění z promluvy jako celku.

> [!div class="nextstepaction"] 
> [Kontrola promluv koncového bodu v aplikaci HR](luis-tutorial-review-endpoint-utterances.md) 

