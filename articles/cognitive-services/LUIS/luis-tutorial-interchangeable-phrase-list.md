---
title: Kurz pomocí seznamu frázi ke zlepšení LEOŠ předpovědi - Azure | Microsoft Docs
description: V tomto kurzu přidejte seznam frázi LEOŠ aplikaci a najdete v části zlepšení skóre.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: feb8acb674fd2dc62b62c26da6a6b42515f30242
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265967"
---
# <a name="tutorial-add-phrase-list-to-improve-predictions"></a>Kurz: Přidejte seznam frází ke zlepšení předpovědi
V tomto kurzu, zvýšit přesnost záměrné skóre a identifikovat entity pro slova, která mají stejný význam (synonyma) tak, že přidáte zaměňovat [funkce seznamu frázi](./luis-concept-feature.md).

> [!div class="checklist"]
* Importovat novou aplikaci  
* Koncový bod dotazu s známé utterance 
* Dotaz na koncový bod s _neznámé_ utterance
* Přidat seznam frází ke zlepšení neznámé utterance skóre
* Ověřte, zda entita je nalezena, při použití seznamu fráze

V tomto článku budete potřebovat bezplatný [LEOŠ] [ LUIS] účet, aby bylo možné vytvořit aplikace LEOŠ.

## <a name="import-a-new-app"></a>Importovat novou aplikaci
1. Stažení [příklad LEOŠ aplikace] [ LuisSampleApp] který je určený pro účely tohoto kurzu. Budete ho používat v dalším kroku. 

