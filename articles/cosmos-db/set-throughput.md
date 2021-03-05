---
title: Zajištění propustnosti v kontejnerech a databázích Azure Cosmos
description: Přečtěte si, jak nastavit zřízenou propustnost pro vaše kontejnery a databáze Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/25/2021
ms.openlocfilehash: d39ade2536b96bf5e665ecfc01e81232f2fec075
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217937"
---
# <a name="introduction-to-provisioned-throughput-in-azure-cosmos-db"></a>Úvod k zřízené propustnosti v Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB umožňuje nastavit zřízenou propustnost pro vaše databáze a kontejnery. Existují dva typy zřízené propustnosti, standardní (ruční) nebo automatické škálování. Tento článek poskytuje přehled o tom, jak zajištěná propustnost funguje. 

Databáze Azure Cosmos je jednotkou správy pro sadu kontejnerů. Databáze se skládá ze sady kontejnerů nezávislých na schématu. Kontejner Azure Cosmos je jednotkou škálovatelnosti pro propustnost i úložiště. Kontejner se horizontálně dělí na sadu počítačů v rámci oblasti Azure a distribuuje se napříč všemi oblastmi Azure přidruženými k vašemu účtu Azure Cosmos.

Pomocí Azure Cosmos DB můžete zřídit propustnost ve dvou členitosti:
 
- Kontejnery Azure Cosmos
- Databáze Azure Cosmos DB

## <a name="set-throughput-on-a-container"></a>Nastavení propustnosti pro kontejner  

Propustnost zřízená v kontejneru Azure Cosmos je exkluzivně vyhrazena pro tento kontejner. Kontejner obdrží veškerou zajištěnou propustnost pokaždé. Zřízená propustnost na kontejneru je finančně zajištěná SLA. Informace o tom, jak nakonfigurovat standardní (ruční) propustnost na kontejneru, najdete v tématu [zřízení propustnosti v kontejneru Azure Cosmos](how-to-provision-container-throughput.md). Informace o tom, jak nakonfigurovat propustnost automatického škálování na kontejneru, najdete v tématu [zřizování propustnosti automatického škálování](how-to-provision-autoscale-throughput.md).

Nastavení zřízené propustnosti kontejneru je nejčastěji používaná možnost. Propustnost pro kontejner můžete elasticky škálovat tím, že zřizujete množství propustnosti pomocí [jednotek žádostí (ru)](request-units.md). 

Propustnost zřízená pro kontejner je rovnoměrně rozdělená mezi své fyzické oddíly a za předpokladu, že klíč oddílu, který distribuuje logické oddíly rovnoměrně mezi fyzické oddíly, je propustnost rovnoměrně rozložená napříč všemi logickými oddíly kontejneru. Nemůžete selektivně zadat propustnost pro logické oddíly. Vzhledem k tomu, že jeden nebo více logických oddílů kontejneru je hostován fyzickým oddílem, fyzické oddíly patří výhradně do kontejneru a podporují propustnost zajištěná v kontejneru. 

Pokud zatížení běžící na logickém oddílu spotřebovává více než propustnost, která byla přidělena základnímu fyzickému oddílu, je možné, že vaše operace budou omezeny na míru. K tomu, co se říká _aktivní oddíl_ , dojde v případě, že jeden logický oddíl má neúměrnější více požadavků než jiné hodnoty klíče oddílu.

Pokud dojde k omezení rychlosti, můžete buď zvýšit zřízenou propustnost celého kontejneru, nebo operaci zopakovat. Ujistěte se také, že jste vybrali klíč oddílu, který rovnoměrně distribuuje úložiště a vyžádá svazek. Další informace o dělení najdete [v tématu dělení a horizontální škálování v Azure Cosmos DB](partitioning-overview.md).

Doporučujeme, abyste nakonfigurovali propustnost na členitosti kontejneru, pokud požadujete předvídatelný výkon kontejneru.

Následující obrázek ukazuje, jak fyzický oddíl je hostitelem jednoho nebo více logických oddílů kontejneru:

:::image type="content" source="./media/set-throughput/resource-partition.png" alt-text="Fyzický oddíl, který je hostitelem jednoho nebo více logických oddílů kontejneru" border="false":::

## <a name="set-throughput-on-a-database"></a>Nastavení propustnosti databáze

