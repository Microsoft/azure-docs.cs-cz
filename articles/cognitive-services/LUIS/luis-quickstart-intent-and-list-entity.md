---
title: '4. kurz: Shodný text – entita seznamu LUIS'
titleSuffix: Azure Cognitive Services
description: Získejte data, která odpovídají předem definovanému seznamu položek. Každá položka v seznamu může mít synonyma, která také přesně odpovídají.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: a4e294687b6c3ea2ba6ff8003e7a8f1ac69ea639
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425067"
---
# <a name="tutorial-4-extract-exact-text-matches"></a>4. kurz: Extrakce shodného textu
V tomto kurzu se dozvíte, jak získat data která přesně odpovídají předdefinovanému seznamu položek. Ke každé položce v seznamu může existovat seznam synonym. V aplikaci pro lidské zdroje může být zaměstnanec identifikovaný různými klíčovými údaji, jako je jeho jméno, e-mail, telefonní číslo nebo daňové identifikační číslo. 

Aplikace pro lidské zdroje potřebuje zjistit, jaký zaměstnanec se pohybuje z jedné budovy do druhé. Pro promluvu o pohybu zaměstnance zjistí LUIS účel a vybere zaměstnance, aby klientská aplikace pro zaměstnance vytvořila standardní příkaz k přesunu.

Aplikace používá k výběru zaměstnance entitu seznamu. Jak odkaz na zaměstnance je možné použít jméno, telefonní linku, číslo mobilního telefonu, e-mail nebo číslo sociálního pojištění. 

Entita seznamu je vhodná pro tento typ dat, když platí následující:

* Hodnoty dat jsou známou sadou.
* Tato sada nepřekračuje maximální [hranice](luis-boundaries.md) aplikace LUIS pro tento typ entity.
* Text promluvy se přesně shoduje se synonymem nebo názvem v kanonickém tvaru. 

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Používat existující ukázkovou aplikaci
> * Přidání záměru MoveEmployee (Přesunutí zaměstnance)
> * Přidání entity seznamu 
> * Trénování 
> * Publikování
> * Zjistit záměry a entity z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Použití existující aplikace
Pokračujte s aplikací **HumanResources**, kterou jste vytvořili v posledním kurzu. 

Pokud aplikaci HumanResources z předchozího kurzu nemáte, postupujte takto:

1.  Stáhněte si [soubor JSON aplikace](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-regex-HumanResources.json) a uložte si ho.

2. Naimportujte soubor JSON do nové aplikace.

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `list`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL. 


## <a name="moveemployee-intent"></a>Záměr MoveEmployee

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Vyberte **Create new intent** (Vytvořit nový záměr). 

