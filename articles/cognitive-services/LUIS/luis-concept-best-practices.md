---
title: Osvědčené postupy pro sestavení aplikace v LUIS
titleSuffix: Azure Cognitive Services
description: Seznamte se s osvědčenými postupy pro dosažení nejlepších výsledků z modelu vaší aplikace v LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b4be79338db71ad83204fae971da0b77885a8070
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280931"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Osvědčené postupy pro sestavování aplikace pro porozumění jazyku (LUIS)
Pomocí procesu vytváření aplikací sestavte aplikaci LUIS: 

* Modely jazyka sestavení (záměry a entity)
* Přidání několika školicích příkladů projevy (15-30 na jeden záměr)
* Publikovat do koncového bodu
* Testování z koncového bodu 

Po [publikování](luis-how-to-publish-app.md)vaší aplikace použijte životní cyklus vývoje k přidání funkcí, publikování a testování z koncového bodu. Nespouštějte další cyklus vytváření, protože přidáte další příklad projevy, protože to neumožňuje LUISí modelu pomocí reálného uživatele projevy. 

Nerozšiřujte projevy, dokud aktuální sada obou příkladů a projevy koncového bodu nevrátí jistotu, skóre s vysokou předpověďí. Vylepšete skóre pomocí [aktivního učení](luis-concept-review-endpoint-utterances.md). 




## <a name="do-and-dont"></a>Proveďte a není
Následující seznam obsahuje osvědčené postupy pro aplikace LUIS:

