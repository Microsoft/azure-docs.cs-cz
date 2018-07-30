---
title: Kurz vytvoření aplikace LUIS pro získání přesné shody textu s daty v seznamu – Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak vytvořit jednoduchou aplikaci LUIS využívající záměry a entity seznamu k extrakci dat v tomto rychlém startu.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: v-geberr
ms.openlocfilehash: da0491aae571f085a8cbe3cd372f905202e9db9b
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931061"
---
# <a name="tutorial-4-add-list-entity"></a>Kurz: 4. Přidání entity seznamu
V tomto kurzu vytvoříte aplikaci, která ukazuje, jak získat data odpovídající předdefinovanému seznamu. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Vysvětlení entit seznamu 
> * Vytvoření nové aplikace LUIS pro doménu lidských zdrojů se záměrem MoveEmployee (Přesunutí zaměstnance)
> * Přidání entity seznamu pro extrakci zaměstnance z promluvy
> * Trénování a publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS

Pro účely tohoto článku potřebujete bezplatný účet [LUIS](luis-reference-regions.md#luis-website), abyste mohli vytvořit svou aplikaci LUIS.

## <a name="before-you-begin"></a>Než začnete
Pokud nemáte aplikaci pro lidské zdroje z kurzu k [entitám regulárních výrazů](luis-quickstart-intents-regex-entity.md), [naimportujte](luis-how-to-start-new-app.md#import-new-app) JSON do nové aplikace na webu služby [LUIS](luis-reference-regions.md#luis-website). Aplikaci k importování najdete v úložišti [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-regex-HumanResources.json) na Githubu.

Pokud chcete zachovat původní aplikaci pro lidské zdroje, naklonujte verzi na stránce [Settings](luis-how-to-manage-versions.md#clone-a-version) (Nastavení) a pojmenujte ji `list`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. 

## <a name="purpose-of-the-list-entity"></a>Účel entity seznamu
Tato aplikace predikuje promluvy o přesunutí zaměstnance z jedné budovy do jiné. Tato aplikace používá k extrakci zaměstnance entitu seznamu. Na zaměstnance je možné odkazovat pomocí jména, telefonního čísla, e-mailu nebo čísla amerického federálního sociálního pojištění. 

Entita seznamu může obsahovat mnoho položek se synonymy pro každou položku. U malých až středně velkých společností slouží entita seznamu k extrakci informací o zaměstnanci. 

Název každé položky v kanonickém tvaru je číslo zaměstnance. Příklady synonym v této doméně: 

|Účel synonyma|Hodnota synonyma|
|--|--|
|Název|John W. Smith|
|E-mailová adresa|john.w.smith@mycompany.com|
|Telefonní linka|x12345|
|Číslo osobního mobilního telefonu|425-555-1212|
|Číslo amerického federálního sociálního pojištění|123-45-6789|

Entita seznamu je vhodná pro tento typ dat, když platí následující:

* Hodnoty dat jsou známou sadou.
* Tato sada nepřekračuje maximální [hranice](luis-boundaries.md) aplikace LUIS pro tento typ entity.
* Text v promluvě se přesně shoduje se synonymem. 

Služba LUIS extrahuje informace o zaměstnanci takovým způsobem, že klientská aplikace může vytvořit standardní pokyn k přesunutí zaměstnance.
<!--
## Example utterances
Simple example utterances for a `MoveEmployee` inent:

```
move John W. Smith from B-1234 to H-4452
mv john.w.smith@mycompany from office b-1234 to office h-4452

```
-->

## <a name="add-moveemployee-intent"></a>Přidání záměru MoveEmployee (Přesunutí zaměstnance)

1. Ujistěte se, že je vaše aplikace pro lidské zdroje uvedená v části **Build** (Sestavení) služby LUIS. Do této části můžete přejít výběrem možnosti **Build** (Sestavit) v pravém horním řádku nabídek. 

    [ ![Snímek obrazovky aplikace LUIS se zvýrazněnou možností Build (Sestavit) na pravém horním navigačním panelu](./media/luis-quickstart-intent-and-list-entity/hr-first-image.png)](./media/luis-quickstart-intent-and-list-entity/hr-first-image.png#lightbox)

2. Vyberte **Create new intent** (Vytvořit nový záměr). 

    [ ![Snímek obrazovky se stránkou Intents (Záměry) a zvýrazněným tlačítkem Create new intent (Vytvořit nový záměr)](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-button.png) ](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-button.png#lightbox)

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

## <a name="create-an-employee-list-entity"></a>Vytvoření entity seznamu zaměstnanců
Když teď má záměr **MoveEmployee** (Přesunutí zaměstnance) promluvy, musí služba LUIS porozumět tomu, co je zaměstnanec. 

1. Na levém panelu vyberte **Entities** (Entity).

    [ ![Snímek obrazovky se stránkou záměru se zvýrazněným tlačítkem Entities (Entity) na levém navigačním panelu](./media/luis-quickstart-intent-and-list-entity/hr-select-entity-button.png) ](./media/luis-quickstart-intent-and-list-entity/hr-select-entity-button.png#lightbox)

2. Vyberte **Create new entity** (Vytvořit novou entitu).

    [ ![Snímek obrazovky se stránkou Entities (Entity) a zvýrazněnou možností Create new entity (Vytvořit novou entitu)](./media/luis-quickstart-intent-and-list-entity/hr-create-new-entity-button.png) ](./media/luis-quickstart-intent-and-list-entity/hr-create-new-entity-button.png#lightbox)

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

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS
Služba LUIS nemá informace o změnách záměrů a entit (tedy modelu), dokud se nenatrénuje. 

1. V pravé horní části webu LUIS vyberte tlačítko **Train** (Trénovat).

    ![Trénování aplikace](./media/luis-quickstart-intent-and-list-entity/train-button.png)

2. Trénování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

    ![Trénování proběhlo úspěšně](./media/luis-quickstart-intent-and-list-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu
Abyste mohli využít předpověď služby LUIS v chatbotu nebo jiné aplikaci, musíte aplikaci publikovat. 

1. V pravé horní části webu LUIS vyberte tlačítko **Publish** (Publikovat). 

    [![](media/luis-quickstart-intent-and-list-entity/publish.png "Snímek obrazovky s výběrem tlačítka Publish (Publikovat)")](media/luis-quickstart-intent-and-list-entity/publish.png#lightbox)

2. Vyberte slot Production (Produkční) a tlačítko **Publish** (Publikovat). 

    [![](media/luis-quickstart-intent-and-list-entity/publish-to-production.png "Snímek obrazovky s výběrem tlačítka Publish to production slot (Publikovat do produkčního slotu)")](media/luis-quickstart-intent-and-list-entity/publish-to-production.png#lightbox)

3. Publikování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Odeslání dotazu na koncový bod s jinou promluvou
1. V dolní části stránky **Publish** (Publikovat) vyberte odkaz na **koncový bod**. Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v adresním řádku. 

    [![](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png "Snímek obrazovky s adresou URL koncového bodu na stránce Publish (Publikovat)")](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png#lightbox)

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

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>Kde se v entitě seznamu provádí zpracování přirozeného jazyka? 
Vzhledem k tomu, že entita seznamu hledá přesnou shodu textu, nespoléhá se na zpracování přirozeného jazyka (ani strojové učení). Služba LUIS však využívá zpracování přirozeného jazyka (nebo strojové učení) k výběru správného záměru s nejvyšším skóre. Promluva navíc může být kombinací více než jedné entity nebo dokonce více než jednoho typu entity. V každé promluvě se zpracovávají všechny entity v aplikaci včetně entit zpracování přirozeného jazyka (nebo strojově naučených entit).

## <a name="what-has-this-luis-app-accomplished"></a>Co tato aplikace LUIS udělala?
Tato aplikace extrahovala pomocí entity sezamu správného zaměstnance. 

Váš chatbot má teď dostatek informací k určení primární akce `MoveEmployee` a toho, který zaměstnanec se má přesunout. 

## <a name="where-is-this-luis-data-used"></a>Kde se tato data služby LUIS používají? 
Služba LUIS s tímto požadavkem skončila. Volající aplikace, například chatbot, může převzít výsledek topScoringIntent a data z entity a provést další krok. Služba LUIS neprovádí tuto programovou práci za chatbota ani nevolá aplikaci. Služba LUIS pouze určuje, co je záměrem uživatele. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. V nabídce vlevo nahoře vyberte **My apps** (Moje aplikace). Vyberte tři tečky (***...***) vpravo od názvu aplikace v seznamu aplikací a potom vyberte **Delete** (Odstranit). V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání hierarchické entity do aplikace](luis-quickstart-intent-and-hier-entity.md)

