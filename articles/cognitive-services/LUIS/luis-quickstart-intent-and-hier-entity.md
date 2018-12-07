---
title: 'Kurz 5: Vztahy nadřazenosti a podřízenosti – hierarchická entita LUIS pro kontextově naučená data'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vyhledáte související části dat na základě kontextu. Spolu například souvisí počáteční a cílová umístění pro fyzický přesun z jedné budovy a kanceláře do jiné.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: diberry
ms.openlocfilehash: 36751f533b59e0ff140f5ad03e7f1fc0fa9cad41
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000569"
---
# <a name="tutorial-5-extract-contextually-related-data"></a>Kurz 5: Extrakce dat souvisejících s kontextem
V tomto kurzu vyhledáte související části dat na základě kontextu. Spolu například souvisí počáteční a cílová umístění pro fyzický přesun z jedné budovy a kanceláře do jiné. Pro vygenerování pracovního zařazení se mohou vyžadovat obě části dat, které spolu vzájemně souvisí.  

Tato aplikace určuje, do jakého cílového umístění (budova a kancelář) se má zaměstnanec přesunout z počátečního místa (budova a kancelář). K určení míst v rámci promluvy se používá hierarchická entita. Účelem **hierarchické** entity je na základě kontextu vyhledat data související s promluvou. 

Hierarchická entita je vhodná pro tento typ dat, protože obě části dat:

* Jsou jednoduché entity.
* Se k sobě v kontextu promluvy navzájem vztahují.
* K označení obou míst používají specifická slova. Mezi příklady těchto slov patří from/to (z/do), leaving/headed to (odstěhovat/nastěhovat), away from/toward (z kanceláře/do kanceláře).
* Obě polohy se často vyskytují ve stejné promluvě. 
* Musí být seskupeny a zpracovány klientskou aplikací jako jediná informace.

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Použít existující ukázkovou aplikaci
> * Přidat záměr 
> * Přidání hierarchické entity polohy s podřízenými entitami počátku a cíle
> * Trénování
> * Publikování
> * Zjistit záměry a entity z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Použití existující aplikace
Pokračujte s aplikací **HumanResources**, kterou jste vytvořili v posledním kurzu. 

Pokud aplikaci HumanResources z předchozího kurzu nemáte, postupujte takto:

1.  Stáhněte si [soubor JSON aplikace](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-list-HumanResources.json) a uložte si ho.

2. Naimportujte soubor JSON do nové aplikace.

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `hier`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL. 

