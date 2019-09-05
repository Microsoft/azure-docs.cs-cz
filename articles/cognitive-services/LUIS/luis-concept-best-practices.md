---
title: Osvědčené postupy – LUIS
titleSuffix: Azure Cognitive Services
description: Podívejte se na LUIS osvědčené postupy k dosažení nejlepších výsledků z modelu aplikace LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 91ff99f674439580d369aad1490ded85d39d377c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382880"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Osvědčené postupy pro vytváření aplikace language understanding pomocí služeb Cognitive Services
Pomocí procesu vytváření aplikací sestavte aplikaci LUIS: 

* Vytvářet jazykový model
* Přidání projevů příklad několika školení (10 – 15 za účelem)
* Publikování 
* Testování z koncového bodu 
* Přidání funkcí

Po [publikování](luis-how-to-publish-app.md)aplikace použijte cyklus vytváření k přidání funkcí, publikování a testování z koncového bodu. Přidáním další příklad projevy nezačínají dalšího vývojového cyklu. Který neumožňuje LUIS další modelu pomocí projevy reálné uživatele. 

Aby LUIS účinný na svou úlohu učení nelze rozbalit projevy až do aktuální sady příklad a koncového bodu projevy vrací předpovědi jistí, vysoké skóre. Vylepšete skóre pomocí seznamů [aktivních kurzů](luis-concept-review-endpoint-utterances.md), [schémat](luis-concept-patterns.md)a [frází](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Proveďte a není
Následující seznam obsahuje osvědčené postupy pro aplikace LUIS:

|Správný postup|Chybný postup|
|--|--|
|[Definování různých záměrů](#do-define-distinct-intents) |[Přidání projevů mnoho příklad pro příkazy](#dont-add-many-example-utterances-to-intents) |
|[Vyhledá sladkost místo mezi příliš obecná a moc specifické pro každý záměr](#do-find-sweet-spot-for-intents)|[Používat službu LUIS jako platforma pro školení](#dont-use-luis-as-a-training-platform)|
|[Opakované sestavení aplikace](#do-build-the-app-iteratively)|[Přidání projevů mnoho příklad stejný formát ignoruje dalších formátů](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Přidat frázi seznamy a vzory ve vyšším počtu iterací](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Kombinace definici záměry a entity](#dont-mix-the-definition-of-intents-and-entities)|
|[Vyvážení projevy napříč všemi záměry](#balance-your-utterances-across-all-intents) s výjimkou záměru None.<br>[Přidání projevů příklad na hodnotu None záměru](#do-add-example-utterances-to-none-intent)|[Vytváření seznamů frázi s všech možných hodnot](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Využijte funkci navrhnout pro aktivní učení](#do-leverage-the-suggest-feature-for-active-learning)|[Přidat příliš mnoho vzorů](#dont-add-many-patterns)|
|[Monitorování výkonu vaší aplikace](#do-monitor-the-performance-of-your-app)|[Trénování a publikování s každou utterance jeden příklad přidá](#dont-train-and-publish-with-every-single-example-utterance)|
|[Použití verzí pro každou iteraci aplikace](#do-use-versions-for-each-app-iteration)||

## <a name="do-define-distinct-intents"></a>Definování různých záměrů
Zkontrolujte, zda že slovník pro každý záměr je jenom pro tohoto záměru a nesmí se překrývat s jinou záměr. Například pokud chcete mít aplikaci, která zpracovává cestovní mechanismy, jako jsou letecké lety a hotely, můžete se rozhodnout, že tyto oblasti budou mít samostatné záměry nebo stejný záměr s entitami pro konkrétní data v rámci utterance.

Pokud je slovník mezi dvěma záměry stejná, kombinovat záměr a používání entit. 

Vezměte v úvahu následující příklad projevy:

|Ukázkové promluvy|
|--|
|Kniha let|
|Kniha hotelu|

"Rezervovat let" a "Rezervovat hotel" použít stejné slovník "knihy". Tento formát je stejný, takže by měl být stejný záměr s různými slovy letu a hotelu jako extrahované entity. 

Další informace:
* " [Koncepty o záměrech v aplikaci LUIS](luis-concept-intent.md)
* Kurz: [Sestavování aplikace LUIS pro určení záměrů uživatelů](luis-quickstart-intents-only.md)
* Postup: [Přidání záměrů k určení záměru uživatele projevy](luis-how-to-add-intents.md)


## <a name="do-find-sweet-spot-for-intents"></a>Najít sladkost místo pro příkazy
Použijte data předpovědí služby luis k určení překrývající vaše záměry. Překrývající se záměry Zaměňujte LUIS. Výsledkem je, horní vyhodnocování záměr je příliš zavřít do jiného záměr. Protože LUIS přesně stejnou cestu dat pro trénování pokaždé, když nepoužívá, má překrývající se záměrem šance na první nebo druhé v školení. Chcete, aby se utterance skóre každého záměru více rozdělilo, aby tato funkce překlopení nedocházelo. Dobré rozlišení pro záměry by měla za následek očekávané hlavní záměr pokaždé, když. 
 
## <a name="do-build-the-app-iteratively"></a>Opakované sestavení aplikace
Ponechte samostatnou sadu projevy, která se nepoužívá jako [příklad projevy](luis-concept-utterance.md) nebo Endpoint projevy. Neustále se zlepšují aplikace pro vaše testovací sada. Přizpůsobení testu nastavit tak, aby odrážely projevy uživatelů. Pomocí této sady testů vyhodnoťte každou iteraci nebo verzi aplikace. 

Vývojáři by měli mít tři sady data. První je příklad projevy pro vytváření modelu. Druhá je pro testování modelu v koncovém bodě. Třetí je nevidomé testovací data použít v [batch testování](luis-how-to-batch-test.md). Tato poslední sada se nepoužívá při školení aplikace ani odeslání na koncovém bodu.  

Další informace:
* " [Cyklus vytváření aplikací pro LUIS](luis-concept-app-iteration.md)

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Přidat frázi seznamy a vzory ve vyšším počtu iterací

Osvědčeným postupem je nepoužívat tyto postupy předtím, než byla aplikace testována. Měli byste porozumět tomu, jak se aplikace chová před přidáním [seznamů frází](luis-concept-feature.md) a [vzorů](luis-concept-patterns.md) , protože tyto funkce jsou vážeější, než je například projevy a jejich spolehlivost bude zkreslené. 

Jakmile pochopíte, jak se vaše aplikace chová bez nich, přidejte každou z těchto funkcí tak, jak se vztahují k vaší aplikaci. Tyto funkce není nutné přidávat s každou [iterací](luis-concept-app-iteration.md) nebo měnit funkce s každou verzí. 

Nemusíte je přidávat na začátek návrhu modelu, ale je snazší zjistit, jak se jednotlivé funkce po otestování modelu s projevy snadněji dostanou měnit. 

Osvědčeným postupem je testování prostřednictvím [koncového bodu](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) , abyste získali přidanou výhodu [aktivního učení](luis-concept-review-endpoint-utterances.md). [Podokno interaktivní testování](luis-interactive-test.md) je také platná metodologie testu. 
 

### <a name="phrase-lists"></a>Seznamy frází

[Frázi seznamy](luis-concept-feature.md) umožňují definovat slovníky slov související s doménou aplikace. Dosadíte seznam frází s několika slovy a pak použijte funkci navrhnout, aby LUIS ví o více slovech v slovnících specifických pro vaši aplikaci. Seznam frází zlepšuje detekci záměrů a klasifikaci entit tím, že zvyšuje signál spojený s slovy nebo frázemi, které jsou pro vaši aplikaci významné. 

Nepřidávejte všechna slova pro slovník od seznamu frázi není přesná shoda. 

Další informace:
* " [Funkce seznamu frází ve vaší aplikaci LUIS](luis-concept-feature.md)
* Postupy: [Použití seznamů frází ke zvýšení signálu v seznamu slov](luis-how-to-add-features.md)

### <a name="patterns"></a>Vzory

Projevy uživatelů z koncového bodu, velmi podobné k sobě navzájem, může odhalit vzory voleb aplikace word a umístění. [Vzor](luis-concept-patterns.md) funkce přebírá tato volba slov a umístění spolu s regulárních výrazů ke zlepšení vaší přesnost předpovědi. Ve vzoru regulárního výrazu umožňuje slova a interpunkční znaménka, které máte v úmyslu ignorovat při stále odpovídající vzoru. 

Použijte [volitelnou syntaxi](luis-concept-patterns.md) vzoru pro interpunkční znaménka, aby bylo možné interpunkční znaménko ignorovat. K kompenzaci vzoru použijte [explicitní seznam](luis-concept-patterns.md#explicit-lists) . jakékoli problémy s syntaxí. 

Další informace:
* " [Vzorce zlepšují přesnost předpovědi](luis-concept-patterns.md)
* Postupy: [Postup přidání vzorů pro zlepšení přesnosti předpovědi](luis-how-to-model-intent-pattern.md)

## <a name="balance-your-utterances-across-all-intents"></a>Vyvážení projevy napříč všemi záměry

Aby LUIS předpovědi bylo přesné, množství příkladu projevy v každém záměru (s výjimkou záměru None) musí být poměrně rovno. 

Pokud máte záměr s 100 příkladem projevy a záměrem s 20 příkladem projevy, bude mít záměr 100-utterance vyšší míru předpovědi.  

## <a name="do-add-example-utterances-to-none-intent"></a>Přidání projevů příklad na hodnotu None záměru

Tento záměr je záložním záměrem, který uvádí všechno mimo vaši aplikaci. Přidejte jeden příklad utterance záměru pro každých 10 příklad projevy ve zbývající části aplikace LUIS None.

Další informace:
* " [Vysvětlení, co je pro vaši aplikaci LUIS dobré projevy](luis-concept-utterance.md)

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Využijte funkci navrhnout pro aktivní učení

Použití [aktivně učit](luis-how-to-review-endpoint-utterances.md)společnosti **zkontrolujte koncový bod projevy** v pravidelných intervalech, nepřidávat další příklad projevy na záměry. Protože aplikace neustále přijímá projevy koncový bod, je tento seznam rozrůstá a změna.

Další informace:
* " [Koncepty pro povolení aktivního učení pomocí kontroly projevy koncového bodu](luis-concept-review-endpoint-utterances.md)
* Kurz: [Kurz: Opravte nejistotu předpovědi kontrolou Endpoint projevy](luis-tutorial-review-endpoint-utterances.md)
* Postupy: [Postup kontroly služby Endpoint projevy na portálu LUIS](luis-how-to-review-endpoint-utterances.md)

## <a name="do-monitor-the-performance-of-your-app"></a>Monitorování výkonu vaší aplikace

Sledujte přesnost předpovědi pomocí sady [dávkových testů](luis-concept-batch-test.md) . 

## <a name="dont-add-many-example-utterances-to-intents"></a>Nepřidávejte do záměry mnoho příklad projevy

Po publikování aplikace přidáte pouze projevy ze služby active learning v iterativní proces. Pokud jsou příliš podobná projevy, přidejte vzor. 

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

Chatovací robot se rezervuje letecká společnost lety, vytvořit **BookFlight** záměr. Nevytvářejte záměru pro každý letecká společnost nebo každý cíl. Pomocí těchto částí dat jako [entity](luis-concept-entity-types.md) a označte je v příkladu projevy. 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Nevytvářejte frázi seznamy s všech možných hodnot

Poskytují několik příkladů [frázi seznamy](luis-concept-feature.md) , ale ne každá aplikace word. Služba LUIS umožňuje zobecnit a bere v úvahu kontext. 

## <a name="dont-add-many-patterns"></a>Nepřidávejte mnoho vzorů

Nepřidávejte příliš mnoho [vzory](luis-concept-patterns.md). Služba LUIS je určená další rychle s méně příklady. Nechcete zbytečně přetížení systému.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Nemáte trénování a publikování s každou utterance jeden příklad

Přidání projevů 10 až 15 před trénování a publikování. Který můžete vidět, jaký dopad na přesnost předpovědi. Přidání jednoho utterance nemusí mít viditelné dopad na skóre. 

## <a name="do-use-versions-for-each-app-iteration"></a>Použijte pro každou iteraci aplikace verze

Každý cyklus vytváření obsahu by měl být v rámci nové [verze](luis-concept-version.md), který je klonován z existující verze. LUIS nemá žádné omezení pro verze. Název verze se používá jako součást trasy rozhraní API, takže je důležité vybrat znaky povolené v adrese URL i zachovat počet znaků, které jsou pro danou verzi 10. Vytvořte strategii názvů verzí, aby byly vaše verze uspořádané. 

Další informace:
* " [Vysvětlení, jak a kdy použít verzi LUIS](luis-concept-version.md)
* Postupy: [Použití verzí k úpravám a testování bez dopadu na pracovní nebo produkční aplikace](luis-how-to-manage-versions.md)


## <a name="next-steps"></a>Další postup

* Zjistěte, jak [plánování aplikace](luis-how-plan-your-app.md) ve vaší aplikaci LUIS.
