---
title: Řídicí panel – jazykové znalosti – LUIS
titleSuffix: Azure Cognitive Services
description: Opravte záměry a entity pomocí řídicího panelu trénované aplikace. Řídicí panel zobrazuje celkové informace o aplikaci s nejdůležitějšími záměry, které by měly být opraveny.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d9ae126753f55349f9bf3eefd20bc4d222866af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73888198"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Jak vylepšit aplikaci pomocí řídicího panelu

Vyhledejte a opravte problémy se záměry trénované aplikace, když používáte ukázkové projevy. Řídicí panel zobrazuje celkové informace o aplikaci s nejdůležitějšími záměry, které by měly být opraveny. 

Kontrola analýzy řídicího panelu je iterativní proces, opakujte při změně a vylepšování modelu.

Tato stránka nebude mít relevantní analýzu pro aplikace, které nemají žádné příklad projevy v záměry, označované jako pouze _vzor_ aplikace. 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Jaké problémy lze opravit z řídicího panelu?

Tři problémy řešené v řídicím panelu jsou:

|Problém|Barva grafu|Vysvětlení|
|--|--|--|
|Nevyváženost údajů|-|K tomu dochází, když množství příklad projevy výrazně liší. Všechny záměry musí mít _zhruba_ stejný počet příklad projevy – s výjimkou None záměr. By měl mít pouze 10%-15% z celkového množství projevy v aplikaci.<br><br> Pokud jsou data nevyvážená, ale přesnost záměru je nad určitou prahovou hodnotou, tato nerovnováha není hlášena jako problém.<br><br>**Začněte s tímto problémem - to může být hlavní příčinou dalších problémů.**|
|Nejasné předpovědi|Orange|K tomu dochází, když hlavní záměr a další záměr skóre jsou dostatečně blízko, že mohou překlopit na další školení, z důvodu [negativní vzorkování](luis-how-to-train.md#train-with-all-data) nebo další příklad projevy přidány k záměru. |
|Nesprávné předpovědi|Červený|K tomu dochází, když příklad utterance není předpovědět pro popisek záměru (záměr je v).|

Správné předpovědi jsou reprezentovány modrou barvou.

Řídicí panel zobrazuje tyto problémy a informuje vás, které záměry jsou ovlivněny, a navrhne, co byste měli udělat pro zlepšení aplikace. 

## <a name="before-app-is-trained"></a>Před vyškolením aplikace 

Před trénování aplikace, řídicí panel neobsahuje žádné návrhy na opravy. Vyškolte svou aplikaci, abyste viděli tyto návrhy.  

## <a name="check-your-publishing-status"></a>Kontrola stavu publikování

**Stavová** karta Publikování obsahuje informace o posledním publikování aktivní verze. 

Zkontrolujte, zda je aktivní verze verzí, kterou chcete opravit. 

![Řídicí panel zobrazuje externí služby aplikace, publikované oblasti a agregované požadavky na koncový bod.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

To také zobrazuje všechny externí služby, publikované oblasti a agregované přístupů koncového bodu. 

## <a name="review-training-evaluation"></a>Přezkoumat hodnocení školení

Karta **hodnocení školení** obsahuje souhrnný souhrn celkové přesnosti aplikace podle oblasti. Skóre označuje kvalitu záměru. 

![Karta hodnocení školení obsahuje první oblast informací o celkové přesnosti aplikace.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

Graf označuje správně předpovídané záměry a problémové oblasti s různými barvami. Jak si vylepšit aplikaci s návrhy, toto skóre se zvyšuje. 

Navrhované opravy jsou odděleny podle typu problému a jsou nejdůležitější pro vaši aplikaci. Pokud dáváte přednost kontrole a opravě problémů podle záměru, použijte kartu **[Záměry s chybami](#intents-with-errors)** v dolní části stránky. 

Každá problémová oblast má záměry, které je třeba opravit. Když vyberete název záměru, otevře se stránka **Záměr** s filtrem použitým na projevy. Tento filtr umožňuje zaměřit se na projevy, které jsou příčinou problému.

### <a name="compare-changes-across-versions"></a>Porovnání změn ve verzích

Před provedením změn v aplikaci vytvořte novou verzi. V nové verzi proveďte navrhované změny v příkladu projevy záměru a pak znovu trénovat. Na stránce Řídicí panel **školení hodnocení** karty, použijte **Zobrazit změnu z trénované verze** porovnat změny. 

![Porovnání změn ve verzích](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Oprava verze přidáním nebo úpravou ukázkových promluv a rekvalifikací

Primární metodou opravy aplikace bude přidání nebo úprava ukázkových promluv a přeškolení. Nové nebo změněné projevy je třeba dodržovat pokyny pro [různé projevy](luis-concept-utterance.md).

Přidání příklad projevy by měl y provádět někdo, kdo:

* má vysoký stupeň pochopení toho, co projevy jsou v různých záměrech.
* ví, jak projevy v jednom záměru může být zaměňována s jiným záměrem.
* je schopen rozhodnout, zda dva záměry, které jsou často zaměňovány mezi sebou, by měly být sbaleny do jednoho záměru. V takovém případě musí být různá data vytažena s entitami.

### <a name="patterns-and-phrase-lists"></a>Vzory a seznamy frází

Stránka analýzy neoznačuje, kdy použít [vzorky](luis-concept-patterns.md) nebo [seznamy frází](luis-concept-feature.md). Pokud je přidáte, může pomoci s nesprávnými nebo nejasnými předpověďmi, ale nepomůže s nerovnováhou dat. 

### <a name="review-data-imbalance"></a>Kontrola nerovnováhy dat

Začněte s tímto problémem - to může být hlavní příčinou dalších problémů.

Seznam **záměru nerovnováhy dat** zobrazuje záměry, které potřebují více projevy, aby bylo možné opravit nerovnováhu dat. 

**Chcete-li tento problém vyřešit:**

* Přidejte další projevy záměru a pak trénovat znovu. 

Nepřidávejte projevy none záměru, pokud je navrženna na řídicím panelu.

> [!Tip]
> Použijte třetí část na stránce **Utterances per intent** s **utterances (číslo)** nastavení, jako rychlé vizuální vodítko, které záměry potřebují více projevy.  
    ![Použití 'Utterances (číslo)' najít záměry s nerovnováhou dat.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Zkontrolujte nesprávné předpovědi

Seznam **záměru nesprávné předpovědi** zobrazuje záměry, které mají projevy, které se používají jako příklady pro konkrétní záměr, ale jsou předpovězeny pro různé záměry. 

**Chcete-li tento problém vyřešit:**

* Upravte projevy, aby byly konkrétnější pro záměr a znovu trénovat.
* Kombinovat záměry, pokud projevy jsou příliš úzce zarovnané a trénování znovu.

### <a name="review-unclear-predictions"></a>Přezkoumat nejasné předpovědi

**Nejasné předpověď** záměr seznam zobrazuje záměry s projevy s předpověď skóre, které nejsou dostatečně daleko od svého nejbližšího soupeře, že nejvyšší záměr pro utterance může změnit na další školení, z důvodu [negativní vzorkování](luis-how-to-train.md#train-with-all-data).

**Chcete-li tento problém vyřešit**;

* Upravte projevy, aby byly konkrétnější pro záměr a znovu trénovat.
* Kombinovat záměry, pokud projevy jsou příliš úzce zarovnané a trénování znovu.

## <a name="utterances-per-intent"></a>Projevy na záměr

Tato karta zobrazuje celkový stav aplikace v rámci záměrů. Při opravě záměrů a přeškolení, pokračovat v pohledu na tuto kartu pro problémy.

Následující graf ukazuje vyváženou aplikaci, která nemá téměř žádné problémy, které by bylo možné opravit.

![Následující graf ukazuje vyváženou aplikaci, která nemá téměř žádné problémy, které by bylo možné opravit.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

Následující graf ukazuje špatně vyváženou aplikaci s mnoha problémy, které je třeba opravit.

![Následující graf ukazuje vyváženou aplikaci, která nemá téměř žádné problémy, které by bylo možné opravit.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Najeďte nad každým indikátorem záměru, abyste získali informace o záměru. 

![Následující graf ukazuje vyváženou aplikaci, která nemá téměř žádné problémy, které by bylo možné opravit.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Pomocí funkce **Seřadit** podle uspořádat záměry podle typu problému, takže se můžete zaměřit na nejproblematičtější záměry s tímto problémem. 

## <a name="intents-with-errors"></a>Záměry s chybami

Tato karta umožňuje zkontrolovat problémy pro konkrétní záměr. Výchozí zobrazení této karty je nejproblematičtější záměry, takže víte, kam zaměřit své úsilí.

![Záměry s chybami karta umožňuje zkontrolovat problémy pro konkrétní záměr. Karta je filtrována na nejproblematičtější záměry, ve výchozím nastavení, takže víte, kam se zaměřit své úsilí.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

Horní donut graf zobrazuje problémy se záměrem přes tři typy problémů. Pokud existují problémy ve třech typech problémů, každý typ má svůj vlastní graf níže, spolu s všechny konkurenční záměry. 

### <a name="filter-intents-by-issue-and-percentage"></a>Filtrování záměrů podle problému a procenta

Tato část karty umožňuje najít příklad projevy, které spadají mimo prahovou hodnotu chyby. V ideálním případě chcete správné předpovědi být významné. Toto procento je řízeno obchodem a zákazníkem. 

Určete prahová procenta, která jsou pro vaši firmu spokojená. 

Filtr umožňuje najít záměry s konkrétním problémem:

|Filtr|Navrhované procento|Účel|
|--|--|--|
|Nejproblematičtější záměry|-|**Začněte zde** – oprava projevy v tomto záměru zlepší aplikace více než jiné opravy.|
|Správné předpovědi níže|60 %|Toto je procento projevy ve vybraném záměru, které jsou správné, ale mají skóre spolehlivosti pod prahovou hodnotou. |
|Nejasné předpovědi výše|15 %|Toto je procento projevy ve vybraném záměru, které jsou zaměňovány s nejbližší mnoství soupeře.|
|Nesprávné předpovědi výše|15 %|Toto je procento projevy ve vybraném záměru, které jsou nesprávně předpovědět. |

### <a name="correct-prediction-threshold"></a>Správná prahová hodnota předpovědi

Co je sebevědomá předpověď skóre důvěry pro vás? Na začátku vývoje aplikací může být vaším cílem 60 %. Pomocí **správné předpovědi níže** s procentem 60 % najít všechny projevy ve vybraném záměru, které je třeba opravit.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Nejasná nebo nesprávná prahová hodnota predikce

Tyto dva filtry umožňují najít projevy ve vybraném záměru mimo prahovou hodnotu. Tato dvě procenta si můžete myslet jako procenta chyb. Pokud jste spokojeni s 10-15% chybovost pro předpovědi, nastavte prahovou hodnotu filtru na 15 % najít všechny projevy nad tuto hodnotu. 

## <a name="next-steps"></a>Další kroky

* [Správa prostředků Azure](luis-how-to-azure-subscription.md)
