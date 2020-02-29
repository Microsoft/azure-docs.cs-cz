---
title: Zřizování propustnosti na kontejnery Azure Cosmos a databází
description: Přečtěte si, jak nastavit zřízenou propustnost pro vaše kontejnery a databáze Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: 31ad7a9d1108adc9071812454419252a813cb93e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194865"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Zřízení propustnosti u kontejnerů a databází

Databáze Azure Cosmos je jednotka správy pro skupinu kontejnerů. Databáze obsahuje sadu kontejnerů dogmaticky na schématu. Kontejner služby Azure Cosmos je jednotka škálovatelnost pro propustnost a úložiště. Kontejner je horizontálně dělené do oddílů mezi sadu počítačů v rámci oblasti Azure a je distribuovaná napříč všemi oblastmi Azure spojené s vaším účtem Azure Cosmos.

Pomocí Azure Cosmos DB můžete zřídit propustnost ve dvou členitosti:
 
- Kontejnery Azure Cosmos DB
- Databáze Azure Cosmos DB

## <a name="set-throughput-on-a-container"></a>Nastavení propustnosti pro kontejner  

Propustnost zřízená v kontejneru Azure Cosmos je exkluzivně vyhrazena pro tento kontejner. Kontejner obdrží zřízená propustnost celou dobu. Zřízenou propustnosti kontejneru finančně podložená smlouvami SLA. Informace o tom, jak nakonfigurovat propustnost na kontejneru, najdete v tématu [zřízení propustnosti v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).

Nastavení zřízené propustnosti kontejneru je nejčastěji používaná možnost. Propustnost pro kontejner můžete elasticky škálovat tím, že zřizujete množství propustnosti pomocí [jednotek žádostí (ru)](request-units.md). 

Propustnost zřízená pro kontejner je rovnoměrně rozdělená mezi své fyzické oddíly a předpokládá se dobrý klíč oddílu, který distribuuje logické oddíly rovnoměrně mezi fyzické oddíly. propustnost se také rovnoměrně distribuuje napříč všemi. logické oddíly kontejneru. Nemůžete selektivně zadat propustnost pro logické oddíly. Vzhledem k tomu, že jeden nebo více logických oddílů kontejneru je hostován fyzickým oddílem, fyzické oddíly patří výhradně do kontejneru a podporují propustnost zajištěná v kontejneru. 

Pokud zatížení, které běží na logickém oddílu, spotřebovává více než propustnost, která byla přidělena tomuto logickému oddílu, operace získají omezené sazby. Pokud dojde k omezení rychlosti, můžete buď zvýšit zřízenou propustnost celého kontejneru, nebo operaci zopakovat. Další informace o dělení najdete v tématu [logické oddíly](partition-data.md).

Doporučujeme, abyste nakonfigurovali propustnost na členitosti kontejneru, pokud chcete zaručit výkon kontejneru.

Následující obrázek ukazuje, jak fyzický oddíl je hostitelem jednoho nebo více logických oddílů kontejneru:

![Fyzický oddíl](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Nastavení propustnosti pro databázi

Při zřizování propustnosti v databázi Azure Cosmos se propustnost sdílí ve všech kontejnerech (nazývaných sdílené databázové kontejnery) v databázi. Výjimkou je, že jste pro konkrétní kontejnery v databázi zadali zřízenou propustnost. Sdílení zřízené propustnosti databáze mezi kontejnery je podobné jako hostování databáze na clusteru počítačů. Vzhledem k tomu, že všechny kontejnery v rámci databáze sdílejí prostředky dostupné v počítači, přirozeně nedosáhnete předvídatelného výkonu u žádného konkrétního kontejneru. Informace o tom, jak nakonfigurovat zřízenou propustnost v databázi, najdete v tématu [Konfigurace zřízené propustnosti v databázi Azure Cosmos](how-to-provision-database-throughput.md).

Nastavení propustnosti v databázi Azure Cosmos zaručuje, že se vám bude pořídit zajištěná propustnost pro tuto databázi. Vzhledem k tomu, že všechny kontejnery v databázi sdílejí zřízenou propustnost, Azure Cosmos DB pro konkrétní kontejner v této databázi neposkytují předvídatelné záruky propustnosti. Část propustnost, které můžou přijímat konkrétní kontejner je závislá na:

* Počet kontejnerů.
* Volba klíčů oddílů pro různé kontejnery.
* Rozdělení zatížení napříč různými logické oddíly kontejnerů. 

Doporučujeme, abyste nakonfigurovali propustnost v databázi, pokud chcete sdílet propustnost napříč více kontejnery, ale nechcete vyhradit propustnost pro jakýkoliv konkrétní kontejner. 

Následující příklady ukazují, kde je upřednostňováno zřídit propustnost na úrovni databáze:

* Sdílení zřízené propustnosti databáze napříč sadou kontejnerů je užitečné pro víceklientské aplikace. Každý uživatel může být reprezentována odlišné kontejneru Azure Cosmos.

* Sdílení zřízené propustnosti databáze napříč sadou kontejnerů je užitečné při migraci databáze NoSQL, jako je třeba MongoDB nebo Cassandra, která je hostovaná v clusteru virtuálních počítačů nebo z místních fyzických serverů na Azure Cosmos DB. Zamyslete se nad zajištěnou propustností nakonfigurovanou v databázi Azure Cosmos jako s logickým ekvivalentem, ale cenově výhodnější a elastické pro výpočetní kapacitu vašeho clusteru MongoDB nebo Cassandra.  

Všechny kontejnery vytvořené v databázi s zřízenou propustností se musí vytvořit s [klíčem oddílu](partition-data.md). V jakémkoli okamžiku je propustnost přidělená kontejneru v rámci databáze distribuována napříč všemi logickými oddíly tohoto kontejneru. Pokud máte kontejnery, které sdílejí zřízenou propustnost nakonfigurovanou v databázi, nemůžete selektivně použít tuto propustnost na konkrétní kontejner nebo na logický oddíl. 

Pokud zatížení na logickém oddílu spotřebovává více než propustnost, která je přidělena konkrétnímu logickému oddílu, jsou operace omezené na míru. Pokud dojde k omezení rychlosti, můžete buď zvýšit propustnost pro celou databázi, nebo opakovat operace. Další informace o dělení najdete v tématu [logické oddíly](partition-data.md).

Kontejnery ve sdílené databázi propustnosti sdílejí propustnost (RU/s) přidělenou této databázi. V databázi můžete mít až čtyři kontejnery s minimálně 400 RU/s. Každý nový kontejner po prvním 4 bude vyžadovat minimálně dalších 100 RU/s. Pokud máte například sdílenou databázi propustnosti s osmi kontejnery, minimální RU/s v databázi bude 800 RU/s.

> [!NOTE]
> V únoru 2020 jsme představili změnu, která vám umožní mít ve sdílené databázi propustnosti maximálně 25 kontejnerů, což lépe umožňuje sdílení propustnosti v kontejnerech. Po prvních 25 kontejnerech můžete do databáze přidat další kontejnery pouze v případě, že jsou [zřízeny s vyhrazenou propustností](#set-throughput-on-a-database-and-a-container), která je oddělená od sdílené propustnosti databáze.<br>
Pokud váš Azure Cosmos DB účet už obsahuje sdílenou databázi propustnosti s > = 25 kontejnerů, účet a všechny ostatní účty ve stejném předplatném Azure se z této změny nevztahují. Pokud máte svůj názor nebo dotazy, obraťte se prosím na [podporu produktu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) . 

Pokud vaše úlohy zahrnují odstranění a opětovné vytvoření všech kolekcí v databázi, doporučuje se odstranit prázdnou databázi a znovu vytvořit novou databázi před vytvořením kolekce. Následující obrázek ukazuje, jak může fyzický oddíl hostovat jeden nebo více logických oddílů, které patří do různých kontejnerů v rámci databáze:

![Fyzický oddíl](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Nastavení propustnosti pro databázi a kontejner

Tyto dva modely můžete kombinovat. Zajištění propustnosti databáze i kontejneru je povoleno. Následující příklad ukazuje, jak zřídit propustnost v databázi Azure Cosmos a kontejneru:

* Můžete vytvořit databázi Azure Cosmos s názvem *Z* s zřízenou propustností *"K"* ru. 
* Dále v rámci databáze vytvořte pět kontejnerů s názvem *a*, *B*, *C*, *D*a *E* . Při vytváření kontejneru B Nezapomeňte povolit **vyhrazenou propustnost pro tuto možnost kontejneru** a explicitně nakonfigurovat *"P"* ru zřízené propustnosti v tomto kontejneru. Všimněte si, že se sdílená a vyhrazená propustnost dá nakonfigurovat jenom při vytváření databáze a kontejneru. 

   ![Nastavení propustnosti na úrovni kontejneru](./media/set-throughput/coll-level-throughput.png)

* Propustnost *"K"* ru se sdílí mezi čtyřmi kontejnery *a*, *C*, *D*a *E*. Přesné množství propustnosti, které je dostupné pro *a*, *C*, *D*nebo *E* , se liší. Pro každou propustnost jednotlivých kontejnerů neexistují žádné SLA.
* U kontejneru s názvem *B* je zaručeno, že se po celou dobu vrátí propustnost ru *"P"* . Je zajištěný pomocí SLA.

> [!NOTE]
> Kontejner s zřízenou propustností nelze převést na sdílený kontejner databáze. Sdílený kontejner databáze nejde převést na vyhrazenou propustnost.

## <a name="update-throughput-on-a-database-or-a-container"></a>Aktualizace propustnosti v databázi nebo kontejneru

Po vytvoření kontejneru Azure Cosmos nebo databáze můžete aktualizovat zřízenou propustnost. Maximální zajištěná propustnost, kterou můžete nakonfigurovat v databázi nebo v kontejneru, není nijak omezena. Minimální zajištěná propustnost závisí na následujících faktorech: 

* Maximální velikost dat, kterou jste někdy uložili do kontejneru
* Maximální propustnost, kterou jste na kontejneru někdy zřídili.
* Maximální počet Cosmos kontejnerů Azure, které jste někdy vytvořili v databázi se sdílenou propustností. 

Minimální propustnost kontejneru nebo databáze můžete načíst programově pomocí sad SDK nebo zobrazit hodnotu v Azure Portal. Při použití sady .NET SDK umožňuje metoda [DocumentClient. ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) škálovat hodnotu zřízené propustnosti. Při použití sady Java SDK umožňuje metoda [RequestOptions. setOfferThroughput](sql-api-java-samples.md#offer-examples) škálovat hodnotu zřízené propustnosti. 

Při použití sady .NET SDK umožňuje metoda [DocumentClient. ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) načíst minimální propustnost kontejneru nebo databáze. 

Zřízenou propustnost kontejneru nebo databáze můžete kdykoli škálovat. Při provádění operace škálování pro zvýšení propustnosti může trvat delší dobu, než systémové úlohy zřídí požadované prostředky. Můžete kontrolovat stav operace škálování v Azure Portal nebo programově pomocí sad SDK. Při použití sady .NET SDK můžete získat stav operace škálování pomocí metody `DocumentClient.ReadOfferAsync`.

## <a name="comparison-of-models"></a>Porovnání modelů

|**Ukazatele**  |**Propustnost zajištěná v databázi**  |**Zajištěná propustnost na kontejneru**|
|---------|---------|---------|
|Minimální ru |400 (za první čtyři kontejnery vyžaduje každý další kontejner minimálně 100 ru za sekundu.) |400|
|Minimální jednotek požadavku za kontejner|100|400|
|Maximální ru|Neomezeno, v databázi.|Bez omezení, na kontejneru.|
|Ru přiřazený nebo dostupný ke konkrétnímu kontejneru|Žádné záruky. Ru přiřazený k danému kontejneru závisí na vlastnostech. Vlastnosti mohou být volbou klíčů oddílů kontejnerů, které sdílejí propustnost, distribuci zatížení a počet kontejnerů. |Všechny jednotky ru nakonfigurované v kontejneru jsou rezervovaných exkluzivně pro kontejner.|
|Maximální velikost úložiště pro kontejner|Neomezený počet.|Neomezený počet.|
|Maximální propustnost na logický oddíl kontejneru|10 tisíc ru|10 tisíc ru|
|Maximální velikost úložiště (data a index) na logický oddíl kontejneru|20 GB|20 GB|

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [logických oddílech](partition-data.md).
* Naučte se [zřídit propustnost v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Naučte se [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

