---
title: Typy entit – LUIS
description: Entita extrahuje data z utterance uživatele v předpokládaném modulu runtime. _Volitelným_ a sekundárním účelem je zvýšit předpověď záměru nebo jiných entit pomocí entity jako funkce.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 44cffecd653ec2ec748e73d01dc86a87cfcd7de9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500324"
---
# <a name="entities-in-luis"></a>Entity v LUIS

Entita je položka nebo prvek, který je relevantní pro záměr uživatele. Entity definují data, která je možné extrahovat z utterance a jsou zásadní pro dokončení akce vyžadované uživatelem. Například:

|Výrok|Předpokládané záměr|Extrahované entity|Vysvětlení|
|--|--|--|--|
|Ahoj, jak se máš?|Pozdrav|-|Nic k extrakci.|
|Chci objednat malé Pizza|orderPizza| nejmenší | Entita "size" je extrahována jako "malá".|
|Vypnout Bedroom světlo|turnOff| "bedroom" | Entita "místnost" je extrahována jako "Bedroom".|
|Kontrolovat rovnováhu v účtu úspory na konci v 4406|checkBalance| "úspory", "4406" | entita "accountType" se extrahuje jako "úspory" a entita "accountNumber" se extrahuje jako "4406".|
|Koupit 3 lístky do Praha|buyTickets| "3"; "New York" | entita "ticketsCount" je extrahována jako "3" a "cílová" entita je extrahována jako "New York".|

Entity jsou volitelné, ale doporučené. Pro každý koncept ve vaší aplikaci nemusíte vytvářet entity, jenom pro ty, kde:

* Klientská aplikace potřebuje data, nebo 
* Entita slouží jako pomocný parametr nebo signál k jiné entitě nebo záměru. Další informace o entitách jako funkce najdete [v entitách jako funkce](#entities-as-features).

## <a name="entity-types"></a>Typy entit

Chcete-li vytvořit entitu, je nutné zadat název a typ. V LUIS existuje několik typů entit. 

### <a name="list-entity"></a>Seznam entit

Entita seznamu představuje pevně uzavřenou sadu příbuzných slov spolu s jejich synonymy. Entity seznam můžete použít k rozpoznání více synonym nebo variací a k extrakci normalizovaných výstupů. Pomocí možnosti *doporučit* můžete zobrazit návrhy nových slov na základě aktuálního seznamu. 

Entita seznamu není zjištěna počítačem, což znamená, že LUIS nezjistí další hodnoty pro entity seznamu. LUIS označí jakoukoli shodu s položkou v libovolném seznamu jako entitu v odpovědi.

Párování v seznamu entit rozlišuje velká a malá písmena a musí být pro extrakci přesné shody. Normalizované hodnoty se použijí i při shodě s entitou seznam. Například:

|Normalizovaná hodnota|Synonyma|
|--|--|
|Malá|SM, SML, malý, nejmenší|
|Střední|MD, MDM, Regular, Average, prostřední|
|Velká|LG, LRG, velký|

Další informace najdete v [článku referenční informace o entitách seznamu](reference-entity-list.md) .

### <a name="regex-entity"></a>Entita Regex

Entita regulárního výrazu extrahuje entitu na základě vzoru regulárního výrazu, který zadáte. Ignoruje velikost písmen a ignoruje kulturní variantu. Regulární výraz je nejvhodnější pro strukturovaný text nebo předdefinované sekvence alfanumerických hodnot, které jsou očekávány v určitém formátu. Například:

|Entita|Regulární výraz|Příklad|
|--|--|--|
|Číslo letu|letů [A-Z] {2} [0-9]{4}| letů jako 1234|
|Číslo platební karty|[0-9]{16}|5478789865437632|

Další informace najdete v [článku referenční informace o entitách Regex](reference-entity-regular-expression.md) .

### <a name="prebuilt-entity"></a>Předem připravená entita

LUIS nabízí sadu předem připravených entit pro rozpoznávání běžných typů dat, jako je název, datum, číslo a měna.  Chování předem sestavených entit je opraveno. Předem připravená podpora entit se liší v závislosti na jazykové verzi aplikace LUIS. Například:

|Předem vytvořená entita|Příklad hodnoty|
|--|--|
|PersonName|James, faktura, kdo|
|DatetimeV2|2019-05-02, Květen 2. května 8:00 2. května 2019|

Další informace najdete v [článku referenční informace k předem vytvořeným entitám](./luis-reference-prebuilt-entities.md) .

### <a name="patternany-entity"></a>Vzor. kterákoli entita

Vzor. Každá entita je zástupný symbol s proměnlivou délkou, který se používá jenom v šabloně vzoru utterance k označení, kde začíná a končí entita. Sleduje konkrétní pravidlo nebo vzor a nejlépe se používá pro věty s pevnou lexikální strukturou. Například:

|Ukázková promluva|Vzor|Entita|
|--|--|--|
|Můžu si Burger?|Můžu mít {jídlo} [prosím] [?]| burger
|Můžu mít Pizza?|Můžu mít {jídlo} [prosím] [?]| Pizza
|Kde můžu najít skvělé Gatsby?|Kde najdu {Book}?| Skvělé Gatsby|

Další informace najdete v [článku o modelu. všechny entity](./reference-entity-pattern-any.md) .

### <a name="machine-learned-ml-entity"></a>Entita získaná strojem (ML)

Entita získaná počítačem používá kontext k extrakci entit na základě popisků označených popisky. Je upřednostňovanou entitou pro sestavování aplikací LUIS. Spoléhá se na algoritmy strojového učení a vyžaduje, aby popisek byl úspěšně upravený na vaši aplikaci. Použijte entitu ML k identifikaci dat, která nejsou vždy ve formátu, ale mají stejný význam. 

|Ukázková promluva|Extrahovaná entita *produktu*|
|--|--|
|Chci si koupit knihu.|účetních|
|Můžu mi tuto obuv získat?|obuvi|
|Přidejte je do košíku.|"krátká"|

Další informace o entitách strojového učení najdete [tady](./reference-entity-machine-learned-entity.md).

Další informace najdete v [článku referenční informace o entitách strojového učení](./reference-entity-pattern-any.md) .

#### <a name="ml-entity-with-structure"></a>Entita ML se strukturou

Entita ML se může skládat z menších dílčích entit, z nichž každá může mít své vlastní vlastnosti. Například *adresa* může mít následující strukturu:

* Adresa: 4567 hlavní ulice, NY, 98052, USA
    * Číslo budovy: 4567
    * Název ulice: hlavní ulice
    * Stav: NY
    * PSČ: 98052
    * Země: USA


### <a name="building-effective-ml-entities"></a>Sestavování efektivních entit ML

Chcete-li efektivně sestavovat entity počítače, postupujte podle těchto osvědčených postupů:

* Pokud máte entitu počítače s podentitami, zajistěte, aby byly různé objednávky a varianty entit a dílčích entit uvedeny v popisku projevy. Vzorový příklad projevy by měl zahrnovat všechny platné formuláře a zahrnout entity, které se zobrazí a jsou chybějící a také přeobjednány v rámci utterance.

* Vyhněte se přebudování entit na velmi pevně danou sadu. K překrytí dojde, když model nebude správně generalizovat a jedná se o běžný problém v modelech strojového učení. To znamená, že aplikace nebude dostatečně fungovat na nových typech příkladů. V takovém případě byste měli měnit příklad projevy, aby se aplikace mohla zobecnit nad rámec omezených příkladů, které zadáte.

* Štítky by měly být v souladu s těmito záměry. To zahrnuje i projevy, které zadáte do záměru *none* , který obsahuje tuto entitu. V opačném případě model nebude moci určit sekvence efektivně.

## <a name="entities-as-features"></a>Entity jako funkce

Další důležitou funkcí entit je jejich použití jako funkcí nebo rozlišení vlastností pro jiné záměry nebo entity, aby systém mohl sledovat a učit se prostřednictvím nich.

### <a name="entities-as-features-for-intents"></a>Entity jako funkce pro záměry

Entity můžete použít jako signál pro záměr. Například přítomnost určité entity v utterance může rozlišovat, na které záměr spadají.

|Ukázková promluva|Entita|Záměr|
|--|--|--|
|Seznamte se s *bojem do Praha*.|City (Město)|Kniha let|
|Kniha pro *Hlavní konferenční místnost*.|Prostor|Rezervovat místnost|

### <a name="entities-as-feature-for-entities"></a>Entity jako funkce pro entity

Entity můžete použít také jako ukazatel na přítomnost jiných entit. Běžným příkladem je použití předem sestavené entity jako funkce pro jinou entitu ML.
Pokud vytváříte systém pro rezervaci letů a váš utterance vypadá jako "Book mi let od Cairo do Seattle", budete mít *původ města* a *město města* jako jednotky ml. Dobrým postupem je použít předem vytvořenou `GeographyV2` entitu jako funkci pro obě entity.

Další informace najdete v [článku referenční informace o entitách GeographyV2](./luis-reference-prebuilt-geographyv2.md) .

Entity můžete používat také jako požadované funkce pro jiné entity. To pomáhá při řešení extrahovaných entit. Pokud například vytváříte aplikaci řazení Pizza a máte `Size` entitu ml, můžete vytvořit `SizeList` entitu seznam a použít ji jako požadovanou funkci pro `Size` entitu. Vaše aplikace Vrátí normalizovanou hodnotu jako extrahovanou entitu z utterance. 

V tématu [funkce](luis-concept-feature.md) najdete další informace a [předem připravené entity](./luis-reference-prebuilt-entities.md) , které vám pomůžou získat další informace o řešení předem sestavených entit dostupných ve vaší jazykové verzi. 


## <a name="entity-prediction-status-and-errors"></a>Stav a chyby předpovědi entity

Portál LUIS zobrazuje následující, pokud má entita jinou předpověď entit než entita, kterou jste zavedli pro příklad utterance. Toto jiné skóre vychází z aktuálně vyškolených modelů. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="Portál LUIS se zobrazí, když má entita jinou předpověď entit než entita, kterou jste vybrali pro příklad utterance.":::

Text, který způsobuje chybu, je zvýrazněný v příkladu utterance a příklad utterance čára má na pravé straně indikátor chyby, který se zobrazuje jako červený trojúhelník. 

Pokud chcete vyřešit chyby entity, zkuste použít jednu z následujících možností:

* Zvýrazněný text je označený jako nepopisekný. Pokud chcete problém opravit, zkontrolujte ho a opravte ho a znovu ho rozhlaste. 
* Vytvořte [funkci](luis-concept-feature.md) pro entitu, která vám usnadní identifikaci konceptu entity.
* Přidejte k entitě další [Příklady projevy](luis-concept-utterance.md) a Label.
* [Přečtěte si přehled aktivních kurzů](luis-concept-review-endpoint-utterances.md) pro všechny projevy přijaté na koncovém bodu předpovědi, které vám pomůžou identifikovat koncept entity.


## <a name="next-steps"></a>Další kroky

* Seznamte se s dobrým příkladem [projevy](luis-concept-utterance.md).
* Další informace o tom, jak přidat entity do aplikace LUIS, najdete v tématu věnovaném [Přidání entit](luis-how-to-add-entities.md) .
* Přečtěte si další informace o [limitech aplikací](./luis-limits.md)Luis. 
* Pomocí [kurzu](tutorial-machine-learned-entity.md) se dozvíte, jak extrahovat strukturovaná data z utterance pomocí entity strojového učení.
