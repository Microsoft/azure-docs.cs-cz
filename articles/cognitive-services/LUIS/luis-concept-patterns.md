---
title: Vzorce – předpověď pro LUIS
titleSuffix: Azure Cognitive Services
description: Vzor umožňuje získat vyšší přesnost pro záměru bez zadání projevy mnoho více.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: bad3bdc2b4508c082ca50647d5de5e7265c763a1
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639192"
---
# <a name="patterns-improve-prediction-accuracy"></a>Vzory zvyšte přesnost předpovědi
Vzory jsou navržené pro zlepšení přesnosti, když několik projevy jsou velmi podobné.  Vzor umožňuje získat vyšší přesnost pro záměru bez zadání projevy mnoho více. 

## <a name="patterns-solve-low-intent-confidence"></a>Způsoby řešení s nízkou spolehlivostí záměru
Vezměte v úvahu aplikaci lidských zdrojů, která generuje sestavy v organizační grafu ve vztahu k zaměstnance. Zadaný název a relace zaměstnance, LUIS vrátí zaměstnanci zahrnuté. Berte v úvahu zaměstnance, název, s názvem manažera Alice a týmu podřízených s názvem: Michael, a Carl.

![Obrázek organizačního diagramu](./media/luis-concept-patterns/org-chart.png)

|Projevy|Cílem předpovídat|Záměru skóre|
|--|--|--|
|Kdo je na Tom podřízený?|GetOrgChart|.30|
|Kdo je podřízenou položkou tohoto Tom?|GetOrgChart|.30|

Pokud aplikace má mezi 10 a 20 projevy pomocí různých délek věty, jiné pořadí slov a dokonce během různých slova (synonymům těchto "podřízený", "manage", "zpráva"), může vrátit LUIS nízká pravděpodobnost. Vytvořte vzor, který pomůže LUIS pochopit důležitost pořadí slov. 

Způsoby řešení těchto situacích: 

* Skóre záměru je nízké.
* Správný záměr není nejvyšší skóre, ale příliš blízko k hornímu skóre. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Vzory nejsou zárukou záměr
Vzory pomocí kombinace technologií předpovědi. Nastavení pro šablony utterance záměru ve vzorku není zárukou záměru předpovědí, ale je silný signál. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Vzory nezlepšují detekci entit zjištěné počítačem

Vzor je primárně určen pro lepší předpověď záměrů a rolí. Vzor. Každá entita se používá k extrakci entit volných formulářů. I když vzory používají entity, vzor nedokáže detekovat entitu získanou počítačem.  

Neočekává se, že se lepší předpověď entity zobrazí, pokud sbalíte více projevy do jediného vzoru. Aby bylo možné jednoduché entity aktivovat, je nutné přidat projevy nebo použít entity seznamu jinak se váš vzor neaktivuje.

## <a name="patterns-use-entity-roles"></a>Vzory použít entitu role
Pokud souvisí kontextově dva nebo více entit ve vzorku, použijte vzory entity [role](luis-concept-roles.md) extrahovat kontextové informace o entitách.  

## <a name="prediction-scores-with-and-without-patterns"></a>Predikce skóre a bez nich vzory
Zadaný dostatek příklad projevy, LUIS bylo by možné zvýšit důvěru předpovědi vzorů. Vzory zvýšení skóre spolehlivosti aniž by musel zadávat tolik projevy.  

## <a name="pattern-matching"></a>Porovnávání vzorů
Vzor je nalezena shoda podle nejprve zjišťování entit v modelu a ověření zbytek slova a pořadí slov, vzoru. Entity jsou nutné ve vzoru pro odpovídající vzor. Vzor se použije na úrovni tokenu není úroveň znak. 

## <a name="pattern-syntax"></a>Vzor syntaxe
Vzor syntaxe je šablona pro utterance. Šablona by měl obsahovat slova a entity, které chcete porovnat a také slova a interpunkční znaménka, které má být ignorována. Je **není** regulární výraz. 

