---
title: Složený entity"
titleSuffix: Azure Cognitive Services
description: Přidáte složené entitu seskupit do jedné entity obsahující extrahované data různých typů. Seskupí dat, klientská aplikace snadno extrahovat souvisejících dat v různých datových typů.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: b5923d5cd4a704dda76e33ee6a2b76cfd903219d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53079207"
---
# <a name="tutorial-6-group-and-extract-related-data"></a>Kurz 6: Seskupovat a extrahovat souvisejících dat
V tomto kurzu přidáte složené entitu seskupit do jedné entity obsahující extrahované data různých typů. Seskupí dat, klientská aplikace snadno extrahovat souvisejících dat v různých datových typů.

Účelem složený entity je nadřazená entita kategorie pro seskupení souvisejících entit. Informace o existuje jako samostatný entity před vytvořením složeného. Je podobné hierarchické entity, ale může obsahovat různé typy entit. 

Složený entita je vhodný pro tento typ dat, protože data:

* Se vztahují k sobě navzájem. 
* Využijte celou řadu typů entit.
* Musí být seskupeny a zpracovány klientskou aplikací jako jediná informace.

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Používat existující ukázkovou aplikaci
> * Přidání složené entity 
> * Trénování
> * Publikování
> * Zjistit záměry a entity z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Použití existující aplikace
Pokračujte s aplikací **HumanResources**, kterou jste vytvořili v posledním kurzu. 

Pokud aplikaci HumanResources z předchozího kurzu nemáte, postupujte takto:

1.  Stáhněte si [soubor JSON aplikace](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-hier-HumanResources.json) a uložte si ho.

2. Naimportujte soubor JSON do nové aplikace.

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `composite`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.


## <a name="composite-entity"></a>Složený entity
Vytvoření složeného entity při samostatné entity je možné logicky seskupit a tato logická seskupení je vhodné klientské aplikace. 

V této aplikaci je podle jméno zaměstnance **zaměstnance** seznam entit a zahrnuje synonyma název, e-mailovou adresu, Telefonní klapka společnosti, číslo mobilního telefonu a USA ID federálních daňových. 

**MoveEmployee** má záměr projevů příklad požádat o zaměstnance na jiný přesunout z jednoho sestavení a office. Názvy sestavení jsou abecední znaky: "A", "B" a další pobočky jsou číselná: "1234", "13245". 

Příklad projevy v **MoveEmployee** záměr patří:

|Ukázkové promluvy|
|--|
|Přesuňte W Jan. Smith a-2345|
|shift x12345 to h-1234 tomorrow (přestěhujte zítra x12345 do h-1234).|
 
Žádost o přesunutí musí obsahovat zaměstnance (s použitím libovolné synonym) a konečné umístění sestavení a office. Žádost může také zahrnovat původní office, jakož i datum, kdy se stane při přechodu. 

Extrahovaných dat z koncového bodu by měl obsahovat tyto informace a vrátit ho `RequestEmployeeMove` složený entity:

```json
"compositeEntities": [
  {
    "parentType": "RequestEmployeeMove",
    "value": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
    "children": [
      {
        "type": "builtin.datetimeV2.datetime",
        "value": "march 3 2 p.m"
      },
      {
        "type": "Locations::Destination",
        "value": "z - 2345"
      },
      {
        "type": "Employee",
        "value": "jill jones"
      },
      {
        "type": "Locations::Origin",
        "value": "a - 1234"
      }
    ]
  }
]
```

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Na **záměry** stránce **MoveEmployee** záměr. 