Při zřizování propustnosti v databázi Azure Cosmos se propustnost sdílí ve všech kontejnerech (nazývaných sdílené databázové kontejnery) v databázi. Výjimkou je, pokud některým kontejnerům v databázi nastavíte zřízenou propustnost. Sdílení zřízené propustnosti databáze mezi kontejnery je podobné jako hostování databáze na clusteru počítačů. Vzhledem k tomu, že všechny kontejnery v rámci databáze sdílejí prostředky dostupné v počítači, přirozeně nedosáhnete předvídatelného výkonu u žádného konkrétního kontejneru. Informace o tom, jak nakonfigurovat zřízenou propustnost v databázi, najdete v tématu [Konfigurace zřízené propustnosti v databázi Azure Cosmos](how-to-provision-database-throughput.md). Informace o tom, jak nakonfigurovat propustnost automatického škálování v databázi, najdete v tématu [zřizování propustnosti automatického škálování](how-to-provision-autoscale-throughput.md).

Vzhledem k tomu, že všechny kontejnery v databázi sdílejí zřízenou propustnost, Azure Cosmos DB pro konkrétní kontejner v této databázi neposkytují předvídatelné záruky propustnosti. Část propustnosti, kterou může určitý kontejner obdržet, závisí na:

* Počet kontejnerů.
* Volba klíčů oddílů pro různé kontejnery.
* Rozdělení zatížení napříč různými logickými oddíly kontejnerů. 

Doporučujeme, abyste nakonfigurovali propustnost v databázi, pokud chcete sdílet propustnost napříč více kontejnery, ale nechcete vyhradit propustnost pro jakýkoliv konkrétní kontejner. 

Následující příklady ukazují, kde je upřednostňováno zřídit propustnost na úrovni databáze:

* Sdílení zřízené propustnosti databáze napříč sadou kontejnerů je užitečné pro víceklientské aplikace. Každý uživatel může být reprezentován jedinečným kontejnerem Azure Cosmos.

* Sdílení zřízené propustnosti databáze napříč sadou kontejnerů je užitečné při migraci databáze NoSQL, jako je třeba MongoDB nebo Cassandra, která je hostovaná v clusteru virtuálních počítačů nebo z místních fyzických serverů na Azure Cosmos DB. Zamyslete se nad zajištěnou propustností nakonfigurovanou v databázi Azure Cosmos jako s logickým ekvivalentem, ale cenově výhodnější a elastické pro výpočetní kapacitu vašeho clusteru MongoDB nebo Cassandra.  

Všechny kontejnery vytvořené v databázi s zřízenou propustností se musí vytvořit s [klíčem oddílu](partitioning-overview.md). V jakémkoli okamžiku je propustnost přidělená kontejneru v rámci databáze distribuována napříč všemi logickými oddíly tohoto kontejneru. Pokud máte kontejnery, které sdílejí zřízenou propustnost nakonfigurovanou v databázi, nemůžete selektivně použít tuto propustnost na konkrétní kontejner nebo na logický oddíl. 

Pokud zatížení na logickém oddílu spotřebovává více než propustnost, která je přidělena konkrétnímu logickému oddílu, jsou operace omezené na míru. Pokud dojde k omezení rychlosti, můžete buď zvýšit propustnost pro celou databázi, nebo opakovat operace. Další informace o dělení najdete v tématu [logické oddíly](partitioning-overview.md).

Kontejnery v databázi se sdílenou propustností sdílí propustnost (RU/s) přidělenou dané databázi. Při standardní (ruční) zřízené propustnosti můžete mít v databázi až 25 kontejnerů s minimálně 400 RU/s. Díky zajištění vysoce škálovatelné propustnosti můžete mít až 25 kontejnerů v databázi s AutoScale Max 4000 RU/s (škály 400-4000 RU/s).

