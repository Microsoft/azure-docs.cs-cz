---
title: Postup výběru správné nabídky propustnosti v nástroji Azure Cosmos DB
description: Přečtěte si, jak zvolit standardní (ruční) zřízené propustnost a propustnost zřízené při automatickém škálování pro vaše úlohy.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: dech
ms.openlocfilehash: fbe17d75ad809c54939624b1409e281b2f62a037
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605201"
---
# <a name="how-to-choose-between-standard-manual-and-autoscale-provisioned-throughput"></a>Jak vybrat standardní (ruční) a zajištěné propustnosti v rámci automatického škálování 

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

Dále určete, jak se normalizované využití v průběhu času mění. Pokud zjistíte, že vaše normalizované využití je proměnné nebo nepředvídatelné, zvažte povolení automatického škálování ve vaší databázi nebo kontejneru. Na rozdíl od toho, pokud je stabilní a předvídatelný, zvažte zbývající na standardní (ruční) zřízené propustnost. 

> [!TIP]
> Při standardní (ruční) propustnosti můžete použít normalizovanou metriku využití k odhadu skutečného RU/s, který můžete použít, když přepnete na automatické škálování. Vynásobte normalizované využití k určitému bodu v čase aktuálně zajištěným standardem (ruční) RU/s. Pokud jste například zřídili 5000 RU/s a normalizované využití je 90%, použití RU/s je 0,9 * 5000 = 4500 RU/s. Pokud vidíte, že váš vzor přenosů je proměnlivý, ale jste nad nebo v něm zřízeni, možná budete chtít povolit automatické škálování a odpovídajícím způsobem změnit nastavení automatického škálování max. RU/s.

## <a name="measure-and-monitor-your-usage"></a>Měření a monitorování využití
Po výběru typu propustnosti v průběhu času byste měli monitorovat svoji aplikaci a podle potřeby provádět úpravy. 

Pokud používáte automatické škálování, použijte Azure Monitor k zobrazení maximálního množství RU/s poskytovaného pro automatické škálování (**maximální propustnost automatického škálování**) a ru/s, na který je systém aktuálně škálovatelný (**zřízená propustnost**). Níže je uveden příklad proměnné nebo nepředvídatelné úlohy pomocí automatického škálování. Všimněte si, že pokud nedochází k žádným přenosům, systém škáluje RU/s na minimum z 10% maximálního RU/s, což je v tomto případě 5000 RU/s a 50 000 RU/s. 

:::image type="content" source="media/how-to-choose-offer/autoscale-metrics-azure-monitor.png" alt-text="Příklad úlohy pomocí automatického škálování":::

> [!NOTE]
> Když použijete standardní (ruční) zřízené propustnost, metrika **zřízené propustnosti** odkazuje na to, co jste nastavili jako uživatel. Když použijete propustnost automatického škálování, tato metrika odkazuje na RU/s. systém je aktuálně škálovat na.

## <a name="next-steps"></a>Další kroky
* K odhadu propustnosti pro nové úlohy použijte [kalkulačku ru](https://cosmos.azure.com/capacitycalculator/) .
* Pomocí [Azure monitor](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) můžete monitorovat stávající úlohy.
* Naučte se [zřídit propustnost automatického škálování v databázi nebo kontejneru Azure Cosmos](how-to-provision-autoscale-throughput.md).
* Přečtěte si [Nejčastější dotazy k automatickému škálování](autoscale-faq.md).