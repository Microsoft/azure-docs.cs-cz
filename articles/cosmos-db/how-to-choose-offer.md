---
title: Jak vybrat ruční a automatické škálování na Azure Cosmos DB
description: Naučte se vybírat standardní (ruční) zřízené propustnost a propustnost zřízené při automatickém škálování pro vaše úlohy.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: dech
ms.openlocfilehash: d8a6471d53ad4b2428504f9c53cbec6bc1967c49
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089633"
---
# <a name="how-to-choose-between-standard-manual-and-autoscale-provisioned-throughput"></a>Jak vybrat standardní (ruční) a zajištěné propustnosti v rámci automatického škálování 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB podporuje dva typy nebo nabídky zřízené propustnosti: standardní (ruční) a automatické škálování. Oba typy propustnosti jsou vhodné pro klíčové úlohy, které vyžadují vysoký výkon a škálování a jsou založené na stejné Azure Cosmos DB SLA na propustnost, dostupnost, latenci a konzistenci.

Tento článek popisuje, jak vybrat standardní (ruční) a automatické škálování zřízené propustnost pro vaše úlohy. 

## <a name="overview-of-provisioned-throughput-types"></a>Přehled zajištěných typů propustnosti
Než se začnete do rozdílu mezi standardem (ručním) a autoškálou, je důležité nejprve pochopit, jak zajištěná propustnost funguje v Azure Cosmos DB. 

Při použití zřízené propustnosti nastavíte propustnost měřenou v jednotkách žádosti za sekundu (RU/s) vyžadovaných pro vaše zatížení. Služba zřídí kapacitu potřebnou k podpoře požadavků na propustnost. Databázové operace proti službě, jako jsou čtení, zápisy a dotazy, spotřebovávají určité množství jednotek žádostí (ru). Přečtěte si další informace o [jednotkách žádostí](request-units.md).

V následující tabulce je uvedeno porovnání na vysoké úrovni mezi standardem (ručním) a AutoScale.