3. Vyberte ikonu lupy na panelu nástrojů pro filtrování seznamu projevy. 

    [![Snímek obrazovky služby LUIS na záměr "MoveEmployee" se zvýrazněným tlačítkem s ikonou lupy](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "snímek obrazovky služby LUIS na záměr \"MoveEmployee\" se zvýrazněným tlačítkem s ikonou lupy")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. Zadejte `tomorrow` do textového pole filtru k vyhledání utterance `shift x12345 to h-1234 tomorrow`.

    [![Snímek obrazovky služby LUIS na záměr "MoveEmployee" s filtrem "zítřka" zvýrazněný](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "zvýrazněnou snímek obrazovky služby LUIS na záměr \"MoveEmployee\" s filtrem \"zítra.")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    Jinou metodou je vyfiltrovat entity datetimeV2, tak, že vyberete **Entity filtry** vyberte **datetimeV2** ze seznamu. 

5. Vyberte první entitu `Employee`a pak vyberte **zabalit do složeného entity** v rozbalovacím seznamu. 

    [![Snímek obrazovky služby LUIS na záměr "MoveEmployee" vyberete první entitu v složeného zvýrazněnou](media/luis-tutorial-composite-entity/hr-create-entity-1.png "snímek obrazovky služby LUIS na záměr \"MoveEmployee\" vyberete první entitu v složeného zvýrazněnou")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. Vyberte poslední entita okamžitě `datetimeV2` v utterance. Panel zelené vykreslením v rámci vybrané slova označující složený entity. V rozbalovací nabídce, zadejte název složený `RequestEmployeeMove` vyberte ENTER. 

    [![Snímek obrazovky služby LUIS na záměr "MoveEmployee" výběrem poslední entita v složeného a vytváří se entita zvýrazněnou](media/luis-tutorial-composite-entity/hr-create-entity-2.png "snímek obrazovky služby LUIS na záměr \"MoveEmployee\" výběrem poslední entita v složeného a vytváří se entita zvýrazněnou")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. V **jaký typ entity chcete vytvořit?**, téměř všechna pole, vyžaduje se v seznamu. Chybí pouze původní umístění. Vyberte **přidat podřízené entity**vyberte **Locations::Origin** ze seznamu existující entity, pak vyberte **provádí**. 

    Všimněte si, že předem připravených entit, number, byl přidán do složeného entity. Pokud jste mohli předem připravených entit zobrazí mezi počáteční a koncové tokeny složený entity, složený entity musí obsahovat předem připravených entit. Pokud předem připravených entit nejsou zahrnuty, složený entity není správně předpovědět, ale je každý jednotlivý prvek.

    ![Snímek obrazovky služby LUIS na záměr "MoveEmployee" Přidání jiná entita v automaticky otevíraném okně](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. Vyberte ikonu lupy na panelu nástrojů Filtr odeberete. 

9. Odstranit slovo `tomorrow` z filtru, abyste viděli všechny projevy příklad znovu. 

## <a name="label-example-utterances-with-composite-entity"></a>Projevy příklad popisku s složený entity


1. V každé utterance příklad vyberte entitu úplně vlevo, by měla být v složeného. Potom vyberte **zabalit do složeného entity**.

    [![Snímek obrazovky služby LUIS na záměr "MoveEmployee" vyberete první entitu v složeného zvýrazněnou](media/luis-tutorial-composite-entity/hr-label-entity-1.png "snímek obrazovky služby LUIS na záměr \"MoveEmployee\" vyberete první entitu v složeného zvýrazněnou")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. Vyberte poslední entita složený potom **RequestEmployeeMove** v místní nabídce. 

    [![Snímek obrazovky služby LUIS na záměr "MoveEmployee" Výběr poslední entita v složeného zvýrazněnou](media/luis-tutorial-composite-entity/hr-label-entity-2.png "snímek obrazovky služby LUIS na záměr \"MoveEmployee\" Výběr poslední entita v složeného zvýrazněnou")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. Ověřte, že všechny projevy v záměr jsou označeny složený entity. 

    [![Snímek obrazovky služby LUIS na "MoveEmployee" s všechny projevy, které jsou označené jako](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "snímek obrazovky služby LUIS na \"MoveEmployee\" s všechny projevy s popiskem")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train"></a>Trénování

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikování

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Získání záměru a entit z koncového bodu 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Na konec adresy URL zadejte `Move Jill Jones from a-1234 to z-2345 on March 3 2 p.m.`. Poslední parametr querystring `q`, dotaz utterance. 

    Ověřte, zda že je správně extrahován složeného je tento test, test může obsahovat buď existující utterance ukázka nebo nové utterance. Dobrá se zahrnou všechny podřízené entity složený entity.

    ```json
    {
      "query": "Move Jill Jones from a-1234 to z-2345 on March 3  2 p.m",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9959525
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9959525
        },
        {
          "intent": "GetJobInformation",
          "score": 0.009858314
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00728598563
        },
        {
          "intent": "FindForm",
          "score": 0.0058053555
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.005371796
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00266987388
        },
        {
          "intent": "None",
          "score": 0.00123299169
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00116407464
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00102653319
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0006628214
        }
      ],
      "entities": [
        {
          "entity": "march 3 2 p.m",
          "type": "builtin.datetimeV2.datetime",
          "startIndex": 41,
          "endIndex": 54,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2018-03-03 14:00:00"
              },
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2019-03-03 14:00:00"
              }
            ]
          }
        },
        {
          "entity": "jill jones",
          "type": "Employee",
          "startIndex": 5,
          "endIndex": 14,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "z - 2345",
          "type": "Locations::Destination",
          "startIndex": 31,
          "endIndex": 36,
          "score": 0.9690751
        },
        {
          "entity": "a - 1234",
          "type": "Locations::Origin",
          "startIndex": 21,
          "endIndex": 26,
          "score": 0.9713137
        },
        {
          "entity": "-1234",
          "type": "builtin.number",
          "startIndex": 22,
          "endIndex": 26,
          "resolution": {
            "value": "-1234"
          }
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 32,
          "endIndex": 36,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 47,
          "endIndex": 47,
          "resolution": {
            "value": "3"
          }
        },
        {
          "entity": "2",
          "type": "builtin.number",
          "startIndex": 50,
          "endIndex": 50,
          "resolution": {
            "value": "2"
          }
        },
        {
          "entity": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "type": "RequestEmployeeMove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "RequestEmployeeMove",
          "value": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "children": [
            {
              "type": "builtin.datetimeV2.datetime",
              "value": "march 3 2 p.m"
            },
            {
              "type": "Locations::Destination",
              "value": "z - 2345"
            },
            {
              "type": "Employee",
              "value": "jill jones"
            },
            {
              "type": "Locations::Origin",
              "value": "a - 1234"
            }
          ]
        }
      ]
    }
    ```

  Tato utterance vrátí pole složený entity. Každá entita je uveden typ a hodnotu. Najít další přesnosti pro každé podřízené entity, použijte kombinaci typu a hodnoty z položky složený pole Najít odpovídající položku v poli entity.  

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu vytvořili složený entity k zapouzdření existující entity. To umožňuje klientské aplikaci najít skupinu souvisejících dat v různých datových typů, chcete-li pokračovat v konverzaci. Klientská aplikace pro tuto aplikaci lidských zdrojů může požádat, jaký den a čas přechodu musí začínat a končit. Také může požádat o další Logistika movesuch jako fyzický telefon. 

> [!div class="nextstepaction"] 
> [Zjistěte, jak přidat jednoduchou entitu se seznamem fráze](luis-quickstart-primary-and-secondary-data.md)  
