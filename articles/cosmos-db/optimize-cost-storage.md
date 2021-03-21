---
title: Optimalizace nákladů na úložiště ve službě Azure Cosmos DB
description: Tento článek vysvětluje, jak spravovat náklady na úložiště pro data uložená v Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 92bd3ff925080def4b2f074d07e662dfdbdbee01
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93080846"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Optimalizace nákladů na úložiště ve službě Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB nabízí neomezené úložiště a propustnost. Na rozdíl od propustnosti, kterou musíte zřídit a konfigurovat v kontejnerech nebo databázích Azure Cosmos, se úložiště účtuje na základě spotřeby. Účtují se vám jenom logické úložiště, které spotřebováváte, a nemusíte nic rezervovat. Úložiště se automaticky škáluje nahoru a dolů na základě dat, která přidáte nebo odeberete do kontejneru Azure Cosmos.

## <a name="storage-cost"></a>Náklady na úložiště

Služba Storage se účtuje s jednotkou GB. Místní úložiště zálohované na jednotku SSD používá vaše data a indexování. Celkové využité úložiště je stejné jako úložiště vyžadované daty a indexy používané ve všech oblastech, ve kterých používáte Azure Cosmos DB. Pokud účet Azure Cosmos globálně replikujte ve třech oblastech, platíte za celkové náklady na úložiště v každé z těchto tří oblastí. Informace o odhadu požadavku na úložiště najdete v tématu Nástroj [Capacity Planner](https://www.documentdb.com/capacityplanner) . Náklady na úložiště v Azure Cosmos DB jsou $0,25 GB/měsíc, najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) nejnovější aktualizace. Můžete nastavit výstrahy pro určení úložiště používaného vaším kontejnerem Azure Cosmos, abyste mohli monitorovat úložiště, viz článek [monitorování Azure Cosmos DB](./monitor-cosmos-db.md)).

## <a name="optimize-cost-with-item-size"></a>Optimalizace nákladů pomocí velikosti položky

Azure Cosmos DB očekává, že velikost položky bude 2 MB nebo méně pro optimální výkon a náklady. Pokud potřebujete, aby položka ukládala více než 2 MB dat, zvažte přenávrh schématu položky. Ve výjimečné události, kterou nemůžete změnit návrh schématu, můžete rozdělit položku na podpoložky a logicky propojit se společným identifikátorem (ID). Všechny funkce Azure Cosmos DB fungují konzistentně pomocí ukotvení k tomuto logickému identifikátoru.

## <a name="optimize-cost-with-indexing"></a>Optimalizace nákladů s indexováním

Ve výchozím nastavení jsou data automaticky indexována, což může zvýšit celkové spotřebované úložiště. Můžete ale použít vlastní zásady indexů a snížit tak tuto režii. Automatické indexování, které se nevytvořily prostřednictvím zásad, je přibližně 10-20% velikosti položky. Odebráním nebo přizpůsobením zásad indexování nebudete platit za nadbytečné náklady za zápisy a nebudete potřebovat další kapacitu propustnosti. Pokud chcete nakonfigurovat vlastní zásady indexování, přečtěte si téma [indexování v Azure Cosmos DB](index-policy.md) . Pokud jste předtím pracovali s relačními databázemi, můžete si všimnout, že "index všeho" znamená zdvojnásobení úložiště nebo vyšší. V Azure Cosmos DB se ale ve střední části velkých písmen mnohem snižuje. V Azure Cosmos DB je režie úložiště indexu obvykle nízká (10-20%) i s automatickým indexováním, protože je navržena pro menší nároky na úložiště. Díky správě zásad indexování můžete řídit kompromisy nad nároky na index a výkon dotazů v jemně odstupňovaném způsobem.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Optimalizace nákladů s využitím času na živé a změna kanálu

Jakmile už data nepotřebujete, můžete je ze svého účtu Azure Cosmos bez problémů odstranit, a to pomocí [Time to Live](time-to-live.md), [změnit kanál](change-feed.md) nebo můžete migrovat stará data do jiného úložiště dat, jako je Azure Blob Storage nebo Azure Data Warehouse. S hodnotou Time to Live nebo TTL Azure Cosmos DB poskytuje možnost automaticky odstraňovat položky z kontejneru po určitém časovém období. Ve výchozím nastavení můžete nastavit možnost čas na živý na úrovni kontejneru a přepsat hodnotu na základě jednotlivých položek. Jakmile nastavíte hodnotu TTL v kontejneru nebo na úrovni položky, Azure Cosmos DB tyto položky po uplynutí časového období od poslední změny automaticky odebrat. Pomocí kanálu změn můžete migrovat data do jiného kontejneru v Azure Cosmos DB nebo do externího úložiště dat. Migrace trvá nepotřebnou dobu od času a po dokončení migrace můžete odstranit nebo nakonfigurovat dobu v reálném čase, kdy se má zdrojový kontejner Azure Cosmos odstranit.

## <a name="optimize-cost-with-rich-media-data-types"></a>Optimalizace nákladů pomocí bohatých mediálních datových typů 

Pokud chcete ukládat bohatě formátované typy médií, například videa, obrázky atd., máte k dispozici řadu možností v Azure Cosmos DB. Jednou z možností je ukládat tyto typy bohatých médií do Cosmos položek Azure. U každé položky je povolený limit 2 MB a toto omezení se může vyhnout zřetězením datové položky do více podpoložek. Nebo je můžete uložit ve službě Azure Blob Storage a použít metadata k jejich odkazování z položek Azure Cosmos. Tento přístup má několik specialistů a nevýhody. První postup vám získá nejlepší výkon z hlediska latence, propustnosti SLA a klíč globální distribuce pro datové typy s bohatými mediálními daty kromě běžných položek Azure Cosmos. Podpora je však dostupná za vyšší cenu. Uložením médií do úložiště objektů BLOB v Azure můžete snížit celkové náklady. Pokud je latence kritická, můžete použít službu Premium Storage pro mediální soubory, na které se odkazuje z položek Azure Cosmos. Integruje se nativně s CDN pro poskytování imagí z hraničního serveru za sníženou cenu za účelem obcházení geografického omezení. V tomto scénáři je potřeba se zabývat dvěma službami – Azure Cosmos DB a úložištěm objektů BLOB v Azure, což může zvýšit provozní náklady. 

## <a name="check-storage-consumed"></a>Ověřit spotřebované úložiště

Pokud chcete zkontrolovat spotřebu úložiště kontejneru Azure Cosmos, můžete na kontejneru spustit kopii nebo získat požadavek a zkontrolovat `x-ms-request-quota` `x-ms-request-usage` hlavičky a. Případně při práci s .NET SDK můžete použít vlastnosti [DocumentSizeQuota](/previous-versions/azure/dn850325(v%3Dazure.100))a [DocumentSizeUsage](/previous-versions/azure/dn850324(v=azure.100)) a získat tak využité úložiště.

## <a name="using-sdk"></a>S využitím sady SDK

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci nákladů v Azure Cosmos DB najdete v následujících článcích:

* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [Azure Cosmos DB vyúčtování](understand-your-bill.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na dotazy](./optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na účty Azure Cosmos ve více oblastech](optimize-cost-regions.md)
