---
title: Vytvoření složeného entity k extrakci komplexní dat – Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit složenou entitu ve vaší aplikaci LUIS k extrakci různé typy dat entity.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 03/28/2018
ms.author: v-geberr
ms.openlocfilehash: 375b52f9206f55e620d5e664844b8fa1d7249a07
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888741"
---
# <a name="use-composite-entity-to-extract-complex-data"></a>Extrahovat komplexní data pomocí složeného entity
Tato jednoduchá aplikace má dva [záměry](luis-concept-intent.md) a několik entit. Jeho účelem je rezervuje lety, jako je například "1 lístku ze Seattlu Cairo v pátek" a vrátí všechny specifikace rezervace jako jednu část dat. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
* Přidat datetimeV2 předem připravených entit a číslo
* Vytvoření složeného entity
* Dotazování služby LUIS a přijímat složené entity data

## <a name="before-you-begin"></a>Než začnete
* Aplikace LUIS z  **[hierarchické quickstart](luis-tutorial-composite-entity.md)**. 

> [!Tip]
> Pokud ještě nemáte předplatné, si můžete zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="composite-entity-is-a-logical-grouping"></a>Složený entity je logické seskupení 
Účelem této entity je vyhledat v promluvě části textu a uspořádat je do kategorií. A [složené](luis-concept-entity-types.md) entity se skládá z jiných typů entit se dozvěděli, z kontextu. Cestovní aplikace, která přebírá rezervace letenek existují různé druhy informace, jako jsou data, umístění a počet licencí. 

Informace o existuje jako samostatný entity před vytvořením složeného. Vytvoření složeného entity při samostatné entity je možné logicky seskupit a tato logická seskupení je vhodné chatovací robot nebo jiné aplikace LUIS využívání. 

