---
title: Zajištění propustnosti v kontejnerech a databázích Azure Cosmos
description: Přečtěte si, jak nastavit zřízenou propustnost pro vaše kontejnery a databáze Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: a345b5a8a4d6a99b1b3928d61b22dfba0ba2735b
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248834"
---
# <a name="introduction-to-provisioned-throughput-in-azure-cosmos-db"></a>Úvod k zřízené propustnosti v Azure Cosmos DB

Azure Cosmos DB umožňuje nastavit zřízenou propustnost pro vaše databáze a kontejnery. Existují dva typy zřízené propustnosti, standardní (ruční) nebo automatické škálování. Tyto články poskytují přehled o tom, jak zajištěná propustnost funguje. 

Databáze Azure Cosmos je jednotka správy pro sadu kontejnerů. Databáze se skládá ze sady nezávislá kontejnerů schématu. Kontejner Azure Cosmos je jednotka škálovatelnosti pro propustnost i úložiště. Kontejner je vodorovně rozdělený na celou skupinu počítačů v rámci oblasti Azure a distribuuje se do všech oblastí Azure přidružených k vašemu účtu Azure Cosmos.

Pomocí Azure Cosmos DB můžete zřídit propustnost ve dvou členitosti:
 
- Kontejnery Azure Cosmos DB
- Databáze Azure Cosmos DB

## <a name="set-throughput-on-a-container"></a>Nastavení propustnosti pro kontejner  

Propustnost zřízená v kontejneru Azure Cosmos je exkluzivně vyhrazena pro tento kontejner. Kontejner obdrží veškerou zajištěnou propustnost pokaždé. Zřízená propustnost na kontejneru je finančně zajištěná SLA. Informace o tom, jak nakonfigurovat standardní (ruční) propustnost na kontejneru, najdete v tématu [zřízení propustnosti v kontejneru Azure Cosmos](how-to-provision-container-throughput.md). Informace o tom, jak nakonfigurovat propustnost automatického škálování na kontejneru, najdete v tématu [zřizování propustnosti automatického škálování](how-to-provision-autoscale-throughput.md).

Nastavení zřízené propustnosti kontejneru je nejčastěji používaná možnost. Propustnost pro kontejner můžete elasticky škálovat tím, že zřizujete množství propustnosti pomocí [jednotek žádostí (ru)](request-units.md). 

Propustnost zřízená pro kontejner je rovnoměrně rozdělená mezi své fyzické oddíly a za předpokladu, že klíč oddílu, který distribuuje logické oddíly rovnoměrně mezi fyzické oddíly, je propustnost rovnoměrně rozložená napříč všemi logickými oddíly kontejneru. Nemůžete selektivně zadat propustnost pro logické oddíly. Vzhledem k tomu, že jeden nebo více logických oddílů kontejneru je hostován fyzickým oddílem, fyzické oddíly patří výhradně do kontejneru a podporují propustnost zajištěná v kontejneru. 

Pokud zatížení, které běží na logickém oddílu, spotřebovává více než propustnost, která byla přidělena tomuto logickému oddílu, operace získají omezené sazby. Pokud dojde k omezení rychlosti, můžete buď zvýšit zřízenou propustnost celého kontejneru, nebo operaci zopakovat. Další informace o dělení najdete v tématu [logické oddíly](partition-data.md).

Doporučujeme, abyste nakonfigurovali propustnost na členitosti kontejneru, pokud chcete zaručit výkon kontejneru.

Následující obrázek ukazuje, jak fyzický oddíl je hostitelem jednoho nebo více logických oddílů kontejneru:

![Fyzický oddíl](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Nastavení propustnosti pro databázi

Při zřizování propustnosti v databázi Azure Cosmos se propustnost sdílí ve všech kontejnerech (nazývaných sdílené databázové kontejnery) v databázi. Výjimkou je, že jste pro konkrétní kontejnery v databázi zadali zřízenou propustnost. Sdílení zřízené propustnosti databáze mezi kontejnery je podobné jako hostování databáze na clusteru počítačů. Vzhledem k tomu, že všechny kontejnery v rámci databáze sdílejí prostředky dostupné v počítači, přirozeně nedosáhnete předvídatelného výkonu u žádného konkrétního kontejneru. Informace o tom, jak nakonfigurovat zřízenou propustnost v databázi, najdete v tématu [Konfigurace zřízené propustnosti v databázi Azure Cosmos](how-to-provision-database-throughput.md). Informace o tom, jak nakonfigurovat propustnost automatického škálování v databázi, najdete v tématu [zřizování propustnosti automatického škálování](how-to-provision-autoscale-throughput.md).

Nastavení propustnosti v databázi Azure Cosmos zaručuje, že se vám bude pořídit zajištěná propustnost pro tuto databázi. Vzhledem k tomu, že všechny kontejnery v databázi sdílejí zřízenou propustnost, Azure Cosmos DB pro konkrétní kontejner v této databázi neposkytují předvídatelné záruky propustnosti. Část propustnosti, kterou může určitý kontejner obdržet, závisí na:

* Počet kontejnerů.
* Volba klíčů oddílů pro různé kontejnery.
* Rozdělení zatížení napříč různými logickými oddíly kontejnerů. 

Doporučujeme, abyste nakonfigurovali propustnost v databázi, pokud chcete sdílet propustnost napříč více kontejnery, ale nechcete vyhradit propustnost pro jakýkoliv konkrétní kontejner. 

Následující příklady ukazují, kde je upřednostňováno zřídit propustnost na úrovni databáze:

* Sdílení zřízené propustnosti databáze napříč sadou kontejnerů je užitečné pro víceklientské aplikace. Každý uživatel může být reprezentován jedinečným kontejnerem Azure Cosmos.

* Sdílení zřízené propustnosti databáze napříč sadou kontejnerů je užitečné při migraci databáze NoSQL, jako je třeba MongoDB nebo Cassandra, která je hostovaná v clusteru virtuálních počítačů nebo z místních fyzických serverů na Azure Cosmos DB. Zamyslete se nad zajištěnou propustností nakonfigurovanou v databázi Azure Cosmos jako s logickým ekvivalentem, ale cenově výhodnější a elastické pro výpočetní kapacitu vašeho clusteru MongoDB nebo Cassandra.  

Všechny kontejnery vytvořené v databázi s zřízenou propustností se musí vytvořit s [klíčem oddílu](partition-data.md). V jakémkoli okamžiku je propustnost přidělená kontejneru v rámci databáze distribuována napříč všemi logickými oddíly tohoto kontejneru. Pokud máte kontejnery, které sdílejí zřízenou propustnost nakonfigurovanou v databázi, nemůžete selektivně použít tuto propustnost na konkrétní kontejner nebo na logický oddíl. 

Pokud zatížení na logickém oddílu spotřebovává více než propustnost, která je přidělena konkrétnímu logickému oddílu, jsou operace omezené na míru. Pokud dojde k omezení rychlosti, můžete buď zvýšit propustnost pro celou databázi, nebo opakovat operace. Další informace o dělení najdete v tématu [logické oddíly](partition-data.md).

Kontejnery ve sdílené databázi propustnosti sdílejí propustnost (RU/s) přidělenou této databázi. V databázi můžete mít až čtyři kontejnery s minimálně 400 RU/s. Při standardní (ruční) zřízené propustnosti budou mít každý nový kontejner po prvních čtyřech dalších minimálních 100 RU/s. Pokud máte například sdílenou databázi propustnosti s osmi kontejnery, minimální RU/s v databázi bude 800 RU/s. Díky zajištěné propustnosti v rámci automatického škálování můžete mít až 25 kontejnerů v databázi s AutoScale Max/s 4000 RU/s (škály 400-4000 RU/s).

> [!NOTE]
> V únoru 2020 jsme představili změnu, která vám umožní mít ve sdílené databázi propustnosti maximálně 25 kontejnerů, což lépe umožňuje sdílení propustnosti v kontejnerech. Po prvních 25 kontejnerech můžete do databáze přidat další kontejnery pouze v případě, že jsou [zřízeny s vyhrazenou propustností](#set-throughput-on-a-database-and-a-container), která je oddělená od sdílené propustnosti databáze.<br>
Pokud váš Azure Cosmos DB účet už obsahuje sdílenou databázi propustnosti s >= 25 kontejnerů, účet a všechny ostatní účty ve stejném předplatném Azure se z této změny nevztahují. Pokud máte svůj názor nebo dotazy, obraťte se prosím na [podporu produktu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) . 

Pokud vaše úlohy zahrnují odstranění a opětovné vytvoření všech kolekcí v databázi, doporučuje se odstranit prázdnou databázi a znovu vytvořit novou databázi před vytvořením kolekce. Následující obrázek ukazuje, jak může fyzický oddíl hostovat jeden nebo více logických oddílů, které patří do různých kontejnerů v rámci databáze:

![Fyzický oddíl](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Nastavení propustnosti pro databázi a kontejner

Tyto dva modely můžete kombinovat. Zajištění propustnosti databáze i kontejneru je povoleno. Následující příklad ukazuje, jak zřídit standardní (ruční) zřízené propustnost v databázi Azure Cosmos a v kontejneru:

* Můžete vytvořit databázi Azure Cosmos s názvem *Z* s zřízenou propustností *"K"* ru. 
* Dále v rámci databáze vytvořte pět kontejnerů s názvem *a*, *B*, *C*, *D*a *E* . Při vytváření kontejneru B Nezapomeňte povolit **vyhrazenou propustnost pro tuto možnost kontejneru** a explicitně nakonfigurovat *"P"* ru zřízené propustnosti v tomto kontejneru. Všimněte si, že se sdílená a vyhrazená propustnost dá nakonfigurovat jenom při vytváření databáze a kontejneru. 

   ![Nastavení propustnosti na úrovni kontejneru](./media/set-throughput/coll-level-throughput.png)

* Propustnost *"K"* ru se sdílí mezi čtyřmi kontejnery *a*, *C*, *D*a *E*. Přesné množství propustnosti, které je dostupné pro *a*, *C*, *D*nebo *E* , se liší. Pro každou propustnost jednotlivých kontejnerů neexistují žádné SLA.
* U kontejneru s názvem *B* je zaručeno, že se po celou dobu vrátí propustnost ru *"P"* . Je zajištěný pomocí SLA.

> [!NOTE]
> Kontejner s zřízenou propustností nelze převést na sdílený kontejner databáze. Sdílený kontejner databáze nejde převést na vyhrazenou propustnost.

## <a name="update-throughput-on-a-database-or-a-container"></a>Aktualizace propustnosti v databázi nebo kontejneru

Po vytvoření kontejneru Azure Cosmos nebo databáze můžete aktualizovat zřízenou propustnost. Maximální zajištěná propustnost, kterou můžete nakonfigurovat v databázi nebo v kontejneru, není nijak omezena. [Minimální zajištěná propustnost](concepts-limits.md#storage-and-throughput) závisí na následujících faktorech: 

* Aktuální velikost dat, kterou ukládáte do kontejneru
* Maximální propustnost, kterou jste na kontejneru někdy zřídili.
* Aktuální počet Cosmos kontejnerů Azure, které máte v databázi se sdílenou propustností. 

Minimální propustnost kontejneru nebo databáze můžete načíst programově pomocí sad SDK nebo zobrazit hodnotu v Azure Portal. Při použití sady .NET SDK umožňuje metoda [DocumentClient. ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) škálovat hodnotu zřízené propustnosti. Při použití sady Java SDK umožňuje metoda [RequestOptions. setOfferThroughput](sql-api-java-sdk-samples.md) škálovat hodnotu zřízené propustnosti. 

Při použití sady .NET SDK umožňuje metoda [DocumentClient. ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) načíst minimální propustnost kontejneru nebo databáze. 

Zřízenou propustnost kontejneru nebo databáze můžete kdykoli škálovat. Při provádění operace škálování pro zvýšení propustnosti může trvat delší dobu, než systémové úlohy zřídí požadované prostředky. Můžete kontrolovat stav operace škálování v Azure Portal nebo programově pomocí sad SDK. Při použití sady .NET SDK můžete získat stav operace škálování pomocí `DocumentClient.ReadOfferAsync` metody.

## <a name="comparison-of-models"></a>Porovnání modelů
Tato tabulka ukazuje porovnání mezi zřizováním standardní (ruční) propustností databáze vs. v kontejneru. 

|**Parametr**  |**Standardní (ruční) propustnost v databázi**  |**Standardní (ruční) propustnost na kontejneru**|**Automatické škálování propustnosti v databázi** | **Automatické škálování propustnosti na kontejneru**|
|---------|---------|---------|---------|---------|
|Vstupní bod (minimální RU/s) |400 RU/s. Po prvních čtyřech kontejnerech vyžaduje každý další kontejner minimálně 100 RU/s.</li> |400| Automatické škálování mezi 400 – 4000 RU/s. Může mít až 25 kontejnerů bez minimálního minima RU/s na jeden kontejner.</li> | Automatické škálování mezi 400 – 4000 RU/s.|
|Minimální RU/s na kontejner|100|400|--|Automatické škálování mezi 400 – 4000 RU/s|
|Maximální ru|Neomezeno, v databázi.|Bez omezení, na kontejneru.|Neomezeno, v databázi.|Bez omezení, na kontejneru.
|Ru přiřazený nebo dostupný ke konkrétnímu kontejneru|Žádné záruky. Ru přiřazený k danému kontejneru závisí na vlastnostech. Vlastnosti mohou být volbou klíčů oddílů kontejnerů, které sdílejí propustnost, distribuci zatížení a počet kontejnerů. |Všechny ru nakonfigurované na kontejneru jsou výhradně vyhrazené pro kontejner.|Žádné záruky. Ru přiřazený k danému kontejneru závisí na vlastnostech. Vlastnosti mohou být volbou klíčů oddílů kontejnerů, které sdílejí propustnost, distribuci zatížení a počet kontejnerů. |Všechny ru nakonfigurované na kontejneru jsou výhradně vyhrazené pro kontejner.|
|Maximální úložiště pro kontejner|Počet.|Unlimited|Unlimited|Unlimited|
|Maximální propustnost na logický oddíl kontejneru|10 000 RU/s|10 000 RU/s|10 000 RU/s|10 000 RU/s|
|Maximální velikost úložiště (data + index) na logický oddíl kontejneru|20 GB|20 GB|20 GB|20 GB|

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [logických oddílech](partition-data.md).
* Přečtěte si, jak [zřídit standardní (ruční) na kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Naučte se [zřídit standardní (ruční) propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).
* Naučte se [zřídit propustnost automatického škálování v databázi nebo kontejneru Azure Cosmos](how-to-provision-autoscale-throughput.md).
