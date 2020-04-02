---
title: 'Výuka: Vzory - LUIS'
description: Pomocí vzorů zvýšit záměr a predikce entity při poskytování méně příklad projevy v tomto kurzu. Vzor je k dispozici jako příklad utterance šablony, který zahrnuje syntaxi k identifikaci entit a ignorovatelný text.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 10f0ade45dedb3413887cc4b4dea89e857c1bde7
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545992"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>Kurz: Přidání běžných formátů promluv šablony vzoru pro zlepšení předpovědí

V tomto kurzu pomocí vzorů zvýšit záměr a entity předpověď, která umožňuje poskytnout méně příklad projevy. Vzor je promluva šablony přiřazená záměru, která obsahuje syntaxi k identifikaci entit a ignorovatelný text.

**V tomto kurzu se dozvíte, jak:**

> [!div class="checklist"]
> * Vytvořit vzor
> * Ověřit vylepšení predikce vzoru
> * Označit text jako ignorovatelný a vnořit ho do vzoru
> * Používat testovací panel k ověření úspěchu vzoru

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>Projevy v záměru a vzoru

Existují dva typy projevy uložené v aplikaci LUIS:

* Příklad projevy v záměru
* Projevy šablony ve vzoru

Přidání promluvy šablony jako vzor umožňuje poskytnout méně příklad projevy celkově záměru.

Vzorek se použije jako kombinace shody textu a strojového učení.  Promluva šablony ve vzoru, spolu s příklad projevy v záměru, poskytují LUIS lepší pochopení jaké projevy fit záměru.

## <a name="import-example-app-and-clone-to-new-version"></a>Import ukázkové aplikace a klonování do nové verze

Použijte k tomu následující postup:

1.  Stáhněte a uložte [soubor JSON aplikace](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json?raw=true).

