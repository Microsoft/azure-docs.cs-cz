---
title: Osvědčené postupy – LUIS
titleSuffix: Azure Cognitive Services
description: Seznamte se s osvědčenými postupy pro LUIS, abyste získali nejlepší výsledky z modelu vaší aplikace LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 64d67edaf5affbc908fba7b6c261096589bc84d0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487619"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Osvědčené postupy pro vytváření aplikací pro porozumění jazyku pomocí Cognitive Services
Pomocí procesu vytváření aplikací sestavte aplikaci LUIS: 

* Modely jazyka sestavení (záměry a entity)
* Přidání několika školicích příkladů projevy (15-30 na jeden záměr)
* Publikovat do koncového bodu
* Test z koncového bodu 

Po [publikování](luis-how-to-publish-app.md)vaší aplikace použijte životní cyklus vývoje k přidání funkcí, publikování a testování z koncového bodu. Nespouštějte další cyklus vytváření, protože přidáte další příklad projevy, protože to neumožňuje LUISí modelu pomocí reálného uživatele projevy. 

Nerozšiřujte projevy, dokud aktuální sada obou příkladů a projevy koncového bodu nevrátí jistotu, skóre s vysokou předpověďí. Vylepšete skóre pomocí [aktivního učení](luis-concept-review-endpoint-utterances.md). 




## <a name="do-and-dont"></a>A ne
Následující seznam obsahuje osvědčené postupy pro aplikace LUIS:

|Správný postup|Chybný postup|
|--|--|
|[Definovat jedinečné záměry](#do-define-distinct-intents)<br>[Přidat popisovače do záměrů](#do-add-descriptors-to-intents) |[Přidejte spoustu příkladů projevy k záměrům](#dont-add-many-example-utterances-to-intents)<br>[Použití několika nebo jednoduchých entit](#dont-use-few-or-simple-entities) |
|[Najděte sladkou skvrnu mezi příliš obecným a příliš specifickou pro každý záměr.](#do-find-sweet-spot-for-intents)|[Použití LUIS jako školicí platformy](#dont-use-luis-as-a-training-platform)|
|[Opakované sestavení aplikace s použitím verzí](#do-build-your-app-iteratively-with-versions)<br>[Sestavení entit pro rozložení modelu](#do-build-for-model-decomposition)|[Přidejte spoustu příkladů projevy stejného formátu a ignorujte jiné formáty.](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Přidat vzory v pozdějších iteracích](#do-add-patterns-in-later-iterations)|[Kombinace definice záměrů a entit](#dont-mix-the-definition-of-intents-and-entities)|
|[Vyvážení projevy napříč všemi záměry](#balance-your-utterances-across-all-intents) s výjimkou záměru None.<br>[Přidat příklad projevy k žádnému záměru](#do-add-example-utterances-to-none-intent)|[Vytvoření popisovačů se všemi možnými hodnotami](#dont-create-descriptors-with-all-the-possible-values)|
|[Využijte funkci navrhnout pro aktivní učení.](#do-leverage-the-suggest-feature-for-active-learning)|[Přidat příliš mnoho vzorů](#dont-add-many-patterns)|
|[Sledování výkonu aplikace pomocí dávkového testování](#do-monitor-the-performance-of-your-app)|[Školení a publikování s každým jedním příkladem utterance přidání](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definování jedinečných záměrů
Ujistěte se, že slovník pro každý záměr je pouze pro tento záměr a nepřekrývá s jiným záměrem. Například pokud chcete mít aplikaci, která zpracovává cestovní mechanismy, jako jsou letecké lety a hotely, můžete se rozhodnout, že tyto oblasti budou mít samostatné záměry nebo stejný záměr s entitami pro konkrétní data v rámci utterance.

Je-li slovník mezi dvěma záměry stejný, slučte záměr a použijte entity. 

Vezměte v úvahu následující příklad projevy:

|Ukázkové promluvy|
|--|
|Kniha a letu|
|Kniha a Hotel|

`Book a flight` a `Book a hotel` používají stejný slovník `book a `. Tento formát je stejný, takže by měl být stejný záměr s různými slovy `flight` a `hotel` jako extrahované entity. 

## <a name="do-add-descriptors-to-intents"></a>Přidat popisovače do záměrů

Popisovače jsou popsány funkcemi pro záměr. Popisovačem může být seznam slov, která jsou významné pro daný záměr nebo na entitu, která je pro tento záměr významná. 

## <a name="do-find-sweet-spot-for-intents"></a>Najděte si pro záměry sladkou skvrnu
Data předpovědi z LUIS můžete použít k určení, jestli se jejich záměry překrývají. Překrývající se záměry Zaměňujte LUIS. Výsledkem je, že nejvyšší záměr bodování je příliš blízko k jinému záměru. Vzhledem k tomu, že LUIS nepoužívá přesnou cestu přes data pro školení pokaždé, překrývající se záměr je pravděpodobné, že je první nebo druhý v rámci školení. Chcete, aby se utterance skóre každého záměru více rozdělilo, aby tato funkce překlopení nedocházelo. Dobrý rozdíl pro záměry by měl vést k očekávanému nejvyššímu záměru pokaždé. 
 
<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Opakované sestavování aplikace pomocí verzí

Každý cyklus vytváření obsahu by měl být v rámci nové [verze](luis-concept-version.md), který je klonován z existující verze. 

## <a name="do-build-for-model-decomposition"></a>Sestavit pro dekompozici modelu

Dekompozice modelu má typický proces:

* vytvoření **záměru** založeného na záměrech uživatelů klientské aplikace
* Přidat vzorový projevy 15-30 založený na vstupu uživatele Real-World
* označení konceptu dat na nejvyšší úrovni v příkladu utterance
* přerušit koncept dat na dílčí součásti
* Přidání popisovačů (funkcí) k dílčím součástem
* Přidání popisovačů (funkcí) k záměru 

Jakmile vytvoříte záměr a přidáte příklad projevy, následující příklad popisuje dekompozici entit. 

Začněte tím, že identifikujete kompletní koncepty dat, které chcete extrahovat v utterance. Toto je vaše entita získaná počítačem. Pak rozložíte frázi na její části. To zahrnuje identifikaci dílčích součástí (jako entit) společně s popisovači a omezeními. 

Pokud například chcete extrahovat adresu, je možné, že se na nejvyšší počítačově učenou entitu říká `Address`. Při vytváření adresy Identifikujte některé z jejích dílčích součástí, jako je adresa ulice, město, stát a poštovní směrovací číslo. 

Pokračujte v nastavování těchto prvků **omezením** poštovního kódu na regulární výraz. Rozloží adresu ulice na části čísla ulice (pomocí předem vytvořeného čísla), názvu ulice a typu ulice. Typ ulice lze popsat pomocí seznamu **popisovačů** , jako je způsob, Circle, Road a Lane.

Rozhraní API pro vytváření obsahu pro sestavení V3 umožňuje dekompozici modelu. 

## <a name="do-add-patterns-in-later-iterations"></a>Přidejte vzory do pozdějších iterací.

Měli byste porozumět tomu, jak se aplikace chová před přidáním [vzorů](luis-concept-patterns.md) , protože vzory jsou vážeější, než je například projevy a jejich spolehlivost se bude zkosit. 

Jakmile pochopíte, jak se vaše aplikace chová, přidejte vzory, které se vztahují k vaší aplikaci. Nemusíte je přidávat s každou [iterací](luis-concept-app-iteration.md). 

Nemusíte je přidávat na začátek návrhu modelu, ale je snazší zjistit, jak každý vzorek mění model po otestování modelu pomocí projevy. 
 
<!--

### Phrase lists

[Phrase lists](luis-concept-feature.md) allow you to define dictionaries of words related to your app domain. Seed your phrase list with a few words then use the suggest feature so LUIS knows about more words in the vocabulary specific to your app. A Phrase List improves intent detection and entity classification by boosting the signal associated with words or phrases that are significant to your app. 

Don't add every word to the vocabulary since the phrase list isn't an exact match. 

For more information:
* Concept: [Phrase list features in your LUIS app](luis-concept-feature.md)
* How-to: [Use phrase lists to boost signal of word list](luis-how-to-add-features.md)



### Patterns

Real user utterances from the endpoint, very similar to each other, may reveal patterns of word choice and placement. The [pattern](luis-concept-patterns.md) feature takes this word choice and placement along with regular expressions to improve your prediction accuracy. A regular expression in the pattern allows for words and punctuation you intend to ignore while still matching the pattern. 

Use pattern's [optional syntax](luis-concept-patterns.md) for punctuation so punctuation can be ignored. Use the [explicit list](luis-concept-patterns.md#explicit-lists) to compensate for pattern.any syntax issues. 

For more information:
* Concept: [Patterns improve prediction accuracy](luis-concept-patterns.md)
* How-to: [How to add Patterns to improve prediction accuracy](luis-how-to-model-intent-pattern.md)
-->

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Vyrovnávání projevy napříč všemi záměry

Aby LUIS předpovědi bylo přesné, množství příkladu projevy v každém záměru (s výjimkou záměru None) musí být poměrně rovno. 

Pokud máte záměr s 100 příkladem projevy a záměrem s 20 příkladem projevy, bude mít záměr 100-utterance vyšší míru předpovědi.  

## <a name="do-add-example-utterances-to-none-intent"></a>Přidejte příklad projevy k žádnému záměru.

Tento záměr je záložním záměrem, který označuje všechno mimo vaši aplikaci. Přidejte příklad utterance do záměru None pro každých 10 příkladů projevy ve zbývající části vaší aplikace LUIS.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Využijte funkci navrhnout pro aktivní učení.

**Projevy** pravidelně pomocí [aktivního vzdělávacího bodu služby Active Learning](luis-how-to-review-endpoint-utterances.md)místo přidání dalších příkladů projevy k záměrům. Vzhledem k tomu, že aplikace neustále přijímá koncový bod projevy, tento seznam roste a mění.

## <a name="do-monitor-the-performance-of-your-app"></a>Sledování výkonu aplikace

Sledujte přesnost předpovědi pomocí sady [dávkových testů](luis-concept-batch-test.md) . 

Ponechte samostatnou sadu projevy, která se nepoužívá jako [příklad projevy](luis-concept-utterance.md) nebo Endpoint projevy. Udržujte lepší aplikaci pro sadu testů. Přizpůsobte sadu testů tak, aby odrážela reálného uživatele projevy. Pomocí této sady testů vyhodnoťte každou iteraci nebo verzi aplikace. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Nepřidávejte spoustu příkladů projevy k záměrům

Po publikování aplikace se do procesu životního cyklu vývoje přidají jenom projevy z aktivního učení. Pokud je projevy příliš podobný, přidejte vzor. 

## <a name="dont-use-few-or-simple-entities"></a>Nepoužívejte pár nebo jednoduché entity.

Entity jsou sestaveny pro extrakci a předpověď dat. Je důležité, aby každý záměr získal entity, které popisují data v záměru. To pomáhá LUIS odhadnout záměr, i když klientská aplikace nemusí používat extrahovanou entitu. 

## <a name="dont-use-luis-as-a-training-platform"></a>Nepoužívejte LUIS jako školicí platformu

LUIS je specifický pro doménu jazykového modelu. Nejedná se o to, aby fungoval jako obecná platforma pro školení v přirozeném jazyce. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Nepřidávejte spoustu příkladů projevy stejného formátu a ignorují se jiné formáty.

LUIS očekává variace v projevy záměru. Projevy se může lišit se stejným celkovým významem. Variace můžou zahrnovat délku utterance, volbu slov a umístění slov. 

|Nepoužívat stejný formát|Používejte proměnlivý formát|
|--|--|
|Koupit lístek na Seattle<br>Koupit lístek na Paříž<br>Koupit lístek na Orlandu|Koupit 1 lístek na Seattle<br>Vyhrazení dvou míst červeného oka k Paříži dalšímu pondělí<br>Chci kniha 3 lístky k Orlandu pro přerušení pružiny|

Druhý sloupec používá různé operace (nákup, rezervace, kniha), různá množství (1, 2, 3) a různá ustanovení slov, ale všechny mají stejný záměr koupit letecké lístky pro cestování. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Nekombinovat definice záměrů a entit

Vytvořte záměr pro všechny akce, které vaše bot bude trvat. Použijte entity jako parametry, které umožní tuto akci. 

V případě robota, který bude v leteckém letu, vytvořte **BookFlight** záměr. Nevytvářejte záměr pro všechny letecké společnosti nebo všechny cíle. Tyto části dat používejte jako [entity](luis-concept-entity-types.md) a označte je v příkladech projevy. 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>Nevytvářejte popisovače se všemi možnými hodnotami.

V [seznamech frází](luis-concept-feature.md) popisovače zadejte několik příkladů, ale ne všechna slova. LUIS generalizace a bere kontext na účet. 

## <a name="dont-add-many-patterns"></a>Nepřidávat mnoho vzorů

Nepřidávejte příliš mnoho [vzorů](luis-concept-patterns.md). LUIS je určeno pro rychlé seznámení s méně příklady. Nepoužívejte zbytečně přetížení systému.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Nevytvářejte výuku a publikujte s každým jedním příkladem utterance

Před školením a publikováním přidejte projevy 10 nebo 15. To vám umožní vidět dopad na přesnost předpovědi. Přidání jednoho utterance nemusí mít viditelný dopad na skóre. 

## <a name="next-steps"></a>Další kroky

* Naučte se, jak [naplánovat aplikaci](luis-how-plan-your-app.md) v aplikaci Luis.
