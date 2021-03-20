---
title: Řídicí panel – Language Understanding – LUIS
titleSuffix: Azure Cognitive Services
description: Opravte záměry a entity pomocí řídicího panelu vaší školené aplikace. Řídicí panel zobrazuje celkové informace o aplikaci s příklady záměrů, které by se měly opravit.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/08/2019
ms.openlocfilehash: 4867a065a85fab1e4abc7f19401239e5b76e1da4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541403"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Vylepšení aplikace pomocí řídicího panelu

Vyhledejte a opravte problémy s záměry školené aplikace, když používáte příklad projevy. Řídicí panel zobrazuje celkové informace o aplikaci s příklady záměrů, které by se měly opravit.

Kontrola analýzy řídicího panelu je iterativní proces, který se opakuje při změně a vylepšení modelu.

Tato stránka nebude mít relevantní analýzu pro aplikace, které nemají žádný příklad projevy v úmyslech, označované jako aplikace _jenom pro vzorky_ .

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Jaké problémy můžete z řídicího panelu opravit?

Tři problémy řešené na řídicím panelu jsou:

|Problém|Barva grafu|Vysvětlení|
|--|--|--|
|Nevyrovnanost dat|-|K tomu dochází, když se množství příkladů projevy významně liší. Všechny záměry musí mít _zhruba_ stejný počet příkladů projevy – s výjimkou záměru None. Mělo by to mít jenom 10%-15% celkového počtu projevy v aplikaci.<br><br> Pokud jsou data nevyvážená, ale přesnost záměru je nad určitou prahovou hodnotou, tato nerovnováha není hlášena jako problém.<br><br>**Začněte s tímto problémem – může to být hlavní příčina dalších problémů.**|
|Zrušit zaškrtnutí předpovědi|Oranžový|K tomu dojde v případě, že je přední záměr a skóre dalšího záměru dostatečně blízko, že se mohou vrátit k dalšímu školení, z důvodu [negativního vzorkování](luis-how-to-train.md#train-with-all-data) nebo více příkladů projevy přidaných do záměru. |
|Nesprávné předpovědi|Red|K tomu dochází, když se příklad utterance nepředpokládá pro označení záměru (záměr je v rámci).|

Správné předpovědi jsou reprezentovány barvou modrá.

Na řídicím panelu se tyto problémy zobrazí a dozvíte se, které záměry jsou ovlivněné a navrhuje, co byste měli udělat ke zlepšení aplikace.

## <a name="before-app-is-trained"></a>Před školením aplikace

Než aplikaci provedete, řídicí panel neobsahuje žádné návrhy na opravy. Výuku aplikace vám umožní zobrazit tyto návrhy.

## <a name="check-your-publishing-status"></a>Zkontroluje stav publikování.

**Stavová karta publikování** obsahuje informace o posledním publikování aktivní verze.

Ověřte, že aktivní verze je verze, kterou chcete opravit.

![Řídicí panel zobrazuje externí služby, publikované oblasti a naagregované přístupy koncového bodu aplikace.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Zobrazí se také všechny externí služby, publikované oblasti a přístupy do agregovaného koncového bodu.

## <a name="review-training-evaluation"></a>Kontrola vyhodnocení školení

Karta **hodnocení školení** obsahuje agregovaný souhrn celkové přesnosti vaší aplikace podle oblasti. Skóre indikuje kvalitu záměru.

![Karta hodnocení školení obsahuje první oblast informací o celkové přesnosti vaší aplikace.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

Graf indikuje správné předpovězené záměry a problematické oblasti s různými barvami. Při vylepšování aplikace pomocí návrhů se toto skóre zvyšuje.

Navrhované opravy jsou oddělené podle typu problému a jsou pro vaši aplikaci nejvýznamnější. Pokud budete chtít zkontrolovat a opravit problémy na záměr, použijte kartu **[záměry s chybami](#intents-with-errors)** v dolní části stránky.

Každá problematická oblast má záměry, které je třeba opravit. Když vyberete název záměru, otevře se stránka **záměr** s filtrem použitým pro projevy. Tento filtr vám umožní soustředit se na projevy, které způsobují problém.

### <a name="compare-changes-across-versions"></a>Porovnání změn ve verzích

Před provedením změn v aplikaci vytvořte novou verzi. V nové verzi proveďte navrhované změny v příkladu projevy záměru a potom znovu spusťte vlak. Na kartě **zkušební vyhodnocení školení** stránky řídicího panelu použijte příkaz **Zobrazit změnu z vyučené verze** k porovnání změn.

![Porovnání změn ve verzích](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Oprava verze přidáním nebo úpravou příkladu projevy a retraining

Primární metodou opravy vaší aplikace bude přidání nebo úprava příkladu projevy a revlak. Nové nebo změněné projevy musí postupovat podle pokynů pro [různé projevy](luis-concept-utterance.md).

Přidání příkladu projevy by mělo dělat osoba, která:

* má vysoký stupeň porozumění, co projevy jsou v různých záměrech.
* ví, jak může být projevy v jednom záměru zaměňovat s jiným záměrem.
* je možné rozhodnout, zda dva záměry, které jsou často zaměňovány, by měly být sbaleny do jediného záměru. V takovém případě musí být různá data vydaná s entitami.

### <a name="patterns-and-phrase-lists"></a>Seznamy vzorů a frází

Stránka Analytics neindikuje, kdy použít [vzory](luis-concept-patterns.md) nebo [seznamy frází](luis-concept-feature.md). Pokud je přidáte, může vám pomáhat s nesprávnými nebo nejasnými předpovědimi, ale neumožňuje nerovnováhu mezi daty.

### <a name="review-data-imbalance"></a>Kontrola nerovnováhy dat

Začněte s tímto problémem – může to být hlavní příčina dalších problémů.

Seznam záměrů **nevyrovnanosti dat** zobrazuje záměry, které vyžadují více projevy, aby bylo možné opravit nerovnováhu mezi daty.

**Chcete-li tento problém vyřešit**:

* Přidejte k záměru další projevy a potom znovu spusťte vlak.

Nepřidávejte projevy do záměru None pouze v případě, že to není navrženo na řídicím panelu.

> [!Tip]
> Použijte třetí část na stránce, **projevy na záměr** s nastavením **projevy (číslo)** jako rychlý vizuální průvodce, který potřebuje další projevy.
    ![Pomocí příkazu "projevy (číslo)" Najděte záměry s nevyrovnanou únikem dat.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Kontrola nesprávných předpovědi

**Nesprávný** seznam záměrů předpovědi zobrazuje záměry, které mají projevy, které se používají jako příklady pro určitý záměr, ale jsou předpovězeny pro různé záměry.

**Chcete-li tento problém vyřešit**:

* Upravte projevy tak, aby byly pro záměr a vlaky konkrétnější.
* Zkombinujte záměry, pokud je projevy příliš úzce zarovnané a znovu spusťte vlak.

### <a name="review-unclear-predictions"></a>Kontrola nejasného předpovědiu

Seznam **nejasných** záměrů předpovědi ukazuje záměry s projevy s skóre předpovědi, která nejsou dostatečně velká z nejbližších rival, že se nejvyšší záměr utterance může u dalšího školení změnit z důvodu [negativního vzorkování](luis-how-to-train.md#train-with-all-data).

**Chcete-li tento problém vyřešit**;

* Upravte projevy tak, aby byly pro záměr a vlaky konkrétnější.
* Zkombinujte záměry, pokud je projevy příliš úzce zarovnané a znovu spusťte vlak.

## <a name="utterances-per-intent"></a>Projevy na záměr

Tato karta zobrazuje celkový stav aplikace napříč záměry. Při opravování a přeučení se na této kartě budou zobrazovat problémy.

Následující graf znázorňuje dobře vyváženou aplikaci s téměř žádnými problémy, které je potřeba opravit.

![Následující graf znázorňuje dobře vyváženou aplikaci s téměř žádnými problémy, které je potřeba opravit.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

Následující graf zobrazuje nedostatečně vyváženou aplikaci s mnoha problémy, které je potřeba opravit.

![Snímek obrazovky ukazuje předpovědi na záměr s několika nejasnými nebo nesprávně předpokládanými výsledky.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Najeďte myší na panel jednotlivých záměrů, abyste získali informace o záměru.

![Snímek obrazovky ukazuje předpovědi na záměr s podrobnostmi o nejasných nebo nesprávně předpokládaných výsledcích.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

K uspořádání záměrů podle typu problému použijte funkci **Seřadit podle** , abyste se mohli soustředit na nejvíce problematické záměry s tímto problémem.

## <a name="intents-with-errors"></a>Záměry s chybami

Tato karta vám umožní zkontrolovat problémy konkrétního záměru. Výchozí zobrazení této karty je nejvíce problematické záměry, abyste věděli, kde se zaměřujete na své úsilí.

![Karta záměry s chybami vám umožní zkontrolovat problémy s konkrétním záměrem. Tato karta je ve výchozím nastavení filtrovaná na nejvíce problematické záměry, takže víte, kde se zaměřujete na své úsilí.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

Horní prstencový graf znázorňuje problémy s záměrem v rámci tří typů problémů. Pokud se vyskytnou problémy se třemi typy problémů, každý z nich má svůj vlastní graf spolu s případnými rival záměry.

### <a name="filter-intents-by-issue-and-percentage"></a>Filtrovat záměry podle problému a procenta

Tato část karty vám umožní najít příklady projevy, které jsou na něm mimo prahovou hodnotu chyby. V ideálním případě je vhodné, aby bylo správné předpovědi důležité. Toto procento je obchodní a řízené zákazníky.

Určete procentuální prahové hodnoty, které vám vyhovuje pro vaši firmu.

Filtr vám umožní najít záměry s konkrétním problémem:

|Filtrovat|Navrhované procento|Účel|
|--|--|--|
|Nejvíce problematických záměrů|-|**Začněte tady** – oprava projevy v tomto záměru vylepšuje aplikaci více než jiné opravy.|
|Opravit předpovědi níže|60 %|Toto je procento projevy ve vybraném záměru, které jsou správné, ale mají pod prahovou hodnotou hodnocení spolehlivosti. |
|Zrušit zaškrtnutí předpovědi nad|15 %|Toto je procentuální podíl projevy ve zvoleném záměru, který se zaměňuje s nejbližším záměrem Rival.|
|Nesprávná předpovědi nad|15 %|Toto je procento projevy ve vybraném záměru, které jsou nesprávně předpovězené. |

### <a name="correct-prediction-threshold"></a>Správná prahová hodnota předpovědi

Co je důležité skóre pro předpověď spolehlivosti? Na začátku vývoje aplikace může být 60% vaší cíli. Pomocí **správného předpovědi uvedeného níže** s procentem 60% Najděte libovolný projevy ve vybraném záměru, který je potřeba opravit.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Nejasná nebo nesprávná prahová hodnota předpovědi

Tyto dva filtry umožňují najít projevy na vybraném záměru nad rámec vaší prahové hodnoty. Tyto dva procentní podíly si můžete představit jako procento chyb. Pokud jste spokojeni s 10-15% chybou pro předpovědi, nastavte prahovou hodnotu pro filtr na 15% a vyhledejte všechny projevy nad touto hodnotou.

## <a name="next-steps"></a>Další kroky

* [Správa prostředků Azure](luis-how-to-azure-subscription.md)
