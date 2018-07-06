---
title: Kurz pomocí fráze seznamu k vylepšení predikce služby LUIS – Azure | Dokumentace Microsoftu
description: V tomto kurzu přidat seznam frází na aplikaci LUIS a najdete v článku zlepšování skóre.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 2fd53225a455a34d03772487a10f62a94ac86735
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868969"
---
# <a name="tutorial-add-phrase-list-to-improve-predictions"></a>Kurz: Přidání seznamu frází k vylepšení predikce
V tomto kurzu, zvýšit přesnost záměru skóre a identifikaci entity pro slova, která mají stejný význam (synonym) tak, že přidáte zaměnitelné [funkci seznamu frázi](./luis-concept-feature.md).

> [!div class="checklist"]
* Importovat novou aplikaci  
* Koncový bod dotazu s známé utterance 
* Dotazování koncový bod s _neznámý_ utterance
* Přidat seznam frází ke zlepšení Neznámý utterance skóre
* Ověřte, zda je najít při používání seznamu frází

Pro účely tohoto článku potřebujete bezplatný účet [LUIS][LUIS], abyste mohli vytvořit svou aplikaci LUIS.

## <a name="import-a-new-app"></a>Importovat novou aplikaci
1. Stáhněte si [příkladu aplikace LUIS] [ LuisSampleApp] , která je navržená pro účely tohoto kurzu. Použijete ho v dalším kroku. 

