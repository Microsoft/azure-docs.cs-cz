---
title: Pochopení osvědčených postupů služby LUIS – Azure | Dokumentace Microsoftu
description: Podívejte se na LUIS osvědčené postupy pro dosažení nejlepších výsledků.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: f819952c0b20a632f3ee2b95f0b5b2d4613be3e1
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345970"
---
# <a name="best-practices"></a>Osvědčené postupy
Proces tvorby aplikace používejte k sestavení aplikace LUIS. 

* Vytvářet jazykový model
* Přidání projevů příklad několika školení (10 – 15 za účelem)
* Publikování 
* Testování z koncového bodu 
* Přidání funkcí

Jakmile je vaše aplikace [publikované](luis-how-to-publish-app.md), použijte vývojového cyklu přidat funkce, publikování a testování z koncového bodu. Přidáním další příklad projevy nezačínají dalšího vývojového cyklu. Který neumožňuje LUIS další modelu pomocí projevy reálné uživatele. 

Aby LUIS účinný na svou úlohu učení nelze rozbalit projevy až do aktuální sady příklad a koncového bodu projevy vrací předpovědi jistí, vysoké skóre. Zvýšení skóre pomocí aktivně učit a [vzory](luis-concept-patterns.md), a [frázi seznamy](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Proveďte a není
Následující seznam obsahuje osvědčené postupy pro aplikace LUIS:

|Správný postup|Chybný postup|
|--|--|
|[Definování různých záměrů](#do-define-distinct-intents) |[Přidání projevů mnoho příklad pro příkazy](#dont-add-many-example-utterances-to-intents) |
|[Vyhledá sladkost místo mezi příliš obecná a moc specifické pro každý záměr](#do-find-sweet-spot-for-intents)|[Používat službu LUIS jako platforma pro školení](#dont-use-luis-as-a-training-platform)|
|[Opakované sestavení aplikace](#do-build-the-app-iteratively)|[Přidání projevů mnoho příklad stejný formát ignoruje dalších formátů](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Přidat frázi seznamy a vzory ve vyšším počtu iterací](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Kombinace definici záměry a entity](#dont-mix-the-definition-of-intents-and-entities)|
|[Přidání projevů příklad na hodnotu None záměru](#do-add-example-utterances-to-none-intent)|[Vytváření seznamů frázi s všech možných hodnot](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Využijte funkci navrhnout pro aktivní učení](#do-leverage-the-suggest-feature-for-active-learning)|[Přidejte tolik vzory](#dont-add-many-patterns)|
|[Monitorování výkonu vaší aplikace](#do-monitor-the-performance-of-your-app)|[Trénování a publikování s každou utterance jeden příklad přidá](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definování různých záměrů
Zkontrolujte, zda že slovník pro každý záměr je jenom pro tohoto záměru a nesmí se překrývat s jinou záměr. Například pokud chcete mít aplikaci, která zpracovávají cestovní opatření, jako je například letecká společnost letů a hotelů, můžete mít jako samostatné záměry nebo stejné záměr s entitami pro konkrétní data uvnitř utterance.

Pokud je slovník mezi dvěma záměry stejná, kombinovat záměr a používání entit. 

Vezměte v úvahu následující příklad projevy:

```
Book a flight
Book a hotel
```

"Rezervovat let" a "Rezervovat hotel" použít stejné slovník "knihy". To je překrývající se proto musí být stejná záměr s jiná slova letů a hotelů extrahovat entity. 

## <a name="do-find-sweet-spot-for-intents"></a>Najít sladkost místo pro příkazy
Použijte data předpovědí služby luis k určení překrývající vaše záměry. Překrývající se záměry mate LUIS. Výsledkem je, horní vyhodnocování záměr je příliš zavřít do jiného záměr. Protože LUIS přesně stejnou cestu dat pro trénování pokaždé, když nepoužívá, má překrývající se záměrem šance na první nebo druhé v školení. Chcete, aby utterance skóre pro každý záměr za sebe, takže to nestane. Dobré rozlišení pro záměry by měla za následek očekávané hlavní záměr pokaždé, když. 
 
## <a name="do-build-the-app-iteratively"></a>Opakované sestavení aplikace
Udržovat samostatnou *Skrytá* testovací sadu, která se nepoužívá jako [příklad projevy](luis-concept-utterance.md) nebo projevy koncový bod. Neustále se zlepšují aplikace pro vaše testovací sada. Přizpůsobení testu nastavit tak, aby odrážely projevy uživatelů. Použijte tento nevidomí test nastavit k vyhodnocení každé iterace. 

Vývojáři by měli mít tři sady data. První je příklad projevy pro vytváření modelu. Druhá je pro testování modelu v koncovém bodě. Třetí je nevidomé testovací data použít v [batch testování](luis-how-to-batch-test.md). Tento poslední sady není používán školení aplikace ani odeslané pro koncový bod.  

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Přidat frázi seznamy a vzory ve vyšším počtu iterací
[Frázi seznamy](luis-concept-feature.md) umožňují definovat slovníky slov související s doménou aplikace. Počáteční hodnota vaše frázi seznam s pár slov, použijte funkci navrhnout tak LUIS ví o více slov ve slovníku. Nepřidávejte všechna slova pro slovník od seznamu frázi není přesná shoda. 

Projevy uživatelů z koncového bodu, velmi podobné k sobě navzájem, může odhalit vzory voleb aplikace word a umístění. [Vzor](luis-concept-patterns.md) funkce přebírá tato volba slov a umístění spolu s regulárních výrazů ke zlepšení vaší přesnost předpovědi. Ve vzoru regulárního výrazu umožňuje slova a interpunkční znaménka, které máte v úmyslu ignorovat při stále odpovídající vzoru. 

Použijte syntaxi volitelné vzorku pro interpunkční znaménka, může být ignorována interpunkce.

Předtím, než je vaše aplikace má přijaté žádosti koncového bodu, vzhledem k tomu, že zkosí důvěru nelze použít tyto postupy.  

## <a name="do-add-example-utterances-to-none-intent"></a>Přidání projevů příklad na hodnotu None záměru
Toto je použití náhrady záměr, uvedené všechno mimo vaši aplikaci. Přidejte jeden příklad utterance záměru pro každých 10 příklad projevy ve zbývající části aplikace LUIS None.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Využijte funkci navrhnout pro aktivní učení
Použití [aktivně učit](label-suggested-utterances.md)společnosti **zkontrolujte koncový bod projevy** v pravidelných intervalech, nepřidávat další příklad projevy na záměry. Protože aplikace neustále přijímá projevy koncový bod, je tento seznam rozrůstá a změna.

## <a name="do-monitor-the-performance-of-your-app"></a>Monitorování výkonu vaší aplikace
Monitorování přesnost předpovědi pomocí sady testů. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Nepřidávejte do záměry mnoho příklad projevy
Po publikování aplikace přidáte pouze projevy ze služby active learning v iterativní proces. Pokud jsou příliš podobná projevy, přidejte vzor. 

## <a name="dont-use-luis-as-a-training-platform"></a>Nepoužívejte LUIS jako platforma pro školení
Služba LUIS je specifický pro doménu jazykový model. Není určený k použití jako platformu obecné školení. 

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

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [plánování aplikace](plan-your-app.md) ve vaší aplikaci LUIS.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