## <a name="remove-prebuilt-number-entity-from-app"></a>Odebrání předem připravené entity čísla z aplikace
Pokud chcete zobrazit celý utterance a označit hierarchické podřízené objekty, [dočasnému odstranění předem připravených entit číslo](luis-prebuilt-entities.md#marking-entities-containing-a-prebuilt-entity-token). 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. V levé nabídce vyberte **Entities** (Entity).

3. V seznamu zaškrtněte políčko vlevo od entity čísla. Vyberte **Odstranit**. 

## <a name="add-utterances-to-moveemployee-intent"></a>Přidání projevů do záměru MoveEmployee

1. V levé nabídce vyberte **Intents** (Záměry).

2. V seznamu záměrů vyberte **MoveEmployee** (Přesunutí zaměstnance).

3. Přidejte následující příklady promluv:

    |Ukázkové promluvy|
    |--|
    |Přesunout John Smithe **do** a-2345.|
    |Poslat Jill Jonesovou **do** b-3499.|
    |Zorganizovat přesun x23456 **z** hh 2345 **do** e-0234.|
    |Začít připravovat podklady, abys se mohl x12345 **odstěhovat** z a-3459 a **nastěhovat** do f-34567.|
    |Přemístit 425-555-0000 **z kanceláře** g-2323 **do kanceláře** hh 2345.|

    [ ![Snímek obrazovky aplikace LUIS s novými promluvami v záměru MoveEmployee (Přesunutí zaměstnance)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

    V kurzu k [entitám seznamu](luis-quickstart-intent-and-list-entity.md) je zaměstnanec určen pomocí jména, e-mailu, čísla telefonní linky, čísla mobilního telefonu a čísla amerického federálního sociálního pojištění. V promluvách se používají tato čísla zaměstnanců. Předchozí příklady promluv obsahují různé způsoby (zobrazené tučně), jak označit počáteční a cílové místo. Dvě z těchto promluv obsahují záměrně jen cílové místo. To pomáhá aplikaci LUIS porozumět tomu, jak jsou tato místa v promluvě umístěna, když není určeno počáteční místo.     

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="create-a-location-entity"></a>Vytvoření entity místa
Služba LUIS potřebuje porozumět tomu, co je místo, tím, že v promluvách označí počáteční a cílové místo. Pokud potřebujete promluvu zobrazit v zobrazení tokenů (nezpracovaných), vyberte přepínač na panelu nad promluvami s popiskem **Entities View** (Zobrazení entit). Po přepnutí přepínače bude mít tento ovládací prvek popisek **Tokens View** (Zobrazení tokenů).

Představte si následující promluvu:

```JSON
mv Jill Jones from a-2349 to b-1298
```

V promluvě jsou určená dvě místa – `a-2349` a `b-1298`. Předpokládejme, že písmeno odpovídá názvu budovy a číslo označuje kancelář v této budově. V tomto případě dává smysl, aby byly obě informace seskupeny jako podřízené prvky hierarchické entity `Locations`, protože obě části dat je potřeba z promluvy extrahovat, aby klientská aplikace mohla žádost dokončit, a navzájem se k sobě vztahují. 
 
Pokud je k dispozici pouze jeden podřízený prvek (počátek nebo cíl) hierarchické entity, přesto se extrahuje. Není potřeba vyhledat všechny podřízené entity, aby se extrahovala jedna nebo několik z nich. 

1. V promluvě `Displace 425-555-0000 away from g-2323 toward hh-2345` vyberte slovo `g-2323`. Zobrazí se rozevírací nabídka s textovým polem v horní části. Zadejte do textového pole název entity `Locations` a pak v rozevírací nabídce vyberte **Create new entity** (Vytvořit novou entitu). 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png "Snímek obrazovky s vytvářením nové entity na stránce záměru")](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png#lightbox)

2. V automaticky otevíraném okně vyberte typ entity **Hierarchical** (Hierarchická) s podřízenými entitami `Origin` a `Destination`. Vyberte **Done** (Hotovo).

    ![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Snímek obrazovky s automaticky otevíraným dialogovým oknem pro novou entitu místa")

3. Popisek pro `g-2323` je označený jako `Locations`, protože služba LUIS neví, jestli je toto slovo počátkem, cílem, nebo ani jedním. Vyberte `g-2323`, pak vyberte **Locations** (Místa) a pak přejděte do nabídky vpravo a vyberte `Origin`.

    [![](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png "Snímek obrazovky s automaticky otevíraným dialogovým oknem označování entit pro změnu podřízeného prvku entity místa")](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png#lightbox)

5. Označte ostatní místa ve všech dalších promluvách tím, že v promluvě vyberete budovu a kancelář, pak vyberete Locations (Místa) a pak v nabídce vpravo vyberete `Origin` (Počátek) nebo `Destination` (Cíl). Až označíte všechna místa, začnou promluvy v zobrazení **Tokens View** (Zobrazení tokenů) vypadat jako vzor. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png "Snímek obrazovky s entitami míst označených v promluvách")](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png#lightbox)

## <a name="add-prebuilt-number-entity-to-app"></a>Přidání předem připravené entity čísla do aplikace
Přidejte předem připravenou entitu čísla zpět do aplikace.

1. V levé navigační nabídce vyberte **Entities** (Entity).

2. Vyberte tlačítko **Add prebuilt entity** (Přidat předem připravenou entitu).

3. V seznamu předem připravených entit vyberte možnost **number** (číslo) a pak vyberte **Done** (Hotovo).

    ![Snímek obrazovky s vybranou možností number (číslo) v dialogovém okně s předem připravenými entitami](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Odeslání dotazu na koncový bod s jinou promluvou

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


2. Přejděte na konec adresy URL v panelu adresy a zadejte `Please relocation jill-jones@mycompany.com from x-2345 to g-23456`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `MoveEmployee` s extrahovanou hierarchickou entitou.

    ```JSON
    {
      "query": "Please relocation jill-jones@mycompany.com from x-2345 to g-23456",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9966052
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9966052
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0325253047
        },
        {
          "intent": "FindForm",
          "score": 0.006137873
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00462633232
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00415637763
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00382325822
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00249120337
        },
        {
          "intent": "None",
          "score": 0.00130756292
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00119622645
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.26910036E-05
        }
      ],
      "entities": [
        {
          "entity": "jill - jones @ mycompany . com",
          "type": "Employee",
          "startIndex": 18,
          "endIndex": 41,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "x - 2345",
          "type": "Locations::Origin",
          "startIndex": 48,
          "endIndex": 53,
          "score": 0.8520272
        },
        {
          "entity": "g - 23456",
          "type": "Locations::Destination",
          "startIndex": 58,
          "endIndex": 64,
          "score": 0.974032
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 49,
          "endIndex": 53,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "-23456",
          "type": "builtin.number",
          "startIndex": 59,
          "endIndex": 64,
          "resolution": {
            "value": "-23456"
          }
        }
      ]
    }
    ```
    
    Správný záměr je predikován a pole entit má hodnoty počátku i cíle v odpovídající vlastnosti **entity**.
    

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>Bylo by možné použít pro každé místo regulární výraz?
Ano, můžete vytvořit entitu regulárního výrazu s rolemi počátečního a cílového místa a používat ji ve vzoru.

Místa v tomto příkladu, jako je třeba `a-1234`, používají specifický formát, který obsahuje jedno nebo dvě písmena, pomlčku a pak řadu 4 nebo 5 číslic. Tato data je možné popsat jako entitu regulárního výrazu s rolí pro každé místo. Role jsou k dispozici pouze u vzorů. Můžete vytvořit vzory založené na těchto promluvách a pak vytvořit regulární výraz pro formát místa a přidat ho do vzorů. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="hierarchical-entities-versus-roles"></a>Hierarchické entity a role

Další informace najdete v tématu věnovaném [porovnání rolí a hierarchických entit](luis-concept-roles.md#roles-versus-hierarchical-entities).

## <a name="next-steps"></a>Další postup
V tomto kurzu jste vytvořili nový záměr a přidali ukázkové promluvy pro kontextově naučená data počátečního a cílového umístění. Jakmile aplikaci vytrénujete a publikujete, klientská aplikace může tyto informace použít k vytvoření lístku přesunu s relevantními informacemi.

> [!div class="nextstepaction"] 
> [Další informace o přidání složené entity](luis-tutorial-composite-entity.md) 
