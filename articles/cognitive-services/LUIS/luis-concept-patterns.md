---
title: Vzory pomáhají predikcí
titleSuffix: Language Understanding - Azure Cognitive Services
description: Vzor umožňuje získat vyšší přesnost pro záměru bez zadání projevy mnoho více.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: diberry
ms.openlocfilehash: 1c408a77f0bdd097db1fb462fa06245133773b71
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195383"
---
# <a name="patterns-improve-prediction-accuracy"></a>Vzory zvyšte přesnost předpovědi
Vzory jsou navržené pro zlepšení přesnosti, když několik projevy jsou velmi podobné.  Vzor umožňuje získat vyšší přesnost pro záměru bez zadání projevy mnoho více. 

## <a name="patterns-solve-low-intent-confidence"></a>Způsoby řešení s nízkou spolehlivostí záměru
Vezměte v úvahu aplikaci lidských zdrojů, která generuje sestavy v organizační grafu ve vztahu k zaměstnance. Zadaný název a relace zaměstnance, LUIS vrátí zaměstnanci zahrnuté. Vezměte v úvahu zaměstnanec Petr, se správcem název Alice a tým s názvem podřízené uzly: Michael Milena a Carl.

![Obrázek organizačního diagramu](./media/luis-concept-patterns/org-chart.png)

|Projevy|Cílem předpovídat|Záměru skóre|
|--|--|--|
|Kdo je na Tom podřízený?|GetOrgChart|.30|
|Kdo je podřízenou položkou tohoto Tom?|GetOrgChart|.30|

Pokud aplikace má mezi 10 a 20 projevy pomocí různých délek věty, jiné pořadí slov a dokonce během různých slova (synonymům těchto "podřízený", "manage", "zpráva"), může vrátit LUIS nízká pravděpodobnost. Společně tvoří masku usnadňují pochopení důležitosti slovosled, LUIS. 

Způsoby řešení těchto situacích: 

* Záměru skóre je nízká
* Správné záměr není horním skóre, ale příliš blízko horní skóre. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Vzory nejsou zárukou záměr
Vzory pomocí kombinace technologií předpovědi. Nastavení pro šablony utterance záměru ve vzorku není zárukou záměru předpovědí, ale je silný signál. 

## <a name="patterns-do-not-improve-entity-detection"></a>Vzory nevedou k lepšímu zjišťování entit
Zatímco vzory vyžadují entity, vzor nepomůže, detekovat entity. Vzor je určená jenom usnadňují půjček s využitím záměry a rolí.  

Nečekejte zobrazíte predikcí vylepšené entity při sbalení více projevy do jednoho modelu. Pro jednoduché entity má provést, budete muset přidání projevů nebo použijte jiný seznam entit vzorku neaktivují.

## <a name="patterns-use-entity-roles"></a>Vzory použít entitu role
Pokud souvisí kontextově dva nebo více entit ve vzorku, použijte vzory entity [role](luis-concept-roles.md) extrahovat kontextové informace o entitách. To je ekvivalentní na podřízené položky v hierarchické entity, ale je **pouze** k dispozici ve vzorcích. 

## <a name="prediction-scores-with-and-without-patterns"></a>Predikce skóre a bez nich vzory
Zadaný dostatek příklad projevy, LUIS bylo by možné zvýšit důvěru předpovědi vzorů. Vzory zvýšení skóre spolehlivosti aniž by musel zadávat tolik projevy.  

## <a name="pattern-matching"></a>Porovnávání vzorů
Vzor je nalezena shoda podle nejprve zjišťování entit v modelu a ověření zbytek slova a pořadí slov, vzoru. Entity jsou nutné ve vzoru pro odpovídající vzor. Vzor se použije na úrovni tokenu není úroveň znak. 

## <a name="pattern-syntax"></a>Vzor syntaxe
Vzor syntaxe je šablona pro utterance. Šablona by měl obsahovat slova a entity, které chcete porovnat a také slova a interpunkční znaménka, které má být ignorována. Je **není** regulární výraz. 