|Description|Standardní (ruční)|Automatické škálování|
|-------------|------|-------|
|Nejlépe vhodné pro|Úlohy se stabilním nebo předvídatelným provozem|Úlohy s proměnným nebo nepředvídatelným provozem. Viz [případy použití automatického škálování](provision-throughput-autoscale.md#use-cases-of-autoscale).|
|Jak to funguje|V průběhu času zřizujete nastavenou velikost RU/s `T` , pokud je ručně nezměníte. Každou sekundu můžete použít `T` propustnost až ru/s. <br/><br/>Pokud například nastavíte standardní (ruční) 400 RU/s, propustnost zůstane v 400 RU/s.|Nastavíte nejvyšší nebo maximální RU/s, `Tmax` které nechcete, aby systém překročil. Systém automaticky škáluje propustnost `T` tak, aby `0.1* Tmax <= T <= Tmax` . <br/><br/>Pokud například nastavíte automatické škálování maximum RU/s z 4000 RU/s, systém bude škálovat mezi 400-4000 RU/s.|
|Kdy ji použít|Chcete ručně spravovat kapacitu propustnosti (RU/s) a škálovat ji sami.<br/><br/>Máte vysoké a konzistentní využití zřízené RU/s. Po všech hodinách v měsíci, pokud nastavíte zřízené RU/s `T` a použijete celou částku 66% hodin nebo více, odhademe, že jste ušetřili standardní (ruční) ZŘÍZENÉ ru/s.<br/><br/>To je založené na porovnání mezi nastavením `T` Standard (ruční) a stejnou velikostí `Tmax` v automatickém škálování. |Chcete Azure Cosmos DB spravovat kapacitu propustnosti (RU/s) a škálovat na základě využití.<br/><br/>Máte použití RU/s, které je pro předpověď v proměnné nebo obtížné. Po všech hodinách v měsíci, pokud nastavíte automatické škálování maximum RU/s `Tmax` a použijete celou částku `Tmax` 66% z hodin nebo i méně, odhademe, že ušetříte pomocí automatického škálování.<br/><br/>To je založené na porovnání mezi nastavením automatického škálování `Tmax` a stejnou velikostí `T` ve standardní (ruční) propustnosti.|
|Model fakturace|Fakturace se provádí po hodinách pro zřízené RU/s, bez ohledu na to, kolik ru bylo spotřebováno.<br/><br/>Příklad: <li>Zřídit 400 RU/s</li><li>Hodina 1: žádné žádosti</li><li>Počet žádostí o hodinu 2:400 RU/s</li><br/><br/>Pro hodiny 1 a 2 se vám bude účtovat 400 RU/s, a to v rozmezí [Standard (ruční) sazba](https://azure.microsoft.com/pricing/details/cosmos-db/).|Fakturace se provádí po hodinách, a to po dobu nejvyšších RU/s, po kterou se systém škáluje za hodinu. <br/><br/>Příklad: <li>Zřídit automatické škálování max. RU/s 4000 RU/s (škály mezi 400-4000 RU/s)</li><li>Hodina 1: systém se škáluje až na nejvyšší hodnotu 3500 RU/s.</li><li>Hodina 2: systém se škáluje dolů na minimálně 400 RU/s (vždy 10% z `Tmax` ) z důvodu nevyužití</li><br/><br/>Budou se vám účtovat poplatky za 3500 RU/s za hodinu 1 a 400 RU/s za hodinu 2 v [sazbách propustnosti v rámci automatického škálování](https://azure.microsoft.com/pricing/details/cosmos-db/). Míra automatického škálování za RU/s je 1,5 × standardní (ruční) sazba.
|Co se stane, když překročíte zřízené RU/s|Důležité informace o tom, co se zřídí, zůstávají statické. Všechny žádosti, které využívají rámec zřízené ru za sekundu, budou omezené na míru, přičemž odpověď, která doporučuje dobu čekání před opakováním pokusu. V případě potřeby můžete v případě potřeby ručně zvýšit nebo snížit RU/s.| Systém bude škálovat RU/s až do automatického škálování max. RU/s. Všechny žádosti, které se spotřebují nad rámec automatického škálování max. RU/s za sekundu, budou omezené na míru s odpovědí, která doporučuje dobu čekání před opakováním.|

## <a name="understand-your-traffic-patterns"></a>Pochopení způsobů přenosu

### <a name="new-applications"></a>Nové aplikace ###

Pokud vytváříte novou aplikaci a ještě neznáte svůj vzor provozu, možná budete chtít začít na vstupním bodu RU/s (nebo minimálně RU/s), abyste se vyhnuli nadměrnému zřizování na začátku. Nebo, pokud máte malou aplikaci, která nevyžaduje vysoké škálování, možná budete chtít zřídit jenom minimální vstupní bod RU/s, abyste mohli optimalizovat náklady. U malých aplikací s nízkým předpokládaným provozem můžete také vzít v úvahu režim kapacity bez [serveru](throughput-serverless.md) .

Bez ohledu na to, jestli plánujete používat standardní (ruční) nebo automatické škálování, je třeba vzít v úvahu:

Pokud provedete standardní (ruční) RU/s v vstupním bodě 400 RU/s, nebudete moct spotřebovat výše 400 RU/s, pokud nebudete propustnost ručně měnit. Za 400 RU/s se vám bude účtovat standardně (ruční) zajištěná propustnost za hodinu.

Pokud zřizujete propustnost automatického škálování v vstupním bodě maxima RU/s 4000 RU/s, bude se tento prostředek škálovat mezi 400 a 4000 RU/s. Vzhledem k tomu, že sazba pro účtování propustnosti automatického škálování na RU/s je 1,5 x (ruční) sazba, ve které je velikost systému nižší než minimálně 400 RU/s, bude vaše vyúčtování vyšší, než kdybyste zřídili 400 RU/s ručně. Pokud ale používáte automatické škálování, můžete v případě špičky provozu vaší aplikace využívat až 4000 RU/s bez nutnosti zásahu uživatele. Obecně platí, že byste měli zvážit výhody, které je možné v každé chvíli využít až do maximálního počtu RU/s, a to s 1,5 x rychlostí automatického škálování.

Pomocí [kalkulačky Azure Cosmos DB kapacity](estimate-ru-with-capacity-planner.md) můžete odhadnout své požadavky na propustnost. 

### <a name="existing-applications"></a>Existující aplikace ###

Pokud máte existující aplikaci s využitím standardní (ruční) zřízené propustnosti, můžete použít [Azure monitor metriky](../azure-monitor/insights/cosmosdb-insights-overview.md) k určení, jestli je váš vzor provozu vhodný pro automatické škálování. 

Nejprve vyhledejte [metriku normalizované spotřeby požadavků](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) ve vaší databázi nebo kontejneru. Normalizované využití představuje míru toho, kolik aktuálně využíváte standardní (ruční) zřízené propustnost. Bližším číslem je 100%, tím více budete plně používat vaše zřízené RU/s. [Přečtěte si další informace](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) o této metrikě.

Dále určete, jak se normalizované využití v průběhu času mění. Vyhledá nejvyšší normalizované využití každé hodiny. Pak vypočítá průměrně normalizované využití napříč všemi hodinami. Pokud zjistíte, že vaše průměrné využití je méně než 66%, zvažte povolení automatického škálování v databázi nebo kontejneru. Naopak, pokud je průměrné využití větší než 66%, doporučujeme zůstat na standardní (ruční) zřízené propustnost.

> [!TIP]
> Pokud je váš účet nakonfigurovaný tak, aby používal zápisy ve více oblastech a má více než jednu oblast, sazba za 100 RU/s je stejná pro ruční i automatické škálování. To znamená, že povolení automatického škálování nevzniká žádné další náklady bez ohledu na využití. V důsledku toho se vždy doporučuje používat automatické škálování s zápisy ve více oblastech, pokud máte více než jednu oblast, abyste využili úspory jenom za RU/s. vaše aplikace se škáluje na. Pokud máte zápisy ve více oblastech a jednu oblast, pomocí průměrného využití určete, jestli má automatické škálování za následek úsporu nákladů. 

#### <a name="examples"></a>Příklady

Pojďme se podívat na dvě různé ukázkové úlohy a analyzovat, jestli jsou vhodné pro ruční nebo automatické škálování. Abychom vám pomohli obecný přístup, analyzujeme tři hodiny historie, abyste zjistili rozdíl mezi náklady mezi ruční a automatické škálování. U produkčních úloh se doporučuje použít 7 až 30 dnů historie (nebo delší, pokud je k dispozici) k vytvoření vzoru pro použití RU/s.

> [!NOTE]
> Všechny příklady zobrazené v tomto dokumentu jsou založené na ceně za účet Azure Cosmos nasazený v oblasti mimo státní správu v USA. Ceny a kalkulace se liší v závislosti na oblasti, kterou používáte, na nejnovější informace o cenách najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) Azure Cosmos DB.

Předpoklady:
- Předpokládejme, že aktuálně máme ruční propustnost 30 000 RU/s. 
- V naší oblasti je nakonfigurované zápisy s jednou oblastí s jednou oblastí. Pokud máme více oblastí, vynásobí hodinové náklady podle počtu oblastí.
- Využijte veřejné cenové sazby pro ruční ($0,008 USD za 100 RU/s za hodinu) a propustnost automatického škálování ($0,012 USD za 100 RU/s za hodinu) v rámci účtů pro zápis v jedné oblasti. Podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) . 

