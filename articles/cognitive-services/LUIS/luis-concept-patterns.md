---
title: Vzorce – předpověď pro LUIS
titleSuffix: Azure Cognitive Services
description: Vzory umožňují dosáhnout určení záměru s větší přesností bez nutnosti poskytovat mnoho dalších promluv.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: diberry
ms.openlocfilehash: 4ca3a27a63f84eccb66b24d5046e2ae7d751387d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487545"
---
# <a name="patterns-improve-prediction-accuracy"></a>Vzorce zlepšují přesnost předpovědi
Vzory jsou navržené tak, aby se zlepšila přesnost, když je několik projevy velmi podobné.  Vzory umožňují dosáhnout určení záměru s větší přesností bez nutnosti poskytovat mnoho dalších promluv. 

## <a name="patterns-solve-low-intent-confidence"></a>Vzorce řeší důvěru v nízkém záměru.
Vezměte v úvahu aplikaci lidských zdrojů, která v souvislosti s zaměstnancem hlásí v organizačním grafu. V případě jména a vztahu zaměstnance LUIS vrátí zaměstnance, kterých se účastní. Berte v úvahu zaměstnance, to, s názvem manažera Alice a týmu podřízených položek s názvem: Michael, Carl a.

![Obrázek organizačního diagramu](./media/luis-concept-patterns/org-chart.png)

|Projevy|Předpokládané záměr|Skóre záměru|
|--|--|--|
|Kdo je k podřízenému?|GetOrgChart|.30|
|Kdo je podřízená?|GetOrgChart|.30|

Pokud má aplikace více než 10 až 20 projevy s různou délkou věty, jiné pořadí slov a dokonce i různá slova (synonyma "podřízeného", "Manage", "Report"), LUIS může vracet skóre s nízkou mírou spolehlivosti. Vytvořte vzor, který pomůže LUIS pochopit důležitost pořadí slov. 

Vzory řeší následující situace: 

* Skóre záměru je nízké.
* Správný záměr není nejvyšší skóre, ale příliš blízko k hornímu skóre. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Vzory nejsou zárukou záměru.
Vzory využívají kombinaci předpovědních technologií. Nastavení záměru pro šablonu utterance ve vzoru není zárukou předpovědi záměru, ale jedná se o silný signál. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Vzory nezlepšují detekci entit zjištěné počítačem

Vzor je primárně určen pro lepší předpověď záměrů a rolí. Vzor. Každá entita se používá k extrakci entit volných formulářů. I když vzory používají entity, vzor nedokáže detekovat entitu získanou počítačem.  

Neočekává se, že se lepší předpověď entity zobrazí, pokud sbalíte více projevy do jediného vzoru. Aby bylo možné jednoduché entity aktivovat, je nutné přidat projevy nebo použít entity seznamu jinak se váš vzor neaktivuje.

## <a name="patterns-use-entity-roles"></a>Vzory používají role entit
Pokud jsou dvě nebo více entit ve vzorci kontextově závislé, vzory používají [role](luis-concept-roles.md) entit k extrakci kontextových informací o entitách.  

## <a name="prediction-scores-with-and-without-patterns"></a>Předpověď skóre s použitím vzorů a bez nich
LUIS by měl mít dostatečný příklad projevy, což by mohlo zvýšit důvěru v předpovědi bez vzorců. Vzorce zvyšují skóre spolehlivosti bez nutnosti poskytovat tolik projevy.  

## <a name="pattern-matching"></a>Porovnávání vzorů
Vzor se shoduje na základě zjištění entit uvnitř vzoru a pak ověřování zbývajících slov a pořadí slov ve vzorci. V modelu jsou vyžadovány entity, aby bylo možné model porovnat. Vzor je použit na úrovni tokenu, nikoli na úrovni znaků. 

## <a name="pattern-syntax"></a>Syntaxe vzoru
Syntaxe vzoru je šablona pro utterance. Šablona by měla obsahovat slova a entity, které chcete porovnat, a také slova a interpunkční znaménka, která chcete ignorovat. Nejedná **se o regulární** výraz. 