2. Jak je popsáno v [vytvořit aplikaci](Create-new-app.md#import-new-app), importovat soubor, který jste stáhli do [LEOŠ] [ LUIS] webu jako novou aplikaci. Název aplikace je "Kurz Moje seznam frází." Má záměry, entit a utterances. 

3. [Train](luis-how-to-train.md) vaší aplikace. Dokud není nastavena, nemůžete [interaktivně testování](interactive-test.md#interactive-testing) ho [LEOŠ] [ LUIS] webu. 

4. Na [publikovat](PublishApp.md) vyberte **zahrnout všechny předpovědět záměrné skóre** zaškrtávací políčko. Pokud je zaškrtnuté políčko, jsou vráceny všechny záměry. Pokud není políčko zaškrtnuto, je vrácena pouze nejvyšší záměr. 

5. [Publikování](PublishApp.md) aplikace. Publikování aplikace, můžete otestovat pomocí koncový bod HTTPS. 

## <a name="test-a-trained-utterance"></a>Testování vyškolení utterance
Dotaz utterance, že aplikace již zná pomocí publikovaných koncový bod. Vzhledem k tomu, že LEOŠ již zná utterance, skóre je vysoká a je zjištěn entity.

1. Na [znalosti jazyka (LEOŠ)] [ LUIS] webu na **publikovat** stránku pro nové aplikace, vyberte adresu URL koncového bodu v **prostředky a klíče**části. 

    ![Publikovat adresu URL koncového bodu](./media/luis-tutorial-interchangeable-phrase-list/luis-publish-url.png)

2. V prohlížeči na konci adresy URL, přidejte následující dotaz po `q=`.

    `I want a computer replacement`

    Koncový bod odpoví následujícím kódu JSON:
    
    ```JSON
    {
      "query": "I want a computer replacement",
      "topScoringIntent": {
        "intent": "GetHardware",
        "score": 0.9735458
      },
      "intents": [
        {
          "intent": "GetHardware",
          "score": 0.9735458
        },
        {
          "intent": "None",
          "score": 0.07053033
        },
        {
          "intent": "Whois",
          "score": 0.03760778
        },
        {
          "intent": "CheckCalendar",
          "score": 0.02285902
        },
        {
          "intent": "CheckInventory",
          "score": 0.0110072717
        }
      ],
      "entities": [
        {
          "entity": "computer",
          "type": "Hardware",
          "startIndex": 9,
          "endIndex": 16,
          "score": 0.8465915
        }
      ]
    }
    ```

    Záměrné skóre 0.973 a skóre detekce entity 0.846 jsou vysoké, protože aplikace byla trénink na tento utterance. Utterance je v aplikaci LEOŠ na stránce záměrné **GetHardware**. Text utterance `computer`, je označeno jako **hardwaru** entity. 
    
    |Status|Word| Záměrné skóre | Skóre entity |
    |--|--|--|--|
    |Cvičení| Chcete | 0.973 | 0.846 |
    
    
## <a name="test-an-untrained-utterance"></a>Testování nevyškoleným utterance
V prohlížeči použijte stejný publikované koncový bod pro dotaz s utterance, který již není známo, aplikace:

`I require a computer replacement`

Tato utterance používá synonymum předchozí utterance:

| Vyškolení aplikace word | Nezkušený synonymum |
|--|--|
| Chcete | Vyžadovat |

Koncový bod odpověď je:

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.840912163
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.840912163
    },
    {
      "intent": "None",
      "score": 0.0972757638
    },
    {
      "intent": "Whois",
      "score": 0.0448251367
    },
    {
      "intent": "CheckCalendar",
      "score": 0.0291390456
    },
    {
      "intent": "CheckInventory",
      "score": 0.0137837809
    }
  ],
  "entities": []
}
```

| Status | Word | Záměrné skóre | Skóre entity |
|--|--|--|--|
| Cvičení| Chcete | 0.973 | 0.846 |
| Nezkušený| Vyžadovat | 0.840 | - |

Záměrné skóre nevyškoleným utterance je nižší než u s popiskem utterance, protože LEOŠ ví, že se věta je gramaticky stejný. Ale LEOŠ neví, že utterances mají stejný význam. Navíc bez seznamu frázi **hardwaru** entita nebyla nalezena.

Musí LEOŠ naučit, *má* a *vyžadují* synonymum v této doméně aplikace, protože slova můžou mít více než jeden význam. 

## <a name="improve-the-score-of-untrained-utterance-with-phrase-list"></a>Zlepšení skóre nevyškoleným utterance s frázi seznamu 
1. Přidat [frázi seznamu](luis-how-to-add-features.md) funkci s názvem **má** s hodnotou `want`a potom vyberte **Enter**.

    > [!TIP]
    > Po každé slovo nebo frázi, vyberte **Enter** klíč. Slovo nebo frázi je přidán do **fráze hodnoty seznamu** pole, když ukazatel zůstane v **hodnotu** pole. Můžete zadat mnoho hodnot rychle s touto funkcí.

2. Chcete-li zobrazit slova, která doporučuje LEOŠ, vyberte **doporučujeme**. 

    ![Doporučujeme hodnoty](./media/luis-tutorial-interchangeable-phrase-list/recommend.png)

3. Přidejte všechna slova. Pokud `require` není v seznamu doporučené přidejte jej jako požadovaná hodnota. 

4. Protože tato slova jsou synonyma, zachovat *zaměňovat* nastavení a potom vyberte **Uložit**.

    ![Hodnoty seznamu fráze](./media/luis-tutorial-interchangeable-phrase-list/phrase-list-values.png)

5. V horním navigačním panelu, vyberte **cvičení** ke cvičení aplikace, ale nemáte publikovat. Nyní máte dva modely. Můžete porovnat hodnoty v obou modelů.

## <a name="compare-the-phrase-list-model-to-the-published-model"></a>Porovnání modelu frázi seznam publikovaných modelu
V této aplikaci není cvičení publikované modelu s synonyma. Pouze aktuálně upravená model zahrnuje frázi seznam synonyma. Chcete-li porovnat modely, použijte [interaktivní testování](interactive-test.md#interactive-testing). 

1. Otevřete **Test** podokně a zadejte následující utterance:

    `I require a computer replacement`

2. Chcete-li otevřít panel kontroly, vyberte **kontroly**. 

    ![Vyberte zkontrolovat](./media/luis-tutorial-interchangeable-phrase-list/inspect-button.png)

3. Chcete-li porovnat publikované modelu, který má nový model frázi seznamu, vyberte **publikovaná porovnat s**.

    ![Zkontrolujte publikována a aktuální](./media/luis-tutorial-interchangeable-phrase-list/inspect.png)

Po přidání seznamu frázi vyšší přesnost utterance a **hardwaru** entita je nalezena. 

|Status | Seznam frází| Záměrné skóre | Skóre entity |
|--|--|--|--|
| Publikováno | - | 0,84 | - |
| Aktuálně úpravy |✔| 0.92 | Hardware entitu identifikovanou |

> [!TIP]
> * Pomocí [interaktivní testování](interactive-test.md#interactive-testing), můžete porovnat publikované modelu, který má vyškolení změny provedené po publikování. 
> * Pomocí [testování koncový bod](PublishApp.md#test-your-published-endpoint-in-a-browser), můžete zobrazit přesný LEOŠ odpovědi JSON. 

## <a name="get-the-entity-score-with-the-endpoint-test"></a>Získat entity skóre s testovací koncový bod
Chcete-li zobrazit skóre entity [publikování modelu](PublishApp.md) a dotazovat se na koncový bod. 

`I require a computer replacement`

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.916503668
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.916503668
    },
    {
      "intent": "None",
      "score": 0.136505231
    },
    {
      "intent": "Whois",
      "score": 0.02778677
    },
    {
      "intent": "CheckInventory",
      "score": 0.0144592477
    },
    {
      "intent": "CheckCalendar",
      "score": 0.01401332
    }
  ],
  "entities": [
    {
      "entity": "computer",
      "type": "Hardware",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.5959917
    }
  ]
}
```

**Hardwaru** entity ukazuje skóre 0.595 se seznamem frázi. Dříve, než seznamu frázi vznikla, nebyla zjištěna entity. 

|Status | Seznam frází| Záměrné skóre | Skóre entity |
|--|--|--|--|
| Publikováno | - | 0,84 | - |
| Aktuálně úpravy |✔| 0.92 | 0.595 |


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud již nepotřebujete, odstraňte aplikaci LEOŠ. To pokud chcete udělat, vyberte v seznamu aplikací, vyberte v nabídce tři tečky (...) vpravo od názvu aplikace **odstranit**. V místním dialogovém okně **aplikaci odstranit?**, vyberte **Ok**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získat utterance předpovědi s dotazem koncový bod](luis-get-started-cs-get-intent.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[LuisFeatures]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[LuisSampleApp]: https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/phrase_list/interchangeable/luis-app-before-phrase-list.json
