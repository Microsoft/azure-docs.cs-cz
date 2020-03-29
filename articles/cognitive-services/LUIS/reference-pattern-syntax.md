---
title: Odkaz na syntaxi vzoru – LUIS
titleSuffix: Azure Cognitive Services
description: Vytvořte entity pro extrahování klíčových dat z projevy uživatelů v aplikacích language understanding (LUIS). Extrahovaná data používá klientská aplikace.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 696f4bdc22bed01a4b5be8bff63ade482a8dbe0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219728"
---
# <a name="pattern-syntax"></a>Syntaxe vzoru

Syntaxe vzoru je šablona pro utterance. Šablona by měla obsahovat slova a entity, které chcete spárovat, stejně jako slova a interpunkci, které chcete ignorovat. **Nejedná** se o regulární výraz.

> [!CAUTION]
> Vzorky zahrnují pouze počítačem učené entity, nikoli dílčí součásti.

Entity ve vzorech jsou obklopeny složených `{}`závorek . Vzorky mohou zahrnovat entity a entity s rolemi. [Pattern.any](luis-concept-entity-types.md#patternany-entity) je entita používaná pouze ve vzorcích.

Syntaxe vzoru podporuje následující syntaxi:

|Funkce|Syntaxe|Úroveň vnoření|Příklad|
|--|--|--|--|
|entita| {}- kudrnaté držáky|2|Kde je formulář {entity-name}?|
|optional|[] - hranaté závorky<BR><BR>Existuje limit 3 na úrovních vnoření libovolné kombinace volitelných a seskupovacích |2|Otazník je nepovinný [?]|
|grouping|() - závorky|2|je (a \| b)|
|– nebo –| \|- svislá tyč (trubka)<br><br>Na svislých tyčích (Nebo) v jedné skupině je limit 2 |-|Kde je formulář ({form-name-short} &#x7c; {form-name-long} &#x7c; {form-number})|
|začátek a/nebo konec utterance|↑ - stříška|-|^zahájit promluvu<br>utterance se provádí ^<br>^striktní shoda literálu celé entity s entitou {number} ^|

## <a name="nesting-syntax-in-patterns"></a>Syntaxe vnoření do vzorků

**Volitelná** syntaxe se čtvercovými závorkami může být vnořena dvě úrovně. Například: `[[this]is] a new form`. Tento příklad umožňuje následující projevy:

|Vnořený příklad volitelné holčení|Vysvětlení|
|--|--|
|toto je nový formulář|odpovídá všem slovům ve vzoru|
|je nová forma|odpovídá vnějším volitelným slovům a nevolitelným slovům ve vzoru|
|nový formulář|odpovídá pouze požadovaným slovům|

Syntaxe **seskupení** s závorky může být vnořena dvě úrovně. Například: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Tato funkce umožňuje spárovat některou ze tří entit.

Pokud Entity1 je Umístění s rolemi, jako je původ (Seattle) a cíl (Káhira) a Entity 2 je známý název budovy z entity seznamu (RedWest-C), následující projevy by mapovat na tento vzor:

|Příklad vnořeného seskupení utterance|Vysvětlení|
|--|--|
|Červený západ-C|odpovídá vnější entitě seskupení|
|Seattle|odpovídá jedné z vnitřních entit seskupení.|
|Cairo|odpovídá jedné z vnitřních entit seskupení.|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Vnoření omezení pro skupiny s volitelnou syntaxí

Kombinace **seskupení** s **volitelnou** syntaxí má limit 3 úrovně vnoření.

|Povoleno|Příklad|
|--|--|
|Ano|( [ ( test1 &#x7c; test2 ) ] &#x7c; zkouška3 )|
|Ne|[ [ [ zkouška1 ] &#x7c; test2 ) ] &#x7c; zkouška3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limity vnoření pro skupiny se syntaxí nebo s ním

Kombinace **seskupování** s **syntaxí nebo nebo má** limit 2 svislé pruhy.

|Povoleno|Příklad|
|--|--|
|Ano|( zkouška1 &#x7c; zkouška2 &#x7c; ( zkouška3 &#x7c; zkouška4 )|
|Ne|( zkouška1 &#x7c; zkouška2 &#x7c; zkouška3 &#x7c; ( zkouška4 &#x7c; zkouška5 ) ) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntaxe přidání entity do šablony vzoru
Chcete-li do šablony vzoru přidat entitu, obklopte `Who does {Employee} manage?`název entity složenými závorkami, například .

|Vzor s entitou|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntaxe přidání entity a role do šablony vzoru
Role entity je označena jako `{entity:role}` s názvem entity následovaným dvojtečkou a potom názvem role. Chcete-li do šablony vzoru přidat entitu s rolí, obklopte název `Book a ticket from {Location:Origin} to {Location:Destination}`entity a název role složenými závorkami, například .

|Vzor s rolemi entit|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntaxe pro přidání pattern.any do šablony vzoru
Entita Pattern.any umožňuje přidat entitu různé délky do vzoru. Tak dlouho, dokud je sledována šablona vzoru, pattern.any může mít libovolnou délku.

Chcete-li do šablony vzoru přidat entitu **Pattern.any,** obklopte entitu Pattern.any s složenými závorkami, například `How much does {Booktitle} cost and what format is it available in?`.

|Vzor s entitou Pattern.any|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Knižní tituly ve vzoru|
|--|
|Kolik **stojí tato kniha** a v jakém formátu je k dispozici?|
|Kolik **stojí dotaz** a v jakém formátu je k dispozici?|
|Kolik stojí **The Curious Incident of the Dog in the Night-Time** a v jakém formátu je k dispozici?|

Slova názvu knihy nejsou matoucí pro LUIS, protože LUIS ví, kde končí název knihy, na základě Pattern.any entity.

## <a name="explicit-lists"></a>Explicitní seznamy

vytvořte [explicitní seznam](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) prostřednictvím rozhraní API pro vytváření, abyste povolili výjimku, když:

* Váš vzor obsahuje [Pattern.any](luis-concept-entity-types.md#patternany-entity)
* A že syntaxe vzoru umožňuje možnost extrakce nesprávné entity na základě utterance.

Předpokládejme například, že máte vzorek obsahující `[]`jak volitelnou `{}`syntaxi , tak syntaxi entity , zkombinují se způsobem, jak nesprávně extrahovat data.

Zvažte vzor '[najít] e-mail o {subject} [od {person}]'.

V následujících projevech jsou subjekt a **entita subjektu** **subjektu** objektu a osoby extrahovány správně a nesprávně:

|Promluva|Entita|Správná extrakce|
|--|--|:--:|
|e-mail o psech od Chrise|subject=psi<br>person=Chris|✔|
|e-mail o muži z La Mancha|subject= muž<br>person=La Mancha|×|

V předchozí tabulce by měl `the man from La Mancha` být předmět (název knihy), ale protože `from`předmět obsahuje volitelné slovo , název je nesprávně předpovězen.

Chcete-li opravit tuto výjimku vzoru, přidejte `the man from la mancha` jako explicitní shodu seznamu pro entitu {subject} pomocí [vývojového rozhraní API pro explicitní seznam](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxe pro označení volitelného textu v utterance šablony
Označte volitelný text v utterance pomocí `[]`syntaxe čtvercové závorky regulárních výrazů . Volitelný text může vnořit pouze hranatá závorka až do dvou závorek.

|Vzorek s volitelným textem|Význam|
|--|--|
|`[find] email about {subject} [from {person}]`|`find`a `from {person}` jsou volitelné|
|'Můžete mi pomoci[?]|Interpunkční znaménko je volitelné|

Interpunkční znaménka (`?`, `!`, `.`) by měla být ignorována a je třeba je ignorovat pomocí syntaxe hranatých závorek ve vzorcích.

## <a name="next-steps"></a>Další kroky

Další informace o vzorcích:

* [Jak přidat vzory](luis-how-to-model-intent-pattern.md)
* [Jak přidat entitu pattern.any](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Koncepty vzorů](luis-concept-patterns.md)

Pochopte, jak je v odpovědi json vrácen [mínění.](luis-reference-prebuilt-sentiment.md)