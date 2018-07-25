---
title: Kurz použití vzorců k vylepšení predikce služby LUIS – Azure | Dokumentace Microsoftu
titleSuffix: Cognitive Services
description: V tomto kurzu používá vzor pro příkazy k vylepšení predikce služby LUIS záměr a entity.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/20/2018
ms.author: diberry
ms.openlocfilehash: 9ad1d9e1543c3d9a74025fb23bd1767478b53b4b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238450"
---
# <a name="tutorial-improve-app-with-patterns"></a>Kurz: Vylepšit aplikaci pomocí vzorů

V tomto kurzu pomocí vzorů zvýšit předpovědi záměr a entity.  

> [!div class="checklist"]
* Jak určit, že vzor by umožňují, aby aplikace
* Postup vytvoření vzoru 
* Postup ověření vylepšení predikce modelu

Pro účely tohoto článku potřebujete bezplatný účet [LUIS](luis-reference-regions.md), abyste mohli vytvořit svou aplikaci LUIS.

## <a name="before-you-begin"></a>Než začnete
Pokud nemáte aplikaci lidských zdrojů [dávky testů](luis-tutorial-batch-testing.md) kurzu [importovat](luis-how-to-start-new-app.md#import-new-app) JSON do nové aplikace ve [LUIS](luis-reference-regions.md#luis-website) webu. App k importu se nachází v [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-batchtest-HumanResources.json) úložiště GitHub.

Pokud chcete zachovat původní aplikaci pro lidské zdroje, naklonujte verzi na stránce [Settings](luis-how-to-manage-versions.md#clone-a-version) (Nastavení) a pojmenujte ji `patterns`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Vzory běžných projevy s menším počtem příklady naučit LUIS
Vzhledem k povaze domény lidských zdrojů existuje pár běžné způsoby s žádostí o relacích zaměstnancům v organizacích. Příklad:

```
Who does Jill Jones report to?
Who reports to Jill Jones? 
```

Tyto projevy jsou příliš zavřít do určit kontextové jedinečnost jednotlivých bez zadání mnoho příkladů utterance. Přidáním vzor záměru LUIS zjišťuje běžné vzory utterance pro záměru bez poskytnutí mnoho příkladů utterance. 

Příklad šablony projevy záměru zahrnují:

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

Vzor je k dispozici prostřednictvím utterance příkladu šablony, zahrnující syntaxi k identifikaci entity a textového ignorable. Vzorek je kombinací porovnávání regulárních výrazů a strojové učení.  Příklad utterance šablony, spolu s záměr projevů poskytnout LUIS lepší představu, z jaké projevy přizpůsobit záměr.

V pořadí pro vzor lze porovnat utterance entity v rámci utterance muset nejprve srovnat entity v šabloně utterance. Šablony ale nepomůže, předpovídat entity, pouze záměry. 

**Zatímco vzorky umožňují poskytovat méně příklad projevy, pokud nejsou zjištěny entity, neodpovídá vzoru.**

Mějte na paměti, že zaměstnanci byly vytvořeny [seznam entit kurzu](luis-quickstart-intent-and-list-entity.md).

## <a name="create-new-intents-and-their-utterances"></a>Vytvořit nový záměry a jejich projevy
Přidejte dva nové záměry: `OrgChart-Manager` a `OrgChart-Reports`. Jednou LUIS vrací předpověď na klientskou aplikaci, záměru název lze použít jako název funkce v klientské aplikaci a entitou zaměstnanců může použít jako parametr této funkce.

```
OrgChart-Manager(employee){
    ///
}
```

1. Ujistěte se, že je vaše aplikace pro lidské zdroje uvedená v části **Build** (Sestavení) služby LUIS. Do této části můžete přejít výběrem možnosti **Build** (Sestavit) v pravém horním řádku nabídek. 

2. Na stránce **Intents** (Záměry) vyberte **Create new intent** (Vytvořit nový záměr). 

3. V automaticky otevíraném dialogovém okně zadejte `OrgChart-Manager` a pak vyberte **Done** (Hotovo).

    ![Vytvoření automaticky otevírané okno nové zprávy](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. Přidejte do záměru ukázkové promluvy.

    |Ukázkové promluvy|
    |--|
    |Kdo je podřízenou položkou tohoto Jan Macek W.?|
    |Kdo Jan Macek W. nahlásit?|
    |Kdo je správce Macek Jan W.?|
    |Kdo Jill Jones přímo nahlásit?|
    |Kdo je správce Jill Jones?|

    [![](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "Snímek obrazovky LUIS přidání nové projevy na záměr")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    Nedělejte si starosti, pokud entita keyPhrase označen v projevy záměr místo entity zaměstnance. Obě jsou správně předpovědět, v podokně testu a na koncovém bodu. 

5. Na levém navigačním panelu vyberte **Intents** (Záměry).

6. Vyberte **Create new intent** (Vytvořit nový záměr). 

7. V automaticky otevíraném dialogovém okně zadejte `OrgChart-Reports` a pak vyberte **Done** (Hotovo).

8. Přidejte do záměru ukázkové promluvy.

    |Ukázkové promluvy|
    |--|
    |Kdo jsou podřízené Macek Jan W.?|
    |Kdo ohlásí Jan Macek W.?|
    |Tím, kdo Jan Macek W. spravovat?|
    |Přímí podřízení Jill Jones, kteří jsou?|
    |Kdo Jill Jones dohled nad?|

## <a name="caution-about-example-utterance-quantity"></a>Upozornění o příklad utterance množství
Množství projevy příklad v tyto záměry není dostatečně tak moct trénovat LUIS správně. V reálné aplikaci každý záměr by měl mít aspoň 15 projevy s širokou škálu možností a utterance Délka slova. Těchto několik projevy vybraných speciálně pro zvýraznění vzory. 

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS
Nové záměr a projevy vyžadují školení. 

1. V pravé horní části webu LUIS vyberte tlačítko **Train** (Trénovat).

    ![Obrázek tlačítka pro trénování](./media/luis-tutorial-pattern/hr-train-button.png)

2. Trénování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

    ![Obrázek pruhu s oznámením o úspěchu](./media/luis-tutorial-pattern/hr-trained-inline.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu
Abyste mohli využít předpověď služby LUIS v chatbotu nebo jiné aplikaci, musíte aplikaci publikovat. 

1. V pravé horní části webu LUIS vyberte tlačítko **Publish** (Publikovat). 

2. Vyberte slot Production (Produkční) a tlačítko **Publish** (Publikovat).

    [ ![Snímek obrazovky publikovat stránku ve funkci Publikovat na produkční slot tlačítkem](./media/luis-tutorial-pattern/hr-publish-to-production.png)](./media/luis-tutorial-pattern/hr-publish-to-production.png#lightbox)

3. Publikování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Odeslání dotazu na koncový bod s jinou promluvou
1. V dolní části stránky **Publish** (Publikovat) vyberte odkaz na **koncový bod**. Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v adresním řádku. 

    [ ![Snímek obrazovky publikování stránek se zvýrazněnou adresou URL koncového bodu](./media/luis-tutorial-pattern/hr-publish-select-endpoint.png)](./media/luis-tutorial-pattern/hr-publish-select-endpoint.png#lightbox)


2. Na konec adresy URL zadejte `Who is the boss of Jill Jones?`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. 

    ```JSON
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.353984952
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.353984952
            },
            {
                "intent": "OrgChart-Reports",
                "score": 0.214128986
            },
            {
                "intent": "EmployeeFeedback",
                "score": 0.08434003
            },
            {
                "intent": "MoveEmployee",
                "score": 0.019131
            },
            {
                "intent": "GetJobInformation",
                "score": 0.004819009
            },
            {
                "intent": "Utilities.Confirm",
                "score": 0.0043958663
            },
            {
                "intent": "Utilities.StartOver",
                "score": 0.00312064588
            },
            {
                "intent": "Utilities.Cancel",
                "score": 0.002265454
            },
            {
                "intent": "Utilities.Help",
                "score": 0.00133465114
            },
            {
                "intent": "None",
                "score": 0.0011388344
            },
            {
                "intent": "Utilities.Stop",
                "score": 0.00111166481
            },
            {
                "intent": "FindForm",
                "score": 0.0008900076
            },
            {
                "intent": "ApplyForJob",
                "score": 0.0007836131
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                }
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

Nezdařilo se tento dotaz? Pro tento cyklus školení úspěšná. Skóre dva hlavní záměry se zavřít. Protože služba LUIS školení je přesně stejné pokaždé, když se trochu variace, může tyto dva výsledky Invertovat v příštím cyklu školení. Výsledkem je, aby mohl být vrácen chybný záměr. 

Vzory používejte k zajištění správného záměr skóre výrazně vyšší v procentech a nastavit další nejvyšším skóre. 

## <a name="add-the-template-utterances"></a>Přidání projevů šablony

1. Vyberte **sestavení** v horní nabídce.

2. V levém navigačním panelu v části **zvýšit výkon aplikace**vyberte **vzory** z levé navigace.

3. Vyberte **organizačního diagramu správce** záměr, pak zadejte následující projevy šablony, jeden po druhém, vyberete zadejte po každé šablony utterance:

    |Projevy šablony|
    |:--|
    |Kdo je podřízenou položkou tohoto [?] {zaměstnance}|
    |Kdo dělá {zaměstnance} hlásit [?]|
    |Kdo je správce {zaměstnance} [v] [?]|
    |Kdo dělá {zaměstnance} přímo nahlásit [?]|
    |Kdo je {} [v] vedoucího. [?]|
    |Kdo je nadřízeného {zaměstnance} [?]|

    `{Employee}` Syntaxe označí entitu umístění v rámci šablony utterance jako i které entitě je. 
    
    Entity s rolemi používají syntaxi, která obsahuje název role a jsou popsané v [samostatné kurz pro role](luis-tutorial-pattern-roles.md). 

    Volitelné syntaxe `[]`, označí slova nebo interpunkční znaménka, která jsou volitelné. Služba LUIS odpovídá utterance, Nepovinný text v závorkách se ignoruje.

    Pokud zadáte utterance šablony, LUIS pomáhá vyplnění v entitě při zadání levá složená závorka `{`.

    [ ![Snímek obrazovky zadání projevy šablony pro záměr](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)



4. Vyberte **organizačního diagramu sestavy** záměr, pak zadejte následující projevy šablony, jeden po druhém, vyberete zadejte po každé šablony utterance:

    |Projevy šablony|
    |:--|
    |Kdo zaměstnancem {} [v] podřízené [?]|
    |Kdo ohlásí {zaměstnance} [?]|
    |Kdo dělá {zaměstnance} spravovat [?]|
    |Kdo jsou přímí podřízení {zaměstnance} [?]|
    |Kdo dělá {zaměstnance} dohled nad [?]|
    |Kdo dělá {zaměstnance} nadřízeného [?]|

## <a name="query-endpoint-when-patterns-are-used"></a>Vzorky se používají dotazu koncového bodu

1. Trénování a publikování aplikace znovu.

2. V dolní části stránky **Publish** (Publikovat) vyberte odkaz na **koncový bod**. Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v adresním řádku. 

3. Přejděte na konec adresy URL do pole adresy a zadejte `Who is the boss of Jill Jones?` jako utterance. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. 

    ```JSON
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.9999989
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.9999989
            },
            {
                "intent": "OrgChart-Reports",
                "score": 7.616303E-05
            },
            {
                "intent": "EmployeeFeedback",
                "score": 7.84204349E-06
            },
            {
                "intent": "GetJobInformation",
                "score": 1.20674213E-06
            },
            {
                "intent": "MoveEmployee",
                "score": 7.91245157E-07
            },
            {
                "intent": "None",
                "score": 3.875E-09
            },
            {
                "intent": "Utilities.StartOver",
                "score": 1.49E-09
            },
            {
                "intent": "Utilities.Confirm",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Help",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Stop",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Cancel",
                "score": 1.225E-09
            },
            {
                "intent": "FindForm",
                "score": 1.123077E-09
            },
            {
                "intent": "ApplyForJob",
                "score": 5.625E-10
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                },
                "role": ""
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

Nyní je výrazně vyšší záměru předpovědi. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. Uděláte to tak, vyberte tři tečky (***...*** ) napravo od názvu aplikace v seznamu aplikací vyberte **odstranit**. V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o použití rolí pomocí vzoru](luis-tutorial-pattern-roles.md)