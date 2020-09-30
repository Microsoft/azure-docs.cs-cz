---
title: Referenční informace o syntaxi vzoru – LUIS
description: Vytvořte entity pro extrakci klíčových dat z uživatelských projevy v aplikacích Language Understanding (LUIS). Klientská aplikace používá extrahovaná data.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 3caccd6766226ce68b371856b081b052c1033f71
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91542185"
---
# <a name="pattern-syntax"></a>Syntaxe vzoru

Syntaxe vzoru je šablona pro utterance. Šablona by měla obsahovat slova a entity, které chcete porovnat, a také slova a [interpunkční znaménka](luis-reference-application-settings.md#punctuation-normalization) , která chcete ignorovat. Nejedná **se o regulární** výraz.

> [!CAUTION]
> Vzory zahrnují jenom nadřazené entity strojového učení, ne subentity.

Entity v vzorcích jsou obklopené složenými závorkami `{}` . Vzory mohou zahrnovat entity a entity s rolemi. [Vzor. any](luis-concept-entity-types.md#patternany-entity) je entita, která se používá pouze ve vzorcích.

Syntaxe vzoru podporuje následující syntaxi:

|Funkce|Syntaxe|Úroveň vnoření|Příklad|
|--|--|--|--|
|entita| {} – složené závorky|2|Kde je tvar {entity-Name}?|
|optional|[] – hranaté závorky<BR><BR>Existuje limit 3 pro vnořování úrovní jakékoli kombinace Optional and Grouping. |2|Otazník je nepovinný [?].|
|grouping|() – závorky|2|je (a \| b)|
|nebo| \| – svislá čára (svislá čára)<br><br>U svislých pruhů (nebo) v jedné skupině je povolený limit 2. |-|Kde je Form ({Form-Name-short} &#x7c; {Form-Name-Long} &#x7c; {Form-Number})|
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

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntaxe pro přidání entity do šablony vzoru
Chcete-li přidat entitu do šablony vzoru, uzavřete název entity do složených závorek, například `Who does {Employee} manage?` .

|Vzor s entitou|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntaxe pro přidání entity a role do šablony vzoru
Role entity se označuje jako `{entity:role}` název entity následovaný dvojtečkou a názvem role. Chcete-li přidat entitu s rolí do šablony vzoru, uzavřete název entity a název role se složenými závorkami, například `Book a ticket from {Location:Origin} to {Location:Destination}` .

|Vzor s rolemi entit|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntaxe pro přidání vzoru. jakékoli do šablony vzoru
Vzor. kterákoli entita umožňuje přidat do vzoru entitu s různou délkou. Dokud je šablona vzoru následována, vzor. kterákoli z nich může být libovolná délka.

Chcete-li přidat **vzor. libovolnou** entitu do šablony vzoru, uzavřete vzorek. libovolnou entitu se složenými závorkami, například `How much does {Booktitle} cost and what format is it available in?` .

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

Předpokládejme například, že máte vzor obsahující jak volitelnou syntaxi, `[]` tak i syntaxi entit, a `{}` v kombinaci způsobem pro nekorektně extrahování dat.

Vezměte v úvahu vzor e-mailu [najít] o {Subject} [z {Person}].

V následujícím projevy jsou subjekt a **osoba** správně extrahovány **a nesprávně** :

|Promluva|Entita|Opravit extrakci|
|--|--|:--:|
|e-mail o psy z Chrisho|Předmět = psi<br>person = Novák|✔|
|e-mail o člověku z La Mancha|Předmět = muž<br>person = La Mancha|X|

V předchozí tabulce by měl předmět obsahovat `the man from La Mancha` (název knihy), ale vzhledem k tomu, že předmět obsahuje volitelné slovo `from` , název je nesprávně předpovězený.

Chcete-li tuto výjimku opravit na vzor, přidejte `the man from la mancha` jako explicitní shodu seznamu pro entitu {Subject} pomocí [rozhraní API pro vytváření obsahu pro explicitní seznam](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxe k označení volitelného textu v šabloně utterance
Označte Nepovinný text v utterance pomocí syntaxe hranaté závorky regulárního výrazu `[]` . Volitelný text může vnořovat hranaté závorky až do dvou závorek.

|Vzor s volitelným textem|Význam|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` a `from {person}` jsou volitelné|
|' Vám může pomáhat: [?]|Interpunkční znaménko je volitelné.|

Interpunkční znaménka ( `?` , `!` , `.` ) by měla být ignorována a je třeba je ignorovat pomocí syntaxe hranaté závorky ve vzorcích.

## <a name="next-steps"></a>Další kroky

Další informace o vzorcích:

* [Postup přidání vzorů](luis-how-to-model-intent-pattern.md)
* [Postup přidání vzoru. kterákoli entita](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Koncepty vzorů](luis-concept-patterns.md)

Zjistěte, jak je vrácen [mínění](luis-reference-prebuilt-sentiment.md) v odpovědi. JSON.