#### <a name="example-1-variable-workload-autoscale-recommended"></a>Příklad 1: variabilní zatížení (doporučeno automatického škálování)

Nejdřív se podíváme na normalizovanou spotřebu RU. Tato úloha má proměnlivý provoz s normalizovanou spotřebou RU v rozmezí od 6 do 100%. Existují občasné špičky až 100%, které je obtížné odhadnout, ale spousta hodin s nízkým využitím. 

:::image type="content" source="media/how-to-choose-offer/variable-workload_use_autoscale.png" alt-text="Zatížení s proměnnými přenosy – normalizovaná spotřeba RU mezi 6% a 100% po dobu všech hodin":::

Porovnáme ruční propustnost zřizování 30 000 RU/s a nastavujte automatické škálování max. RU/s na 30 000 (škáluje se v rozsahu 3000 – 30 000 RU/s). 

Teď analyzujeme historii. Předpokládejme, že máme využití popsané v následující tabulce. Průměrné využití v těchto třech hodinách je 39%. Vzhledem k tomu, že normalizované průměry s využitím RU na méně než 66%, ušetříme pomocí automatického škálování. 

Všimněte si, že ve hodinu 1 platí, že pokud je využití 6%, bude automatické škálování platit RU/s po dobu 10% maximálního RU/s, což je minimum za hodinu. I když náklady na automatické škálování můžou být v určitou dobu vyšší než ruční propustnost, pokud je průměrné využití méně než 66% všech hodin, automatické škálování bude finančně levnější.

