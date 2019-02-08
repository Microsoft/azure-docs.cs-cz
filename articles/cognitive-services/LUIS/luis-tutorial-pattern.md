---
title: Vzory
titleSuffix: Azure Cognitive Services
description: Použití vzorů ke zvýšení záměru a predikce entity pomocí menšího počtu ukázkových promluv. Vzor je k dispozici jako šablona příkladu promluvy, který obsahuje syntaxi identifikace entit a ignorovatelného textu.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: diberry
ms.openlocfilehash: 3fe549a63f0fb4662ba5beb2e28f1ca72fcc1ee4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55855879"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats"></a>Kurz: Přidejte společný vzor šablony utterance formátů

V tomto kurzu budete požívat vzory ke zvýšení záměru a predikce entity pomocí menšího počtu ukázkových promluv. Vzor je k dispozici jako šablona příkladu promluvy, který obsahuje syntaxi identifikace entit a ignorovatelného textu. Vzor je kombinací párování jazykových výrazů a strojového učení.  Šablona příkladu promluvy spolu se záměrem promluvy umožňují LUIS lépe porozumět tomu, jaké promluvy odpovídají určitému záměru. 

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Importovat ukázková aplikace 
> * Vytvořit záměr
> * Trénování
> * Publikování
> * Zjistit záměry a entity z koncového bodu
> * Vytvořit vzor
> * Ověřit vylepšení predikce vzoru
> * Označit text jako ignorovatelný a vnořit ho do vzoru
> * Používat testovací panel k ověření úspěchu vzoru

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importovat ukázková aplikace

Pokračujte s aplikací **HumanResources**, kterou jste vytvořili v posledním kurzu. 

Použijte k tomu následující postup:

1.  Stáhněte si [soubor JSON aplikace](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json) a uložte si ho.

2. Naimportujte soubor JSON do nové aplikace.

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `patterns`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.

## <a name="create-new-intents-and-their-utterances"></a>Tvorba nových záměrů a jejich promluv

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Na stránce **Intents** (Záměry) vyberte **Create new intent** (Vytvořit nový záměr). 

