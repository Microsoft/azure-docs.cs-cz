---
title: Návrh s modely – LUIS
titleSuffix: Azure Cognitive Services
description: Jazyk porozumění nabízí několik typů modelů. Některé modely lze použít více než jedním způsobem.
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280864"
---
# <a name="design-with-intent-and-entity-models"></a>Návrh s využitím a modelů entit 

Jazyk porozumění nabízí několik typů modelů. Některé modely lze použít více než jedním způsobem. 

## <a name="v3-authoring-uses-machine-teaching"></a>Vytváření obsahu V3 používá učebnu počítače

LUIS umožňuje lidem snadno naučit koncepty pro počítač. Počítač pak může sestavit modely (funkce odhadu konceptů, jako jsou klasifikátory a extraktory), které se dají použít k napájení inteligentních aplikací. I když je Služba LUIS založená na strojovém učení, není nutné pochopit službu Machine Learning. Místo toho Machine učitelé sdělují koncepty LUIS zobrazením pozitivních a negativních příkladů konceptu a vysvětlením, jak by měl být koncept modelován pomocí dalších souvisejících konceptů. Učitelé můžou také interaktivně zlepšit LUIS model tím, že identifikují a opravují chyby předpovědi. 

## <a name="v3-authoring-model-decomposition"></a>Rekompozice modelu vytváření modelů V3

LUIS podporuje _rozložení modelu_ s rozhraními API pro vytváření obsahu v3 a rozdělí model na menší části. To vám umožní sestavovat modely s jistotou v tom, jak jsou jednotlivé části sestavené a předpovídané.

Dekompozice modelu má následující části:

* [záměry](#intents-classify-utterances)
    * [popisovače](#descriptors-are-features) poskytované funkcemi
* [entity rozpoznané počítačem](#machine-learned-entities)
    * dílčí [součásti](#entity-subcomponents-help-extract-data) (také přeučené entity počítače)
        * [popisovače](#descriptors-are-features) poskytované funkcemi 
        * [omezení](#constraints-are-text-rules) poskytovaná entitami, které nejsou učené počítačem, jako jsou regulární výrazy a seznamy

## <a name="v2-authoring-models"></a>Modely vytváření sestav v2

LUIS podporuje složené entity s rozhraními API pro vytváření obsahu V2. To poskytuje podobné rozložení modelu, ale není stejné jako dekompozice modelu v3. Doporučovanou architekturou modelů je přesun do rozloženého modelu v rozhraních API pro vytváření obsahu v3. 

## <a name="intents-classify-utterances"></a>Záměry klasifikovat projevy

Záměr klasifikuje příklad projevy na učení LUIS o záměru. Příklad projevy v rámci záměru slouží jako pozitivní příklady utterance. Stejné projevy se používají jako negativní příklady ve všech dalších záměrech.

Vezměte v úvahu aplikaci, která potřebuje určit záměr uživatele pro seřazení knihy a aplikace, které potřebují dodací adresu pro zákazníka. Tato aplikace má dva záměry: `OrderBook` a `ShippingLocation`.

Následující utterance je **kladný příklad** pro `OrderBook` záměr a **negativní příklad** pro `ShippingLocation` a `None` záměry: 

`Buy the top-rated book on bot architecture.`

Výsledek dobře navržených záměrů, s jejich příkladem projevy, je předpověď vysokého záměru. 

## <a name="entities-extract-data"></a>Entity extrahují data

Entita představuje jednotku dat, která se má extrahovat z utterance. 

### <a name="machine-learned-entities"></a>Entity rozpoznané počítačem

Entita získaná počítačem je entita nejvyšší úrovně obsahující dílčí komponenty, které jsou také entitami, které se naučily strojově. 

**Použijte počítačově získanou entitu**:

* Pokud jsou dílčí součásti vyžadovány klientskou aplikací
* informace o tom, jak algoritmus strojového učení rozloží entity

Každá dílčí komponenta může mít:

* dílčí součásti
* omezení (entita regulárního výrazu nebo entita seznamu)
* popisovače (funkce jako seznam frází) 

Příkladem uživatelsky řízené entity je objednávka pro lístek roviny. V koncepčním případě je to jediná transakce s mnoha menšími jednotkami dat, jako je datum, čas, množství sedadel, typ sedadla, jako je například první třída nebo autokar, umístění původu, cílové umístění a volba krupice.


### <a name="entity-subcomponents-help-extract-data"></a>Dílčí komponenty entity – pomůžou extrahovat data

Dílčí komponenta je podřízená entita získaná počítačem v rámci nadřazené entity, kterou se naučila počítač. 

**Podsoučást použijte k**těmto akcím:

* rozloží části uživatelsky rozpoznané entity (nadřazená entita).

Následující představuje entitu získanou počítačem, která obsahuje všechny tyto samostatné části dat:

* TravelOrder (entita se naučila počítač)
    * DateTime (předem sestavené datetimeV2)
    * Umístění (entita poučená počítač)
        * Počátek (role byla nalezena prostřednictvím kontextu, jako je například `from`)
        * Cíl (role byla nalezena prostřednictvím kontextu, jako je například `to`)
    * Pracovní stanice (entita s učením počítače)
        * Množství (předem sestavené číslo)
        * Kvalita (entita poučená počítačem s popisovačem seznamu frází)
    * Jídla (entita poučená počítačem s omezením entity seznam jako potravinové volby)

Některá z těchto dat, jako je zdrojové umístění a cílové umístění, by se měla považovat z kontextu utterance, třeba s takovými slovy `from` a `to`. Ostatní části dat lze extrahovat pomocí přesných shod řetězců (`Vegan`) nebo předem sestavených entit (geographyV2 `Seattle` a `Cairo`). 

Navrhujete, jak jsou data shodná a extrahována podle modelů, které zvolíte, a jak je nakonfigurujete.

### <a name="constraints-are-text-rules"></a>Omezení jsou textová pravidla

Omezení je pravidlo pro porovnání textu, které poskytuje entita získaná mimo počítač, jako je například entita regulárního výrazu nebo seznam entit. Omezení je použito v době předpovědi k omezení předpovědi a k poskytnutí řešení entity, které vyžaduje klientská aplikace. Tato pravidla definujete při vytváření dílčí součásti. 

**Použít omezení**:
* Pokud znáte přesný text k extrakci.

Omezení zahrnují:

* entity [regulárních výrazů](reference-entity-regular-expression.md)
* [seznam](reference-entity-list.md) entit 
* [předem připravené](luis-reference-prebuilt-entities.md) entity

V případě, že budete pokračovat v příkladu lístku roviny, mohou být kódy letiště v entitě seznamu pro přesné shody textu. 

V seznamu letišť je položka seznamu pro Seattle název města `Seattle` a synonyma pro Seattle zahrnují kód letiště pro Seattle spolu s okolními městy a městy:

|synonyma entit seznamu `Seattle`|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

Pokud chcete rozpoznávat pouze 3 kódy pro letištní kódy, použijte jako omezení regulární výraz. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>Záměry versus entity

Záměrem je požadovaný výsledek _celého_ utterance, zatímco entity jsou částí dat extrahovaných z utterance. Obvykle jsou záměry svázány s akcemi, které by měla klientská aplikace brát v úvahu, a entity jsou informace potřebné k provedení této akce. Z hlediska programování by záměr aktivoval volání metody a entity budou použity jako parametry pro volání metody.

Tento utterance _musí_ mít záměr a _může_ mít entity:

`Buy an airline ticket from Seattle to Cairo`

Tento utterance má jeden záměr:

* Nákup lístku roviny

Tento utterance _může_ mít několik entit:

* Umístění Seattle (Origin) a Cairo (cíl)
* Množství jediného lístku

## <a name="descriptors-are-features"></a>Popisovače jsou funkce

Popisovač je funkce použitá pro model v době školení, včetně seznamů frází a entit. 

**Použijte popisovač, pokud chcete**:

* Zvyšte význam slov a frází identifikovaných popisovačem.
* LUIS doporučit nový text nebo frázi, které se doporučují pro popisovač
* Oprava chyby v školicích datech

## <a name="next-steps"></a>Další kroky

* Pochopení [záměrů](luis-concept-intent.md) a [entit](luis-concept-entity-types.md). 