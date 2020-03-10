---
title: Referenční informace o syntaxi vzoru – LUIS
titleSuffix: Azure Cognitive Services
description: Vytvořte entity pro extrakci klíčových dat z uživatelských projevy v aplikacích Language Understanding (LUIS). Klientská aplikace používá extrahovaná data.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 696f4bdc22bed01a4b5be8bff63ade482a8dbe0a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393846"
---
# <a name="pattern-syntax"></a>Syntaxe vzoru

Vzor syntaxe je šablona pro utterance. Šablona by měl obsahovat slova a entity, které chcete porovnat a také slova a interpunkční znaménka, které má být ignorována. Nejedná **se o regulární** výraz.

> [!CAUTION]
> Vzory zahrnují pouze nadřazené entity, které se strojově naučily, ne subsoučásti.

Entity v vzorcích jsou obklopené složenými závorkami `{}`. Vzory mohou zahrnovat entit a entit s rolemi. [Vzor. any](luis-concept-entity-types.md#patternany-entity) je entita, která se používá pouze ve vzorcích.

Syntaxe vzoru podporuje následující syntaxi:

|Funkce|Syntaxe|Úroveň vnoření|Příklad|
|--|--|--|--|
|entita| {} – složené závorky|2|Kde je tvar {entity-Name}?|
|volitelné|[] – hranaté závorky<BR><BR>Existuje limit 3 pro vnořování úrovní jakékoli kombinace Optional and Grouping. |2|Otazník je nepovinný [?].|
|sloučení|() – závorky|2|je (a \| b)|
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
Chcete-li přidat entitu do šablony vzoru, uzavřete název entity do složených závorek, například `Who does {Employee} manage?`.

|Vzor s entitou|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntaxe pro vzor šablony přidat entitu a role
Role entity je označená jako `{entity:role}` s názvem entity následovaným dvojtečkou a názvem role. Chcete-li přidat entitu s rolí do šablony vzoru, uzavřete název entity a název role se složenými závorkami, například `Book a ticket from {Location:Origin} to {Location:Destination}`.

|Vzor s rolemi entity|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntaxe pro přidání pattern.any vzor šablony
Pattern.any entity umožňuje přidat entitu různé délky se vzorem. Za předpokladu, následuje vzor šablony, může být pattern.any jakékoli délky.

Chcete-li přidat **vzor. libovolnou** entitu do šablony vzoru, uzavřete vzorek. libovolnou entitu se složenými závorkami, například `How much does {Booktitle} cost and what format is it available in?`.

|Vzor s entitou Pattern.any|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Názvů knih ve vzoru|
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

Zvažte vzor "[najít] e-mailu o {subject} [od {osoby}]".

V následujícím projevy jsou subjekt a **osoba** správně extrahovány **a nesprávně** :

|Promluva|Entita|Správné extrakce|
|--|--|:--:|
|e-mailu o PSI ze Chris|předmět = PSI<br>osoba = Jan|✔|
|e-mailu o man z La Mancha|předmět = mužem<br>osoba = La Mancha|X|

V předchozí tabulce by měl být předmět `the man from La Mancha` (název knihy), ale protože předmět obsahuje volitelný `from`Word, název je nesprávně předpovězený.

Chcete-li tuto výjimku opravit na vzor, přidejte `the man from la mancha` jako explicitní shodu seznamu pro entitu {Subject} pomocí [rozhraní API pro vytváření explicitního seznamu](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxe pro označení volitelný text, který v šabloně utterance
Označte volitelný text v utterance pomocí syntaxe hranaté závorky regulárního výrazu `[]`. Nepovinný text můžete vnořit hranaté závorky až pouze dvě závorky.

|Vzor s volitelným textem|Význam|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` a `from {person}` jsou volitelné.|
|' Vám může pomáhat: [?]|Interpunkční znaménko je volitelné.|

Interpunkční znaménka (`?`, `!`, `.`) by měla být ignorována a je třeba je ignorovat pomocí syntaxe hranaté závorky ve vzorcích.

## <a name="next-steps"></a>Další kroky

Další informace o vzorcích:

* [Postup přidání vzorů](luis-how-to-model-intent-pattern.md)
* [Postup přidání vzoru. kterákoli entita](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Koncepty vzorů](luis-concept-patterns.md)

Zjistěte, jak je vrácen [mínění](luis-reference-prebuilt-sentiment.md) v odpovědi. JSON.