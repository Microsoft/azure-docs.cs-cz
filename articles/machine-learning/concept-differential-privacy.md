---
title: Implementace rozdílového soukromí pomocí balíčku WhiteNoise (Preview)
titleSuffix: Azure Machine Learning
description: Přečtěte si, jaká je rozdílová ochrana osobních údajů a jak může balíček WhiteNoise pomáhat při implementaci rozdílových privátních systémů, které zachovávají ochranu dat.
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.openlocfilehash: 9728bf2c86c0629b09e2325650ce288cf9b3cc7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86199788"
---
# <a name="preserve-data-privacy-by-using-differential-privacy-and-the-whitenoise-package-preview"></a>Zachování ochrany soukromí dat pomocí rozdílového soukromí a balíčku WhiteNoise (Preview)

Přečtěte si, jaké jsou rozdílové ochrany osobních údajů a jak může balíček WhiteNoise pomáhat při implementaci rozdílových privátních systémů.

Jak množství dat, které organizace shromažďuje a používá ke analýzám, se zvyšuje na ochranu osobních údajů a zabezpečení. Analýzy vyžadují data. Data, která se používají k učení modelů, jsou typicky přesnější. Pokud se pro tyto analýzy použijí osobní informace, je obzvláště důležité, aby data zůstala soukromá během jejího používání.

> [!NOTE]
> Všimněte si, že přejmenováváme sadu nástrojů a zavádíme nové jméno do nadcházejících týdnů. 

## <a name="how-differential-privacy-works"></a>Jak funguje rozdílová ochrana osobních údajů

Rozdílová ochrana osobních údajů je sada systémů a postupů, které pomůžou udržet data jednotlivců v bezpečí a soukromí.

> [!div class="mx-imgBorder"]
> ![Rozdílový proces ochrany osobních údajů](./media/concept-differential-privacy/differential-privacy-process.jpg)

V tradičních scénářích se nezpracovaná data ukládají do souborů a databází. Když uživatelé analyzují data, obvykle používají nezpracovaná data. To je důležité, protože by mohlo dojít k porušení ochrany osobních údajů jednotlivce. Rozdílové soukromí se snaží s tímto problémem zabývat přidáním "šumu" nebo náhodnosti k datům, aby uživatelé nemohli identifikovat jednotlivé datové body. V takovém případě takový systém poskytuje odepření plausible.

V rozdílných privátních systémech se data sdílejí prostřednictvím požadavků nazývaných **dotazy**. Když uživatel odešle dotaz na data, operace označované jako **mechanismy ochrany osobních údajů** přidávají pro požadovaná data šum. Mechanismy ochrany osobních údajů vrátí *aproximaci dat* místo nezpracovaných dat. Tato ochrana osobních údajů – výsledek se zobrazí v **sestavě**. Sestavy se skládají ze dvou částí, z nichž jsou vypočítána skutečná data a popis způsobu, jakým byla data vytvořena.

## <a name="differential-privacy-metrics"></a>Rozdílová metrika ochrany osobních údajů

Rozdílové soukromí se snaží chránit před možností, že uživatel může vytvořit nekonečný počet sestav, aby nakonec odhalil citlivá data. Hodnota známá jako **Epsilon** měří způsob, jakým je sestava vysokou úrovní šumu nebo soukromá. Epsilon má inverzní relaci ke hluku nebo ochraně osobních údajů. Čím méně je v Epsilon, tím větší je přenos dat (a soukromých).

Hodnoty Epsilon jsou nezáporné. Hodnoty nižší než 1 poskytují plnou plausibleou odepření. Cokoli nad 1 se dodává s vyšším rizikem při expozici skutečných dat. Při implementaci rozdílových privátních systémů budete chtít sestavovat sestavy s hodnotami Epsilon v rozmezí 0 až 1.

Jiná hodnota, která je přímo v relaci do Epsilon, je **rozdílová**. Rozdíl je míra pravděpodobnosti, že sestava není plně soukromá. Čím vyšší je rozdíl, tím větší je Epsilon. Vzhledem k tomu, že se tyto hodnoty korelují, často se používají Epsilon.

## <a name="privacy-budget"></a>Rozpočet ochrany osobních údajů

Pro zajištění ochrany osobních údajů v systémech, kde je povoleno více dotazů, rozdílové soukromí definuje omezení přenosové rychlosti. Toto omezení se označuje jako **rozpočet ochrany osobních údajů**. Rozpočty osobních údajů se přidělují na množství Epsilon, obvykle mezi 1 a 3, aby se omezilo riziko přeidentifikace. Po vygenerování sestav mohou rozpočty ochrany osobních údajů sledovat hodnoty Epsilon jednotlivých sestav i agregace pro všechny sestavy. Po vyčerpání nebo poškozování rozpočtu ochrany osobních údajů uživatelé již nebudou mít přístup k datům.  

