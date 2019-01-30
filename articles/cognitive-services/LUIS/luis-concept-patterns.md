---
title: Vzory pomáhají predikcí
titleSuffix: Language Understanding - Azure Cognitive Services
description: Vzor umožňuje získat vyšší přesnost pro záměru bez zadání projevy mnoho více.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: diberry
ms.openlocfilehash: ed7f50d51b46b9e6204522751fdc1a1e996442f2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207572"
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

Pokud aplikace má mezi 10 a 20 projevy pomocí různých délek věty, jiné pořadí slov a dokonce během různých slova (synonymům těchto "podřízený", "manage", "zpráva"), může vrátit LUIS nízká pravděpodobnost. Pokud chcete pomoct LUIS pochopení důležitosti pořadí slov, společně tvoří masku. 

Způsoby řešení těchto situacích: 

* Když je nízká záměru skóre
* Při správné záměr není horním skóre, ale příliš blízko horní skóre. 

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

Entity ve vzorech jsou ohraničeny složených závorek, `{}`. Vzory mohou zahrnovat entit a entit s rolemi. Pattern.any je entita použít jenom ve vzorcích. Syntaxe je vysvětlené v následujících částech.

### <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntaxe pro přidání entity do modelu šablony
Přidání entity do modelu šablony, uzavřete název entity se složenými závorkami, `Who does {Employee} manage?`. 

|Vzor s entitou|
|--|
|`Who does {Employee} manage?`|

### <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntaxe pro vzor šablony přidat entitu a role
Roli entity je označena jako `{entity:role}` s názvem entity, za nímž následuje dvojtečka, potom název role. Přidat entitu s rolí do šablony vzor, před a za název entity a názvu role pomocí složených závorek, například `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Vzor s rolemi entity|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

### <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntaxe pro přidání pattern.any vzor šablony
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

V těchto příkladech název knihy nejsou kontextové slova název knihy pro LUIS matoucí. Služba LUIS ví, kde název knihy skončí, protože je ve vzoru a označené Pattern.any entity.

### <a name="explicit-lists"></a>Explicitní seznamy
Pokud váš model obsahuje Pattern.any a umožňuje vzor syntaxe pro možnost extrakci nesprávné entity podle utterance, vytvořte [explicitní seznam](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) prostřednictvím rozhraní API pro vytváření povolit výjimku. 

Předpokládejme například, že máte model obsahující jak syntaxi volitelné `[]`a syntaxi entity `{}`kombinované v způsob, jak extrahovat data nesprávně.

Zvažte vzor "[najít] e-mailu o {subject} [od {osoby}]". V následující projevy **subjektu** a **osoba** entity se extrahují správně a správně:

|Promluva|Entita|Správné extrakce|
|--|--|:--:|
|e-mailu o PSI ze Chris|předmět = PSI<br>osoba = Jan|✔|
|e-mailu o man z La Mancha|předmět = mužem<br>osoba = La Mancha|X|

V předchozí tabulce, utterance `email about the man from La Mancha`, předmět by měl být `the man from La Mancha` (název knihy), ale vzhledem k tomu subjekt zahrnuje volitelné slovo `from`, nesprávně očekává název. 

Chcete-li vyřešit tato výjimka se vzorem, přidejte `the man from la mancha` jako explicitní seznam v případě shody s využitím entity {subject} [vytváření rozhraní API pro explicitní seznam](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

### <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxe pro označení volitelný text, který v šabloně utterance
Označit volitelný text, který v utterance pomocí syntaxe regulárních výrazů hranatá závorka, `[]`. Nepovinný text můžete vnořit hranaté závorky až pouze dvě závorky.

|Vzor s volitelným textem|
|--|
|`[find] email about {subject} [from {person}]`|

Interpunkčních znamének, jako `.`, `!`, a `?` můžete ignorovat pomocí hranatých závorek. Pokud chcete ignorovat těchto známky, každá značka musí být v samostatné vzor. Volitelné syntaxe v současné době nepodporuje ignoruje se položka v seznamu několik položek.

## <a name="patterns-only"></a>Pouze vzory
Služba LUIS umožňuje aplikacím bez jakékoli projevy příklad v záměr. Toto použití je povoleno pouze v případě, že vzorky se používají. Vzory vyžadují minimálně jednu entitu v každý vzorek. Pro aplikaci pouze pro vzor vzor nesmí obsahovat zjištěné počítače entity protože vyžadují projevy příklad. 

## <a name="best-practices"></a>Osvědčené postupy
Přečtěte si [osvědčené postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak implementovat vzory v tomto kurzu](luis-tutorial-pattern.md)