Mezi jednoduché ukázkové promluvy od uživatelů patří:

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Reserve a seat from New York to Paris on the first of April
```
 
Složený entity odpovídá počet licencovaných míst, původní umístění, cílové umístění a data. 

## <a name="what-luis-does"></a>Co dělá služba LUIS
Když se záměr a entity promluvy identifikují, [extrahují](luis-concept-data-extraction.md#list-entity-data) a vrátí z [koncového bodu](https://aka.ms/luis-endpoint-apis) ve formátu JSON, služba LUIS je hotová. Volající aplikace nebo chatbot převezme tuto odpověď JSON a splní požadavek způsobem, pro který jsou aplikace nebo chatbot určené. 

## <a name="add-prebuilt-entities-number-and-datetimev2"></a>Přidat počtu předem připravených entit a datetimeV2
1. Vyberte `MyTravelApp` aplikace ze seznamu aplikací na [LUIS](luis-reference-regions.md#luis-website) webu.

2. Když se aplikace otevře, vyberte **entity** levé navigační odkaz.

    ![Vyberte tlačítko entity](./media/luis-tutorial-composite-entity/intents-page-select-entities.png)    

3. Vyberte **Manage prebuilt entities** (Spravovat předem připravené entity).

    ![Vyberte tlačítko entity](./media/luis-tutorial-composite-entity/manage-prebuilt-entities-button.png)

4. V dialogovém okně místní vyberte **číslo** a **datetimeV2**.

    ![Vyberte tlačítko entity](./media/luis-tutorial-composite-entity/prebuilt-entity-ddl.png)

5. Aby nové entity, které chcete extrahovat, vyberte **Train** v horním navigačním panelu.

    ![Výběr tlačítka Train (Trénovat)](./media/luis-tutorial-composite-entity/train.png)

## <a name="use-existing-intent-to-create-composite-entity"></a>Použít existující záměr mohla vytvořit entita složený
1. Vyberte **záměry** z levé navigace. 

    ![Vyberte stránku záměrů](./media/luis-tutorial-composite-entity/intents-from-entities-page.png)

2. Vyberte `BookFlight` z **záměry** seznamu.  

    ![Vyberte ze seznamu BookFlight záměr](./media/luis-tutorial-composite-entity/intent-page-with-prebuilt-entities-labeled.png)

    Číslo a datetimeV2 na projevy jsou označeny předem připravených entit.

3. Pro utterance `book 2 flights from seattle to cairo next monday`, vyberte modrý `number` entity, pak vyberte **zabalit do složeného entity** ze seznamu. Zelená čára, v části slova, se ale řídí kurzor přesune na pravé straně označující složený entity. Posuňte doprava a vyberte poslední předem připravených entit `datetimeV2`, zadejte `FlightReservation` do textového pole v automaticky otevíraném okně vyberte **vytvořit nové kompozitní**. 

    ![Vytvoření složeného entity na stránce záměrů](./media/luis-tutorial-composite-entity/create-new-composite.png)

4. Zobrazení dialogu se zobrazí, vám umožní ověřit složený entity podřízené objekty. Vyberte **Done** (Hotovo).

    ![Vytvoření složeného entity na stránce záměrů](./media/luis-tutorial-composite-entity/validate-composite-entity.png)

## <a name="wrap-the-entities-in-the-composite-entity"></a>Zabalit entity do složeného entity
Po vytvoření složeného entity popisek zbývající projevy složený entity. Pokud chcete zabalit fráze jako složený entity, je potřeba vybrat slovo nejvíce vlevo a pak vyberte **zabalit do složeného entity** ze seznamu, který se zobrazí, pak vybrat slovo úplně vpravo a pak vyberte pojmenovaná entita složený `FlightReservation`. Toto je rychlý a hladký průběh krok výběrů rozdělené podle následujících kroků:

1. V utterance `schedule 4 seats from paris to london for april 1`, vyberte na 4 jako číslo předem připravených entit.

    ![Vybrat slovo nejvíce vlevo](./media/luis-tutorial-composite-entity/wrap-composite-step-1.png)

2. Vyberte **zabalit do složeného entity** ze seznamu, který se zobrazí.

    ![Wrap vyberte ze seznamu](./media/luis-tutorial-composite-entity/wrap-composite-step-2.png)

3. Vyberte slovo úplně vpravo. Zelená čára se zobrazí pod frázi označující složený entity.

    ![Vybrat slovo úplně vpravo](./media/luis-tutorial-composite-entity/wrap-composite-step-3.png)

4. Vyberte název složený `FlightReservation` ze seznamu, který se zobrazí.

    ![Vyberte složené pojmenovaných entit](./media/luis-tutorial-composite-entity/wrap-composite-step-4.png)

    Poslední utterance zabalení `London` a `tomorrow` složený entity pomocí stejných pokynů. 

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS
Služba LUIS nemá informace o změnách záměrů a entit (tedy modelu), dokud se nenatrénuje. 

1. V pravé horní části webu LUIS vyberte tlačítko **Train** (Trénovat).

    ![Trénování aplikace](./media/luis-tutorial-composite-entity/train-button.png)

2. Trénování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

    ![Trénování proběhlo úspěšně](./media/luis-tutorial-composite-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu
Abyste mohli využít předpověď služby LUIS v chatbotu nebo jiné aplikaci, musíte aplikaci publikovat. 

1. V pravé horní části webu LUIS vyberte tlačítko **Publish** (Publikovat). 

2. Vyberte slot Production (Produkční) a tlačítko **Publish** (Publikovat).

    ![publikování aplikace](./media/luis-tutorial-composite-entity/publish-to-production.png)

3. Publikování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Odeslání dotazu na koncový bod s jinou promluvou
1. V dolní části stránky **Publish** (Publikovat) vyberte odkaz na **koncový bod**. Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v adresním řádku. 

    ![Vyberte adresu URL koncového bodu](./media/luis-tutorial-composite-entity/publish-select-endpoint.png)

2. Na konec adresy URL zadejte `reserve 3 seats from London to Cairo on Sunday`. Poslední parametr querystring `q`, dotaz utterance. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `BookFlight` s extrahovanou hierarchickou entitou.

```
{
  "query": "reserve 3 seats from London to Cairo on Sunday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.999999046
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.999999046
    },
    {
      "intent": "None",
      "score": 0.227036044
    }
  ],
  "entities": [
    {
      "entity": "sunday",
      "type": "builtin.datetimeV2.date",
      "startIndex": 40,
      "endIndex": 45,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-03-25"
          },
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-04-01"
          }
        ]
      }
    },
    {
      "entity": "3 seats from london to cairo on sunday",
      "type": "flightreservation",
      "startIndex": 8,
      "endIndex": 45,
      "score": 0.6892485
    },
    {
      "entity": "cairo",
      "type": "Location::Destination",
      "startIndex": 31,
      "endIndex": 35,
      "score": 0.557570755
    },
    {
      "entity": "london",
      "type": "Location::Origin",
      "startIndex": 21,
      "endIndex": 26,
      "score": 0.8933808
    },
    {
      "entity": "3",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "3"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "flightreservation",
      "value": "3 seats from london to cairo on sunday",
      "children": [
        {
          "type": "builtin.datetimeV2.date",
          "value": "sunday"
        },
        {
          "type": "Location::Destination",
          "value": "cairo"
        },
        {
          "type": "builtin.number",
          "value": "3"
        },
        {
          "type": "Location::Origin",
          "value": "london"
        }
      ]
    }
  ]
}
```

Vrátí tento utterance složené entit pole včetně **flightreservation** objekt se dat extrahovaných.  

## <a name="what-has-this-luis-app-accomplished"></a>Co tato aplikace LUIS udělala?
Tuto aplikaci pouze dvě záměry a složené entity identifikované záměru dotazu přirozeného jazyka a vrátí extrahovaná data. 

Váš robot teď obsahuje dostatek informací k určení primární akce `BookFlight`a informace o rezervacích součástí utterance. 

## <a name="where-is-this-luis-data-used"></a>Kde se tato data služby LUIS používají? 
Služba LUIS s tímto požadavkem skončila. Volající aplikace, například chatbot, může převzít výsledek topScoringIntent a data z entity a provést další krok. Služba LUIS neprovádí tuto programovou práci za chatbota ani nevolá aplikaci. Služba LUIS pouze určuje, co je záměrem uživatele. 

## <a name="next-steps"></a>Další postup

[Další informace o entitách](luis-concept-entity-types.md). 
