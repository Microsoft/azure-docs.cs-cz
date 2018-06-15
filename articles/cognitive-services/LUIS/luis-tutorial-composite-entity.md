---
title: Vytvoření složeného entity extrahovat komplexní data - Azure | Microsoft Docs
description: Informace o vytváření složených entity ve vaší aplikaci LEOŠ extrahovat různé typy dat entity.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 03/28/2018
ms.author: v-geberr
ms.openlocfilehash: 5e4d5a03db6210c159227530b94fbde6873bc211
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "35343927"
---
# <a name="use-composite-entity-to-extract-complex-data"></a>Pomocí složeného entity komplexní data
Tato jednoduchá aplikace má dva [záměry](luis-concept-intent.md) a několik entit. Jejím účelem je sešit lety, jako je například "1 lístek z Prahy Káhiře na pátek", a vraťte všechny specifikace rezervace jako jediný data. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
* Přidat datetimeV2 předem entity a číslo
* Vytvoření složeného entity
* Dotaz LEOŠ a přijímat složené entity data

## <a name="before-you-begin"></a>Než začnete
* LEOŠ aplikaci z  **[hierarchické rychlý Start](luis-tutorial-composite-entity.md)**. 

> [!Tip]
> Pokud již nemáte předplatné, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="composite-entity-is-a-logical-grouping"></a>Složené entity je logické seskupení 
Účelem entity, která je k vyhledání a kategorizace části textu v utterance. A [složené](luis-concept-entity-types.md) entity se skládá z jiné typy entit naučili z kontextu. Pro tuto aplikaci cesta, která přebírá letu rezervace jsou několik informací jako je například data, umístění a počtu licencí. 

Informace existuje jako samostatné entity, před vytvořením složené. Vytvořte složenou entitu, když samostatné entity je možné logicky seskupit a toto logické seskupení je vhodné chatbot nebo jinou aplikaci LEOŠ využívání. 