|Časové období  | Využití |Poplatky za automatické škálování (RU/s)  |Možnost 1: ruční 30 000 RU/s  | Možnost 2: automatické škálování mezi 3000 – 30 000 RU/s |
|---------|---------|---------|---------|---------|
|Hodina 1  | 6 %  |     3000  |  30 000 * 0,008/100 = $2,40        |   3000 * 0,012/100 = $0,36      |
|Hodina 2  | 100 %  |     30 000    |  30 000 * 0,008/100 = $2,40       |  30 000 * 0,012/100 = $3,60      |
|Hodina 3 |  odst  |     3300    |  30 000 * 0,008/100 = $2,40       |    3300 * 0,012/100 = $0,40     |
|**Celkem**   |  |        |  $7,20       |    $4,36 (úspory v 39%)    |

#### <a name="example-2-steady-workload-manual-throughput-recommended"></a>Příklad 2: konstantní zatížení (doporučeno ruční propustnost)

Tato úloha má stálý provoz s normalizovanou spotřebou RU v rozsahu od 72% do 100%. 30 000 RU/s zřízené to znamená, že v rozmezí od 21 600 do 30 000 RU/s.

:::image type="content" source="media/how-to-choose-offer/steady_workload_use_manual_throughput.png" alt-text="Zatížení s proměnnými přenosy – normalizovaná spotřeba RU mezi 6% a 100% po dobu všech hodin":::

Porovnáme ruční propustnost zřizování 30 000 RU/s a nastavujte automatické škálování max. RU/s na 30 000 (škáluje se v rozsahu 3000 – 30 000 RU/s).

Předpokládejme, že máme historii využití, jak je popsáno v tabulce. Naše průměrné využití během těchto tří hodin je 88%. Vzhledem k tomu, že normalizované průměry z hlediska využití RU na více než 66%, ušetříme pomocí ruční propustnosti.

Obecně platí, že pokud je průměrné využití napříč všemi 730 hodinami v jednom měsíci větší než 66%, uložíme je pomocí ruční propustnosti. 

| Časové období | Využití |Poplatky za automatické škálování (RU/s)  |Možnost 1: ruční 30 000 RU/s  | Možnost 2: automatické škálování mezi 3000 – 30 000 RU/s |
|---------|---------|---------|---------|---------|
|Hodina 1  | 72%  |     21 600   |  30 000 * 0,008/100 = $2,40        |   21600 * 0,012/100 = $2,59      |
|Hodina 2  | 93 %  |     28 000    |  30 000 * 0,008/100 = $2,40       |  28 000 * 0,012/100 = $3,36       |
|Hodina 3 |  100 %  |     30 000    |  30 000 * 0,008/100 = $2,40       |    30 000 * 0,012/100 = $3,60     |
|**Celkem**   |  |        |  $7,20       |    $9,55     |