Entity ve vzorech jsou ohraničeny složených závorek, `{}`. Vzory mohou zahrnovat entit a entit s rolemi. [Pattern.Any](luis-concept-entity-types.md#patternany-entity) je entita použít jenom ve vzorcích. 

Vzor syntaxe podporuje následující syntaxi:

|Funkce|Syntaxe|[Úroveň vnoření](#nesting-syntax)|Příklad:|
|--|--|--|--|
|entita| {} -složené závorky|2|Kde je formulář {název entity}?|
|nepovinné|[] - hranaté závorky<BR><BR>Je stanovený limit 3 na úrovní vnoření z jakékoli kombinace volitelné a seskupování |2|Otazník je volitelné [?]|
|Seskupení|() – závorky|2|je ( \| b).|
|nebo| \| -svislá čára (kanál)<br><br>Je stanovený limit 2 na svislé čáry (nebo) v jedné skupině |-|Kde je formulář ({formuláře krátký název} &#x7c; {formuláře název dlouho} &#x7c; {číslo formuláře})| 
|začátek nebo konec utterance|^ – blikajícího kurzoru|-|^ začít utterance<br>provádí utterance ^<br>^ striktní literálu shodu celého utterance s entitou {number} ^|

## <a name="nesting-syntax-in-patterns"></a>Vnoření syntaxe ve vzorcích

**Volitelné** syntaxe do složených závorek, mohou být vnořené dvě úrovně. Například: `[[this]is] a new form`. Tento příklad umožňuje projevy následující: 

|Příklad vnořené volitelné utterance|Vysvětlení|
|--|--|
|Toto je nový formulář|odpovídá všem slovům ve vzoru|
|je nový formulář|odpovídá vnější volitelné aplikace word a povinný slova ve vzoru|
|nový formulář|odpovídá vyžaduje pouze slova|

**Seskupení** syntaxi se závorkami, mohou být vnořené dvě úrovně. Například: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. To umožňuje libovolné tři entity lze porovnat. 

Pokud Entity1 se umístění s rolemi, jako je například původ (Praha) a cíl (Cairo) a Entity 2 je název známé sestavení ze seznamu entity (RedWest-C), by následující projevy mapování pro tento model:

|Příklad utterance vnořeného seskupení|Vysvětlení|
|--|--|
|RedWest-C|odpovídá vnější seskupení entity|
|Seattle|odpovídá jednomu z entity vnitřní seskupení|
|Káhira|odpovídá jednomu z entity vnitřní seskupení|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Vnoření omezení pro skupiny se syntaxí volitelné

Kombinace **seskupení** s **volitelné** syntaxe je omezena limitem 3 úrovní vnoření.

|Povoleno|Příklad:|
|--|--|
|Ano|([(test1 &#x7c; test2)] &#x7c; test3)|
|Ne|([([test1] &#x7c; test2)] &#x7c; test3)|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Vnoření omezení pro skupiny se syntaxí nebo ing

Kombinace **seskupení** s **nebo ing** syntaxe má omezení 2 svislých pruhů.

|Povoleno|Příklad:|
|--|--|
|Ano|( test1 &#x7c; test2 &#x7c; ( test3 &#x7c; test4 ) )|
|Ne|( test1 &#x7c; test2 &#x7c; test3 &#x7c; ( test4 &#x7c; test5 ) ) |

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

Slova název knihy nejsou matoucí LUIS protože LUIS ví, kde končí název knihy, na základě Pattern.any entity.

## <a name="explicit-lists"></a>Explicitní seznamy

vytvoření [explicitní seznam](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) prostřednictvím rozhraní API pro vytváření povolit výjimku při:

* Váš model obsahuje [Pattern.any](luis-concept-entity-types.md#patternany-entity)
* A jestli vzor syntaxe poskytuje možnost extrakci nesprávné entity podle utterance. 

Předpokládejme například, že máte model obsahující jak syntaxi volitelné `[]`a syntaxi entity `{}`kombinované v způsob, jak extrahovat data nesprávně.

Zvažte vzor "[najít] e-mailu o {subject} [od {osoby}]".

V následující projevy **subjektu** a **osoba** entity se extrahují správně a správně:

|Promluva|Entita|Správné extrakce|
|--|--|:--:|
|e-mailu o PSI ze Chris|předmět = PSI<br>osoba = Jan|✔|
|e-mailu o man z La Mancha|předmět = mužem<br>osoba = La Mancha|X|

V předchozí tabulce by měl být předmět `the man from La Mancha` (název knihy), ale vzhledem k tomu subjekt zahrnuje volitelné slovo `from`, nesprávně očekává název. 

Chcete-li vyřešit tato výjimka se vzorem, přidejte `the man from la mancha` jako explicitní seznam v případě shody s využitím entity {subject} [vytváření rozhraní API pro explicitní seznam](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxe pro označení volitelný text, který v šabloně utterance
Označit volitelný text, který v utterance pomocí syntaxe regulárních výrazů hranatá závorka, `[]`. Nepovinný text můžete vnořit hranaté závorky až pouze dvě závorky.

|Vzor s volitelným textem|Význam|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` a `from {person}` jsou volitelné|
|' Je mi může pomoci [?] | Je volitelný interpunkčního znaménka.|

Interpunkční znaménka (`?`, `!`, `.`) ignorovat a je potřeba je pomocí syntaxe hranatá závorka ve vzorech ignorovat. 

## <a name="pattern-only-apps"></a>Pouze model aplikace
Můžete vytvořit aplikaci s příkazy, které mají žádné projevy příklad, za předpokladu, zde neexistuje vzor pro každý záměr. Pro aplikaci pouze pro vzor vzor nesmí obsahovat zjištěné počítače entity protože vyžadují projevy příklad. 

## <a name="best-practices"></a>Osvědčené postupy
Přečtěte si [osvědčené postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak implementovat vzory v tomto kurzu](luis-tutorial-pattern.md)
