---
title: Řídicí panel – Language Understanding
titleSuffix: Azure Cognitive Services
description: Opravte záměry analytics souhrnný řídicí panel vizualizačního nástrojů pro generování sestav.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a518a697369ff74689a0c4ac05af96453b6a5ca4
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072474"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Použití řídicího panelu můžete aplikaci vylepšovat

Najít a opravit problémy s záměry trénovaného aplikace při použití projevy příklad. Souhrnný řídicí panel zobrazuje celkový informace o aplikaci, s nejdůležitější funkce obsahuje příkazy, které by měly být opraveny. 

Zkontrolujte řídicí panel analýza je iterativní proces, opakuje změnit a zlepšit váš model.

Tato stránka nebude mít relevantní analýza pro aplikace, které nemají v záměrů, označované jako jakékoli projevy příklad _jen vzor_ aplikace. 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Jaké problémy lze napravit z řídicího panelu?

Jsou tři problémy vyřešené v řídicím panelu:

|Problém|Barva grafu|Vysvětlení|
|--|--|--|
|Nevyváženosti dat|-|Proběhne, když se výrazně liší množství projevy příklad. Musí mít všechny záměry _zhruba_ stejný počet příklad projevy – s výjimkou záměru None. 10 – 15 % z celkového množství projevy by měl mít jenom v aplikaci.<br><br> Pokud data jsou imbalanced ale záměru přesnost je nad určitou prahovou hodnotu, není tento imbalance označení problém.<br><br>**Začněte s tímto problémem - může být původní příčiny jiných problémů.**|
|Jasné predikcí|Orange|Proběhne, když hlavní záměr a další záměr skóre jsou dost blízko, že může překlopit na další školicí kvůli [negativní vzorkování](luis-how-to-train.md#train-with-all-data) nebo další projevy příklad přidá k záměru. |
|Nesprávný predikcí|Červená|Proběhne, když není příkladu utterance předpovědět pro označené záměr (záměru, který je v).|

Správné předpovědi jsou reprezentovány s modrou barvu.

Souhrnný řídicí panel zobrazuje tyto problémy a zjistíte, které příkazy se to týká a navrhne, co byste měli dělat ke zlepšení aplikace. 

## <a name="before-app-is-trained"></a>Předtím, než se trénuje aplikace 

Před tréninku aplikace řídicí panel shrnutí neobsahuje žádné návrhů pro opravy. Trénování vaši aplikaci tyto návrhy.  

## <a name="check-your-publishing-status"></a>Zkontrolovat stav publikování

**Stav publikování** karta obsahuje informace o aktivním posledního publikování verze. 

Zkontrolujte, že aktivní verze je verze, kterou chcete opravit. 

![Souhrnný řídicí panel zobrazuje aplikace externích služeb, publikování oblastí a agregovat přístupy do koncového bodu.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

To také zobrazuje všechny publikované regionů, externích služeb a agregovat přístupy do koncového bodu. 

## <a name="review-training-evaluation"></a>Projděte si hodnocení školení

**Hodnocení školení** karta obsahuje agregovaný souhrn celkové přesnost vaší aplikace podle oblasti. Skóre označuje záměru kvality. 

![Karta vyhodnocení školení obsahuje první oblasti, informace o přesnost celkové vaší aplikace.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

Tabulka naznačuje správně předpokládané záměry a problémových oblastí pomocí různých barev. Jak můžete vylepšit aplikaci s návrhem, toto zvýšení skóre. 

Navrhované opravy jsou oddělené typ problému a jsou nejdůležitější pro vaši aplikaci. Pokud si přejete zkontrolovat a opravit problémy na záměr, použijte **[záměry s chybami](#intents-with-errors)** karty v dolní části stránky. 

Každá oblast problému má záměrů, které je potřeba opravit. Když vyberete název záměru **záměr** stránka se otevře na projevy aplikovaném filtru. Tento filtr umožňuje zaměřit se na projevy, které jsou příčinou problému.

### <a name="compare-changes-across-versions"></a>Porovnání změn mezi verzemi

Vytvořte novou verzi před prováděním změn do aplikace. V nové verzi proveďte záměr projevů příklad navrhované změny a potom znovu trénování. Na stránce řídicího panelu **hodnocení školení** karet, použijte **zobrazit změnu oproti verzi trénovaného** k porovnání změn. 

![Porovnání změn mezi verzemi](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Opravit přidáním nebo úpravou příklad projevy a přetrénování verze

Přidat nebo upravit příklad projevy a přeučování bude primární způsob opravy aplikace. Nové nebo změněné projevy muset postupovat podle pokynů pro [různorodé projevy](luis-concept-utterance.md).

Přidání projevů příkladu by mělo být provedeno někdo, kdo:

* má vysokou míru porozumění projevy jsou v různých záměrů
* ví, jak v jedné záměr projevů může být zaměňován s jinou záměr
* může rozhodnout, jestli dvě záměrů, které jsou často vzájemně zaměňovat, by měl sbaleny do jedné záměr a různých dat získaná navýšení kapacity s entitami

### <a name="patterns-and-phrase-lists"></a>Vzory a seznamy fráze

Na stránce Analýza neukazuje, kdy použít [vzory](luis-concept-patterns.md) nebo [frázi seznamy](luis-concept-feature.md). Pokud je přidáte, může pomoct s nesprávnou nebo nejasného predikcí ale nepomůže s data imbalance. 

### <a name="review-data-imbalance"></a>Kontrola dat nevyváženosti

Začněte s tímto problémem - může být původní příčiny jiných problémů.

**Data imbalance** záměru seznam zobrazuje příkazy, které vyžadují další projevy aby bylo možné nevyváženosti data. 

**Chcete-li vyřešit tento problém**:

* Přidejte další projevy k příslušnému záměru pak trénování znovu. 

Nepřidávejte projevy na záměru žádný, pokud, která je navržena na řídicí panel shrnutí.

> [!Tip]
> Pomocí třetí části na stránce **projevy za účelem** s **projevy (číslo)** nastavení jako Stručného průvodce visual, který z nich záměry potřebovat další projevy.  
    !["Projevy (číslo)' použijte k vyhledání záměry s data imbalance.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Zkontrolujte správné predikcí

**Nesprávné předpovědi** záměru seznam zobrazuje příkazy, které mají projevy, které jsou použity jako příklady pro konkrétní záměr, ale jsou předpokládány pro různými záměry. 

**Chcete-li vyřešit tento problém**:

* Upravte projevy na konkrétnější záměr a trénování znovu.
* Kombinovat záměry, pokud jsou příliš úzce zarovnané projevy a trénování znovu.

### <a name="review-unclear-predictions"></a>Zkontrolujte jasné predikcí

**Jasné předpovědi** záměru seznam zobrazuje záměry s projevy s předpovědi skóre, které nejsou daleko dostatek způsob, jak z nejbližší rival, který hlavní záměr pro utterance se může změnit při dalším školení, z důvodu [ Záporná vzorkování](luis-how-to-train.md#train-with-all-data).

**Chcete-li vyřešit tento problém**;

* Upravte projevy na konkrétnější záměr a trénování znovu.
* Kombinovat záměry, pokud jsou příliš úzce zarovnané projevy a trénování znovu.

## <a name="utterances-per-intent"></a>Projevy na záměr

Tato karta zobrazuje celkový stav aplikace napříč příkazů. Jak je opravit záměry a obsloužených, i nadále okamžitý přehled o tuto kartu problémů.

Následující graf ukazuje dosáhl dobře vyvážené aplikace s téměř žádné problémy není potřeba opravit.

![Následující graf ukazuje dosáhl dobře vyvážené aplikace s téměř žádné problémy není potřeba opravit.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

Následující graf ukazuje, nesprávně vyvážené aplikaci s mnoha problémy vyřešit.

![Následující graf ukazuje dosáhl dobře vyvážené aplikace s téměř žádné problémy není potřeba opravit.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Najeďte myší každý záměr panelu zobrazíte informace o záměru. 

![Následující graf ukazuje dosáhl dobře vyvážené aplikace s téměř žádné problémy není potřeba opravit.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Použití **řadit** funkci k uspořádání záměry typem problému, abyste se mohli soustředit na nejvíce problematické záměry se tento problém. 

## <a name="intents-with-errors"></a>Záměry s chybami

Tato karta vám umožní zkontrolovat problémy pro specifické cílem. Výchozí zobrazení této karty je nejvíce problematické záměry, takže víte, na co zaměřit své úsilí.

![Záměry chyby karta vám umožní zkontrolovat problémy pro specifické cílem. Karta je filtrován k nejvíce problematické záměry ve výchozím nastavení, abyste věděli, na co zaměřit své úsilí.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

Začátek prstencový graf ukazuje problémy s cílem typům tři problém. Pokud dojde k problémům v typy problémů tři, každý typ má vlastní graf níže, spolu s jakékoli soupeřících záměry. 

### <a name="filter-intents-by-issue-and-percentage"></a>Filtrovat záměry tak, že problém a procento

Tato část karty, která umožňuje najít příklad projevy, které jsou nespadají vaše mezní hodnota chyby. V ideálním případě byste měli správné předpovědí bude významný. Procento se firmy a zákazníků. 

Určení procenta prahové hodnoty, které jste zvyklí pro vaši firmu. 

Filtr umožňuje najít záměry s konkrétním problémem:

|Filtr|Navrhované procento|Účel|
|--|--|--|
|Většina problematické záměrů|-|**Začněte tady** – oprava projevy v tomto záměr zlepší aplikace více než ostatní opravy.|
|Správné predikcí níže|60 %|Toto je procento projevy ve vybrané záměru, které jsou správné, ale mají skóre spolehlivosti pod prahovou hodnotou. |
|Jasné predikcí výše|15 %|Toto je procento projevy ve vybrané záměru, které jsou zaměnitelná s přesností soupeřících záměr.|
|Nesprávný predikcí výše|15 %|Toto je procento projevy ve vybrané záměru, které jsou nesprávně předpovědět. |

### <a name="correct-prediction-threshold"></a>Prahová hodnota správná predikcí

Co je skóre spolehlivosti jistotu předpovědi pro vás? Na začátek vývoje aplikací, může být 60 % cíle. Použití **opravte predikcí níže** s procento 60 % najít jakékoli projevy ve vybrané záměru, který potřeba opravit.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Prahová hodnota jasné nebo nesprávné predikcí

Tyto dva filtry umožní najít projevy ve vybrané záměru nad rámec vašeho prahovou hodnotu. Tyto dvě procenta si můžete představit jako procenta chyb. Pokud jste obeznámeni 10 až 15 % míra chyb pro předpovědi, nastavení prahové hodnoty filtru 15 % najít všechny projevy nad tuto hodnotu. 

## <a name="next-steps"></a>Další postup

* [Správa prostředků Azure](luis-how-to-azure-subscription.md)