2. Jak je popsáno v [vytvořit aplikaci](Create-new-app.md#import-new-app), import souboru, který jste stáhli do [LUIS] [ LUIS] webu jako novou aplikaci. Název aplikace je "Kurz Můj seznam frází." Má projevy, záměry a entity. 

3. [Trénování](luis-how-to-train.md) vaší aplikace. Dokud není nastavena, nelze [testovat](interactive-test.md#interactive-testing) ho [LUIS] [ LUIS] webu. 

4. Na [publikovat](luis-how-to-publish-app.md) stránky, vyberte **zahrnout všechny předpovědět záměru skóre** zaškrtávací políčko. Pokud zaškrtávací políčko je zaškrtnuto, budou vráceny všechny záměry. Pokud políčko není zaškrtnuto, je vrácen pouze hlavní záměr. 

5. [Publikování](luis-how-to-publish-app.md) aplikace. Publikování aplikace můžete otestovat pomocí koncového bodu HTTPS. 

## <a name="test-a-trained-utterance"></a>Testování trénovaného utterance
Použití publikované koncový bod k dotazování utterance, která aplikace už zná. Vzhledem k tomu, že LUIS již ví, utterance, skóre je vysoké a zjištění entity.

1. Na [Language Understanding (LUIS)] [ LUIS] webu na **publikovat** stránky pro novou aplikaci, vyberte adresu URL koncového bodu v **prostředky a klíče**oddílu. 

    ![Publikovat adresu URL koncového bodu](./media/luis-tutorial-interchangeable-phrase-list/luis-publish-url.png)

2. V prohlížeči, na konci adresy URL, přidejte následující dotaz po `q=`.

    `I want a computer replacement`

    Koncový bod odpovídá následujícím kódem JSON:
    
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

    Záměru skóre 0.973 a skóre detekce entity 0.846 jsou vysoká, protože aplikace byla natrénovaný pomocí této utterance. Utterance je v aplikaci LUIS na stránce záměru **GetHardware**. Text utterance `computer`, je označena jako **hardwaru** entity. 
    
    |Status|Word| Záměru skóre | Skóre entity |
    |--|--|--|--|
    |Školení| Chcete | 0.973 | 0.846 |
    
    
## <a name="test-an-untrained-utterance"></a>Testování Nezkušený utterance
V prohlížeči použijte stejný koncový bod publikované do dotazu s utterance, aplikace nebude již víte, že:

`I require a computer replacement`

Tato utterance používá synonymum předchozí utterance:

| Trénované aplikace word | Nezkušený synonym |
|--|--|
| Chcete | vyžadovat |

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

| Status | Word | Záměru skóre | Skóre entity |
|--|--|--|--|
| Školení| Chcete | 0.973 | 0.846 |
| Nezkušený| vyžadovat | 0.840 | - |

Skóre záměru Nezkušený utterance je nižší než s popiskem utterance, protože LUIS ví, že věty je gramaticky stejný. Ale služba LUIS neví, že projevy mají stejný význam. Navíc bez seznamu frázi **hardwaru** entita nebyla nalezena.

Musí naučit LUIS, který *má* a *vyžadují* to samé v této doméně AppDomain, protože u slov velká, může mít více než jeden význam. 

## <a name="improve-the-score-of-untrained-utterance-with-phrase-list"></a>Zvýšení skóre Nezkušený utterance seznamem fráze 
1. Přidat [seznam frází](luis-how-to-add-features.md) funkci s názvem **má** s hodnotou `want`a pak vyberte **Enter**.

    > [!TIP]
    > Po každé slovo nebo frázi, vyberte **Enter** klíč. Slovo nebo frázi, se přidá do **frázi hodnoty seznamu** pole, když ukazatel zůstane v **hodnotu** pole. Můžete zadat více hodnot rychle s touto funkcí.

2. Chcete-li zobrazit slova, která doporučuje LUIS, vyberte **doporučujeme**. 

    ![Doporučujeme hodnoty](./media/luis-tutorial-interchangeable-phrase-list/recommend.png)

3. Přidáte všechna slova. Pokud `require` není v doporučeném seznamu, přidejte jej jako povinná hodnota. 

4. Protože tato slova jsou synonyma, zachovat *zaměnitelné* nastavení a pak vyberte **Uložit**.

    ![Hodnoty seznamu fráze](./media/luis-tutorial-interchangeable-phrase-list/phrase-list-values.png)

5. V horním navigačním panelu vyberte **trénování** tak moct trénovat na aplikaci, ale není publikovat. Teď máte dva modely. Můžete porovnat hodnoty v obou modelů.

## <a name="compare-the-phrase-list-model-to-the-published-model"></a>Porovnání modelu frázi seznam publikovaných modelu
V této aplikaci není publikovaný model natrénovaný pomocí synonym. Pouze aktuálně upravované model obsahuje seznam frázi synonym. Chcete-li porovnat modely, použijte [interaktivní testování](interactive-test.md#interactive-testing). 

1. Otevřít **Test** podokně a zadejte následující utterance:

    `I require a computer replacement`

2. Chcete-li otevřít panel kontroly, vyberte **zkontrolujte, jestli se**. 

    ![Zkontrolujte výběr](./media/luis-tutorial-interchangeable-phrase-list/inspect-button.png)

3. Chcete-li porovnat publikované modelu na nový model seznamu frázi, vyberte **porovnat s publikované**.

    ![Kontrola publikované srovnání s aktuální](./media/luis-tutorial-interchangeable-phrase-list/inspect.png)

Po přidání seznamu frázi vyšší přesnost utterance a **hardwaru** entita se nenašla. 

|Status | Seznam frází| Záměru skóre | Skóre entity |
|--|--|--|--|
| Publikováno | - | 0,84 | - |
| Právě se upravuje |✔| 0.92 | Hardware entitu identifikovanou |

> [!TIP]
> * S použitím [interaktivní testování](interactive-test.md#interactive-testing), můžete porovnat publikované model trénovaného změny provedené po publikování. 
> * S použitím [testování koncového bodu](luis-how-to-publish-app.md#test-your-published-endpoint-in-a-browser), můžete zobrazit přesný LUIS odpověď JSON. 

## <a name="get-the-entity-score-with-the-endpoint-test"></a>Získat entity skóre v testu koncového bodu
Chcete-li zobrazit skóre entity [model publikujte](luis-how-to-publish-app.md) a dotazování na koncový bod. 

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

**Hardwaru** entity se zobrazí skóre 0.595 seznamem frázi. Před existovalo seznamu frázi, entita nebyla rozpoznána. 

|Status | Seznam frází| Záměru skóre | Skóre entity |
|--|--|--|--|
| Publikováno | - | 0,84 | - |
| Právě se upravuje |✔| 0.92 | 0.595 |


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. Uděláte to tak, vyberte tři tečky (***...*** ) napravo od názvu aplikace v seznamu aplikací vyberte **odstranit**. V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získat utterance půjček s využitím koncového bodu dotazu](luis-get-started-cs-get-intent.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[LuisFeatures]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[LuisSampleApp]: https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/phrase_list/interchangeable/luis-app-before-phrase-list.json