3. V automaticky otevíraném dialogovém okně zadejte `OrgChart-Manager` a pak vyberte **Done** (Hotovo).

    ![Vytvořte nové automaticky otevírané okno zpráv](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. Přidejte do záměru ukázkové promluvy.

    |Ukázkové promluvy|
    |--|
    |Čí podřízený je Jan Novák?|
    |Komu se Jan Novák zodpovídá?|
    |Kdo je manažer Jana Nováka?|
    |Komu se Jana Nováková přímo zodpovídá?|
    |Kdo je nadřízený Jany Novákové?|

    [![Screenshot LUIS přidává nové promluvy k záměru](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "Screenshot LUIS přidává nové promluvy k záměru")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    Nedělejte si starosti, když je entita keyPhrase označená v promluvách záměru místo v entitě zaměstnance. Obě se správně predikují v testovacím podokně a koncovém bodu. 

5. Na levém navigačním panelu vyberte **Intents** (Záměry).

6. Vyberte **Create new intent** (Vytvořit nový záměr). 

7. V automaticky otevíraném dialogovém okně zadejte `OrgChart-Reports` a pak vyberte **Done** (Hotovo).

8. Přidejte do záměru ukázkové promluvy.

    |Ukázkové promluvy|
    |--|
    |Kdo jsou podřízení Jana Nováka?|
    |Kdo se zodpovídá Janu Novákovi?|
    |Čí manažer je Jan Novák?|
    |Kdo se přímo zodpovídá Janě Novákové?|
    |Čí nadřízená je Jana Nováková?|

## <a name="caution-about-example-utterance-quantity"></a>Upozornění na množství ukázkových promluv

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

## <a name="train"></a>Trénování

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikování

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Získání záměru a entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Na konec adresy URL zadejte `Who is the boss of Jill Jones?`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. 

    ```json
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

Uspěl tento dotaz? Pro potřeby tohoto kurzu uspěl. Skóre prvních dvou záměrů je těsné. Protože trénování LUIS není vždycky stejné a dochází k drobným odchylkám, může se skóre v dalším tréninkovém cyklu obrátit. Výsledkem pak může být vrácení špatného záměru. 

Použitím vzorů výrazně procentuálně zvýšíte skóre správného záměru a zvětšíte tak jeho odstup od druhého nejvyššího skóre. 

Toto druhé okno prohlížeče ponechte otevřené. Použijete je později v průběhu kurzu. 

## <a name="template-utterances"></a>Šablony promluv
Vzhledem k povaze domény oddělení lidských zdrojů existuje několik běžných způsobů, jak se zeptat na zaměstnanecké vztahy v organizaci. Příklad:

|Projevy|
|--|
|Komu se zodpovídá Jana Nováková?|
|Kdo se zodpovídá Janě Novákové?|

Tyto promluvy jsou si příliš blízké na to, aby bylo možné určit jejich obsahovou jedinečnost bez poskytnutí mnoha příkladů promluv. LUIS se učí obecné vzory promluv pro určitý záměr bez dodání většího množství příkladů promluv tak, že přidává vzory k záměru. 

Šablony příkladů promluvy pro tento záměr zahrnují:

|Šablony příkladů promluv|význam syntaxe|
|--|--|
|Komu se zodpovídá {Employee}[?]|interchangeable {Employee}, ignore [?]}|
|Kdo se zodpovídá {Employee}[?]|interchangeable {Employee}, ignore [?]}|

Syntaxe `{Employee}` označí umístění entity v šabloně promluvy a to, o jakou entitu se jedná. Volitelná syntaxe `[?]` označuje slova nebo interpunkci, která jsou volitelná. LUIS páruje promluvy a ignoruje volitelný text v závorkách.

I když syntaxe vypadá jako regulérní výraz, o regulérní výraz to není. Podporovaná je pouze syntaxe se složenými `{}` a hranatými `[]` závorkami. Můžou být vnořené až dvě úrovně.

Entity v promluvě musejí nejdříve souhlasit s entitami v šabloně promluvy, aby se vzor k promluvě přiřadil. Šablona nepomůže predikovat entity, ale jen záměry. 

**Vzory sice umožňují poskytovat méně ukázkových promluv, když ale není detekovaná entita, vzor se nespáruje.**

V tomto kurzu přidejte dva nové záměry: `OrgChart-Manager` a `OrgChart-Reports`. 

|Záměr|Promluva|
|--|--|
|OrgChart-Manager|Komu se zodpovídá Jana Nováková?|
|OrgChart-Reports|Kdo se zodpovídá Janě Novákové?|

Když LUIS vrátí klientské aplikaci predikci, může být název záměru použitý jako název funkce klientské aplikace a entita Zaměstnanec se dá použít jako parametr této funkce.

```javascript
OrgChartManager(employee){
    ///
}
```

Zapamatujte si, že se zaměstnanci vytvořili v [kurzu seznamu entit](luis-quickstart-intent-and-list-entity.md).

1. Vyberte **Sestavení** v horní nabídce.

2. Vyberte **Vzory** v levém navigačním panelu v části **Zvýšení výkonu aplikace**.

3. Vyberte záměr **OrgChart-Manager**, pak zadejte následující šablony promluv:

    |Šablony promluv|
    |:--|
    |Komu je {Employee} podřízený[?]|
    |Komu se zodpovídá {Employee}[?]|
    |Kdo je manažer {Employee}[?]|
    |Komu se {Employee} přímo zodpovídá[?]|
    |Kdo je nadřízený {Employee}[?]|
    |Kdo je šéf {Employee}[?]|

    Entity s rolemi používají syntaxi, která obsahuje název role. Zabývá se jimi [samostatný kurz rolí](luis-tutorial-pattern-roles.md). 

    Když zadáváte šablonu promluvy, pomůže vám LUIS po vložení levé složené závorky `{` doplnit entitu.

    [![Screenshot zadávání šablony promluv záměru](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. Vyberte záměr **OrgChart-Reports**, pak zadejte následující šablony promluv:

    |Šablony promluv|
    |:--|
    |Kdo jsou podřízení {Employee}[?]|
    |Kdo se zodpovídá {Employee}[?]|
    |Čí manažer je {Employee}[?]|
    |Kdo jsou přímí podřízení {Employee}[?]|
    |Či nadřízený je {Employee}[?]|
    |Čí šéf je {Employee}[?]|

## <a name="query-endpoint-when-patterns-are-used"></a>Dotaz koncovému bodu při použití šablon

1. Trénujte a publikujte aplikaci znovu.

2. Přepněte panel prohlížeče zpět na panel webové adresy koncového bodu.

3. Na konec adresy URL zadejte `Who is the boss of Jill Jones?` podle promluvy. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. 

    ```json
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

Predikce záměru je teď výrazně vyšší.

## <a name="working-with-optional-text-and-prebuilt-entities"></a>Práce s volitelným textem a předpřipravenými entitami

Předchozí vzor šablony promluv použitý v tomto kurzu obsahoval několik ukázek volitelného textu`'s`, například použití `?`. Předpokládejme, že promluvy koncového bou ukazují, že manažeři a pracovníci oddělení lidských zdrojů hledají historická data a také v budoucnu plánované přesuny zaměstnanců uvnitř společnosti.

Ukázkové promluvy jsou:

|Záměr|Ukázkové promluvy s volitelným textem a předpřipravenými entitami|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

Každý z těchto příkladů používá slovesný čas (`was`, `is`, `will be`) a datum (`March 3`, `now`, `in a month`), které LUIS potřebuje k tomu, aby predikoval správně. Všimněte si, že poslední dva příklady používají stejný text s výjimkou `in` a `on`.

Ukázková šablona promluv:
|Záměr|Ukázkové promluvy s volitelným textem a předpřipravenými entitami|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

Použití volitelné syntaxe v hranatých závorkách `[]` usnadňuje přidávání volitelného textu do šablony promluvy. Tato syntaxe může být vnořená až na druhou úroveň `[[]]` a obsahovat entity nebo text.

**Otázka: Proč nelze projevy poslední dva příklad zkombinovat do jedné šabloně utterance?** Šablona vzoru nepodporuje syntaxi OR (NEBO). Aby bylo možné zachytit jak verzi `in`, tak verzi `on`, musí být každá z nich v samostatné šabloně promluvy.

**Otázka: Proč jsou všechny `w` písmena, první písmena jednotlivých utterance šablony, malá písmena? Nemělo by být volitelné, jestli budou velká nebo malá?** Promluva, kterou klientská aplikace odesílá koncovému bodu dotazu, se převádí na malá písmena. V šabloně promluvy můžete použít jak malá, tak velká písmena. V promluvě koncového bodu také. Porovnání se provádí vždy až po převodu na malá písmena.

**Otázka: Proč není předem připravených číslo součástí šablony utterance Pokud dne 3 je předpovědět také jako číslo `3` data a času `March 3`?** Šablona promluvy podle kontextu použije datum buď doslova jako `March 3`, nebo abstrahovanou jako `in a month`. Datum sice může obsahovat číslo, ale ne každé číslo musí být nutně datum. Používejte vždy takovou entitu, která nejlépe vystihuje typ požadovaný ve výsledcích JSON predikce.  

**Otázka: A co špatně obsahuje jiné spojení projevy například `Who will {Employee}['s] manager be on March 3?`.** Gramaticky rozdílné slovesné časy, jako tady, kde jsou `will` a `be` oddělené, musejí být v samostatných šablonách promluvy. Existující šablona promluvy se s nimi nespáruje. I když se záměr promluvy nezměnil, změnil se pořádek slov v promluvě. Tato změna ovlivní predikci LUIS.

**Pamatujte: nejdřív se najdou entity, pak se teprve spáruje vzor.**

## <a name="edit-the-existing-pattern-template-utterance"></a>Úprava existujícího vzoru šablony promluvy

1. Na webu LUIS vyberte **Zkompilovat** v horní nabídce, pak vyberte **Vzory** v nabídce vlevo. 

2. Najděte existující šablonu promluvy `Who is {Employee}['s] manager[?]` a vpravo vyberte symbol tří teček (***...***). 

3. Vyberte **Upravit** z místní nabídky. 

4. Změňte šablonu promluvy na `who is {Employee}['s] manager [[on]{datetimeV2}?]]`

## <a name="add-new-pattern-template-utterances"></a>Přidání nových vzorů šablony promluv

1. V sekci **Vzory** nabídky **Zkompilovat** přidejte několik nových vzorů šablon promluv. Vyberte **OrgChart-Manager** v rozevírací nabídce záměru a zadejte následující šablony promluv:

    |Záměr|Ukázkové promluvy s volitelným textem a předpřipravenými entitami|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Trénujte aplikaci.

3. Vyberte **Test** v horní části panelu. Tak otevřete testovací panel. 

4. Zadejte několik testovacích promluv. Ověříte tak, jestli je vzor spárovaný a skóre záměru výrazně vysoké. 

    Po zadání první promluvy vyberte **Zkontrolovat** pod výsledkem. Zobrazíte tak všechny výsledky predikce.

    |Promluva|
    |--|
    |Kdo bude manažerem Jany Novákové|
    |kdo bude manažerem jany novákové|
    |Kdo bude manažerem Jany Novákové?|
    |kdo bude 3. března manažerem Jany novákové|
    |Kdo bude příští Měsíc manažerem Jany Novákové|
    |Kdo bude za měsíc manažerem Jany Novákové?|

Ve všech promluvách jsou entity, proto se všechny shodují se stejným vzorem a mají vysoké skóre predikce.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu přidáme dva záměry promluvám, které bylo bez většího množství ukázkových promluv obtížné predikovat s vysokou přesností. Přidání vzorů pomohlo LUIS lépe predikovat záměr s výrazně vyšším skóre. Označení entit a ignorovatelného textu umožnilo LUIS aplikovat vzor na širší rozmanitost promluv.

> [!div class="nextstepaction"]
> [Naučte se, jak používat role spolu se vzorem](luis-tutorial-pattern-roles.md)
