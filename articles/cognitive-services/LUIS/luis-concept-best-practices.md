---
title: Osvědčené postupy
titleSuffix: Language Understanding - Azure Cognitive Services
description: Podívejte se na LUIS osvědčené postupy k dosažení nejlepších výsledků z modelu aplikace LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: diberry
ms.openlocfilehash: 4a06b30c209828e7ffd9f59d1b4ece06cfe6e2dd
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428903"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Osvědčené postupy pro vytváření aplikace language understanding pomocí služeb Cognitive Services
Proces tvorby aplikace používejte k sestavení aplikace LUIS. 

* Vytvářet jazykový model
* Přidání projevů příklad několika školení (10 – 15 za účelem)
* Publikování 
* Testování z koncového bodu 
* Přidání funkcí

Jakmile je vaše aplikace [publikované](luis-how-to-publish-app.md), použijte vývojového cyklu přidat funkce, publikování a testování z koncového bodu. Přidáním další příklad projevy nezačínají dalšího vývojového cyklu. Který neumožňuje LUIS další modelu pomocí projevy reálné uživatele. 

Aby LUIS účinný na svou úlohu učení nelze rozbalit projevy až do aktuální sady příklad a koncového bodu projevy vrací předpovědi jistí, vysoké skóre. Zvýšení skóre pomocí [aktivně učit](luis-concept-review-endpoint-utterances.md), [vzory](luis-concept-patterns.md), a [frázi seznamy](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Proveďte a není
Následující seznam obsahuje osvědčené postupy pro aplikace LUIS:

|Správný postup|Chybný postup|
|--|--|
|[Definování různých záměrů](#do-define-distinct-intents) |[Přidání projevů mnoho příklad pro příkazy](#dont-add-many-example-utterances-to-intents) |
|[Vyhledá sladkost místo mezi příliš obecná a moc specifické pro každý záměr](#do-find-sweet-spot-for-intents)|[Používat službu LUIS jako platforma pro školení](#dont-use-luis-as-a-training-platform)|
|[Opakované sestavení aplikace](#do-build-the-app-iteratively)|[Přidání projevů mnoho příklad stejný formát ignoruje dalších formátů](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Přidat frázi seznamy a vzory ve vyšším počtu iterací](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Kombinace definici záměry a entity](#dont-mix-the-definition-of-intents-and-entities)|
|[Vaše projevy vyrovnávat všechny záměry](#balance-your-utterances-across-all-intents) s výjimkou záměru None.<br>[Přidání projevů příklad na hodnotu None záměru](#do-add-example-utterances-to-none-intent)|[Vytváření seznamů frázi s všech možných hodnot](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Využijte funkci navrhnout pro aktivní učení](#do-leverage-the-suggest-feature-for-active-learning)|[Přidat příliš mnoho vzorů](#dont-add-many-patterns)|
|[Monitorování výkonu vaší aplikace](#do-monitor-the-performance-of-your-app)|[Trénování a publikování s každou utterance jeden příklad přidá](#dont-train-and-publish-with-every-single-example-utterance)|
|[Použití verze pro každou iteraci aplikace](#do-use-versions-for-each-app-iteration)||

## <a name="do-define-distinct-intents"></a>Definování různých záměrů
Zkontrolujte, zda že slovník pro každý záměr je jenom pro tohoto záměru a nesmí se překrývat s jinou záměr. Například pokud chcete mít aplikace, obslužné rutiny cesty opatření, jako je například letecká společnost letů a hotelů, můžete mít tyto předmětné oblasti jako samostatné záměry nebo stejné záměr s entitami pro konkrétní data uvnitř utterance.

Pokud je slovník mezi dvěma záměry stejná, kombinovat záměr a používání entit. 

Vezměte v úvahu následující příklad projevy:

|Ukázkové promluvy|
|--|
|Kniha let|
|Kniha hotelu|

"Rezervovat let" a "Rezervovat hotel" použít stejné slovník "knihy". Tento formát je stejné, takže by mělo být stejné záměr s jiná slova letů a hotelů jako extrahované entity. 

Další informace najdete tady:
* Koncept: [Principy záměry aplikace LUIS](luis-concept-intent.md)
* Kurz: [Sestavení aplikace LUIS k určení záměry uživatele](luis-quickstart-intents-only.md)
* Postup: [Přidání záměrů k určení uživatelů záměr projevů](luis-how-to-add-intents.md)


## <a name="do-find-sweet-spot-for-intents"></a>Najít sladkost místo pro příkazy
Použijte data předpovědí služby luis k určení překrývající vaše záměry. Překrývající se záměry zaměnit služby LUIS. Výsledkem je, horní vyhodnocování záměr je příliš zavřít do jiného záměr. Protože LUIS přesně stejnou cestu dat pro trénování pokaždé, když nepoužívá, má překrývající se záměrem šance na první nebo druhé v školení. Chcete, aby utterance skóre pro každý záměr za sebe, takže tento vyfiltrují/flop nestane. Dobré rozlišení pro záměry by měla za následek očekávané hlavní záměr pokaždé, když. 
 
## <a name="do-build-the-app-iteratively"></a>Opakované sestavení aplikace
Udržovat samostatnou sadu projevy, které se nepoužívá jako [příklad projevy](luis-concept-utterance.md) nebo projevy koncový bod. Neustále se zlepšují aplikace pro vaše testovací sada. Přizpůsobení testu nastavit tak, aby odrážely projevy uživatelů. Pomocí tohoto testu, nastavte pro vyhodnocení, jestli Každá iterace nebo verzi aplikace. 

Vývojáři by měli mít tři sady data. První je příklad projevy pro vytváření modelu. Druhá je pro testování modelu v koncovém bodě. Třetí je nevidomé testovací data použít v [batch testování](luis-how-to-batch-test.md). Tento poslední sady není používán školení aplikace ani odeslané pro koncový bod.  

Další informace najdete tady:
* Koncept: [Vytváření cyklus aplikace LUIS](luis-concept-app-iteration.md)

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Přidat frázi seznamy a vzory ve vyšším počtu iterací

Osvědčeným postupem je předtím, než je testovaná aplikace se nedá použít tyto postupy. Měli byste porozumět chování aplikace, než přidáte frázi seznamy a vzory. Jakmile pochopíte chování aplikace bez těchto přidejte všechny tyto funkce, která je použita k vaší aplikaci. Není potřeba přidejte tyto funkce s každým [iterace](luis-concept-app-iteration.md) nebo změnit funkce s jednotlivými verzemi. 

Není nezpůsobily žádné potíže, jejich přidání na začátku návrhu modelu, ale je snazší zjistit, jak jednotlivé funkce po modelu je testovat pomocí projevy změní výsledky. 

Osvědčeným postupem je otestovat prostřednictvím [koncový bod](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) tak, aby se zobrazí její Další výhodou [aktivně učit](luis-concept-review-endpoint-utterances.md). [Interaktivní testovací podokno](luis-interactive-test.md) je také platné testovací metody. 
 

### <a name="phrase-lists"></a>Seznamy frází

[Frázi seznamy](luis-concept-feature.md) umožňují definovat slovníky slov související s doménou aplikace. Počáteční hodnota vaše frázi seznam s pár slov a následné použití funkce navrhnout tak LUIS ví o více slov v konkrétní slovník do vaší aplikace. Seznam frází zlepšuje záměru zjišťování a klasifikace entity zvýšení skóre signál přidružený slova nebo fráze, které jsou důležité pro vaši aplikaci. 

Nepřidávejte všechna slova pro slovník od seznamu frázi není přesná shoda. 

Další informace najdete tady:
* Koncept: [Seznam frázi je součástí aplikace LUIS](luis-concept-feature.md)
* Postupy: [Použijte frázi v seznamech boost signál seznam slov](luis-how-to-add-features.md)

### <a name="patterns"></a>Vzory

Projevy uživatelů z koncového bodu, velmi podobné k sobě navzájem, může odhalit vzory voleb aplikace word a umístění. [Vzor](luis-concept-patterns.md) funkce přebírá tato volba slov a umístění spolu s regulárních výrazů ke zlepšení vaší přesnost předpovědi. Ve vzoru regulárního výrazu umožňuje slova a interpunkční znaménka, které máte v úmyslu ignorovat při stále odpovídající vzoru. 

Použití vzoru [volitelné syntaxe](luis-concept-patterns.md) pro interpunkční znaménka, může být ignorována interpunkce. Použití [explicitní seznam](luis-concept-patterns.md#explicit-lists) jako kompenzaci za pattern.any problémů. 

Další informace najdete tady:
* Koncept: [Vzory zvyšte přesnost předpovědi](luis-concept-patterns.md)
* Postupy: [Jak přidat vzorce, a zvyšte přesnost předpovědi](luis-how-to-model-intent-pattern.md)

## <a name="balance-your-utterances-across-all-intents"></a>Vaše projevy vyrovnávat všechny příkazy

V pořadí pro předpovědi LUIS být přesné množství projevy příklad v každé záměr (s výjimkou žádný záměru), musí rovnat relativně. 

Pokud máte záměru s 100 příklad projevy a záměru s 20 příklad projevy, 100 utterance záměr bude mít s vyšší mírou předpovědi.  

## <a name="do-add-example-utterances-to-none-intent"></a>Přidání projevů příklad na hodnotu None záměru

Tato záměrem je použití náhrady záměr, uvedené všechno mimo vaši aplikaci. Přidejte jeden příklad utterance záměru pro každých 10 příklad projevy ve zbývající části aplikace LUIS None.

Další informace najdete tady:
* Koncept: [Porozumění projevy vhodné pro aplikace LUIS](luis-concept-utterance.md)

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Využijte funkci navrhnout pro aktivní učení

Použití [aktivně učit](luis-how-to-review-endoint-utt.md)společnosti **zkontrolujte koncový bod projevy** v pravidelných intervalech, nepřidávat další příklad projevy na záměry. Protože aplikace neustále přijímá projevy koncový bod, je tento seznam rozrůstá a změna.

Další informace najdete tady:
* Koncept: [Koncepty pro povolení aktivně učit kontrolou projevy koncového bodu](luis-concept-review-endpoint-utterances.md)
* Kurz: [Kurz: Oprava jisti predikcí kontrolou projevy koncového bodu](luis-tutorial-review-endpoint-utterances.md)
* Postupy: [Kontrola projevy koncový bod portálu služby LUIS](luis-how-to-review-endoint-utt.md)

## <a name="do-monitor-the-performance-of-your-app"></a>Monitorování výkonu vaší aplikace

Sledování pomocí přesnost předpovědi [dávky testů](luis-concept-batch-test.md) nastavit. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Nepřidávejte do záměry mnoho příklad projevy

Po publikování aplikace přidáte pouze projevy ze služby active learning v iterativní proces. Pokud jsou příliš podobná projevy, přidejte vzor. 

## <a name="dont-use-luis-as-a-training-platform"></a>Nepoužívejte LUIS jako platforma pro školení

Služba LUIS je specifický pro doménu jazykový model. Není určený k použití jako platformu školení obecné přirozeného jazyka. 

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

## <a name="do-use-versions-for-each-app-iteration"></a>Pro každou iteraci aplikace používají verze

Každém vývojovém cyklu by měl být v rámci nového [verze](luis-concept-version.md), naklonované z existující verze. Služba LUIS nemá žádné omezení pro verze. Název verze se používá jako součást rozhraní API route, takže je důležité vybrat znaků povolený v adrese URL, stejně jako uchování v počtu 10 znaků pro verzi. Vývoj strategie název verze zachovat vaše verze uspořádané. 

Další informace najdete tady:
* Koncept: [Pochopit, jak a kdy používat verzi služby LUIS](luis-concept-version.md)
* Postupy: [Verze můžete upravit a testování bez dopadu na pracovní nebo produkční aplikace](luis-how-to-manage-versions.md)


## <a name="next-steps"></a>Další postup

* Zjistěte, jak [plánování aplikace](luis-how-plan-your-app.md) ve vaší aplikaci LUIS.