|Správný postup|Chybný postup|
|--|--|
|[Definování různých záměrů](#do-define-distinct-intents)<br>[Přidat popisovače do záměrů](#do-add-descriptors-to-intents) |[Přidání projevů mnoho příklad pro příkazy](#dont-add-many-example-utterances-to-intents)<br>[Použití několika nebo jednoduchých entit](#dont-use-few-or-simple-entities) |
|[Vyhledá sladkost místo mezi příliš obecná a moc specifické pro každý záměr](#do-find-sweet-spot-for-intents)|[Používat službu LUIS jako platforma pro školení](#dont-use-luis-as-a-training-platform)|
|[Opakované sestavení aplikace s použitím verzí](#do-build-your-app-iteratively-with-versions)<br>[Sestavení entit pro rozložení modelu](#do-build-for-model-decomposition)|[Přidání projevů mnoho příklad stejný formát ignoruje dalších formátů](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Přidat vzory v pozdějších iteracích](#do-add-patterns-in-later-iterations)|[Kombinace definici záměry a entity](#dont-mix-the-definition-of-intents-and-entities)|
|[Vyvážení projevy napříč všemi záměry](#balance-your-utterances-across-all-intents) s výjimkou záměru None.<br>[Přidání projevů příklad na hodnotu None záměru](#do-add-example-utterances-to-none-intent)|[Vytvoření popisovačů se všemi možnými hodnotami](#dont-create-descriptors-with-all-the-possible-values)|
|[Využijte funkci navrhnout pro aktivní učení](#do-leverage-the-suggest-feature-for-active-learning)|[Přidat příliš mnoho vzorů](#dont-add-many-patterns)|
|[Sledování výkonu aplikace pomocí dávkového testování](#do-monitor-the-performance-of-your-app)|[Trénování a publikování s každou utterance jeden příklad přidá](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definování různých záměrů
Zkontrolujte, zda že slovník pro každý záměr je jenom pro tohoto záměru a nesmí se překrývat s jinou záměr. Například pokud chcete mít aplikaci, která zpracovává cestovní mechanismy, jako jsou letecké lety a hotely, můžete se rozhodnout, že tyto oblasti budou mít samostatné záměry nebo stejný záměr s entitami pro konkrétní data v rámci utterance.

Pokud je slovník mezi dvěma záměry stejná, kombinovat záměr a používání entit. 

Vezměte v úvahu následující příklad projevy:

|Ukázkové promluvy|
|--|
|Kniha let|
|Kniha hotelu|

`Book a flight` a `Book a hotel` používají stejný slovník `book a `. Tento formát je stejný, takže by měl být stejný záměr s různými slovy `flight` a `hotel` jako extrahované entity. 

## <a name="do-add-descriptors-to-intents"></a>Přidat popisovače do záměrů

Popisovače jsou popsány funkcemi pro záměr. Popisovačem může být seznam slov, která jsou významné pro daný záměr nebo na entitu, která je pro tento záměr významná. 

## <a name="do-find-sweet-spot-for-intents"></a>Najít sladkost místo pro příkazy
Použijte data předpovědí služby luis k určení překrývající vaše záměry. Překrývající se záměry Zaměňujte LUIS. Výsledkem je, horní vyhodnocování záměr je příliš zavřít do jiného záměr. Protože LUIS přesně stejnou cestu dat pro trénování pokaždé, když nepoužívá, má překrývající se záměrem šance na první nebo druhé v školení. Chcete, aby se utterance skóre každého záměru více rozdělilo, aby tato funkce překlopení nedocházelo. Dobré rozlišení pro záměry by měla za následek očekávané hlavní záměr pokaždé, když. 
 
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

## <a name="do-add-example-utterances-to-none-intent"></a>Přidání projevů příklad na hodnotu None záměru

Tento záměr je záložním záměrem, který označuje všechno mimo vaši aplikaci. Přidejte jeden příklad utterance záměru pro každých 10 příklad projevy ve zbývající části aplikace LUIS None.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Využijte funkci navrhnout pro aktivní učení

Použití [aktivně učit](luis-how-to-review-endpoint-utterances.md)společnosti **zkontrolujte koncový bod projevy** v pravidelných intervalech, nepřidávat další příklad projevy na záměry. Protože aplikace neustále přijímá projevy koncový bod, je tento seznam rozrůstá a změna.

## <a name="do-monitor-the-performance-of-your-app"></a>Monitorování výkonu vaší aplikace

Sledujte přesnost předpovědi pomocí sady [dávkových testů](luis-concept-batch-test.md) . 

Ponechte samostatnou sadu projevy, která se nepoužívá jako [příklad projevy](luis-concept-utterance.md) nebo Endpoint projevy. Neustále se zlepšují aplikace pro vaše testovací sada. Přizpůsobení testu nastavit tak, aby odrážely projevy uživatelů. Pomocí této sady testů vyhodnoťte každou iteraci nebo verzi aplikace. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Nepřidávejte do záměry mnoho příklad projevy

Po publikování aplikace se do procesu životního cyklu vývoje přidají jenom projevy z aktivního učení. Pokud jsou příliš podobná projevy, přidejte vzor. 

## <a name="dont-use-few-or-simple-entities"></a>Nepoužívejte pár nebo jednoduché entity.

Entity jsou sestaveny pro extrakci a předpověď dat. Je důležité, aby každý záměr získal entity, které popisují data v záměru. To pomáhá LUIS odhadnout záměr, i když klientská aplikace nemusí používat extrahovanou entitu. 

## <a name="dont-use-luis-as-a-training-platform"></a>Nepoužívejte LUIS jako platforma pro školení

Služba LUIS je specifický pro doménu jazykový model. Nejedná se o to, aby fungoval jako obecná platforma pro školení v přirozeném jazyce. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Nepřidávejte mnoho projevy příklad stejný formát ignoruje dalších formátů

LUIS očekává, že změny v záměr projevů. Projevy se může lišit přitom má stejný význam celkové. Variace mohou zahrnovat délku utterance, volba slov a umístění aplikace word. 

|Nepoužívejte stejný formát|Můžete použít různé formátu|
|--|--|
|Nákup lístků do Seattlu<br>Nákup lístků na Paříž<br>Nákup lístků na Orlando|Koupit 1 lístek do Seattlu<br>Rezervaci dvou míst na červené očí do Paříže další pondělí<br>Chci rezervovat 3 lístky na Orlandu na konec spring|

Druhý sloupec používá různé příkazy (nákup, rezervace, knihy), jiné množství (1, dvě, 3), a jiný režim slova, ale všechny mít stejný účel nákupu lístků letecká společnost na cesty. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Nekombinujte definici záměry a entity

Vytvoření záměru pro jakoukoliv akci, že bude trvat svého robota. Pomocí entit jako parametry, které umožňují tuto akci. 

V případě robota, který bude v leteckém letu, vytvořte **BookFlight** záměr. Nevytvářejte záměru pro každý letecká společnost nebo každý cíl. Pomocí těchto částí dat jako [entity](luis-concept-entity-types.md) a označte je v příkladu projevy. 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>Nevytvářejte popisovače se všemi možnými hodnotami.

V [seznamech frází](luis-concept-feature.md) popisovače zadejte několik příkladů, ale ne všechna slova. Služba LUIS umožňuje zobecnit a bere v úvahu kontext. 

## <a name="dont-add-many-patterns"></a>Nepřidávejte mnoho vzorů

Nepřidávejte příliš mnoho [vzory](luis-concept-patterns.md). Služba LUIS je určená další rychle s méně příklady. Nechcete zbytečně přetížení systému.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Nemáte trénování a publikování s každou utterance jeden příklad

Přidání projevů 10 až 15 před trénování a publikování. Který můžete vidět, jaký dopad na přesnost předpovědi. Přidání jednoho utterance nemusí mít viditelné dopad na skóre. 

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [plánování aplikace](luis-how-plan-your-app.md) ve vaší aplikaci LUIS.
