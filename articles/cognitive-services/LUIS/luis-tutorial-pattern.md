---
title: 'Kurz: vzory – LUIS'
description: Pomocí vzorů můžete zvýšit záměr a předpověď entit a v tomto kurzu poskytnout méně ukázkového projevy. Vzor je k dispozici jako příklad šablony utterance, který obsahuje syntaxi pro identifikaci entit a ignorovatelné texty.
ms.topic: tutorial
ms.date: 04/14/2020
ms.openlocfilehash: 826334fafd04a6357f529b1dc07408ff1c15ce5c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81380767"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>Kurz: Přidání běžných formátů šablon vzorků utterance ke zlepšení předpovědi

V tomto kurzu použijete vzory ke zvýšení záměru a předpovědi entit, což vám umožní poskytnout méně ukázkového projevy. Vzor je šablona utterance přiřazená k záměru, který obsahuje syntaxi k identifikaci entit a ignorovatelné texty.

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Vytvořit vzor
> * Ověřit vylepšení predikce vzoru
> * Označit text jako ignorovatelný a vnořit ho do vzoru
> * Používat testovací panel k ověření úspěchu vzoru

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>Projevy v záměru a vzorci

V aplikaci LUIS jsou uložené dva typy projevy:

* Příklad projevy v záměru
* Šablona projevy ve vzoru

Přidání šablony projevy jako vzor umožňuje poskytnout méně ukázkového projevy jako celku záměru.

Vzor se použije jako kombinace porovnávání textu a strojového učení.  Šablonu utterance ve vzorci společně s příkladem projevy v záměru dejte LUIS lepší znalosti o tom, co projevy přizpůsobení záměru.

## <a name="import-example-app-and-clone-to-new-version"></a>Importovat ukázkovou aplikaci a klonovat ji do nové verze

Použijte k tomu následující postup:

1.  Stáhněte a uložte [soubor JSON aplikace](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json?raw=true).

