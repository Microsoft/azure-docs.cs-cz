---
title: 'Kurz: vzory – LUIS'
description: Pomocí vzorů můžete zvýšit záměr a předpověď entit a v tomto kurzu poskytnout méně ukázkového projevy. Vzor je k dispozici jako příklad šablony utterance, který obsahuje syntaxi pro identifikaci entit a ignorovatelné texty.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/06/2020
ms.openlocfilehash: 9814304aed4d7a5f307fb2179491b0fa9635fd68
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324650"
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

Vzor se implementuje jako kombinace párování textů a strojového učení.  Šablona promluvy ve vzoru spolu s ukázkovými promluvami v záměru umožňují službě LUIS lépe porozumět tomu, jaké promluvy odpovídají určitému záměru.

## <a name="import-example-app-and-clone-to-new-version"></a>Importovat ukázkovou aplikaci a klonovat ji do nové verze

Použijte k tomu následující postup:

1.  Stáhněte a uložte [soubor JSON aplikace](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true).

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Importujte JSON do nové aplikace na [portál Luis](https://www.luis.ai). Na stránce **Moje aplikace** vyberte **+ Nová aplikace pro konverzaci**a pak vyberte **importovat jako JSON**. Vyberte soubor, který jste si stáhli v předchozím kroku, a pojmenujte aplikaci `Patterns tutorial` .

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

1. V adresním řádku přejde na konec adresy URL a nahraďte _YOUR_QUERY_HERE_ : `Who is the boss of Jill Jones?` .

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.326605469
                },
                "OrgChart-Reports": {
                    "score": 0.127583548
                },
                "EmployeeFeedback": {
                    "score": 0.0299124215
                },
                "MoveEmployee": {
                    "score": 0.01159851
                },
                "GetJobInformation": {
                    "score": 0.0104600191
                },
                "ApplyForJob": {
                    "score": 0.007508645
                },
                "Utilities.StartOver": {
                    "score": 0.00359402061
                },
                "Utilities.Stop": {
                    "score": 0.00336530479
                },
                "FindForm": {
                    "score": 0.002653719
                },
                "Utilities.Cancel": {
                    "score": 0.00263288687
                },
                "None": {
                    "score": 0.00238638581
                },
                "Utilities.Help": {
                    "score": 0.00226386427
                },
                "Utilities.Confirm": {
                    "score": 0.00211663754
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
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
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
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

Byl dosažen správný nejvyšší záměr, `OrgChart-Manager` ale skóre není vyšší než 70% a v nejbližším nejvyšším záměru není dost daleko. Použitím vzorů výrazně procentuálně zvýšíte skóre správného záměru a zvětšíte tak jeho odstup od druhého nejvyššího skóre.

Toto druhé okno prohlížeče ponechte otevřené. Později ji budete používat později v tomto kurzu.

## <a name="template-utterances"></a>Šablony promluv
Vzhledem k povaze domény subjektu lidských zdrojů existuje několik běžných způsobů, jak v organizacích zeptat se na vztahy zaměstnanců. Příklad:

|Výroky|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Tyto projevy jsou příliš blízko k určení kontextové jedinečnosti každé bez poskytování _mnoha_ příkladů utterance. Přidáním vzoru pro záměr se LUIS učí běžné utterance vzory pro záměr bez nutnosti dodávání mnohem dalších utterance příkladů.

Šablony příkladů promluvy pro tento záměr zahrnují:

|Šablony příkladů promluv|význam syntaxe|
|--|--|
|`Who does {EmployeeListEntity} report to[?]`|zaměnitelné `{EmployeeListEntity}`<br>ohled `[?]`|
|`Who reports to {EmployeeListEntity}[?]`|zaměnitelné `{EmployeeListEntity}`<br>ohled `[?]`|

Syntaxe `{EmployeeListEntity}` označí umístění entity v šabloně promluvy a to, o jakou entitu se jedná. Volitelná syntaxe, `[?]` , označuje slova nebo [interpunkční znaménka](luis-reference-application-settings.md#punctuation-normalization) , která jsou volitelná. LUIS páruje promluvy a ignoruje volitelný text v závorkách.

I když syntaxe vypadá jako regulární výraz, nejedná se o regulární výraz. Podporovaná je pouze syntaxe se složenými `{}` a hranatými `[]` závorkami. Můžou být vnořené až dvě úrovně.

Aby se model shodoval s utterance, musí _nejdřív_ entity v rámci utterance odpovídat entitám v šabloně utterance. To znamená, že entity musí mít dostatek příkladů v příkladu projevy s vysokým stupněm předpovědi před tím, než jsou vzory s entitami úspěšné. Šablona nepomůže predikovat entity, ale jen záměry.

**Vzory sice umožňují poskytovat méně ukázkových promluv, když ale není detekovaná entita, vzor se nespáruje.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Přidejte vzory pro záměr vedoucího organizačního diagramu

1. Vyberte **Sestavení** v horní nabídce.

1. Vyberte **Vzory** v levém navigačním panelu v části **Zvýšení výkonu aplikace**.

1. Vyberte záměr **OrgChart-Manager**, pak zadejte následující šablony promluv:

    |Šablony promluv|
    |:--|
    |`Who is {EmployeeListEntity} the subordinate of[?]`|
    |`Who does {EmployeeListEntity} report to[?]`|
    |`Who is {EmployeeListEntity}['s] manager[?]`|
    |`Who does {EmployeeListEntity} directly report to[?]`|
    |`Who is {EmployeeListEntity}['s] supervisor[?]`|
    |`Who is the boss of {EmployeeListEntity}[?]`|

    Tyto šablony projevy obsahují entitu **EmployeeListEntity** se zápisem složené závorky.

1. Pořád na stránce vzory vyberte záměr **organizačního diagramu – sestavy** a potom zadejte následující šablonu projevy:

    |Šablony promluv|
    |:--|
    |`Who are {EmployeeListEntity}['s] subordinates[?]`|
    |`Who reports to {EmployeeListEntity}[?]`|
    |`Who does {EmployeeListEntity} manage[?]`|
    |`Who are {EmployeeListEntity} direct reports[?]`|
    |`Who does {EmployeeListEntity} supervise[?]`|
    |`Who does {EmployeeListEntity} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Dotaz koncovému bodu při použití šablon

Teď, když se do aplikace přidají vzory, naučit se, zveřejňují a dotazují aplikaci na předpokládaném koncovém bodu modulu runtime.

1. Vyberte **Train** (Trénování). Po dokončení školení vyberte **publikovat** a vyberte **produkční** slot a potom vyberte **Hotovo**.

1. Po dokončení publikování přepněte karty prohlížeče zpátky na kartu Adresa URL koncového bodu.

1. V adresním řádku přejde na konec adresy URL a ověří, jestli je váš dotaz pořád a `Who is the boss of Jill Jones?` pak odešle adresu URL pro novou předpověď.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999999046
                },
                "OrgChart-Reports": {
                    "score": 3.237443E-05
                },
                "EmployeeFeedback": {
                    "score": 4.364242E-06
                },
                "GetJobInformation": {
                    "score": 1.616159E-06
                },
                "MoveEmployee": {
                    "score": 7.575752E-07
                },
                "ApplyForJob": {
                    "score": 5.234157E-07
                },
                "None": {
                    "score": 3.3E-09
                },
                "Utilities.StartOver": {
                    "score": 1.26E-09
                },
                "FindForm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Cancel": {
                    "score": 1.13636367E-09
                },
                "Utilities.Confirm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Help": {
                    "score": 1.13636367E-09
                },
                "Utilities.Stop": {
                    "score": 1.13636367E-09
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
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
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
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

Předpověď záměrů je teď výrazně spolehlivější a skóre dalšího nejvyššího záměru je velmi nízké. Tyto dva záměry se při výuce Překlopí.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Práce s volitelným textem a předpřipravenými entitami

Předchozí vzor šablony promluv použitý v tomto kurzu obsahoval několik ukázek volitelného textu`'s`, například použití `?`. Předpokládejme, že je třeba pro aktuální a budoucí kalendářní data v utterance textu.

Ukázkové promluvy jsou:

|Záměr|Ukázkové promluvy s volitelným textem a předpřipravenými entitami|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

Každý z těchto příkladů používá slovesný čas (`was`, `is`, `will be`) a datum (`March 3`, `now`, `in a month`), které LUIS potřebuje k tomu, aby predikoval správně. Všimněte si, že poslední dva příklady v tabulce používají skoro stejný text s výjimkou `in` a `on` .

Příklad šablony projevy, která umožňuje tyto volitelné informace:

|Záměr|Ukázkové promluvy s volitelným textem a předpřipravenými entitami|
|:--|:--|
|OrgChart-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who is {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|


Použití volitelné syntaxe v hranatých závorkách `[]` usnadňuje přidávání volitelného textu do šablony promluvy. Tato syntaxe může být vnořená až na druhou úroveň `[[]]` a obsahovat entity nebo text.


**Otázka: Proč se jedná o všechna `w` písmena, první písmeno v každé šabloně utterance malá a velká písmena? Neměla by být volitelně velká nebo malá?** Promluva, kterou klientská aplikace odesílá koncovému bodu dotazu, se převádí na malá písmena. V šabloně promluvy můžete použít jak malá, tak velká písmena. V promluvě koncového bodu také. Porovnání se provádí vždy až po převodu na malá písmena.

**Otázka: Když se 3. březen predikuje jako číslo `3` i jako datum `March 3`, proč nejsou předpřipravená čísla součástí šablony promluvy?** Šablona promluvy podle kontextu použije datum buď doslova jako `March 3`, nebo abstrahovanou jako `in a month`. Datum sice může obsahovat číslo, ale ne každé číslo musí být nutně datum. Používejte vždy takovou entitu, která nejlépe vystihuje typ požadovaný ve výsledcích JSON predikce.

**Otázka: Co chabě formulované promluvy, jako třeba `Who will {EmployeeListEntity}['s] manager be on March 3?`.** Gramaticky rozdílné slovesné časy, jako tady, kde jsou `will` a `be` oddělené, musejí být v samostatných šablonách promluvy. Existující šablona promluvy se s nimi nespáruje. I když se záměr promluvy nezměnil, změnil se pořádek slov v promluvě. Tato změna ovlivní predikci LUIS. K kombinování těchto projevy můžete [Seskupit a nebo](#use-the-or-operator-and-groups) příkaz-časů.

> [!CAUTION]
> **Pamatujte: nejdřív se najdou entity, pak se teprve spáruje vzor.**

### <a name="add-new-pattern-template-utterances"></a>Přidání nových vzorů šablony promluv

1. V sekci **Vzory** nabídky **Zkompilovat** přidejte několik nových vzorů šablon promluv. Vyberte **OrgChart-Manager** v rozevírací nabídce záměru a zadejte následující šablony promluv:

    |Záměr|Ukázkové promluvy s volitelným textem a předpřipravenými entitami|
    |--|--|
    |OrgChart-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

2. Pro výuku aplikace vyberte v navigačním panelu možnost **vlak** .

3. Po dokončení školení vyberte v horní části panelu **test** a otevřete panel testování.

4. Zadejte několik testovacích promluv. Ověříte tak, jestli je vzor spárovaný a skóre záměru výrazně vysoké.

    Po zadání první promluvy vyberte **Zkontrolovat** pod výsledkem. Zobrazíte tak všechny výsledky predikce. Každý utterance by měl mít záměr **vedoucího organizačního diagramu** a měl by extrahovat hodnoty `EmployeeListEntity` `datetimeV2` entit a.

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

Následující dva vzorce se můžou zkombinovat do jednoho vzoru pomocí `()` syntaxe Group a nebo `|` .

|Záměr|Ukázkové promluvy s volitelným textem a předpřipravenými entitami|
|--|--|
|OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

Nová šablona utterance bude:

`who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`.

To používá **skupinu** kolem požadovaného příkazu vhodné a volitelnou `in` a `on` s kanálem **nebo** mezi nimi.

> [!NOTE]
> Při použití symbolu _nebo_ `|` (kanálu) nezapomeňte symbol kanálu oddělit mezerou před a za ním v ukázkové šabloně.

1. Na stránce **vzory** vyberte filtr **správce organizačního diagramu** . Zužte seznam hledáním `manager` .

1. Ponechte jednu verzi šablony utterance (pro úpravy v dalším kroku) a odstraňte ostatní variace.

1. Změňte šablonu promluvy na 

    `who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`

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

Syntaxe vzoru poskytuje počáteční a koncovou syntaxi kotvy utterance blikajícího kurzoru `^` . Úvodní a koncové kotvy utterance lze použít společně pro cílení na velmi konkrétní a případně literální utterance nebo pro cílové záměry samostatně.

## <a name="using-patternany-entity"></a>Pomocí vzoru. kterákoli entita

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

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

1. Vyberte **+ vytvořit**, zadejte název `FormName` a jako typ vyberte **model. any** . Vyberte **Vytvořit**.

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
