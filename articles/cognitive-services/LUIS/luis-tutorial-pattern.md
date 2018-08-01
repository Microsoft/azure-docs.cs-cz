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
ms.date: 07/30/2018
ms.author: diberry
ms.openlocfilehash: 355c1edd4fa7433e68a9c0e903f4f782203326fe
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365874"
---
# <a name="tutorial-improve-app-with-patterns"></a>Kurz: Vylepšit aplikaci pomocí vzorů

V tomto kurzu pomocí vzorů zvýšit předpovědi záměr a entity.  

> [!div class="checklist"]
* Jak určit, že vzor by umožňují, aby aplikace
* Postup vytvoření vzoru
* Postup ověření vylepšení predikce modelu

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Než začnete

Pokud nemáte aplikaci lidských zdrojů [dávky testů](luis-tutorial-batch-testing.md) kurzu [importovat](luis-how-to-start-new-app.md#import-new-app) JSON do nové aplikace ve [LUIS](luis-reference-regions.md#luis-website) webu. App k importu se nachází v [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-batchtest-HumanResources.json) úložiště GitHub.

Pokud chcete zachovat původní aplikaci pro lidské zdroje, naklonujte verzi na stránce [Settings](luis-how-to-manage-versions.md#clone-a-version) (Nastavení) a pojmenujte ji `patterns`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Vzory běžných projevy s menším počtem příklady naučit LUIS

Vzhledem k povaze domény lidských zdrojů existuje pár běžné způsoby s žádostí o relacích zaměstnancům v organizacích. Příklad:

|Projevy|
|--|
|Kdo nehlásí Jill Jones do?|
|Kdo ohlásí Jill Jones?|

Tyto projevy jsou příliš zavřít do určit kontextové jedinečnost jednotlivých bez zadání mnoho příkladů utterance. Přidáním vzor záměru LUIS zjišťuje běžné vzory utterance pro záměru bez poskytnutí mnoho příkladů utterance. 

Příklad šablony projevy záměru zahrnují:

|Příklad šablony projevy|
|--|
|Kdo dělá {zaměstnance} zprávu?|
|Kdo ohlásí {zaměstnance}?|

Vzor je k dispozici prostřednictvím utterance příkladu šablony, zahrnující syntaxi k identifikaci entity a textového ignorable. Vzorek je kombinací porovnávání regulárních výrazů a strojové učení.  Příklad utterance šablony, spolu s záměr projevů poskytnout LUIS lepší představu, z jaké projevy přizpůsobit záměr.

V pořadí pro vzor lze porovnat utterance entity v rámci utterance muset nejprve srovnat entity v šabloně utterance. Šablony ale nepomůže, předpovídat entity, pouze záměry. 

**Zatímco vzorky umožňují poskytovat méně příklad projevy, pokud nejsou zjištěny entity, neodpovídá vzoru.**

Mějte na paměti, že zaměstnanci byly vytvořeny [seznam entit kurzu](luis-quickstart-intent-and-list-entity.md).

## <a name="create-new-intents-and-their-utterances"></a>Vytvořit nový záměry a jejich projevy

Přidejte dva nové záměry: `OrgChart-Manager` a `OrgChart-Reports`. Jednou LUIS vrací předpověď na klientskou aplikaci, záměru název lze použít jako název funkce v klientské aplikaci a entitou zaměstnanců může použít jako parametr této funkce.

```Javascript
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

    [![Snímek obrazovky LUIS přidání nové projevy na záměr](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "snímek obrazovky LUIS přidání nové projevy na záměr")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

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

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Odeslání dotazu na koncový bod s jinou promluvou

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

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

Ponechte otevřené, druhý okna prohlížeče. Použijete ji později v tomto kurzu. 

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

    Entity s rolemi použít syntaxi, která obsahuje název role a jsou popsané v [samostatné kurz pro role](luis-tutorial-pattern-roles.md). 

    Volitelné syntaxe `[]`, označí slova nebo interpunkční znaménka, která jsou volitelné. Služba LUIS odpovídá utterance, Nepovinný text v závorkách se ignoruje.

    Pokud zadáte utterance šablony, LUIS pomáhá vyplnění v entitě při zadání levá složená závorka `{`.

    [![Snímek obrazovky zadání projevy šablony pro záměr](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

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

2. Přepněte zpátky na kartě adresy URL koncového bodu záložkách prohlížeče.

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

## <a name="working-with-optional-text-and-prebuilt-entities"></a>Práce s volitelný text, který a předem připravených entit

Předchozí šablona projevy model v tomto kurzu má několik příkladů volitelný text, který třeba přivlastňovacího pádu použití písmena s, `'s`a použijte otazník, `?`. Předpokládejme, že koncový bod projevy ukazují, že správci a zástupci lidských zdrojů hledáte historická data i plánované zaměstnancem přesuny v rámci společnosti, který se uskuteční v budoucnu.

Příklad projevy jsou:

|Záměr|Příklad projevy volitelný text, který a předem připravených entit|
|:--|:--|
|Organizační diagram – správce|`Who was Jill Jones manager on March 3?`|
|Organizační diagram – správce|`Who is Jill Jones manager now?`|
|Organizační diagram – správce|`Who will be Jill Jones manager in a month?`|
|Organizační diagram – správce|`Who will be Jill Jones manager on March 3?`|

Každá z těchto příkladech používá příkaz čas, `was`, `is`, `will be`, a také datum, `March 3`, `now`, a `in a month`, kterou je potřeba správně předpovědět LUIS. Všimněte si, že poslední dva příklady používají téměř stejný text s výjimkou `in` a `on`.

Příklad šablony projevy:
|Záměr|Příklad projevy volitelný text, který a předem připravených entit|
|:--|:--|
|Organizační diagram – správce|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|Organizační diagram – správce|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
|Organizační diagram – správce|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|Organizační diagram – správce|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

Použití volitelné syntaxe hranaté závorky, `[]`, usnadňuje tento volitelný text, který chcete přidat do šablony utterance a mohou být vnořené druhou úroveň pracovních stanic `[[]]`a zahrnovat entity nebo text.

**Otázka: Proč nelze projevy poslední dva příklad zkombinovat do jedné šabloně utterance?** Šablona vzor OR syntaxi nepodporuje. Aby bylo možné zachytit i `in` verze a `on` verze, každý musí být samostatné šablony utterance.

**Otázka: Proč nejsou všechny `w` písmena, první písmena jednotlivých utterance šablony, malá písmena? Jejich volitelně malých a velkých by neměl být?** Utterance odeslal klientská aplikace ke koncovému bodu dotazu, je převeden na malá. Utterance šablony mohou být velká nebo malá písmena a buď může být také utterance koncového bodu. Provádí se porovnání vždy po převodu na malá písmena.

**Otázka: Proč se předem připravených číslo součástí šablony utterance Pokud dne 3 je předpovědět také jako číslo `3` data a času `March 3`?** Šablona utterance kontextově používá data, buď doslova v `March 3` nebo abstrahovanou jako `in a month`. Data mohou obsahovat číslo, ale číslo nemusí nutně považovat za data. Vždy používejte entity, která nejlépe představuje typ, který se mají vracet v predikované výsledky JSON.  

**Otázka: Co špatně obsahuje jiné spojení projevy například `Who will {Employee}['s] manager be on March 3?`.** Rodu gramaticky jiný příkaz například tento kde `will` a `be` jsou oddělené musí být nové šablony utterance. Existující šablony utterance nebude spárujte ji. Zatímco záměr utterance nedošlo ke změně, umístění aplikace word v utterance změnila. Tato změna ovlivní predikce v LUIS.

**Mějte na paměti: entity jsou nalezena jako první, pak je vzorek párován.**

## <a name="edit-the-existing-pattern-template-utterance"></a>Upravit existující šablonu utterance vzor

1. Na webu služby LUIS, vyberte **sestavení** v horní nabídce vyberte **vzory** v levé nabídce. 

2. Najít existující utterance šablony `Who is {Employee}['s] manager[?]`a vyberte tři tečky (***...*** ) na pravé straně. 

3. Vyberte **upravit** v místní nabídce. 

4. Změna utterance šablony pro: `who is {Employee}['s] manager [[on]{datetimeV2}?]]`

## <a name="add-new-pattern-template-utterances"></a>Přidání projevů šablony nový model

1. Pokud máte **vzory** část **sestavení**, přidat několik nových vzorku projevy šablony. Vyberte **organizačního diagramu správce** ze záměru rozevírací nabídce a zadejte všechny následující projevy šablony:

    |Záměr|Příklad projevy volitelný text, který a předem připravených entit|
    |--|--|
    |Organizační diagram – správce|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |Organizační diagram – správce|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
    |Organizační diagram – správce|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |Organizační diagram – správce|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Trénování aplikace.

3. Vyberte **Test** v horní části panelu, čímž otevřete panel testování. 

4. Zadejte několik testů projevy a ověřte, že je vzorek párován a záměru skóre je velmi vysokou. 

    Po zadání první utterance vyberte **zkontrolujte, jestli se** podle výsledku, abyste si mohli zobrazit všechny výsledky předpovědí.

    |Promluva|
    |--|
    |Koho bude správce Jill Jones|
    |koho bude správce jill jones|
    |Koho bude správce Jill Jones?|
    |koho bude správce Jill jones 3. března|
    |Koho bude správce Jill Jones příští měsíc|
    |Koho bude správce Jill Jones v daném měsíci?|

Všechny tyto projevy nalezené entity uvnitř, proto shodují stejný vzor a mají vysokou předpovědi skóre.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o použití rolí pomocí vzoru](luis-tutorial-pattern-roles.md)