Entity v vzorcích jsou obklopené složenými závorkami `{}`. Vzory mohou zahrnovat entity a entity s rolemi. [Vzor. any](luis-concept-entity-types.md#patternany-entity) je entita, která se používá pouze ve vzorcích. 

Syntaxe vzoru podporuje následující syntaxi:

|Funkce|Syntaxe|Úroveň vnoření|Příklad:|
|--|--|--|--|
|Právnick| {} – složené závorky|2|Kde je tvar {entity-Name}?|
|Volitelné|[] – hranaté závorky<BR><BR>Existuje limit 3 pro vnořování úrovní jakékoli kombinace Optional and Grouping. |2|Otazník je nepovinný [?].|
|Sloučení|() – závorky|2|je (a \| b)|
|nebo| \| – svislá čára (svislá čára)<br><br>U svislých pruhů (nebo) v jedné skupině je povolený limit 2. |-|Kde je tvar ({Form-Name-short} &#x7c; {Form-Name-Long} &#x7c; {Form-Number})| 
|začátek a/nebo konec utterance|^ – blikající kurzor|-|^ začátek utterance<br>utterance je hotové ^<br>^ striktní shoda literálu celého utterance s {Number} entitou ^|

## <a name="nesting-syntax-in-patterns"></a>Syntaxe vnořování ve vzorcích

**Volitelná** syntaxe s hranatými závorkami může být vnořená na dvě úrovně. Například: `[[this]is] a new form`. Tento příklad umožňuje následující projevy: 

|Příklad vnořeného volitelného utteranceu|Vysvětlení|
|--|--|
|Toto je nový formulář.|odpovídá všem slovům ve vzoru|
|je nový formulář|odpovídá vnějšímu volitelnému slovu a jiným nevolitelným slovům ve vzoru|
|nový formulář|odpovídá pouze povinným slovům|

Syntaxe **seskupení** s kulatými závorkami může být vnořená dvě úrovně. Například: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Tato funkce umožňuje, aby se všechny tři entity shodovaly. 

Pokud je Entity1 umístění s rolemi, jako je počátek (Seattle) a cíl (Cairo) a entita 2 je známý název budovy ze seznamu entit (RedWest-C), následující projevy by se namapovaly na tento vzor:

|Příklad vnořeného seskupení utterance|Vysvětlení|
|--|--|
|RedWest-C|odpovídá vnější entitě seskupení|
|Seattle|odpovídá jedné z vnitřních entit seskupení.|
|Cairo|odpovídá jedné z vnitřních entit seskupení.|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Omezení vnořování pro skupiny s volitelnou syntaxí

Kombinace **seskupení** s **volitelnou** syntaxí má limit 3 úrovní vnoření.

|Povolené|Příklad:|
|--|--|
|Ano|([(Test1 &#x7c; test2)] &#x7c; test3)|
|Ne|([([test1] &#x7c; test2)] &#x7c; test3)|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Omezení vnořování pro skupiny s syntaxí or-Lo

Kombinace **seskupení** se syntaxí **or-Lo** má omezení 2 svislé pruhy.

|Povolené|Příklad:|
|--|--|
|Ano|(Test1 &#x7c; test2 &#x7c; (test3 &#x7c; test4))|
|Ne|(Test1 &#x7c; test2 &#x7c; test3 &#x7c; (test4 &#x7c; test5)) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntaxe pro přidání entity do šablony vzoru
Chcete-li přidat entitu do šablony vzoru, uzavřete název entity do složených závorek, například `Who does {Employee} manage?`. 

|Vzor s entitou|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntaxe pro přidání entity a role do šablony vzoru
Role entity je označená jako `{entity:role}` s názvem entity následovaným dvojtečkou a názvem role. Chcete-li přidat entitu s rolí do šablony vzoru, uzavřete název entity a název role se složenými závorkami, například `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Vzor s rolemi entit|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntaxe pro přidání vzoru. jakékoli do šablony vzoru
Vzor. kterákoli entita umožňuje přidat do vzoru entitu s různou délkou. Dokud je šablona vzoru následována, vzor. kterákoli z nich může být libovolná délka. 

Chcete-li přidat **vzor. libovolnou** entitu do šablony vzoru, uzavřete vzorek. libovolnou entitu se složenými závorkami, například `How much does {Booktitle} cost and what format is it available in?`.  

|Vzor se vzorem. libovolná entita|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Názvy knih ve vzoru|
|--|
|Kolika **ukrást tuto knihu** a jaký je její formát k dispozici?|
|Kolik se **dotazuje** na náklady a jaký formát je k dispozici?|
|Kolik je **zajímá incidentu psa v noci** a v jakém formátu je k dispozici?| 

Slova názvu knihy nejsou matoucí LUIS, protože LUIS ví, kde končí název knihy, na základě vzoru. kterákoli entita.

## <a name="explicit-lists"></a>Explicitní seznamy

Vytvořte [explicitní seznam](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) prostřednictvím rozhraní API pro vytváření obsahu, aby byla výjimka povolena v těchto případech:

* Váš vzor obsahuje [vzorek. any](luis-concept-entity-types.md#patternany-entity)
* A tato syntaxe vzoru umožňuje možnost nesprávného extrakce entit založeného na utterance. 

Předpokládejme například, že máte vzor obsahující jak volitelnou syntaxi, `[]`, tak syntaxi entit, `{}`a kombinaci způsobem pro nekorektně extrahování dat.

Vezměte v úvahu vzor e-mailu [najít] o {Subject} [z {Person}].

V následujícím projevy jsou subjekt a **osoba** správně extrahovány **a nesprávně** :

|Promluva|Entita|Opravit extrakci|
|--|--|:--:|
|e-mail o psy z Chrisho|Předmět = psi<br>person = Novák|✔|
|e-mail o člověku z La Mancha|Předmět = muž<br>person = La Mancha|×|

V předchozí tabulce by měl být předmět `the man from La Mancha` (název knihy), ale protože předmět obsahuje volitelný `from`Word, název je nesprávně předpovězený. 

Chcete-li tuto výjimku opravit na vzor, přidejte `the man from la mancha` jako explicitní shodu seznamu pro entitu {Subject} pomocí [rozhraní API pro vytváření explicitního seznamu](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxe k označení volitelného textu v šabloně utterance
Označte volitelný text v utterance pomocí syntaxe hranaté závorky regulárního výrazu `[]`. Volitelný text může vnořovat hranaté závorky až do dvou závorek.

|Vzor s volitelným textem|Význam|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` a `from {person}` jsou volitelné.|
|' Vám může pomáhat: [?]|Interpunkční znaménko je volitelné.|

Interpunkční znaménka (`?`, `!`, `.`) by měla být ignorována a je třeba je ignorovat pomocí syntaxe hranaté závorky ve vzorcích. 

## <a name="pattern-only-apps"></a>Pouze vzorové aplikace
Můžete vytvořit aplikaci s záměry, které nemají žádný vzorový projevy, pokud existuje vzor pro každý záměr. V případě aplikace jenom se vzorkem by neměl vzor obsahovat entity, které se naučily počítačem, protože to vyžaduje příklad projevy. 

## <a name="best-practices"></a>Osvědčené postupy
Seznamte se s [osvědčenými postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se implementovat vzory v tomto kurzu.](luis-tutorial-pattern.md)