1. Importujte JSON do nové aplikace do [portálu LUIS ve verzi Preview](https://preview.luis.ai). Na stránce **Moje aplikace** vyberte + Nová aplikace **pro konverzaci**a pak vyberte **Importovat jako JSON**. Vyberte soubor, který jste stáhli v předchozím kroku.

1. V části **Spravovat** na kartě **Verze** vyberte aktivní verzi a pak vyberte **Klonovat**. Pojmenujte klonockou verzi `patterns`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.

## <a name="create-new-intents-and-their-utterances"></a>Tvorba nových záměrů a jejich promluv

Tyto dva záměry najít správce nebo správce přímé sestavy, na základě textu utterance. Potíž je v tom, že tyto dva záměry _znamenají_ různé věci, ale většina slov je stejná. Pouze slovo pořadí je jiný. Aby byl záměr předpovězen správně, musel by mít mnoho příkladů.

1. Na navigačním panelu vyberte **Sestavit.**

1. Na stránce **Záměry** vyberte **+ Vytvořit,** chcete-li vytvořit nový záměr.

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

1. Výběrem **možnosti + vytvořit** vytvořte nový záměr. V automaticky otevíraném dialogovém okně zadejte `OrgChart-Reports` a pak vyberte **Done** (Hotovo).

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

### <a name="train-the-app-before-testing-or-publishing"></a>Trénování aplikace před testováním nebo publikováním

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

### <a name="publish-the-app-to-query-from-the-endpoint"></a>Publikování aplikace k dotazování z koncového bodu

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

### <a name="get-intent-and-entities-from-endpoint"></a>Získání záměru a entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Přejděte na konec adresy URL v adresním `Who is the boss of Jill Jones?`řádku a nahraďte _YOUR_QUERY_HERE:_ .

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

Skóre dvou hlavních záměrů je blízko, ale nejvyšší záměr není výrazně vysoký (více než 60%) a není dostatečně daleko nad skóre dalšího záměru.

Vzhledem k tomu, že školení SLUŽBY LUIS není pokaždé úplně stejné (je tu trochu varianty), tyto dvě nejvyšší skóre by mohla invertovat na další tréninkový cyklus. Výsledkem pak může být vrácení špatného záměru.

Použitím vzorů výrazně procentuálně zvýšíte skóre správného záměru a zvětšíte tak jeho odstup od druhého nejvyššího skóre.

Toto druhé okno prohlížeče ponechte otevřené. Použijete je později v průběhu kurzu.

## <a name="template-utterances"></a>Šablony promluv
Vzhledem k povaze domény předmětu lidských zdrojů existuje několik běžných způsobů, jak se ptát na vztahy se zaměstnanci v organizacích. Například:

|Projevy|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Tyto promluvy jsou si příliš blízké na to, aby bylo možné určit jejich obsahovou jedinečnost bez poskytnutí mnoha příkladů promluv. LUIS se učí obecné vzory promluv pro určitý záměr bez dodání většího množství příkladů promluv tak, že přidává vzory k záměru.

Šablony příkladů promluvy pro tento záměr zahrnují:

|Šablony příkladů promluv|význam syntaxe|
|--|--|
|`Who does {Employee} report to[?]`|Zaměnitelné`{Employee}`<br>Ignorovat`[?]`|
|`Who reports to {Employee}[?]`|Zaměnitelné`{Employee}`<br>Ignorovat`[?]`|

Syntaxe `{Employee}` označí umístění entity v šabloně promluvy a to, o jakou entitu se jedná. Volitelná syntaxe , `[?]`označí slova nebo interpunkci, která je volitelná. LUIS páruje promluvy a ignoruje volitelný text v závorkách.

Syntaxe sice vypadá jako regulární výraz, ale není regulárním výrazem. Podporovaná je pouze syntaxe se složenými `{}` a hranatými `[]` závorkami. Můžou být vnořené až dvě úrovně.

Aby vzor, který má být spárován utterance, _nejprve_ entity v rámci utterance musí odpovídat entity v utterance šablony. To znamená, že entity musí mít dostatek příkladů v příkladu projevy s vysokým stupněm předpověď před vzorky s entitami jsou úspěšné. Šablona nepomůže predikovat entity, ale jen záměry.

**Vzory sice umožňují poskytovat méně ukázkových promluv, když ale není detekovaná entita, vzor se nespáruje.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Přidání vzorů pro záměr správce organizačních diagramů

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

    Tyto projevy šablony zahrnují entitu **Employee** s zápisem složené závorky.

1. Zatímco stále na stránce Vzory, vyberte **záměr OrgChart-Reports** a zadejte následující projevy šablony:

    |Šablony promluv|
    |:--|
    |`Who are {Employee}['s] subordinates[?]`|
    |`Who reports to {Employee}[?]`|
    |`Who does {Employee} manage[?]`|
    |`Who are {Employee} direct reports[?]`|
    |`Who does {Employee} supervise[?]`|
    |`Who does {Employee} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Dotaz koncovému bodu při použití šablon

Teď, když vzorky jsou přidány do aplikace, trénování, publikovat a dotaz ovat aplikaci na koncovém bodu predikce runtime.

1. Vyberte **vlak**. Po dokončení školení vyberte **Publikovat** a vyberte **produkční** slot a pak vyberte **Hotovo**.

1. Po dokončení publikování přepněte karty prohlížeče zpět na kartu URL koncového bodu.

1. Přejděte na konec adresy URL v adresním řádku a nahraďte _YOUR_QUERY_HERE:_`Who is the boss of Jill Jones?`

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

Předpověď záměru je nyní výrazně jistější a další nejvyšší záměr skóre je výrazně nižší. Tyto dva záměry nebudou flip-flop při tréninku.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Práce s volitelným textem a předpřipravenými entitami

Předchozí vzor šablony promluv použitý v tomto kurzu obsahoval několik ukázek volitelného textu`'s`, například použití `?`. Předpokládejme, že potřebujete povolit aktuální a budoucí data v textu utterance.

Ukázkové promluvy jsou:

|Záměr|Ukázkové promluvy s volitelným textem a předpřipravenými entitami|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

Každý z těchto příkladů používá slovesný čas (`was`, `is`, `will be`) a datum (`March 3`, `now`, `in a month`), které LUIS potřebuje k tomu, aby predikoval správně. Všimněte si, že poslední dva příklady v `in` tabulce `on`používají téměř stejný text s výjimkou a .

Příklad šablony projevy, které umožňují tyto volitelné informace:

|Záměr|Ukázkové promluvy s volitelným textem a předpřipravenými entitami|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


Použití volitelné syntaxe v hranatých závorkách `[]` usnadňuje přidávání volitelného textu do šablony promluvy. Tato syntaxe může být vnořená až na druhou úroveň `[[]]` a obsahovat entity nebo text.


**Otázka: Proč jsou `w` všechna písmena, první písmeno v každé šabloně utterance, malá písmena? Neměly by být volitelně velká nebo malá písmena?** Promluva, kterou klientská aplikace odesílá koncovému bodu dotazu, se převádí na malá písmena. V šabloně promluvy můžete použít jak malá, tak velká písmena. V promluvě koncového bodu také. Porovnání se provádí vždy až po převodu na malá písmena.

**Otázka: Když se 3. březen predikuje jako číslo `3` i jako datum `March 3`, proč nejsou předpřipravená čísla součástí šablony promluvy?** Šablona promluvy podle kontextu použije datum buď doslova jako `March 3`, nebo abstrahovanou jako `in a month`. Datum sice může obsahovat číslo, ale ne každé číslo musí být nutně datum. Používejte vždy takovou entitu, která nejlépe vystihuje typ požadovaný ve výsledcích JSON predikce.

**Otázka: Co chabě formulované promluvy, jako třeba `Who will {Employee}['s] manager be on March 3?`.** Gramaticky rozdílné slovesné časy, jako tady, kde jsou `will` a `be` oddělené, musejí být v samostatných šablonách promluvy. Existující šablona promluvy se s nimi nespáruje. I když se záměr promluvy nezměnil, změnil se pořádek slov v promluvě. Tato změna ovlivní predikci LUIS. Můžete [seskupit a nebo](#use-the-or-operator-and-groups) slovesa časy kombinovat tyto projevy.

**Pamatujte: nejdřív se najdou entity, pak se teprve spáruje vzor.**

### <a name="add-new-pattern-template-utterances"></a>Přidání nových vzorů šablony promluv

1. V sekci **Vzory** nabídky **Zkompilovat** přidejte několik nových vzorů šablon promluv. Vyberte **OrgChart-Manager** v rozevírací nabídce záměru a zadejte následující šablony promluv:

    |Záměr|Ukázkové promluvy s volitelným textem a předpřipravenými entitami|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Chcete-li aplikaci trénovat, vyberte na navigačním panelu možnost **Trénovat.**

3. Po dokončení tréninku vyberte **Test** v horní části panelu a otevřete panel testování.

4. Zadejte několik testovacích promluv. Ověříte tak, jestli je vzor spárovaný a skóre záměru výrazně vysoké.

    Po zadání první promluvy vyberte **Zkontrolovat** pod výsledkem. Zobrazíte tak všechny výsledky predikce. Každý utterance by měl mít záměr **OrgChart-Manager** a by měl extrahovat hodnoty pro entity Employee a datetimeV2.

    |Promluva|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

Ve všech promluvách jsou entity, proto se všechny shodují se stejným vzorem a mají vysoké skóre predikce. Přidali jste několik vzorů, které budou odpovídat mnoha variantami projevy. Nebylo nutné přidat žádné příklad projevy v záměru mít promluvy šablony práce ve vzoru.

Toto použití vzorů k dispozici:
* Vyšší predikční skóre
* Se stejným příkladem projevy v záměru
* S několika dobře-postavený šablony projevy ve vzoru

### <a name="use-the-or-operator-and-groups"></a>Použití operátoru or a skupin

Několik předchozí šablony projevy jsou velmi blízko. Pomocí **skupiny** `()` a syntaxe **OR** `|` zmenšete projevy šablony.

Následující 2 vzorky lze kombinovat do jednoho `()` vzoru `|` pomocí skupiny a syntaxe OR.

|Záměr|Ukázkové promluvy s volitelným textem a předpřipravenými entitami|
|--|--|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

Nová promluva šablony bude:

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`.

To používá **skupinu** kolem požadovanésloveso `in` `on` čas a volitelné a **s nebo** potrubí mezi nimi.

1. Na stránce **Vzorky** vyberte filtr **OrgChart-Manager.** Zúžit seznam hledáním . `manager`

1. Zachovat jednu verzi šablony utterance (upravit v dalším kroku) a odstranit další varianty.

1. Změňte šablonu promluvy na 

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`

2. Chcete-li aplikaci trénovat, vyberte na navigačním panelu možnost **Trénovat.**

3. Po dokončení tréninku vyberte **Test** v horní části panelu a otevřete panel testování.

    Testovací podokno slouží k testování verzí utterance:

    |Projevy, které chcete zadat v podokně test|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

Pomocí další syntaxe vzoru snížíte počet promluv šablony, které je třeba udržovat ve vaší aplikaci, zatímco stále máte vysoké skóre předpovědi.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>Použití počátečních a koncových kotev utterance

Syntaxe vzoru poskytuje syntaxi kotvy počátečního `^`a koncového utterance stříšky . Počáteční a koncové utterance kotvy lze společně zaměřit na velmi specifické a případně literál utterance nebo samostatně použít k cílové záměry.

## <a name="using-patternany-entity"></a>Použití entity Pattern.any

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

### <a name="add-example-utterances-with-patternany"></a>Přidání ukázkových promluv pomocí pattern.any

1. V horní navigaci vyberte **Build** (Sestavení) a pak v levé navigaci vyberte **Intents** (Záměry).

1. V seznamu záměrů vyberte **FindForm** (Vyhledat formulář).

1. Přidejte některé příklad projevy. Text, který by měl být předpovězen jako Pattern.any je **tučným písmem**. Název formuláře je obtížné určit z jiných slov kolem něj v utterance. Pattern.any pomůže tím, že označí hranice entity.

    |Ukázková promluva|Název formuláře|
    |--|--|
    |Where is the form **What to do when a fire breaks out in the Lab** and who needs to sign it after I read it? (Kde je formulář „Co dělat, když v laboratoři vypukne požár“ a kdo ho musí podepsat, až ho přečtu?)|Co dělat, když v laboratoři vypukne požár
    |Where is **Request relocation from employee new to the company** on the server? (Kde na serveru je Žádost o přeložení nového zaměstnance společnosti?)|Žádost o přemístění ze zaměstnance nového do společnosti|
    |Who authored **Health and wellness requests on the main campus** and what is the most current version? (Kdo vytvořil „Zásady zachování zdraví a duševní pohody v hlavním kampusu“ a jaká je nejnovější verze?)|Požadavky na zdraví a wellness v hlavním kampusu|
    |I'm looking for the form named **Office move request including physical assets**. (Hledám formulář s názvem „Žádost o přesun kanceláře včetně fyzických prostředků“.) |Žádost o přesunutí office včetně fyzického majetku|

    Bez entity Pattern.any by pro službu LUIS bylo kvůli mnoha variantám názvů formulářů obtížné zjistit, kde končí název formuláře.

### <a name="create-a-patternany-entity"></a>Vytvoření entity Pattern.any
Entita Pattern.any extrahuje entity různé délky. Funguje pouze ve vzorku, protože vzorek označuje začátek a konec entity syntaxí.

1. V levé navigaci vyberte **Entities** (Entity).

1. Vyberte **+ Vytvořit** `FormName`, zadejte název a jako typ vyberte **Pattern.any.** Vyberte **Vytvořit**.

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

## <a name="what-did-this-tutorial-accomplish"></a>Čeho tento výukový program dosáhl?

Tento kurz přidal vzory, které pomáhají luis předpovědět záměr s výrazně vyšším skóre bez nutnosti přidávat další příklad projevy. Označení entit a ignorovatelného textu umožnilo LUIS aplikovat vzor na širší rozmanitost promluv.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky


> [!div class="nextstepaction"]
> [Naučte se, jak používat role spolu se vzorem](luis-tutorial-pattern.md)