Entity ve vzorech jsou ohraničeny složených závorek, `{}`. Vzory mohou zahrnovat entit a entit s rolemi. [Vzor. any](luis-concept-entity-types.md#patternany-entity) je entita, která se používá pouze ve vzorcích. 

Syntaxe vzoru podporuje následující syntaxi:

|Funkce|Syntaxe|Úroveň vnoření|Příklad|
|--|--|--|--|
|entita| {}– složené závorky|2|Kde je tvar {entity-Name}?|
|nepovinné|[] – hranaté závorky<BR><BR>Existuje limit 3 pro vnořování úrovní jakékoli kombinace Optional and Grouping. |2|Otazník je nepovinný [?].|
|sloučení|() – závorky|2|je (a \| b)|
|or| \|– svislá čára (svislá čára)<br><br>U svislých pruhů (nebo) v jedné skupině je povolený limit 2. |-|Kde je tvar ({Form-Name-short} &#x7c; {Form-Name-Long} &#x7c; {Form-Number})| 
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
|Káhira|odpovídá jedné z vnitřních entit seskupení.|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Omezení vnořování pro skupiny s volitelnou syntaxí

Kombinace **seskupení** s volitelnou syntaxí má limit 3 úrovní vnoření.

|Povoleno|Příklad|
|--|--|
|Ano|([(Test1 &#x7c; test2)] &#x7c; test3)|
|Ne|([([test1] &#x7c; test2)] &#x7c; test3)|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Omezení vnořování pro skupiny s syntaxí or-Lo

Kombinace **seskupení** se syntaxí **or-Lo** má omezení 2 svislé pruhy.

|Povoleno|Příklad|
|--|--|
|Ano|(Test1 &#x7c; test2 &#x7c; (test3 &#x7c; test4))|
|Ne|(Test1 &#x7c; test2 &#x7c; test3 &#x7c; (test4 &#x7c; test5)) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntaxe pro přidání entity do modelu šablony
Přidání entity do modelu šablony, uzavřete název entity se složenými závorkami, `Who does {Employee} manage?`. 

|Vzor s entitou|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntaxe pro vzor šablony přidat entitu a role
Roli entity je označena jako `{entity:role}` s názvem entity, za nímž následuje dvojtečka, potom název role. Přidat entitu s rolí do šablony vzor, před a za název entity a názvu role pomocí složených závorek, například `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Vzor s rolemi entity|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntaxe pro přidání pattern.any vzor šablony
Pattern.any entity umožňuje přidat entitu různé délky se vzorem. Za předpokladu, následuje vzor šablony, může být pattern.any jakékoli délky. 

Chcete-li přidat **Pattern.any** entity do modelu šablony obklopit Pattern.any entity pomocí složených závorek, například `How much does {Booktitle} cost and what format is it available in?`.  

|Vzor s entitou Pattern.any|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Názvů knih ve vzoru|
|--|
|Kolik **ukrást této knihy** náklady a jaký formát je k dispozici?|
|Kolik **požádejte** náklady a jaký formát je k dispozici?|
|Kolik **The zvědaví Incident pes včas noční** náklady a jaký formát je k dispozici?| 

Slova názvu knihy nejsou matoucí LUIS, protože LUIS ví, kde končí název knihy, na základě vzoru. kterákoli entita.

## <a name="explicit-lists"></a>Explicitní seznamy

Vytvořte [explicitní seznam](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) prostřednictvím rozhraní API pro vytváření obsahu, aby byla výjimka povolena v těchto případech:

* Váš vzor obsahuje [vzorek. any](luis-concept-entity-types.md#patternany-entity)
* A tato syntaxe vzoru umožňuje možnost nesprávného extrakce entit založeného na utterance. 

Předpokládejme například, že máte model obsahující jak syntaxi volitelné `[]`a syntaxi entity `{}`kombinované v způsob, jak extrahovat data nesprávně.

Zvažte vzor "[najít] e-mailu o {subject} [od {osoby}]".

V následující projevy **subjektu** a **osoba** entity se extrahují správně a správně:

|Promluva|Entita|Správné extrakce|
|--|--|:--:|
|e-mailu o PSI ze Chris|předmět = PSI<br>osoba = Jan|✔|
|e-mailu o man z La Mancha|předmět = mužem<br>osoba = La Mancha|X|

V předchozí tabulce by měl předmět obsahovat `the man from La Mancha` (název knihy), ale vzhledem k tomu, že předmět obsahuje volitelné slovo `from`, název je nesprávně předpovězený. 

Chcete-li vyřešit tato výjimka se vzorem, přidejte `the man from la mancha` jako explicitní seznam v případě shody s využitím entity {subject} [vytváření rozhraní API pro explicitní seznam](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxe pro označení volitelný text, který v šabloně utterance
Označit volitelný text, který v utterance pomocí syntaxe regulárních výrazů hranatá závorka, `[]`. Nepovinný text můžete vnořit hranaté závorky až pouze dvě závorky.

|Vzor s volitelným textem|Význam|
|--|--|
|`[find] email about {subject} [from {person}]`|`find`a `from {person}` jsou volitelné|
|' Vám může pomáhat: [?]|Interpunkční znaménko je volitelné.|

Interpunkční znaménka`?`( `!`, `.`,) by měla být ignorována a je třeba je ignorovat pomocí syntaxe hranaté závorky ve vzorcích. 

## <a name="pattern-only-apps"></a>Pouze vzorové aplikace
Můžete vytvořit aplikaci s záměry, které nemají žádný vzorový projevy, pokud existuje vzor pro každý záměr. V případě aplikace jenom se vzorkem by neměl vzor obsahovat entity, které se naučily počítačem, protože to vyžaduje příklad projevy. 

## <a name="best-practices"></a>Osvědčené postupy
Přečtěte si [osvědčené postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak implementovat vzory v tomto kurzu](luis-tutorial-pattern.md)