> [!NOTE]
> V únoru 2020 jsme představili změnu, která vám umožní mít ve sdílené databázi propustnosti maximálně 25 kontejnerů, což lépe umožňuje sdílení propustnosti v kontejnerech. Po prvních 25 kontejnerech můžete do databáze přidat další kontejnery pouze v případě, že jsou [zřízeny s vyhrazenou propustností](#set-throughput-on-a-database-and-a-container), která je oddělená od sdílené propustnosti databáze.<br>
Pokud váš účet služby Azure Cosmos DB již obsahuje databázi se sdílenou propustností a 25 nebo více kontejnery, tato změna na něj ani na jakékoli jiné účty ve stejném předplatném Azure nemá vliv. Pokud máte svůj názor nebo dotazy, obraťte se prosím na [podporu produktu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) . 

Pokud vaše úlohy zahrnují odstranění a opětovné vytvoření všech kolekcí v databázi, doporučuje se odstranit prázdnou databázi a znovu vytvořit novou databázi před vytvořením kolekce. Následující obrázek ukazuje, jak může fyzický oddíl hostovat jeden nebo více logických oddílů, které patří do různých kontejnerů v rámci databáze:

:::image type="content" source="./media/set-throughput/resource-partition2.png" alt-text="Fyzický oddíl, který je hostitelem jednoho nebo více logických oddílů, které patří do různých kontejnerů. " border="false":::

## <a name="set-throughput-on-a-database-and-a-container"></a>Nastavení propustnosti pro databázi a kontejner

Tyto dva modely můžete kombinovat. Zajištění propustnosti databáze i kontejneru je povoleno. Následující příklad ukazuje, jak zřídit standardní (ruční) zřízené propustnost v databázi Azure Cosmos a v kontejneru:

* Můžete vytvořit databázi Azure Cosmos s názvem *Z* s zřízenou propustností *"K"* ru. 
* Dále v rámci databáze vytvořte pět kontejnerů s názvem *a*, *B*, *C*, *D* a *E* . Při vytváření kontejneru B Nezapomeňte povolit **vyhrazenou propustnost pro tuto možnost kontejneru** a explicitně nakonfigurovat *"P"* ru zřízené propustnosti v tomto kontejneru. Sdílenou a vyhrazenou propustnost můžete nakonfigurovat pouze při vytváření databáze a kontejneru. 

   :::image type="content" source="./media/set-throughput/coll-level-throughput.png" alt-text="Nastavení propustnosti na úrovni kontejneru":::

* Propustnost *"K"* ru se sdílí mezi čtyřmi kontejnery *a*, *C*, *D* a *E*. Přesné množství propustnosti, které je dostupné pro *a*, *C*, *D* nebo *E* , se liší. Pro každou propustnost jednotlivých kontejnerů neexistují žádné SLA.
* U kontejneru s názvem *B* je zaručeno, že se po celou dobu vrátí propustnost ru *"P"* . Je zajištěný pomocí SLA.

> [!NOTE]
> Kontejner s zřízenou propustností nelze převést na sdílený kontejner databáze. Sdílený kontejner databáze nejde převést na vyhrazenou propustnost.

## <a name="update-throughput-on-a-database-or-a-container"></a>Aktualizace propustnosti v databázi nebo kontejneru

Po vytvoření kontejneru Azure Cosmos nebo databáze můžete aktualizovat zřízenou propustnost. Maximální zajištěná propustnost, kterou můžete nakonfigurovat v databázi nebo v kontejneru, není nijak omezena.

### <a name="current-provisioned-throughput"></a><a id="current-provisioned-throughput"></a> Současná zřízená propustnost

Zřízenou propustnost kontejneru nebo databáze můžete načíst v Azure Portal nebo pomocí sad SDK:

* [Container. ReadThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.readthroughputasync?view=azure-dotnet&preserve-view=true) v sadě .NET SDK.
* [CosmosContainer. readThroughput](/java/api/com.azure.cosmos.cosmosasynccontainer.readthroughput) v sadě Java SDK.

Reakce těchto metod také obsahuje [minimální zřízenou propustnost](concepts-limits.md#storage-and-database-operations) pro kontejner nebo databázi:

* [ThroughputResponse. MinThroughput](/dotnet/api/microsoft.azure.cosmos.throughputresponse.minthroughput?view=azure-dotnet&preserve-view=true) v sadě .NET SDK.
* [ThroughputResponse. getMinThroughput ()](/java/api/com.azure.cosmos.models.throughputresponse.getminthroughput) v sadě Java SDK.

Skutečné minimální RU/s se může lišit v závislosti na konfiguraci vašeho účtu. Obecně se ale jedná o maximum:

* 400 RU/s 
* Aktuální úložiště v GB × 10 RU/s (Toto omezení se dá v některých případech zmírnit, přečtěte si náš [velký program pro úložiště/nízkou propustnost](#high-storage-low-throughput-program)).
* Nejvyšší RU/s zřízené v databázi nebo kontejneru/100

### <a name="changing-the-provisioned-throughput"></a>Změna zřízené propustnosti

Zřízenou propustnost kontejneru nebo databáze můžete škálovat prostřednictvím Azure Portal nebo pomocí sad SDK:

* [Container. ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet&preserve-view=true) v sadě .NET SDK.
* [CosmosContainer. replaceThroughput](/java/api/com.azure.cosmos.cosmosasynccontainer.replacethroughput) v sadě Java SDK.

Pokud **snižujete zřízenou propustnost**, budete ji moci provést až na [minimum](#current-provisioned-throughput).

Pokud **zvýšíte zřízenou propustnost**, většinou je operace okamžitá. Existují však případy, kdy může operace trvat delší dobu, protože systémové úlohy vyžadují zřízení požadovaných prostředků. V takovém případě se pokus o změnu zřízené propustnosti v průběhu této operace zaznamená odpověď HTTP 423 s chybovou zprávou s vysvětlením, že probíhá jiná operace škálování.

> [!NOTE]
> Pokud plánujete velmi velkou úlohu příjmu, která bude vyžadovat velký nárůst zřízené propustnosti, pamatujte na to, že operace škálování nemá žádnou smlouvu SLA a jak je uvedeno v předchozím odstavci, může trvat dlouhou dobu, než se zvýší velká doba. Možná budete chtít naplánovat dopředu a zahájit škálování před spuštěním úlohy a pomocí níže uvedených metod ověřit průběh.

Průběh škálování můžete programově ověřit načtením [aktuální zřízené propustnosti](#current-provisioned-throughput) a použitím:

* [ThroughputResponse. IsReplacePending](/dotnet/api/microsoft.azure.cosmos.throughputresponse.isreplacepending?view=azure-dotnet&preserve-view=true) v sadě .NET SDK.
* [ThroughputResponse. isReplacePending ()](/java/api/com.azure.cosmos.models.throughputresponse.isreplacepending) v sadě Java SDK.

Pomocí [Azure monitor metrik](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) můžete zobrazit historii zřízené propustnosti (ru/s) a úložiště v prostředku.

## <a name="high-storage--low-throughput-program"></a><a id="high-storage-low-throughput-program"></a> Vysoká úroveň úložiště/program s nízkou propustností

Jak je popsáno výše v předchozí [zřízené propustnosti](#current-provisioned-throughput) , je minimální propustnost, kterou můžete zřídit na kontejneru nebo databázi, závislá na mnoha faktorech. Jedním z nich je množství dat, která jsou momentálně uložená, protože Azure Cosmos DB vynutila minimální propustnost 10 RU/s za GB úložiště.

To může být obavou v situacích, kdy potřebujete ukládat velké objemy dat, ale v porovnání s nízkými nároky na propustnost. Aby lépe vyhovovaly těmto scénářům, Azure Cosmos DB zavedl **program vysokého úložiště/nízké propustnosti** , který u oprávněných účtů sníží omezení ru/s na GB.

Abyste se mohli připojit k tomuto programu a posoudit celou způsobilost, stačí udělat, abyste [Tento průzkum](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRzBPrdEMjvxPuDm8fCLUtXpUREdDU0pCR0lVVFY5T1lRVEhWNUZITUJGMC4u)naplnili. Tým Azure Cosmos DB pak bude sledovat a pokračovat v registraci.

## <a name="comparison-of-models"></a>Porovnání modelů
Tato tabulka ukazuje porovnání mezi zřizováním standardní (ruční) propustností databáze vs. v kontejneru. 

|**Parametr**  |**Standardní (ruční) propustnost v databázi**  |**Standardní (ruční) propustnost na kontejneru**|**Automatické škálování propustnosti v databázi** | **Automatické škálování propustnosti na kontejneru**|
|---------|---------|---------|---------|---------|
|Vstupní bod (minimální RU/s) |400 RU/s. Může mít až 25 kontejnerů bez minimálního minima RU/s na jeden kontejner.</li> |400| Automatické škálování mezi 400 – 4000 RU/s. Může mít až 25 kontejnerů bez minimálního minima RU/s na jeden kontejner.</li> | Automatické škálování mezi 400 – 4000 RU/s.|
|Minimální RU/s na kontejner|--|400|--|Automatické škálování mezi 400 – 4000 RU/s|
|Maximální ru|Neomezeno, v databázi.|Bez omezení, na kontejneru.|Neomezeno, v databázi.|Bez omezení, na kontejneru.
|Ru přiřazený nebo dostupný ke konkrétnímu kontejneru|Žádné záruky. Ru přiřazený k danému kontejneru závisí na vlastnostech. Vlastnosti mohou být volbou klíčů oddílů kontejnerů, které sdílejí propustnost, distribuci zatížení a počet kontejnerů. |Všechny ru nakonfigurované na kontejneru jsou výhradně vyhrazené pro kontejner.|Žádné záruky. Ru přiřazený k danému kontejneru závisí na vlastnostech. Vlastnosti mohou být volbou klíčů oddílů kontejnerů, které sdílejí propustnost, distribuci zatížení a počet kontejnerů. |Všechny ru nakonfigurované na kontejneru jsou výhradně vyhrazené pro kontejner.|
|Maximální úložiště pro kontejner|Počet.|Unlimited|Unlimited|Unlimited|
|Maximální propustnost na logický oddíl kontejneru|10 000 RU/s|10 000 RU/s|10 000 RU/s|10 000 RU/s|
|Maximální velikost úložiště (data + index) na logický oddíl kontejneru|20 GB|20 GB|20 GB|20 GB|

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [logických oddílech](partitioning-overview.md).
* Přečtěte si, jak [zřídit standardní (ruční) na kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Naučte se [zřídit standardní (ruční) propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).
* Naučte se [zřídit propustnost automatického škálování v databázi nebo kontejneru Azure Cosmos](how-to-provision-autoscale-throughput.md).