## <a name="reliability-of-data"></a>Spolehlivost dat

I když zachování ochrany osobních údajů by mělo být cílem, existují kompromisy, které jsou v důsledku použitelnosti a spolehlivosti dat. V rámci analýzy dat je možné chápat přesnost jako míra nejistoty zavedené chybami vzorkování. Tato nejistota má za následek spadat do určitých mezí. **Přesnost** z rozdílového hlediska ochrany osobních údajů místo toho měří spolehlivost dat, která je ovlivněna nejistotou zavedenou mechanismy ochrany osobních údajů. V krátkém případě je vyšší úroveň hluku nebo ochrany osobních údajů převedená na data, která mají dolní Epsilon, přesnost a spolehlivost. I když jsou data více soukromá, protože nejsou spolehlivá, je pravděpodobnější, že ji lze použít.

## <a name="implementing-differentially-private-systems"></a>Implementace rozdílových privátních systémů

Implementace rozdílových privátních systémů je obtížná. WhiteNoise je open source projekt, který obsahuje různé komponenty pro vytváření globálních rozdílných privátních systémů. WhiteNoise se skládá z následujících komponent nejvyšší úrovně:

- Jádro
- Systém

### <a name="core"></a>Jádro

Základní knihovna nástroje zahrnuje následující mechanismy ochrany osobních údajů pro implementaci rozdílového privátního systému:

|Komponenta  |Popis  |
|---------|---------|
|Analýza     | Graf s popisem libovolných výpočtů. |
|Hodnocení     | Knihovna Rust, která obsahuje sadu nástrojů pro kontrolu a odvozování nezbytných podmínek pro účely analýzy rozdílných privátních.          |
|Modul runtime     | Médium pro provedení analýzy. Referenční modul runtime je napsaný v Rust, ale moduly runtime se dají zapisovat pomocí všech výpočetních rozhraní, jako je SQL a Spark, v závislosti na vašich datových potřebách.        |
|Vazby     | Vazby jazyka a pomocné knihovny pro sestavení analýz Aktuálně WhiteNoise poskytuje vazby Pythonu. |

### <a name="system"></a>Systém

Systémová knihovna poskytuje následující nástroje a služby pro práci s tabulkovými a relačními daty:

|Komponenta  |Popis  |
|---------|---------|
|Přístup k datům     | Knihovna, která zachycuje a zpracovává dotazy SQL a vytváří sestavy. Tato knihovna je implementovaná v Pythonu a podporuje následující zdroje dat ODBC a DBAPI:<ul><li>PostgreSQL</li><li>SQL Server</li><li>Spark</li><li>Preston</li><li>PANDAS</li></ul>|
|Služba     | Služba spuštění, která poskytuje koncovým bodem REST pro poskytování požadavků nebo dotazů na sdílené zdroje dat. Služba je navržená tak, aby umožňovala složení rozdílových modulů ochrany osobních údajů, které pracují na žádostech s různými hodnotami delta a Epsilon, označované taky jako heterogenní požadavky. Tento referenční účet implementace má další dopad na dotazy na korelační data. |
|Mohl     | Vyhodnocovací filtr stochastického, který kontroluje porušení ochrany osobních údajů, přesnost a posun. Vyhodnocení podporuje následující testy: <ul><li>Test ochrany osobních údajů – určuje, jestli sestava dodržuje podmínky rozdílového soukromí.</li><li>Test přesnosti – měří, jestli spolehlivost sestav spadá do horní a dolní meze na úrovni spolehlivosti 95%.</li><li>Test nástroje – určuje, zda jsou meze spolehlivosti sestavy dostatečně blízko k datům a zároveň stále maximalizují ochranu osobních údajů.</li><li>Test bias – měří distribuci sestav pro opakované dotazy, aby se zajistilo, že nejsou vyrovnané.</li></ul> |

## <a name="next-steps"></a>Další kroky

[Zachovat ochranu osobních údajů](how-to-differential-privacy.md) v Azure Machine Learning.

Další informace o součástech WhiteNoise najdete v části úložiště GitHub pro [balíček WhiteNoise Core](https://github.com/opendifferentialprivacy/whitenoise-core), [balíček WhiteNoise System](https://github.com/opendifferentialprivacy/whitenoise-system) a [ukázky WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-samples).