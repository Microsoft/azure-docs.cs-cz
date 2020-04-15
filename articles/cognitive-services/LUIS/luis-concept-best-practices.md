---
title: Doporučené postupy pro vytváření aplikace LUIS
description: Seznamte se s osvědčenými postupy, jak dosáhnout nejlepších výsledků z modelu aplikace LUIS.
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: 525d450084723a53ae090319d9ebf3f68d63beee
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382390"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Doporučené postupy pro vytváření aplikace pro porozumění jazykům (LUIS)
K vytvoření aplikace LUIS použijte proces vytváření aplikací:

* Vytváření jazykových modelů (záměry a entity)
* Přidejte několik promluv příklad školení (15-30 na záměr)
* Publikovat do koncového bodu
* Zkouška z koncového bodu

Po [publikování](luis-how-to-publish-app.md)aplikace můžete pomocí životního cyklu vývoje přidávat funkce, publikovat a testovat z koncového bodu. Nezačínejte další cyklus vytváření přidáním další příklad projevy, protože to neumožňuje LUIS naučit váš model s projevy uživatele reálného světa.

Nerozbalujte projevy, dokud aktuální sada ukázkové a koncového bodu projevy jsou vracející se spolehlivé, vysoké předpověď skóre. Zlepšete skóre pomocí [aktivního učení](luis-concept-review-endpoint-utterances.md).




## <a name="do-and-dont"></a>Dělat a nedělat
Následující seznam obsahuje osvědčené postupy pro aplikace LUIS:

|Správný postup|Chybný postup|
|--|--|
|[Definování odlišných záměrů](#do-define-distinct-intents)<br>[Přidání popisovačů k záměrům](#do-add-descriptors-to-intents) |[Přidání mnoha příkladových projevů do záměrů](#dont-add-many-example-utterances-to-intents)<br>[Použití několika nebo jednoduchých entit](#dont-use-few-or-simple-entities) |
|[Najít sweet spot mezi příliš obecné a příliš specifické pro každý záměr](#do-find-sweet-spot-for-intents)|[Použití služby LUIS jako školicí platformy](#dont-use-luis-as-a-training-platform)|
|[Sestavte si aplikaci iterativně s verzemi](#do-build-your-app-iteratively-with-versions)<br>[Entity sestavení pro rozklad modelu](#do-build-for-model-decomposition)|[Přidání mnoha příkladových promluv stejného formátu, ignorování jiných formátů](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Přidání vzorků v pozdějších iteracích](#do-add-patterns-in-later-iterations)|[Kombinace definice záměrů a entit](#dont-mix-the-definition-of-intents-and-entities)|
|[Vyvažte vaše projevy napříč všemi záměry](#balance-your-utterances-across-all-intents) s výjimkou žádný záměr.<br>[Přidat ukázkové projevy do záměru Žádný](#do-add-example-utterances-to-none-intent)|[Vytvoření popisovačů se všemi možnými hodnotami](#dont-create-descriptors-with-all-the-possible-values)|
|[Využijte funkci návrhu pro aktivní učení](#do-leverage-the-suggest-feature-for-active-learning)|[Přidání příliš mnoha vzorů](#dont-add-many-patterns)|
|[Sledování výkonu aplikace pomocí dávkového testování](#do-monitor-the-performance-of-your-app)|[Trénování a publikování s každým přidaným příkladem utterance](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definovat odlišné záměry
Ujistěte se, že slovní zásoba pro každý záměr je pouze pro tento záměr a není překrývající se s jiným záměrem. Například pokud chcete mít aplikaci, která zpracovává cestovní opatření, jako jsou letecké lety a hotely, můžete si vybrat, aby tyto oblasti předmětu jako samostatné záměry nebo stejný záměr s entitami pro konkrétní data uvnitř utterance.

Pokud je slovní zásoba mezi dvěma záměry stejná, zkombinujte záměr a použijte entity.

Zvažte následující příklad projevy:

|Ukázkové promluvy|
|--|
|Rezervace letu|
|Rezervujte si hotel|

`Book a flight`a `Book a hotel` používat stejnou slovní `book a `zásobu . Tento formát je stejný, takže by měl být `flight` stejný `hotel` záměr s různými slovy a jako extrahované entity.

## <a name="do-add-descriptors-to-intents"></a>Přidejte popisovače k záměrům

Popisovače pomáhají popsat funkce záměru. Popisovač může být seznam frází slov, která jsou významná pro tento záměr nebo entitu, která je pro tento záměr významná.

## <a name="do-find-sweet-spot-for-intents"></a>Do najít sladké místo pro záměry
Pomocí dat předpověď z LUIS k určení, pokud vaše záměry se překrývají. Překrývající se záměry zmást LUIS. Výsledkem je, že hlavní záměr bodování je příliš blízko k jinému záměru. Vzhledem k tomu, že služba LUIS nepoužívá přesně stejnou cestu přes data pro školení pokaždé, překrývající záměr má šanci být první nebo druhý v trénování. Chcete, aby skóre utterance pro každý záměr byl dále od sebe, takže tento flip / flop se nestane. Dobré rozlišení pro záměry by mělo mít za následek očekávaný nejvyšší záměr pokaždé.

<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Vytvářejte aplikaci iterativně s verzemi

Každý cyklus vytváření by měl být v rámci nové [verze](luis-concept-version.md), klonované z existující verze.

## <a name="do-build-for-model-decomposition"></a>Vytvořit pro rozklad modelu

Rozklad modelu má typický proces:

* vytvořit **záměr** na základě uživatelských záměrů klient-app
* přidat 15-30 příklad projevy založené na vstupu uživatele reálného světa
* popisek konceptu dat nejvyšší úrovně v příkladu utterance
* rozdělit koncept dat na dílčí součásti
* přidání popisovačů (prvků) do dílčích součástí
* přidání popisovačů (funkcí) k záměru

Po vytvoření záměru a přidané příklad projevy, následující příklad popisuje rozklad entity.

Začněte tím, že identifikuje teplu koncepty dat, které chcete extrahovat v utterance. Tohle je vaše strojově naučená entita. Pak rozložte frázi na její části. To zahrnuje identifikaci dílčích součástí (jako entity), spolu s popisovači a omezeními.

Například pokud chcete extrahovat adresu, může být volána horní počítačově nabytá entita `Address`. Při vytváření adresy identifikujte některé její dílčí součásti, například adresu, město, stát a PSČ.

Pokračujte v rozkladu těchto prvků **omezením** PSČ na regulární výraz. Rozkládají adresu ulice na části čísla ulice (pomocí předem sestaveného čísla), název ulice a typ ulice. Typ ulice lze popsat pomocí **seznamu popisů,** jako je třída, kruh, silnice a jízdní pruh.

Rozhraní API pro vytváření V3 umožňuje rozklad modelu.

## <a name="do-add-patterns-in-later-iterations"></a>Do přidat vzory v pozdějších iterací

Měli byste pochopit, jak se aplikace chová před přidáním [vzory,](luis-concept-patterns.md) protože vzorky jsou váženy více než příklad projevy a zkosení spolehlivosti.

Až pochopíte, jak se vaše aplikace chová, přidejte vzory, které se vztahují na vaši aplikaci. Není nutné je přidávat s každou [iterací](luis-concept-app-iteration.md).

Není na škodu jejich přidání na začátku návrhu modelu, ale je snazší vidět, jak každý vzor změní model po modelu je testován s projevy.

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Vyvažte své projevy ve všech záměrech

Aby luis předpovědi být přesné, množství příklad projevy v každém záměru (s výjimkou žádný záměr), musí být relativně stejné.

Pokud máte záměr s 100 příklad projevy a záměr s 20 příklad projevy, záměr 100 utterance bude mít vyšší rychlost předpověď.

## <a name="do-add-example-utterances-to-none-intent"></a>Přidat příklad projevy žádný záměr

Tento záměr je záložní záměr označující vše mimo vaši aplikaci. Přidejte jeden příklad utterance do none záměru pro každých 10 příklad projevy ve zbytku aplikace LUIS.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Využijte funkci návrhu pro aktivní učení

Používejte **projevy koncového bodu recenzování** [aktivního učení](luis-how-to-review-endpoint-utterances.md)v pravidelných intervalech, namísto přidávání dalších ukázkových projevů k záměrům. Vzhledem k tomu, že aplikace neustále přijímá projevy koncového bodu, tento seznam se zvětšuje a mění.

## <a name="do-monitor-the-performance-of-your-app"></a>Sledujte výkon aplikace

Sledujte přesnost předpovědi pomocí [dávkové testovací](luis-concept-batch-test.md) sady.

Zachovat samostatnou sadu projevy, které nejsou používány jako [ukázkové projevy](luis-concept-utterance.md) nebo projevy koncového bodu. Pokračujte ve vylepšování aplikace pro testovací sadu. Přizpůsobte testovací sadu tak, aby odrážela skutečné uživatelské projevy. Tato testovací sada slouží k vyhodnocení každé iterace nebo verze aplikace.

## <a name="dont-add-many-example-utterances-to-intents"></a>Nepřidávejte mnoho příkladprojevy záměry

Po publikování aplikace přidejte pouze projevy z aktivního učení v procesu životního cyklu vývoje. Pokud projevy jsou příliš podobné, přidejte vzorek.

## <a name="dont-use-few-or-simple-entities"></a>Nepoužívejte několik nebo jednoduchých entit

Entity jsou vytvořeny pro extrakci dat a předpověď. Je důležité, aby každý záměr mít počítačem naučil entity, které popisují data v záměru. To pomáhá luis předpovědět záměr, i v případě, že vaše klientská aplikace není nutné používat extrahované entity.

## <a name="dont-use-luis-as-a-training-platform"></a>Nepoužívejte službu LUIS jako školicí platformu

Služba LUIS je specifická pro doménu modelu jazyka. To není chtěl pracovat jako obecné přirozeného jazyka vzdělávací platformu.

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Nepřidávejte mnoho příkladů promluv stejného formátu a ignorujte jiné formáty

Služba LUIS očekává změny v projevech záměru. Projevy se mohou lišit a zároveň mají stejný celkový význam. Varianty mohou zahrnovat délku utterance, volbu slova a umístění slova.

|Nepoužívejte stejný formát|Používat různý formát|
|--|--|
|Koupit jízdenku do Seattlu<br>Koupit jízdenku do Paříže<br>Koupit jízdenku do Orlanda|Koupit 1 lístek do Seattlu<br>Rezervujte si dvě místa na červenou do Paříže příští pondělí<br>Chtěl bych rezervovat 3 vstupenky do Orlanda na jarní prázdniny|

Druhý sloupec používá různá slovesa (koupit, rezervovat, rezervovat), různá množství (1, dva, 3) a různá uspořádání slov, ale všechny mají stejný záměr zakoupit letenky pro cestování.

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Nemíchejte definici záměrů a entit

Vytvořte záměr pro jakoukoli akci, kterou váš robot provede. Použijte entity jako parametry, které umožňují tuto akci.

Pro bota, který si rezervuje letecké lety, vytvořte záměr **BookFlight.** Nevytvářejte záměr pro každou leteckou společnost nebo každou destinaci. Použijte tyto části dat jako [entity](luis-concept-entity-types.md) a označte je v příkladu projevy.

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>Nevytvářejte popisovače se všemi možnými hodnotami

Uveďte několik příkladů v [seznamech frází deskriptoru,](luis-concept-feature.md) ale ne každé slovo. SLUŽBA LUIS zobecňuje a bere v úvahu kontext.

## <a name="dont-add-many-patterns"></a>Nepřidávat mnoho vzorů

Nepřidávejte příliš mnoho [vzorů](luis-concept-patterns.md). Služba LUIS se má rychle učit s menším počtem příkladů. Zbytečně nepřetěžujte systém.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Netrénujte a publikovat s každým příkladem utterance

Přidejte 10 nebo 15 projevy před školení a publikování. To vám umožní zobrazit dopad na přesnost předpovědi. Přidání jednoho utterance nemusí mít viditelný vliv na skóre.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [naplánovat aplikaci](luis-how-plan-your-app.md) v aplikaci LUIS.