> [!TIP]
> Při standardní (ruční) propustnosti můžete použít normalizovanou metriku využití k odhadu skutečného RU/s, který můžete použít, když přepnete na automatické škálování. Vynásobte normalizované využití k určitému bodu v čase aktuálně zajištěným standardem (ruční) RU/s. Pokud jste například zřídili 5000 RU/s a normalizované využití je 90%, použití RU/s je 0,9 * 5000 = 4500 RU/s. Pokud vidíte, že váš vzor přenosů je proměnlivý, ale jste nad nebo v něm zřízeni, možná budete chtít povolit automatické škálování a odpovídajícím způsobem změnit nastavení automatického škálování max. RU/s.

#### <a name="how-to-calculate-average-utilization"></a>Jak vypočítat průměrné využití
Automatické škálování faktur pro nejvyšší úroveň RU/s za hodinu Při analýze normalizované spotřeby RU v čase je při výpočtu průměru důležité použít nejvyšší využití za hodinu. 

Výpočet průměru nejvyššího využití ve všech hodinách:
1. Nastavte **agregaci** pro metriku spotřeby Noramlized ru na **Max** .
1. Vyberte **časový interval členitosti** na 1 hodinu.
1. Přejděte na **Možnosti grafu** .
1. Vyberte možnost pruhového grafu. 
1. V části **sdílet** vyberte možnost **stáhnout do aplikace Excel** . Z vygenerované tabulky Vypočítejte průměrné využití napříč všemi hodinami. 

:::image type="content" source="media/how-to-choose-offer/variable-workload-highest-util-by-hour.png" alt-text="Zatížení s proměnnými přenosy – normalizovaná spotřeba RU mezi 6% a 100% po dobu všech hodin":::

## <a name="measure-and-monitor-your-usage"></a>Měření a monitorování využití
Po výběru typu propustnosti v průběhu času byste měli monitorovat svoji aplikaci a podle potřeby provádět úpravy. 

Pokud používáte automatické škálování, použijte Azure Monitor k zobrazení maximálního množství RU/s poskytovaného pro automatické škálování ( **maximální propustnost automatického škálování** ) a ru/s, na který je systém aktuálně škálovatelný ( **zřízená propustnost** ). Níže je uveden příklad proměnné nebo nepředvídatelné úlohy pomocí automatického škálování. Všimněte si, že pokud nedochází k žádným přenosům, systém škáluje RU/s na minimum z 10% maximálního RU/s, což je v tomto případě 5000 RU/s a 50 000 RU/s. 

:::image type="content" source="media/how-to-choose-offer/autoscale-metrics-azure-monitor.png" alt-text="Zatížení s proměnnými přenosy – normalizovaná spotřeba RU mezi 6% a 100% po dobu všech hodin":::

> [!NOTE]
> Když použijete standardní (ruční) zřízené propustnost, metrika **zřízené propustnosti** odkazuje na to, co jste nastavili jako uživatel. Když použijete propustnost automatického škálování, tato metrika odkazuje na RU/s. systém je aktuálně škálovat na.

## <a name="next-steps"></a>Další kroky
* K odhadu propustnosti pro nové úlohy použijte [kalkulačku ru](https://cosmos.azure.com/capacitycalculator/) .
* Pomocí [Azure monitor](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) můžete monitorovat stávající úlohy.
* Naučte se [zřídit propustnost automatického škálování v databázi nebo kontejneru Azure Cosmos](how-to-provision-autoscale-throughput.md).
* Přečtěte si [Nejčastější dotazy k automatickému škálování](autoscale-faq.md).