3. V automaticky otevíraném dialogovém okně zadejte `MoveEmployee` a pak vyberte **Done** (Hotovo). 

    ![Snímek obrazovky s dialogovým oknem pro vytvoření nového záměru](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Přidejte do záměru ukázkové promluvy.

    |Ukázkové promluvy|
    |--|
    |move John W. Smith from B-1234 to H-4452 (přesunout Johna W. Smithe z B-1234 do H-4452).|
    |mv john.w.smith@mycompany.com from office b-1234 to office h-4452 (john.w.smith@mycompany.com se přesune z kanceláře b-1234 do kanceláře h-4452).|
    |shift x12345 to h-1234 tomorrow (přestěhujte zítra x12345 do h-1234).|
    |place 425-555-1212 in HH-2345 (umístěte 425-555-1212 do HH-2345).|
    |move 123-45-6789 from A-4321 to J-23456 (přesunout 123-45-6789 z A-4321 do J-23456).|
    |mv Jill Jones from D-2345 to J-23456 (Jill Jonesová se přesune z D-2345 do J-23456).|
    |shift jill-jones@mycompany.com to M-12345 (přestěhujte jill-jones@mycompany.com do M-12345).|
    |x23456 to M-12345 (x23456 do M-12345).|
    |425-555-0000 to h-4452 (425-555-0000 do h-4452)|
    |234-56-7891 to hh-2345 (234 7891 56 do hh 2345)|

    [ ![Snímek obrazovky se stránkou záměru a zvýrazněnými novými promluvami](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png) ](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png#lightbox)

    Nezapomeňte, že hodnoty number a datetimeV2 byly přidány v předchozím kurzu. Pokud budou nalezeny v ukázkových promluvách, budou označeny automaticky.

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="employee-list-entity"></a>Entita seznamu zaměstnanců
Když teď má záměr **MoveEmployee** (Přesunutí zaměstnance) ukázkové promluvy, musí služba LUIS porozumět tomu, co je zaměstnanec. 

Primárním _kanonickým_ názvem každé položky je číslo zaměstnance. Tady jsou příklady synonym ke každému jménu v kanonickém tvaru pro tuto doménu: 

|Účel synonyma|Hodnota synonyma|
|--|--|
|Název|John W. Smith|
|E-mailová adresa|john.w.smith@mycompany.com|
|Telefonní linka|x12345|
|Číslo osobního mobilního telefonu|425-555-1212|
|Číslo amerického federálního sociálního pojištění|123-45-6789|


1. Na levém panelu vyberte **Entities** (Entity).

2. Vyberte **Create new entity** (Vytvořit novou entitu).

3. V automaticky otevíraném dialogovém okně entity zadejte `Employee` jako název entity a **List** (Seznam) jako typ entity. Vyberte **Done** (Hotovo).  

    [![](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png "Snímek obrazovky s dialogovým oknem pro vytváření nové entity")](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png#lightbox)

4. Na stránce entity Employee (Zaměstnanec) zadejte jako novou hodnotu `Employee-24612`.

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "Snímek obrazovky se zadáváním hodnoty")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

5. Pro synonyma zadejte následující hodnoty:

    |Účel synonyma|Hodnota synonyma|
    |--|--|
    |Název|John W. Smith|
    |E-mailová adresa|john.w.smith@mycompany.com|
    |Telefonní linka|x12345|
    |Číslo osobního mobilního telefonu|425-555-1212|
    |Číslo amerického federálního sociálního pojištění|123-45-6789|

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "Snímek obrazovky se zadáváním synonym")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

6. Jako novou hodnotu zadejte `Employee-45612`.

7. Pro synonyma zadejte následující hodnoty:

    |Účel synonyma|Hodnota synonyma|
    |--|--|
    |Název|Jill Jones|
    |E-mailová adresa|jill-jones@mycompany.com|
    |Telefonní linka|x23456|
    |Číslo osobního mobilního telefonu|425-555-0000|
    |Číslo amerického federálního sociálního pojištění|234-56-7891|

## <a name="train"></a>Trénování

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikování

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Získání záměru a entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Na konec adresy URL zadejte `shift 123-45-6789 from Z-1242 to T-54672`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `MoveEmployee` s extrahovanou hodnotou `Employee`.

  ```JSON
  {
    "query": "shift 123-45-6789 from Z-1242 to T-54672",
    "topScoringIntent": {
      "intent": "MoveEmployee",
      "score": 0.9882801
    },
    "intents": [
      {
        "intent": "MoveEmployee",
        "score": 0.9882801
      },
      {
        "intent": "FindForm",
        "score": 0.016044287
      },
      {
        "intent": "GetJobInformation",
        "score": 0.007611245
      },
      {
        "intent": "ApplyForJob",
        "score": 0.007063288
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00684710965
      },
      {
        "intent": "None",
        "score": 0.00304174074
      },
      {
        "intent": "Utilities.Help",
        "score": 0.002981
      },
      {
        "intent": "Utilities.Confirm",
        "score": 0.00212222221
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.00191026414
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0007461446
      }
    ],
    "entities": [
      {
        "entity": "123 - 45 - 6789",
        "type": "Employee",
        "startIndex": 6,
        "endIndex": 16,
        "resolution": {
          "values": [
            "Employee-24612"
          ]
        }
      },
      {
        "entity": "123",
        "type": "builtin.number",
        "startIndex": 6,
        "endIndex": 8,
        "resolution": {
          "value": "123"
        }
      },
      {
        "entity": "45",
        "type": "builtin.number",
        "startIndex": 10,
        "endIndex": 11,
        "resolution": {
          "value": "45"
        }
      },
      {
        "entity": "6789",
        "type": "builtin.number",
        "startIndex": 13,
        "endIndex": 16,
        "resolution": {
          "value": "6789"
        }
      },
      {
        "entity": "-1242",
        "type": "builtin.number",
        "startIndex": 24,
        "endIndex": 28,
        "resolution": {
          "value": "-1242"
        }
      },
      {
        "entity": "-54672",
        "type": "builtin.number",
        "startIndex": 34,
        "endIndex": 39,
        "resolution": {
          "value": "-54672"
        }
      }
    ]
  }
  ```

  Zaměstnanec byl nalezen a vrácen jako typ `Employee` s rozlišovací hodnotou `Employee-24612`.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup
V tomto kurzu jste vytvořili nový záměr, přidali jste ukázkové promluvy a pak jste vytvořili entitu seznamu určenou k extrakci shodného textu ze záznamů. Po natrénování a publikování aplikace jste dotazem adresovaným koncovému bodu zjistili záměr a vrátili extrahovaná data.

> [!div class="nextstepaction"]
> [Přidání hierarchické entity do aplikace](luis-quickstart-intent-and-hier-entity.md)