1. Importujte JSON do nové aplikace na [portál Luis verze Preview](https://preview.luis.ai). Na stránce **Moje aplikace** vyberte **+ Nová aplikace pro konverzaci**a pak vyberte **importovat jako JSON**. Vyberte soubor, který jste stáhli v předchozím kroku.

1. V části **Správa** na kartě **verze** vyberte aktivní verze a pak vyberte **klonovat**. Pojmenujte klonovaných `patterns`verzí. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.

## <a name="create-new-intents-and-their-utterances"></a>Tvorba nových záměrů a jejich promluv

Tyto dva záměry naleznou vedoucí nebo přímé sestavy vedoucí na základě textu utterance. Obtížnost je, že dva záměry _znamenají_ různé věci, ale většina slov je stejná. Liší se pouze pořadí slov. Aby byl záměr vypovídat správně, musel by mít mnoho příkladů.

1. Na navigačním panelu vyberte **sestavení** .

1. Na stránce **záměry** vyberte **+ vytvořit** a vytvořte nový záměr.

1. V automaticky otevíraném dialogovém okně zadejte `OrgChart-Manager` a pak vyberte **Done** (Hotovo).

    ![Vytvořte nové automaticky otevírané okno zpráv](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. Přidejte do záměru ukázkové promluvy. Tyto projevy nejsou _přesně_ stejné, ale mají vzor, který lze extrahovat.

    |Ukázkové promluvy|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

    Nedělejte si starosti, když je entita keyPhrase označená v promluvách záměru místo v entitě zaměstnance. Obě se správně predikují v testovacím podokně a koncovém bodu.

1. Na levém navigačním panelu vyberte **Intents** (Záměry).

1. Vyberte **+ vytvořit** pro vytvoření nového záměru. V automaticky otevíraném dialogovém okně zadejte `OrgChart-Reports` a pak vyberte **Done** (Hotovo).

1. Přidejte do záměru ukázkové promluvy.

    |Ukázkové promluvy|
    |--|
    |`Who are John W. Smith's subordinates?`|
    |`Who reports to John W. Smith?`|
    |`Who does John W. Smith manage?`|
    |`Who are Jill Jones direct reports?`|
    |`Who does Jill Jones supervise?`|

### <a name="caution-about-example-utterance-quantity"></a>Upozornění na množství ukázkových promluv

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

### <a name="train-the-app-before-testing-or-publishing"></a>Výuka aplikace před testováním nebo publikováním

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

### <a name="publish-the-app-to-query-from-the-endpoint"></a>Publikování aplikace pro dotaz z koncového bodu

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

### <a name="get-intent-and-entities-from-endpoint"></a>Získání záměru a entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. V adresním řádku přejde na konec adresy URL a nahraďte _YOUR_QUERY_HERE_ : `Who is the boss of Jill Jones?`.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.313054234
                },
                "OrgChart-Reports": {
                    "score": 0.2462688
                },
                "EmployeeFeedback": {
                    "score": 0.0488328524
                },
                "GetJobInformation": {
                    "score": 0.0156933
                },
                "MoveEmployee": {
                    "score": 0.011265873
                },
                "Utilities.StartOver": {
                    "score": 0.003065792
                },
                "Utilities.Stop": {
                    "score": 0.00300148362
                },
                "Utilities.Cancel": {
                    "score": 0.00271081156
                },
                "None": {
                    "score": 0.00212835032
                },
                "ApplyForJob": {
                    "score": 0.0020669254
                },
                "Utilities.Confirm": {
                    "score": 0.00200891262
                },
                "FindForm": {
                    "score": 0.00194145238
                },
                "Utilities.Help": {
                    "score": 0.00182301877
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "Employee": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "Employee": [
                        {
                            "type": "Employee",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

Skóre obou nejvyšších záměrů je blízko, ale nejvyšší záměr není významně vysoký (více než 60%). a ještě mnohem větší než skóre dalšího záměru.

Vzhledem k tomu, že školení LUIS není přesně stejné, pokaždé, když (existuje bitová variace), může tato horní dvě skóre v dalším školicím cyklu Invertovat. Výsledkem pak může být vrácení špatného záměru.

Použitím vzorů výrazně procentuálně zvýšíte skóre správného záměru a zvětšíte tak jeho odstup od druhého nejvyššího skóre.

Toto druhé okno prohlížeče ponechte otevřené. Použijete je později v průběhu kurzu.

## <a name="template-utterances"></a>Šablony promluv
Vzhledem k povaze domény subjektu lidských zdrojů existuje několik běžných způsobů, jak v organizacích zeptat se na vztahy zaměstnanců. Příklad:

|Projevy|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Tyto promluvy jsou si příliš blízké na to, aby bylo možné určit jejich obsahovou jedinečnost bez poskytnutí mnoha příkladů promluv. LUIS se učí obecné vzory promluv pro určitý záměr bez dodání většího množství příkladů promluv tak, že přidává vzory k záměru.

Šablony příkladů promluvy pro tento záměr zahrnují:

|Šablony příkladů promluv|význam syntaxe|
|--|--|
|`Who does {Employee} report to[?]`|zaměnitelné`{Employee}`<br>ohled`[?]`|
|`Who reports to {Employee}[?]`|zaměnitelné`{Employee}`<br>ohled`[?]`|

Syntaxe `{Employee}` označí umístění entity v šabloně promluvy a to, o jakou entitu se jedná. Volitelná syntaxe, `[?]`, označuje slova nebo [interpunkční znaménka](luis-reference-application-settings.md#punctuation-normalization) , která jsou volitelná. LUIS páruje promluvy a ignoruje volitelný text v závorkách.

I když syntaxe vypadá jako regulární výraz, nejedná se o regulární výraz. Podporovaná je pouze syntaxe se složenými `{}` a hranatými `[]` závorkami. Můžou být vnořené až dvě úrovně.

Aby se model shodoval s utterance, musí _nejdřív_ entity v rámci utterance odpovídat entitám v šabloně utterance. To znamená, že entity musí mít dostatek příkladů v příkladu projevy s vysokým stupněm předpovědi před tím, než jsou vzory s entitami úspěšné. Šablona nepomůže predikovat entity, ale jen záměry.

**Vzory sice umožňují poskytovat méně ukázkových promluv, když ale není detekovaná entita, vzor se nespáruje.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Přidejte vzory pro záměr vedoucího organizačního diagramu

1. Vyberte **Sestavení** v horní nabídce.

1. Vyberte **Vzory** v levém navigačním panelu v části **Zvýšení výkonu aplikace**.

1. Vyberte záměr **OrgChart-Manager**, pak zadejte následující šablony promluv:

    |Šablony promluv|
    |:--|
    |`Who is {Employee} the subordinate of[?]`|
    |`Who does {Employee} report to[?]`|
    |`Who is {Employee}['s] manager[?]`|
    |`Who does {Employee} directly report to[?]`|
    |`Who is {Employee}['s] supervisor[?]`|
    |`Who is the boss of {Employee}[?]`|

    Tyto šablony projevy obsahují entitu **Zaměstnanec** se zápisem do složených závorek.

1. Pořád na stránce vzory vyberte záměr **organizačního diagramu – sestavy** a potom zadejte následující šablonu projevy:

    |Šablony promluv|
    |:--|
    |`Who are {Employee}['s] subordinates[?]`|
    |`Who reports to {Employee}[?]`|
    |`Who does {Employee} manage[?]`|
    |`Who are {Employee} direct reports[?]`|
    |`Who does {Employee} supervise[?]`|
    |`Who does {Employee} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Dotaz koncovému bodu při použití šablon

Teď, když se do aplikace přidávají vzory, můžete ji publikovat a dotazovat na koncový bod předpovědi za běhu.

1. Vyberte **vlak**. Po dokončení školení vyberte **publikovat** a vyberte **produkční** slot a potom vyberte **Hotovo**.

1. Po dokončení publikování přepněte karty prohlížeče zpátky na kartu Adresa URL koncového bodu.

1. V adresním řádku pokračujte na konec adresy URL a nahraďte _YOUR_QUERY_HERE_ :`Who is the boss of Jill Jones?`

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999997854
                },
                "OrgChart-Reports": {
                    "score": 6.13748343E-05
                },
                "EmployeeFeedback": {
                    "score": 8.052567E-06
                },
                "GetJobInformation": {
                    "score": 1.18197136E-06
                },
                "MoveEmployee": {
                    "score": 7.65549657E-07
                },
                "None": {
                    "score": 3.975E-09
                },
                "Utilities.StartOver": {
                    "score": 1.53E-09
                },
                "Utilities.Confirm": {
                    "score": 1.38181822E-09
                },
                "Utilities.Help": {
                    "score": 1.38181822E-09
                },
                "Utilities.Stop": {
                    "score": 1.38181822E-09
                },
                "Utilities.Cancel": {
                    "score": 1.25833333E-09
                },
                "FindForm": {
                    "score": 1.15384613E-09
                },
                "ApplyForJob": {
                    "score": 5.26923061E-10
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "Employee": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "Employee": [
                        {
                            "type": "Employee",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

Předpověď záměrů je teď významně spolehlivější a hodnocení dalšího nejvyššího záměru je výrazně nižší. Tyto dva záměry se při výuce Překlopí.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Práce s volitelným textem a předpřipravenými entitami

Předchozí vzor šablony promluv použitý v tomto kurzu obsahoval několik ukázek volitelného textu`'s`, například použití `?`. Předpokládejme, že je třeba pro aktuální a budoucí kalendářní data v utterance textu.

Ukázkové promluvy jsou:

|Záměr|Ukázkové promluvy s volitelným textem a předpřipravenými entitami|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

Každý z těchto příkladů používá slovesný čas (`was`, `is`, `will be`) a datum (`March 3`, `now`, `in a month`), které LUIS potřebuje k tomu, aby predikoval správně. Všimněte si, že poslední dva příklady v tabulce používají skoro stejný text s výjimkou `in` a `on`.

Příklad šablony projevy, která umožňuje tyto volitelné informace:

|Záměr|Ukázkové promluvy s volitelným textem a předpřipravenými entitami|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


Použití volitelné syntaxe v hranatých závorkách `[]` usnadňuje přidávání volitelného textu do šablony promluvy. Tato syntaxe může být vnořená až na druhou úroveň `[[]]` a obsahovat entity nebo text.


**Otázka: Proč se jedná o `w` všechna písmena, první písmeno v každé šabloně utterance malá a velká písmena? Neměla by být volitelně velká nebo malá?** Promluva, kterou klientská aplikace odesílá koncovému bodu dotazu, se převádí na malá písmena. V šabloně promluvy můžete použít jak malá, tak velká písmena. V promluvě koncového bodu také. Porovnání se provádí vždy až po převodu na malá písmena.

**Otázka: Když se 3. březen predikuje jako číslo `3` i jako datum `March 3`, proč nejsou předpřipravená čísla součástí šablony promluvy?** Šablona promluvy podle kontextu použije datum buď doslova jako `March 3`, nebo abstrahovanou jako `in a month`. Datum sice může obsahovat číslo, ale ne každé číslo musí být nutně datum. Používejte vždy takovou entitu, která nejlépe vystihuje typ požadovaný ve výsledcích JSON predikce.

**Otázka: Co chabě formulované promluvy, jako třeba `Who will {Employee}['s] manager be on March 3?`.** Gramaticky rozdílné slovesné časy, jako tady, kde jsou `will` a `be` oddělené, musejí být v samostatných šablonách promluvy. Existující šablona promluvy se s nimi nespáruje. I když se záměr promluvy nezměnil, změnil se pořádek slov v promluvě. Tato změna ovlivní predikci LUIS. K kombinování těchto projevy můžete [Seskupit a nebo](#use-the-or-operator-and-groups) příkaz-časů.

**Pamatujte: nejdřív se najdou entity, pak se teprve spáruje vzor.**

### <a name="add-new-pattern-template-utterances"></a>Přidání nových vzorů šablony promluv

1. V sekci **Vzory** nabídky **Zkompilovat** přidejte několik nových vzorů šablon promluv. Vyberte **OrgChart-Manager** v rozevírací nabídce záměru a zadejte následující šablony promluv:

    |Záměr|Ukázkové promluvy s volitelným textem a předpřipravenými entitami|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Pro výuku aplikace vyberte v navigačním panelu možnost **vlak** .

3. Po dokončení školení vyberte v horní části panelu **test** a otevřete panel testování.

4. Zadejte několik testovacích promluv. Ověříte tak, jestli je vzor spárovaný a skóre záměru výrazně vysoké.

    Po zadání první promluvy vyberte **Zkontrolovat** pod výsledkem. Zobrazíte tak všechny výsledky predikce. Každý utterance by měl mít záměr **vedoucího organizačního diagramu** a měl by extrahovat hodnoty pro entity Employee a datetimeV2.

    |Promluva|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

Ve všech promluvách jsou entity, proto se všechny shodují se stejným vzorem a mají vysoké skóre predikce. Přidali jste několik vzorů, které budou odpovídat mnoha variacím projevy. Nemuseli byste do tohoto záměru přidat žádný příklad projevy, aby šablona utterance pracovala ve vzorci.

Toto použití zadaných vzorů:
* Skóre vyšší předpovědi
* Se stejným příkladem projevy v záměru
* Ve vzorci stačí jenom pár dobře konstruovaných šablon projevy.

### <a name="use-the-or-operator-and-groups"></a>Použití operátoru nebo a skupin

Některé z předchozích šablon projevy jsou velmi blízko. Pomocí **skupiny** `()` a **nebo** `|` syntaxe zmenšete projevy šablony.

Následující dva vzory se dají zkombinovat do jednoho vzoru pomocí syntaxe Group `()` a nebo. `|`

|Záměr|Ukázkové promluvy s volitelným textem a předpřipravenými entitami|
|--|--|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

Nová šablona utterance bude:

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`.

To používá **skupinu** kolem požadovaného příkazu vhodné a `in` volitelnou a `on` s kanálem **nebo** mezi nimi.

1. Na stránce **vzory** vyberte filtr **správce organizačního diagramu** . Zužte seznam hledáním `manager`.

1. Ponechte jednu verzi šablony utterance (pro úpravy v dalším kroku) a odstraňte ostatní variace.

1. Změňte šablonu promluvy na 

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`

2. Pro výuku aplikace vyberte v navigačním panelu možnost **vlak** .

3. Po dokončení školení vyberte v horní části panelu **test** a otevřete panel testování.

    Použijte testovací podokno k testování verzí utterance:

    |Projevy pro vstup do podokna testu|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

Pomocí další syntaxe vzorů snižujete počet projevy šablon, které je třeba udržovat ve vaší aplikaci, a přitom stále máte skóre s vysokou předpověďí.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>Použití počátečních a koncových kotev utterance

Syntaxe vzoru poskytuje počáteční a koncovou syntaxi kotvy utterance blikajícího kurzoru `^`. Úvodní a koncové kotvy utterance lze použít společně pro cílení na velmi konkrétní a případně literální utterance nebo pro cílové záměry samostatně.

## <a name="using-patternany-entity"></a>Pomocí vzoru. kterákoli entita

Entita Pattern.any umožňuje vyhledat data volného tvaru, kde je kvůli formulaci entity obtížné odlišit konec entity od zbytku promluvy.

Tato aplikace pro lidské zdroje pomáhá zaměstnancům najít formuláře společnosti.

|Promluva|
|--|
|Where is **HRF-123456**? (Kde je HRF-123456?)|
|Who authored **HRF 123234**? (Kdo vytvořil HRF 123234?)|
|**HRF-456098** is published in French? (Je HRF-456098 ve francouzštině?)|

Každý formulář však má formátovaný název použitý v předchozí tabulce i popisný název, jako je například `Request relocation from employee new to the company 2018 version 5`.

Projevy s popisnými názvy formulářů vypadají takto:

|Promluva|
|--|
|Where is **Request relocation from employee new to the company 2018 version 5**? (Kde je Žádost o přeložení nového zaměstnance společnosti z roku 2018 verze 5?)|
|Who authored **Request relocation from employee new to the company 2018 version 5**? (Kdo vytvořil Žádost o přeložení nového zaměstnance společnosti z roku 2018 verze 5?)|
|**Request relocation from employee new to the company 2018 version 5**? (Je Žádost o přeložení nového zaměstnance společnosti z roku 2018 verze 5 ve francouzštině?)|

Různá délka promluv zahrnuje slova, která můžou službě LUIS komplikovat určení konce entity. Když ve vzoru použijete entitu Pattern.any, můžete určit začátek a konec názvu formuláře, aby služba LUIS mohla správně extrahovat název formuláře.

|Příklad šablony promluvy|
|--|
|Where is {FormName}[?] (Kde je {FormName}[?])|
|Who authored {FormName}[?] (Kdo vytvořil {FormName}[?])|
|{FormName} is published in French[?] (Je {FormName} ve francouzštině[?])|

### <a name="add-example-utterances-with-patternany"></a>Přidejte příklad projevy se vzorem. any

1. V horní navigaci vyberte **Build** (Sestavení) a pak v levé navigaci vyberte **Intents** (Záměry).

1. V seznamu záměrů vyberte **FindForm** (Vyhledat formulář).

1. Přidejte příklad projevy. Text, který má být předpovězen jako vzor. libovolný text je v **tučném textu**. Název formuláře je obtížné určit z dalších slov v utterance. Vzor. any vám pomůže Označit hranice entity.

    |Ukázková promluva|Název formuláře|
    |--|--|
    |Where is the form **What to do when a fire breaks out in the Lab** and who needs to sign it after I read it? (Kde je formulář „Co dělat, když v laboratoři vypukne požár“ a kdo ho musí podepsat, až ho přečtu?)|Co dělat, když se v testovacím prostředí naruší požár
    |Where is **Request relocation from employee new to the company** on the server? (Kde na serveru je Žádost o přeložení nového zaměstnance společnosti?)|Požádat o přemístění od zaměstnance nového do společnosti|
    |Who authored **Health and wellness requests on the main campus** and what is the most current version? (Kdo vytvořil „Zásady zachování zdraví a duševní pohody v hlavním kampusu“ a jaká je nejnovější verze?)|Požadavky na stav a wellness v hlavním areálu|
    |I'm looking for the form named **Office move request including physical assets**. (Hledám formulář s názvem „Žádost o přesun kanceláře včetně fyzických prostředků“.) |Požadavek na přesun Office včetně fyzických prostředků|

    Bez entity Pattern.any by pro službu LUIS bylo kvůli mnoha variantám názvů formulářů obtížné zjistit, kde končí název formuláře.

### <a name="create-a-patternany-entity"></a>Vytvoření entity Pattern.any
Entita Pattern.any extrahuje entity různé délky. Funguje pouze ve vzoru, protože vzor označuje začátek a konec entity pomocí syntaxe.

1. V levé navigaci vyberte **Entities** (Entity).

1. Vyberte **+ vytvořit**, zadejte název `FormName`a jako typ vyberte **model. any** . Vyberte **Vytvořit**.

### <a name="add-a-pattern-that-uses-the-patternany"></a>Přidání vzoru využívajícího entitu Pattern.any

1. V levé navigaci vyberte **Patterns** (Vzory).

1. Vyberte záměr **FindForm** (Vyhledat formulář).

1. Zadejte následující šablony promluv, které používají novou entitu:

    |Šablony promluv|
    |--|
    |`Where is the form ["]{FormName}["] and who needs to sign it after I read it[?]`|
    |`Where is ["]{FormName}["] on the server[?]`|
    |`Who authored ["]{FormName}["] and what is the most current version[?]`|
    |`I'm looking for the form named ["]{FormName}["][.]`|

1. Trénujte aplikaci.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Test nového vzoru pro extrakci dat volného tvaru
1. Výběrem možnosti **Test** na horním panelu otevřete panel testování.

1. Zadejte následující promluvu:

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. Pod výsledkem vyberte **Inspect** (Prozkoumat) a zobrazte výsledky testu pro entitu a záměr.

    Jako první se našla entita `FormName` a pak vzor určující záměr. Pokud máte výsledek testu, kde se nezjistily entity a tedy se ani nenašel vzor, musíte do záměru (ne do vzoru) přidat více ukázkových promluv.

1. Zavřete panel testování výběrem tlačítka **Test** v horní navigaci.

### <a name="using-an-explicit-list"></a>Použití explicitního seznamu

Pokud zjistíte, že váš vzor zahrnující entitu Pattern.any neextrahuje entity správně, můžete tento problém opravit pomocí [explicitního seznamu](reference-pattern-syntax.md#explicit-lists).

## <a name="what-did-this-tutorial-accomplish"></a>Co se v tomto kurzu nedokončilo?

V tomto kurzu jsme přidali vzory, které vám pomůžou LUIS odhadnout záměr s výrazně vyšším skóre bez nutnosti přidat další příklad projevy. Označení entit a ignorovatelného textu umožnilo LUIS aplikovat vzor na širší rozmanitost promluv.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky


> [!div class="nextstepaction"]
> [Naučte se, jak používat role spolu se vzorem](luis-tutorial-pattern.md)
