---
title: Design s modely - LUIS
titleSuffix: Azure Cognitive Services
description: Jazykové porozumění poskytuje několik typů modelů. Některé modely lze použít více než jedním způsobem.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d721ceb25b3ce2408563a0bed16457d05affe7b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219992"
---
# <a name="design-with-intent-and-entity-models"></a>Návrh s modely záměru a entit 

Jazykové porozumění poskytuje několik typů modelů. Některé modely lze použít více než jedním způsobem. 

## <a name="v3-authoring-uses-machine-teaching"></a>V3 Authoring používá strojové učení

Služba LUIS umožňuje uživatelům snadno učit koncepty do počítače. Stroj pak může vytvářet modely (funkční aproximace konceptů, jako jsou klasifikátory a extraktory), které lze použít k napájení inteligentních aplikací. Zatímco služba LUIS je poháněna strojovým učením, pochopení strojového učení není nutné k jeho použití. Místo toho učitelé strojů komunikovat koncepty LUIS zobrazením pozitivní a negativní příklady konceptu a vysvětluje, jak by měl být modelován koncept pomocí jiných souvisejících konceptů. Učitelé mohou také interaktivně vylepšit model služby LUIS tím, že identifikují a opravují chyby předpovědi. 

## <a name="v3-authoring-model-decomposition"></a>Rozklad vývojového modelu V3

Služba LUIS podporuje _rozklad modelu_ pomocí vytváření v3 API, rozdělení modelu na menší části. To vám umožní vytvářet modely s důvěrou v tom, jak jsou jednotlivé části konstruovány a předpovězeny.

Rozklad modelu má následující části:

