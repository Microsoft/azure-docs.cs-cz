---
title: Optimalizovat náklady na úložiště ve službě Azure Cosmos DB
description: Tento článek vysvětluje, jak spravovat náklady na úložiště pro data uložená ve službě Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: 6a6f8b47044d36d767721e2d3503d11518dbf5a6
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036116"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Optimalizovat náklady na úložiště ve službě Azure Cosmos DB

Azure Cosmos DB nabízí neomezené úložiště a propustnost. Na rozdíl od propustnost, které budete muset zřizovat a konfigurovat na kontejnery Azure Cosmos nebo databáze, úložiště se účtuje na základě spotřeby. Se účtují jenom u logického úložiště, které skutečně využijete a není nutné rezervovat předem jakékoli úložiště. Úložiště automaticky škáluje směrem nahoru a dolů na základě dat, které přidáváte nebo odebíráte ke kontejneru služby Azure Cosmos DB.

## <a name="storage-cost"></a>Náklady na úložiště

S jednotkou GB, účtuje úložiště. Místní úložiště se zálohují na disky SSD se používá vaše data a indexování. Celkové využití úložiště se rovná úložiště potřebné pro data a indexy použité ve všech oblastech, kde používáte službu Azure Cosmos DB. Pokud účet Azure Cosmos se globálně replikovat ve třech oblastech, zaplatíte za celkové náklady na úložiště v každé z těchto tří oblastí. Chcete-li odhadnout požadavky na úložiště, přečtěte si téma [Plánovač kapacity](https://www.documentdb.com/capacityplanner) nástroj. Náklady na úložiště ve službě Azure Cosmos DB je $0,25 GB/měsíc, najdete v článku [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) nejnovější aktualizace. Můžete nastavit výstrahy a určit úložiště využitá službou kontejner Azure Cosmos, monitorování úložiště najdete v tématu [monitorování služby Azure Cosmos DB](monitor-accounts.md)) článku.

## <a name="optimize-cost-with-item-size"></a>Optimalizujte náklady s velikost položky

Azure Cosmos DB očekává, že velikost položky bude 2 MB pro optimální výkon a náklady. Pokud potřebujete jakoukoli položku si můžete uložit větší než 2 MB dat, vezměte v úvahu realizace schéma položky. V nepravděpodobném případě, že nelze změnit návrh schématu můžete rozdělit položku na podřízené položky a připojit je logicky s common identifier(ID). Všechny funkce služby Azure Cosmos DB, fungují konzistentně ve ukotvení logického identifikátoru.

## <a name="optimize-cost-with-indexing"></a>Optimalizujte náklady s indexování

Ve výchozím nastavení data se automaticky indexován, což může zvýšit celkové úložiště spotřebované. Však můžete uplatnit zásady vlastního indexového omezit tato režie. Automatické indexování, který nebyl byla vyladěný pomocí zásad je přibližně 10 – 20 % velikosti položky. Odebráním nebo přizpůsobení index zásady, neplatíte dodatečné náklady pro zápis a nevyžadují další kapacitu. Zobrazit [indexování ve službě Azure Cosmos DB](indexing-policies.md) nakonfigurovat vlastní zásady indexování. Pokud jste už pracovali s relační databází před, může si myslíte, že "indexování všeho." znamená, že možnost zdvojnásobení úložiště nebo vyšší. Ve službě Azure Cosmos DB, v případě, střední, je však mnohem nižší. Ve službě Azure Cosmos DB nároky na úložiště indexů je obvykle nízké (10-20 %) i s automatické indexování, protože je určená pro nízkou úložiště nároky. Tím, že spravuje zásady indexování, můžete řídit kompromis index nároky na místo a výkonu dotazování umožňujícímu podrobnějšího způsobem.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Optimalizujte náklady s časem a živého kanálu změn

Jakmile již nepotřebujete data řádně odstraníte ji ze svého účtu Azure Cosmos pomocí [TTL](time-to-live.md), [kanálu změn](change-feed.md) nebo migrujete stará data do jiného úložiště dat, jako je například úložiště objektů blob v Azure nebo datový sklad Azure. Hodnota time to live nebo TTL Azure Cosmos DB poskytuje možnost automaticky odstraňovat položky z kontejneru po určité časové období. Ve výchozím nastavení můžete nastavit dobu za provozu na úrovni kontejneru a přepište hodnotu na základě za položku. Po nastavení interval TTL, ZÍSKÁ v kontejneru nebo na úrovni položek služby Azure Cosmos DB tyto položky automaticky odebrat po období od chvíle, kdy bylo naposledy změněno. Pomocí kanálu změn můžete migrovat data do obou jiného kontejneru ve službě Azure Cosmos DB nebo externím úložišti. Přijímá nula dolů čas migrace a po dokončení migrace, můžete buď odstranit nebo nakonfigurovat TTL odstranit zdrojového kontejneru Azure Cosmos.

## <a name="optimize-cost-with-rich-media-data-types"></a>Optimalizujte náklady s bohatý mediální datové typy 

Pokud chcete ukládat bohatý mediální typy, například videa, obrázky, atd., máte řadu možností ve službě Azure Cosmos DB. Jednou z možností je uložit tyto typy bohatý mediální jako položky Azure Cosmos. Je stanovený limit 2 MB za položky a toto omezení se můžete vyhnout tak řetězení datové položky do více podřízených položek. Nebo můžete uložit je do úložiště objektů Blob v Azure a použít metadata k odkazování z položek aplikace Azure Cosmos. Existuje několik výhody a nevýhody tohoto přístupu. První postup vám pomůže co nejlepšího výkonu z hlediska latence, propustnosti smlouvy o úrovni služeb, a navíc možnosti globální distribuce na klíč pro bohatý mediální datové typy, které kromě regulárních položek Azure Cosmos. Podpora je však k dispozici na vyšší cenu. Uložením média v úložišti objektů Blob v Azure může snížit celkové náklady na. Pokud latence je velmi důležité, můžete použít storage úrovně premium pro bohatý mediální soubory, které jsou odkazovány z položek Azure Cosmos. Nativně integruje s síť CDN pro obsluhu bitové kopie z hraničního serveru za nižší cenu pro obejití geografické omezení. Dolů na straně v tomto scénáři je, že budete muset řešit s využitím dvou services – Azure Cosmos DB a Azure Blob storage, což může zvýšit provozní náklady. 

## <a name="check-storage-consumed"></a>Zkontrolujte využité úložiště

Pokud chcete zkontrolovat spotřebu úložiště kontejneru Azure Cosmos, můžete spustit požadavek HEAD nebo GET ke kontejneru a zkontrolovat `x-ms-request-quota` a `x-ms-request-usage` záhlaví. Můžete také při práci s využitím .net SDK, můžete použít [DocumentSizeQuota](https://docs.microsoft.com/previous-versions/azure/dn850325(v%3Dazure.100)), a [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) vlastnosti, které chcete získat využité úložiště.

## <a name="using-sdk"></a>Pomocí sady SDK

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Další postup

Potom můžete přejít k další informace o optimalizaci nákladů ve službě Azure Cosmos DB najdete v následujících článcích:

* Další informace o [optimalizace pro vývoj a testování](optimize-dev-test.md)
* Další informace o [vysvětlení vašeho vyúčtování Azure Cosmos DB](understand-your-bill.md)
* Další informace o [optimalizace propustnosti náklady](optimize-cost-throughput.md)
* Další informace o [optimalizovat náklady na operace čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizovat náklady na dotazy](optimize-cost-queries.md)
* Další informace o [optimalizovat náklady na účty ve více oblastech Azure Cosmos](optimize-cost-regions.md)