Jednoduchý příklad utterances od uživatelů patří:

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Reserve a seat from New York to Paris on the first of April
```
 
Složené entity odpovídá počtu stanici, původní umístění, cílové umístění a datum. 

## <a name="what-luis-does"></a>Jaké jsou LEOŠ
Když jsou identifikuje záměr a entity utterance, [extrahovat](luis-concept-data-extraction.md#list-entity-data)a vrátí ve formátu JSON z [koncový bod](https://aka.ms/luis-endpoint-apis), LEOŠ se provádí. Volání aplikace nebo chatbot trvá této odpovědi JSON a splní požadavek – ať způsobem aplikace nebo chatbot slouží k provést. 

## <a name="add-prebuilt-entities-number-and-datetimev2"></a>Přidat číslo předem entity a datetimeV2
1. Vyberte `MyTravelApp` aplikace ze seznamu aplikací na [LEOŠ] [ LUIS] webu.

2. Když se otevře aplikaci, vyberte **entity** levé navigační odkaz.

    ![Kliknutím na tlačítko entity](./media/luis-tutorial-composite-entity/intents-page-select-entities.png)    

3. Vyberte **spravovat předem entity**.

    ![Kliknutím na tlačítko entity](./media/luis-tutorial-composite-entity/manage-prebuilt-entities-button.png)

4. V dialogovém okně automaticky otevírané okno Vyberte **číslo** a **datetimeV2**.

    ![Kliknutím na tlačítko entity](./media/luis-tutorial-composite-entity/prebuilt-entity-ddl.png)

5. Chcete-li, aby nové entity k extrakci, vyberte **Train** v horním navigačním panelu.

    ![Vyberte train tlačítko](./media/luis-tutorial-composite-entity/train.png)

## <a name="use-existing-intent-to-create-composite-entity"></a>Použít existující záměr k vytvoření složeného entity
1. Vyberte **záměry** z levé navigaci. 

    ![Vyberte stránku záměry](./media/luis-tutorial-composite-entity/intents-from-entities-page.png)

2. Vyberte `BookFlight` z **záměry** seznamu.  

    ![Vyberte BookFlight záměr ze seznamu](./media/luis-tutorial-composite-entity/intent-page-with-prebuilt-entities-labeled.png)

    Počet a datetimeV2 předem entity jsou označeny na utterances.

3. Pro utterance `book 2 flights from seattle to cairo next monday`, vyberte modrý `number` entity, pak vyberte **zalomení v složené entity** ze seznamu. Zelená řádku, v části slova, následuje kurzor při jejich přesunu doprava označující složené entity. Pak přesuňte doprava, abyste vyberte poslední předem entity `datetimeV2`, pak zadejte `FlightReservation` v textovém poli okna automaticky otevírané okno, pak vyberte **vytvořit nové složené**. 

    ![Na stránce záměry vytvořit složené entity](./media/luis-tutorial-composite-entity/create-new-composite.png)

4. Se zobrazí automaticky otevírané okno dialogové okno, umožňuje ověřte, děti složené entity. Vyberte **provádí**.

    ![Na stránce záměry vytvořit složené entity](./media/luis-tutorial-composite-entity/validate-composite-entity.png)

## <a name="wrap-the-entities-in-the-composite-entity"></a>Zalomení entity v složené entity
Po vytvoření složeného entity popisku zbývající utterances složené entity. K zabalení frázi jako složený entitu, je třeba vybrat slovo nejvíce vlevo a pak vyberte **zalomení v složené entity** ze seznamu, který se zobrazí, pak vyberte slovo nejvíce vpravo a pak vyberte pojmenované složené entity `FlightReservation`. To je rychlý a smooth krok pro výběr rozdělené podle následujících kroků:

1. V utterance `schedule 4 seats from paris to london for april 1`, vyberte na 4 jako číslo předem entity.

    ![Vyberte aplikace word nejvíce vlevo](./media/luis-tutorial-composite-entity/wrap-composite-step-1.png)

2. Vyberte **zalomení v složené entity** ze seznamu, který se zobrazí.

    ![Vyberte wrap ze seznamu](./media/luis-tutorial-composite-entity/wrap-composite-step-2.png)

3. Vyberte slovo nejvíce vpravo. Zelená řádku se zobrazí pod fráze, označující složené entity.

    ![Vyberte aplikace word nejvíce vpravo](./media/luis-tutorial-composite-entity/wrap-composite-step-3.png)

4. Vyberte název složený `FlightReservation` ze seznamu, který se zobrazí.

    ![Vyberte možnost s názvem složené entity](./media/luis-tutorial-composite-entity/wrap-composite-step-4.png)

    Poslední utterance zabalení `London` a `tomorrow` složené entity pomocí stejných pokynů. 

## <a name="train-the-luis-app"></a>Cvičení LEOŠ aplikace
LEOŠ neví o změnách tříd Intent a entit (modelu), dokud není nastavena. 

1. V horní pravé straně LEOŠ webu, vyberte **Train** tlačítko.

    ![Cvičení aplikace](./media/luis-tutorial-composite-entity/train-button.png)

2. Školení je dokončena, když se zobrazí zelená stavového řádku v horní části webu potvrzení úspěšné.

    ![Cvičení úspěšně vytvořila.](./media/luis-tutorial-composite-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace získat adresu URL koncového bodu
Chcete-li získat LEOŠ předpovědi chatbot nebo jiné aplikace, budete muset publikování aplikace. 

1. V horní pravé straně LEOŠ webu, vyberte **publikovat** tlačítko. 

2. Vyberte produkční slot a **publikovat** tlačítko.

    ![Publikování aplikace](./media/luis-tutorial-composite-entity/publish-to-production.png)

3. Publikování bylo dokončeno, když se zobrazí zelená stavového řádku v horní části webu potvrzení úspěšné.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Dotaz na koncový bod s jinou utterance
1. Na **publikovat** vyberte **koncový bod** odkaz ve spodní části stránky. Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v panelu Adresa. 

    ![Vyberte adresu URL koncového bodu](./media/luis-tutorial-composite-entity/publish-select-endpoint.png)

2. Přejděte na konec adresy URL v adrese a zadejte `reserve 3 seats from London to Cairo on Sunday`. Poslední parametr řetězce dotazu je `q`, utterance dotazu. Tento utterance není stejný jako kterákoli ze s popiskem utterances proto je vhodný test a by měla vrátit `BookFlight` záměrné s entitou hierarchické extrahovat.

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

Tato utterance vrátí pole složené entity včetně **flightreservation** objekt se dat extrahovaných.  

## <a name="what-has-this-luis-app-accomplished"></a>Co má provést tato LEOŠ aplikace?
Tato aplikace se ještě dvě záměry a složenou entitu, identifikovány záměr dotazu přirozeného jazyka a vrátí extrahovaná data. 

Vaše chatbot teď má dostatek informací k určení primární akce `BookFlight`a informace o rezervaci v utterance nalezen. 

## <a name="where-is-this-luis-data-used"></a>Kde se používá tato data LEOŠ? 
LEOŠ se provádí s touto žádostí. Volající aplikace, jako je například chatbot, může trvat výsledek topScoringIntent a data z entity, která má-li přejít k dalšímu kroku. LEOŠ neprovádí které programový pracují pro robota nebo volající aplikace. LEOŠ pouze určuje, co je mimo jiné uživatele. 

## <a name="next-steps"></a>Další postup

[Další informace o entitách](luis-concept-entity-types.md). 

<!--References-->
[LUIS]:luis-reference-regions.md#luis-website
[LUIS-regions]:luis-reference-regions.md#publishing-regions