* [Záměry](#intents-classify-utterances)
    * [popisovače](#descriptors-are-features) poskytované funkcemi
* [strojově naučené entity](#machine-learned-entities)
    * [dílčí součásti](#entity-subcomponents-help-extract-data) (také strojově učené entity)
        * [popisovače](#descriptors-are-features) poskytované funkcemi 
        * [omezení](#constraints-are-text-rules) poskytovaná nestrojově získanými entitami, jako jsou regulární výrazy a seznamy

## <a name="v2-authoring-models"></a>Vývojové modely V2

Služba LUIS podporuje složené entity pomocí vytváření v2 API. To poskytuje podobný rozklad modelu, ale není stejný jako rozklad modelu V3. Doporučená architektura modelu je přesunout na rozložení modelu v V3 vytváření API. 

## <a name="intents-classify-utterances"></a>Záměry klasifikují projevy

Záměr klasifikuje příklad projevy učit LUIS o záměru. Příklad projevy v rámci záměru se používají jako kladné příklady utterance. Tyto stejné projevy se používají jako negativní příklady ve všech ostatních záměrech.

Zvažte aplikaci, která potřebuje určit záměr uživatele objednat knihu a aplikaci, která potřebuje dodací adresu pro zákazníka. Tato aplikace má dva `OrderBook` `ShippingLocation`záměry: a .

Následující utterance je **pozitivní** příklad `OrderBook` pro záměr a `ShippingLocation` negativní `None` **příklad** pro a záměry: 

`Buy the top-rated book on bot architecture.`

Výsledkem dobře navržené záměry, s jejich příklad projevy, je vysoký záměr předpověď. 

## <a name="entities-extract-data"></a>Entity extrahují data

Entita představuje jednotku dat, kterou chcete extrahovat z utterance. 

### <a name="machine-learned-entities"></a>Strojově naučené entity

Entita načený počítačem je entita nejvyšší úrovně obsahující dílčí součásti, které jsou také entity získané počítačem. 

**Použijte entitu naučenou strojem**:

* pokud klientská aplikace potřebuje dílčí součásti
* pomoci algoritmu strojového učení rozložit entity

Každá dílčí součást může mít:

* Podsoučásti
* omezení (entita regulárního výrazu nebo entita seznamu)
* popisovače (funkce, jako je seznam frází) 

Příkladem entity získané strojem je objednávka letenky. Koncepčně se jedná o jednu transakci s mnoha menšími jednotkami dat, jako je datum, čas, množství sedadel, typ sedadla, jako je první třída nebo trenér, místo původu, místo určení a výběr jídla.


### <a name="entity-subcomponents-help-extract-data"></a>Dílčí součásti entit pomáhají extrahovat data

Dílčí součást je podřízená entita naučná počítačem v rámci nadřazené entity naučené počítačem. 

**Pomocí dílčí součásti můžete:**

* rozkládají části strojově učené entity (nadřazené entity).

Následující představuje strojově načený entitu se všemi těmito samostatnými částmi dat:

* TravelOrder (strojově naučená entita)
    * DateTime (předem sestavené datetimeV2)
    * Umístění (entita naučená strojem)
        * Původ (role nalezená `from`v kontextu, například )
        * Cíl (role nalezená `to`v kontextu, například )
    * Sezení (strojově učená entita)
        * Množství (předem sestavené číslo)
        * Kvalita (strojově naučená entita s deskriptorem seznamu frází)
    * Stravování (strojově naučená entita s omezením subjektu seznamu jako výběru potravin)

Některé z těchto údajů, jako je například umístění původu a cílové umístění, by měly `from` `to`být poučeny z kontextu utterance, možná s takovou formulací jako a . Ostatní části dat lze extrahovat s`Vegan`přesnými řetězcovými shodami ( `Seattle` ) `Cairo`nebo předem vytvořenými entitami (geographyV2 of a). 

Navrhnout, jak jsou data spárována a extrahována podle modelů, které zvolíte a jak je nakonfigurujete.

### <a name="constraints-are-text-rules"></a>Omezení jsou pravidla textu

Omezení je pravidlo pro porovnávání textu poskytované entitou, která není naučená počítačem, například entita regulárního výrazu nebo entita seznamu. Omezení se použije v době předpovědi k omezení předpovědi a poskytnutí řešení entity potřebné pro klientskou aplikaci. Tato pravidla definujete při vytváření dílčí součásti. 

**Použít omezení**:
* když znáte přesný text k extrahování.

Omezení zahrnují:

* entity [regulárního výrazu](reference-entity-regular-expression.md)
* [seznam](reference-entity-list.md) entit 
* [předem sestavené](luis-reference-prebuilt-entities.md) entity

V příkladu letenky mohou být kódy letiště v entitě Seznam pro přesné textové shody. 

Pro seznam letišť, položka seznamu pro Seattle `Seattle` je název města, a synonyma pro Seattle patří kód letiště pro Seattle spolu s okolními městy:

|`Seattle`Seznam synonym entity|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

Chcete-li rozpoznat pouze kódy se 3 písmeny pro kódy letišť, použijte jako omezení regulární výraz. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>Záměry versus entity

Záměr je požadovaný výsledek _celé_ utterance zatímco entity jsou kusy dat extrahované z utterance. Záměry jsou obvykle vázány na akce, které by měla klientská aplikace provést, a entity jsou informace potřebné k provedení této akce. Z hlediska programování záměr by aktivovat volání metody a entity by být použity jako parametry pro volání této metody.

Tento utterance _musí_ mít záměr a _může_ mít entity:

`Buy an airline ticket from Seattle to Cairo`

Tento utterance má jediný záměr:

* Nákup letenky

Tento utterance _může_ mít několik entit:

* Umístění Seattlu (původ) a Káhira (cíl)
* Množství jedné jízdenky

## <a name="descriptors-are-features"></a>Deskriptory jsou funkce

Deskriptor je funkce aplikovaná na model v době školení, včetně seznamů frází a entit. 

**Popis použijte, pokud chcete**:

* význam slov a frází identifikovaných deskriptorem
* doporučovali luis nový text nebo fráze pro popisovač
* oprava chyby na trénovacích datech

## <a name="next-steps"></a>Další kroky

* Pochopit [záměry](luis-concept-intent.md) a [entity](luis-concept-entity-types.md). 