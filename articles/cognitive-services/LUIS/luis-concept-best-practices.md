---
title: Pochopení LEOŠ osvědčené postupy - Azure | Microsoft Docs
description: Přečtěte si LEOŠ osvědčené postupy pro dosažení nejlepších výsledků.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: 729f510de59fe27761389fb1f6edb4025021565a
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35346003"
---
# <a name="best-practices"></a>Osvědčené postupy
K sestavení aplikace LEOŠ používáte aplikaci procesu vytváření. 

* Vytvoření modelu jazyka
* Přidat pár školení příklad utterances (10 až 15 za účelem)
* Publikování 
* Testování z koncového bodu 
* Přidání funkcí

Jakmile je vaše aplikace [publikovaná](publishapp.md), použijte pro tvorbu cyklus přidat funkce, publikování a testování z koncového bodu. Přidáním další příklad utterances začne do dalšího cyklu vytvářením obsahu. LEOŠ další modelu pomocí reálného uživatele utterances neumožňuje. 

Aby LEOŠ účinný na úlohy učení nebudou rozbaleny utterances dokud aktuální sadu utterances příklad a koncového bodu se vrací skóre jisti, vysoce předpovědi. Zlepšení skóre pomocí active learning [vzory](luis-concept-patterns.md), a [fráze seznamy](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Proveďte a nemáte
Následující seznam obsahuje osvědčené postupy pro LEOŠ aplikace:

|Správný postup|Chybný postup|
|--|--|
|[Zadejte odlišné záměry](#do-define-distinct-intents) |[Přidejte do tříd Intent mnoho utterances příklad](#dont-add-many-example-utterances-to-intents) |
|[Vyhledá místo paprika mezi příliš obecný a příliš specifické pro každý záměru](#do-find-sweet-spot-for-intents)|[Použití LEOŠ jako platformu školení](#dont-use-luis-as-a-training-platform)|
|[Opakované sestavení aplikace](#do-build-the-app-iteratively)|[Přidat mnoho utterances příklad stejný formát, ignoruje dalších formátů](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Přidání frázi seznamy a vzory v novější iterací](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Kombinace definice tříd Intent a entity](#dont-mix-the-definition-of-intents-and-entities)|
|[Přidat příklad utterances na hodnotu None záměrné](#do-add-example-utterances-to-none-intent)|[Vytvoření frázi seznamy s všech možných hodnot](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Využívejte funkci navrhovanou pro aktivní learning](#do-leverage-the-suggest-feature-for-active-learning)|[Přidání tolika vzory](#dont-add-many-patterns)|
|[Monitorování výkonu aplikace](#do-monitor-the-performance-of-your-app)|[Trénování a publikovat s každou jeden příklad utterance přidat](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Zadejte odlišné záměry
Zkontrolujte, zda že slovník pro každý záměr je jen pro tento záměr a není překrývající se s různých záměrů. Například pokud chcete mít aplikaci, která zpracovává cestovní plán například lety letecká společnost a hotels, můžete tyto jako samostatné záměry nebo stejné záměr s entitami pro konkrétní data uvnitř utterance.

Pokud je slovník mezi dvěma záměry stejná, kombinovat záměr a používání entit. 

Vezměte v úvahu následující příklad utterances:

```
Book a flight
Book a hotel
```

"Sešit let" a "Sešit hotelů" používat stejné termínů z "adresáře". To se překrývá, musí být stejná záměr s jiná slova letu a hotelů extrahovat entity. 

## <a name="do-find-sweet-spot-for-intents"></a>Najít paprika místo pro záměry
Data předpovědi z LEOŠ použijte k určení, pokud jsou překrývající se vaše záměry. Překrývající se záměry confuses LEOŠ. Výsledkem je, že horní vyhodnocování záměr je příliš zavřete na jiné záměr. Vzhledem k tomu, že pro trénování pokaždé, když nepoužívá LEOŠ přesně stejnou cestu prostřednictvím data, má překrývající se záměrem šanci, že první nebo druhé v školení. Chcete utterance skóre pro každý záměr být dále od sebe, tak to nedojde. Funkční rozdíl pro záměry by měl mít za následek očekávané nejvyšší záměr pokaždé, když. 
 
## <a name="do-build-the-app-iteratively"></a>Opakované vytvoření aplikace
Udržovat samostatné *Skrytá* testovací sada, která není používána jako [příklad utterances](luis-concept-utterance.md) nebo utterances koncový bod. Zachovat zlepšení aplikace pro testovací sada. Přizpůsobíte test nastavit tak, aby odrážela utterances reálný uživatel. Použijte tento skrytá test nastavit vyhodnotit každé iteraci. 

Vývojáři měli mít tři datových sad. První je příklad utterances pro vytváření modelu. Druhá je pro testování modelu v koncový bod. Je třetí blind testovacích dat použít v [testování batch](luis-how-to-batch-test.md). Tento poslední sadu není používán cvičení aplikace ani odeslány na koncovém bodu.  

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Přidání frázi seznamy a vzory v novější iterací
[Fráze seznamy](luis-concept-feature.md) umožňují definovat slovník slova související s vaší domény aplikace. Počáteční hodnoty vaší frázi seznam s pár slov pak použít funkci navrhovanou, aby věděl, může LEOŠ o další slova ve slovníku. Nepřidáte každého slova slovníku od seznamu frázi není přesnou shodu. 

Reálný uživatel utterances z koncového bodu, velmi podobné k sobě navzájem, může odhalit způsobu výběru aplikace word a umístění. [Vzor](luis-concept-patterns.md) funkce používá tento výběr aplikace word a umístění společně s použitím regulárních výrazů ke zlepšení vaší přesnost předpovědi. Regulární výraz ve vzoru umožňuje slova nebo interpunkční znaménka, které máte v úmyslu ignorovat při dál odpovídajícím vzoru. 

Syntaxí vzor pro volitelné pro interpunkční znaménka, může být ignorována interpunkce.

Předtím, než aplikace přijal žádostmi koncových, protože který zkosí je jistota, tyto postupy neplatí.  

## <a name="do-add-example-utterances-to-none-intent"></a>Přidat příklad utterances na hodnotu None záměrné
To je nouzovým záměr, uvedené všechno mimo vaší aplikace. Přidejte jeden příklad utterance záměrné pro každých 10 příklad utterances ve zbývající části aplikace LEOŠ none.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Využívejte funkci navrhovanou pro aktivní learning
Použití [active learning](label-suggested-utterances.md)na **zkontrolujte koncový bod utterances** v pravidelných intervalech, nepřidávat další příklad utterances do tříd Intent. Vzhledem k tomu, že aplikace je neustále přijímá utterances koncový bod, je tento seznam se rozšiřující a změna.

## <a name="do-monitor-the-performance-of-your-app"></a>Monitorování výkonu aplikace
Monitorování pomocí testovací sada přesnost předpovědi. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Nepřidáte mnoho utterances příklad záměry
Po publikování aplikace přidáte jenom utterances ze active učení v iterativní proces. Pokud jsou moc podobné utterances, přidejte vzor. 

## <a name="dont-use-luis-as-a-training-platform"></a>Nepoužívejte LEOŠ jako platformu školení
LEOŠ je specifická pro jazyk modelu domény. Není určená fungovat jako platformu obecné školení. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Nepřidáte mnoho utterances příklad stejný formát, ignoruje dalších formátů
LEOŠ očekává rozdíly v utterances záměr. Utterances se může lišit přitom má stejný celkový význam. Variace mohou zahrnovat utterance délka, výběr aplikace word a word umístění. 

|Nepoužívejte stejný formát|Použijte formát různých|
|--|--|
|Kupte si lístek k webu Seattle<br>Kupte si lístek do Paříž<br>Kupte si lístek do Orlandu|Koupit 1 lístek Praha<br>Rezervovat dvě místa na červené oko Paříž další pondělí<br>Chcete rezervovat 3 lístky pro Orlandu na konec pružiny|

Druhý sloupec používá jiné příkazy (koupit, rezervy, kniha), různé počty (1, dva, 3), a různých uspořádání slova, ale všechny mít stejný účel nákupních letecká společnost lístky pro cesta. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Nekombinujte definice tříd Intent a entity
Vytvořte záměrem pro jakoukoliv akci, bude trvat vaší robota. Použijte jako parametry, které umožňují, tato akce entity. 

Chatbot, který bude sešit lety letecká společnost, vytvoření **BookFlight** záměr. Nevytvářejte záměrem pro každý letecká společnost nebo každé cílové. Pomocí těchto částí dat jako [entity](luis-concept-entity-types.md) a označit je v utterances příklad. 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Nevytvářejte frázi seznamy s všech možných hodnot
Zadejte několik příkladů v [fráze seznamy](luis-concept-feature.md) , ale ne všechna slova. LEOŠ umožňuje zobecnit a zohledňuje kontextu. 

## <a name="dont-add-many-patterns"></a>Nepřidáte mnoha schémat
Nepřidáte příliš mnoho [vzory](luis-concept-patterns.md). LEOŠ slouží k další rychle s méně příklady. Nemáte zbytečně přetížení systému.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Nemáte trénování a publikovat s každou utterance jeden příklad
Přidejte 10 nebo 15 utterances před školení a publikování. Které umožňuje zobrazit dopad na přesnost předpovědi. Přidání jednoho utterance nemusí mít viditelné dopad na skóre. 

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [plánování aplikace](plan-your-app.md) ve vaší aplikaci LEOŠ.
[LEOŠ]: # Leoš. odkaz na regions.md luis webu
