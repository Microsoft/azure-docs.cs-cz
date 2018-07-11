---
title: Kurz vytváření složených entity k extrakci komplexní dat – Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit složenou entitu ve vaší aplikaci LUIS k extrakci různé typy dat entity.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 07/09/2018
ms.author: v-geberr
ms.openlocfilehash: d73dc9b9f204e334a75c9de5e19c6b11e3a95b12
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929181"
---
# <a name="tutorial-6-add-composite-entity"></a>Kurz: 6. Přidat složené entitu 
V tomto kurzu přidáte složené entitu extrahovaná data seskupit do obsahující entity.

V tomto kurzu se naučíte:

<!-- green checkmark -->
> [!div class="checklist"]
> * Vysvětlení konceptu entit složené 
> * Přidat složené entitu k extrahování dat
> * Trénování a publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS

## <a name="before-you-begin"></a>Než začnete
Pokud nemáte aplikaci pro lidské zdroje z kurzu k [hierarchickým entitám](luis-quickstart-intent-and-hier-entity.md), [naimportujte](luis-how-to-start-new-app.md#import-new-app) JSON do nové aplikace na webu služby [LUIS](luis-reference-regions.md#luis-website). Aplikaci k importování najdete v úložišti [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-hier-HumanResources.json) na Githubu.

Pokud chcete zachovat původní aplikaci pro lidské zdroje, naklonujte verzi na stránce [Settings](luis-how-to-manage-versions.md#clone-a-version) (Nastavení) a pojmenujte ji `composite`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi.  

## <a name="composite-entity-is-a-logical-grouping"></a>Složený entity je logické seskupení 
Účelem složený entity je nadřazená entita kategorie pro seskupení souvisejících entit. Informace o existuje jako samostatný entity před vytvořením složeného. Je podobné hierarchické entity, ale může obsahovat další typy entit. 

 Vytvoření složeného entity při samostatné entity je možné logicky seskupit a tato logická seskupení je vhodné klientské aplikace. 

V této aplikaci je podle jméno zaměstnance **zaměstnance** seznam entit a zahrnuje synonyma název, e-mailovou adresu, Telefonní klapka společnosti, číslo mobilního telefonu a USA ID federálních daňových. 

**MoveEmployee** má záměr projevů příklad požádat o zaměstnance na jiný přesunout z jednoho sestavení a office. Názvy sestavení jsou abecední znaky: "A", "B" a další pobočky jsou číselná: "1234", "13245". 

Příklad projevy v **MoveEmployee** záměr patří:

|Ukázkové promluvy|
|--|
|Přesuňte W Jan. Smith a-2345|
|shift x12345 to h-1234 tomorrow (přestěhujte zítra x12345 do h-1234).|
 
Žádost o přesunutí musí obsahovat alespoň zaměstnance (s použitím libovolné synonym) a konečné umístění sestavení a office. Žádost může také zahrnovat původní office, jakož i datum, kdy se stane při přechodu. 

By měl obsahovat tyto informace a vrátit ji do v extrahovaných dat z koncového bodu `RequestEmployeeMove` složený entity. 

## <a name="create-composite-entity"></a>Vytvoření složeného entity
1. Ujistěte se, že je vaše aplikace pro lidské zdroje uvedená v části **Build** (Sestavení) služby LUIS. Do této části můžete přejít výběrem možnosti **Build** (Sestavit) v pravém horním řádku nabídek. 

    [ ![Snímek obrazovky aplikace LUIS se zvýrazněnou možností Build (Sestavit) na pravém horním navigačním panelu](./media/luis-tutorial-composite-entity/hr-first-image.png)](./media/luis-tutorial-composite-entity/hr-first-image.png#lightbox)

2. Na **záměry** stránce **MoveEmployee** záměr. 

    [![](media/luis-tutorial-composite-entity/hr-intents-moveemployee.png "Snímek obrazovky služby LUIS se záměrem \"MoveEmployee\" zvýrazněnou")](media/luis-tutorial-composite-entity/hr-intents-moveemployee.png#lightbox)

3. Vyberte ikonu lupy na panelu nástrojů pro filtrování seznamu projevy. 

    [![](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "Snímek obrazovky služby LUIS na záměr \"MoveEmployee\" se zvýrazněným tlačítkem s ikonou lupy")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. Zadejte `tomorrow` do textového pole filtru k vyhledání utterance `shift x12345 to h-1234 tomorrow`.

    [![](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "Zvýrazněný snímek obrazovky služby LUIS na záměr \"MoveEmployee\" s filtrem \"zítra.")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    Jinou metodou je vyfiltrovat entity datetimeV2, tak, že vyberete **Entity filtry** vyberte **datetimeV2** ze seznamu. 

5. Vyberte první entitu `Employee`a pak vyberte **zabalit do složeného entity** v rozbalovacím seznamu. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-1.png "Snímek obrazovky služby LUIS na záměr \"MoveEmployee\" vyberete první entitu v složeného zvýrazněnou")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. Vyberte poslední entita okamžitě `datetimeV2` v utterance. Panel zelené vykreslením v rámci vybrané slova označující složený entity. V rozbalovací nabídce, zadejte název složený `RequestEmployeeMove` vyberte **vytvořit nové kompozitní** v v místní nabídce. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-2.png "Snímek obrazovky služby LUIS na záměr \"MoveEmployee\" Výběr poslední entity v entity složené a vytváření zvýrazněnou")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. V **jaký typ entity chcete vytvořit?**, téměř všechna pole, vyžaduje se v seznamu. Chybí pouze původní umístění. Vyberte **přidat podřízené entity**vyberte **Locations::Origin** ze seznamu existující entity, pak vyberte **provádí**. 

  ![Snímek obrazovky služby LUIS na záměr "MoveEmployee" Přidání jiná entita v automaticky otevíraném okně](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. Vyberte ikonu lupy na panelu nástrojů Filtr odeberete. 

## <a name="label-example-utterances-with-composite-entity"></a>Projevy příklad popisku s složený entity
1. V každé utterance příklad vyberte entitu úplně vlevo, by měla být v složeného. Potom vyberte **zabalit do složeného entity**.

    [![](media/luis-tutorial-composite-entity/hr-label-entity-1.png "Snímek obrazovky služby LUIS na záměr \"MoveEmployee\" vyberete první entitu v složeného zvýrazněnou")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. Vyberte poslední entita složený potom **RequestEmployeeMove** v místní nabídce. 

    [![](media/luis-tutorial-composite-entity/hr-label-entity-2.png "Snímek obrazovky služby LUIS na záměr \"MoveEmployee\" Výběr poslední entita v složeného zvýrazněnou")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. Ověřte, že všechny projevy v záměr jsou označeny složený entity. 

    [![](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "Snímek obrazovky služby LUIS na \"MoveEmployee\" s všechny projevy s popiskem")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS
LUIS nebude vědět o nové kompozitní entity, dokud se trénuje aplikace. 

1. V pravé horní části webu LUIS vyberte tlačítko **Train** (Trénovat).

    ![Trénování aplikace](./media/luis-tutorial-composite-entity/hr-train-button.png)

2. Trénování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

    ![Trénování proběhlo úspěšně](./media/luis-tutorial-composite-entity/hr-trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu
Abyste mohli využít předpověď služby LUIS v chatbotu nebo jiné aplikaci, musíte aplikaci publikovat. 

1. V pravé horní části webu LUIS vyberte tlačítko **Publish** (Publikovat). 

2. Vyberte slot Production (Produkční) a tlačítko **Publish** (Publikovat).

    ![publikování aplikace](./media/luis-tutorial-composite-entity/hr-publish-to-production.png)

3. Publikování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

## <a name="query-the-endpoint"></a>Dotaz na koncový bod 
1. V dolní části stránky **Publish** (Publikovat) vyberte odkaz na **koncový bod**. Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v adresním řádku. 

    ![Vyberte adresu URL koncového bodu](./media/luis-tutorial-composite-entity/hr-publish-select-endpoint.png)

2. Na konec adresy URL zadejte `Move Jill Jones from a-1234 to z-2345 on March 3 2 p.m.`. Poslední parametr querystring `q`, dotaz utterance. 

    Ověřte, zda že je správně extrahován složeného je tento test, test může obsahovat buď existující utterance ukázka nebo nové utterance. Dobrá se zahrnou všechny podřízené entity složený entity.

```JSON
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
      "type": "requestemployeemove",
      "startIndex": 5,
      "endIndex": 54,
      "score": 0.4027723
    }
  ],
  "compositeEntities": [
    {
      "parentType": "requestemployeemove",
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
  ],
  "sentimentAnalysis": {
    "label": "neutral",
    "score": 0.5
  }
}
```

Tato utterance vrátí pole složený entity. Každá entita je uveden typ a hodnotu. Najít další přesnosti pro každé podřízené entity, použijte kombinaci typu a hodnoty z položky složený pole Najít odpovídající položku v poli entity.  

## <a name="what-has-this-luis-app-accomplished"></a>Co tato aplikace LUIS udělala?
Tuto aplikaci identifikovat záměru dotazu přirozeného jazyka a vrátí extrahovaných dat jako pojmenované skupiny. 

Váš robot teď má dostatek informací k určení primární akce a související podrobnosti v utterance. 

## <a name="where-is-this-luis-data-used"></a>Kde se tato data služby LUIS používají? 
Služba LUIS s tímto požadavkem skončila. Volající aplikace, například chatbot, může převzít výsledek topScoringIntent a data z entity a provést další krok. Služba LUIS neprovádí tuto programovou práci za chatbota ani nevolá aplikaci. Služba LUIS pouze určuje, co je záměrem uživatele. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. Vyberte **Moje aplikace** v nabídce vlevo nahoře. Vyberte tři tečky (***...*** ) napravo od názvu aplikace v seznamu aplikací vyberte **odstranit**. V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"] 
> [Zjistěte, jak přidat jednoduchou entitu se seznamem fráze](luis-quickstart-primary-and-secondary-data.md)  