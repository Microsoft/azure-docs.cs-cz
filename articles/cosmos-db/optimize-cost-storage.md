---
title: Optimalizace nákladů na úložiště v Azure Cosmos DB
description: Tento článek vysvětluje, jak spravovat náklady na úložiště pro data uložená v Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 1508adda761fcba7ba70df3bb212d3eb4e32f242
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72754948"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Optimalizace nákladů na úložiště v Azure Cosmos DB

Azure Cosmos DB nabízí neomezené úložiště a propustnost. Na rozdíl od propustnost, které je nutné zřídit nebo nakonfigurovat na kontejnerech nebo databázích Azure Cosmos, úložiště se účtuje na základě spotřeby. Účtují se vám pouze logické úložiště, které spotřebováváte, a nemusíte si rezervovat žádné úložiště předem. Úložiště se automaticky škáluje nahoru a dolů na základě dat, která přidáte nebo odeberete do kontejneru Azure Cosmos.

## <a name="storage-cost"></a>Náklady na úložiště

Skladování se účtuje pomocí jednotky GBs. Místní úložiště s s ssd-couval se používá vaše data a indexování. Celkové použité úložiště se rovná úložišti vyžadovanému daty a indexy používanými ve všech oblastech, kde používáte Azure Cosmos DB. Pokud globálně replikujete účet Azure Cosmos ve třech oblastech, zaplatíte celkové náklady na úložiště v každé z těchto tří oblastí. Informace o odhadu požadavků na úložiště najdete v tématu nástroj [pro plánování kapacity.](https://www.documentdb.com/capacityplanner) Náklady na úložiště v Azure Cosmos DB jsou $0.25 GB/měsíc, najdete na [stránce Ceny](https://azure.microsoft.com/pricing/details/cosmos-db/) pro nejnovější aktualizace. Můžete nastavit výstrahy k určení úložiště používaného kontejnerem Azure Cosmos ke sledování úložiště, najdete v článku [Monitorování Azure Cosmos DB).](monitor-accounts.md)

## <a name="optimize-cost-with-item-size"></a>Optimalizace nákladů s velikostí položky

Azure Cosmos DB očekává, že velikost položky bude 2 MB nebo méně pro optimální výkon a náklady na výhody. Pokud potřebujete libovolnou položku pro uložení větší než 2 MB dat, zvažte přepracování schématu položky. Ve výjimečných případech, kdy nelze přepracovat schéma, můžete položku rozdělit na dílčí položky a logicky je propojit se společným identifikátorem(ID). Všechny funkce Azure Cosmos DB fungují konzistentně ukotvením tohoto logického identifikátoru.

## <a name="optimize-cost-with-indexing"></a>Optimalizace nákladů pomocí indexování

Ve výchozím nastavení jsou data automaticky indexována, což může zvýšit celkové spotřebované úložiště. Můžete však použít vlastní zásady indexu ke snížení této režie. Automatické indexování, které nebylo vyladěno prostřednictvím zásad, je přibližně 10–20 % velikosti položky. Odebráním nebo přizpůsobením zásad indexu neplatíte za zápisy dodatečné náklady a nepotřebujete další kapacitu propustnosti. Viz [Indexování v Azure Cosmos DB](indexing-policies.md) pro konfiguraci vlastních zásad indexování. Pokud jste pracovali s relační databáze dříve, můžete si myslet, že "index všechno" znamená zdvojnásobení úložiště nebo vyšší. Však v Azure Cosmos DB, v případě mediánu je mnohem nižší. V Azure Cosmos DB je režie úložiště indexu obvykle nízká (10–20 %) i při automatickém indexování, protože je navržen pro nízkou úložnou půdu. Správou zásad indexování můžete řídit kompromis stopy indexu a výkon dotazu v jemnějším způsobem.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Optimalizace nákladů s časem na život a změnu informačního kanálu

Jakmile už nepotřebujete data, můžete je řádně odstranit z účtu Azure Cosmos pomocí [času k životu](time-to-live.md), změnit [zdroj](change-feed.md) nebo můžete migrovat stará data do jiného úložiště dat, jako je úložiště objektů blob Azure nebo datový sklad Azure. S časem žít nebo TTL, Azure Cosmos DB poskytuje možnost odstranit položky automaticky z kontejneru po určitém časovém období. Ve výchozím nastavení můžete nastavit čas žít na úrovni kontejneru a přepsat hodnotu na základě pro položce. Po nastavení TTL na kontejneru nebo na úrovni položky Azure Cosmos DB automaticky odebere tyto položky po časovém období od doby, kdy byly naposledy změněny. Pomocí kanálu změn můžete migrovat data do jiného kontejneru v Azure Cosmos DB nebo do externího úložiště dat. Migrace trvá nula prostoje a po dokončení migrace, můžete odstranit nebo nakonfigurovat čas žít odstranit zdroj ový kontejner Azure Cosmos.

## <a name="optimize-cost-with-rich-media-data-types"></a>Optimalizace nákladů pomocí datových typů rich media 

Pokud chcete ukládat typy rich media, například videa, obrázky atd., máte řadu možností v Azure Cosmos DB. Jednou z možností je ukládat tyto typy rich media jako položky Azure Cosmos. Existuje limit 2 MB na položku a tomuto limitu se můžete vyhnout zřetězením datové položky do více podpoložek. Nebo je můžete uložit do úložiště objektů Blob Azure a použít metadata k jejich odkazu z položek Azure Cosmos. Existuje celá řada klady a zápory s tímto přístupem. První přístup vám kromě běžných položek Azure Cosmos získá nejlepší výkon z hlediska latence, nabídek sla a globálních distribučních funkcí na klíč pro multimediální datové typy. Podpora je však k dispozici za vyšší cenu. Uložením médií v úložišti objektů Blob Azure můžete snížit celkové náklady. Pokud latence je důležité, můžete použít úložiště premium pro soubory rich media, které jsou odkazovány z položek Azure Cosmos. To integruje nativně s CDN sloužit obrázky z okraje serveru za nižší cenu obejít geo-omezení. Nevýhodou tohoto scénáře je, že je třeba řešit dvě služby – Azure Cosmos DB a úložiště objektů blob Azure, což může zvýšit provozní náklady. 

## <a name="check-storage-consumed"></a>Kontrola spotřebovaného úložiště

Chcete-li zkontrolovat spotřebu úložiště kontejneru Azure Cosmos, můžete spustit požadavek HEAD `x-ms-request-quota` nebo `x-ms-request-usage` GET v kontejneru a zkontrolovat a záhlaví. Alternativně při práci s .NET SDK, můžete použít [DocumentSizeQuota](https://docs.microsoft.com/previous-versions/azure/dn850325(v%3Dazure.100))a [DocumentSizeUsage](https://msdn.microsoft.com/library/azure/dn850324.aspx) vlastnosti získat úložiště spotřebované.

## <a name="using-sdk"></a>S využitím sady SDK

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci nákladů v Azure Cosmos DB najdete v následujících článcích:

* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [vysvětlení vaší faktury z DB Služby Azure Cosmos](understand-your-bill.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na dotazy](optimize-cost-queries.md)
* Další informace o [optimalizaci nákladů na účty Azure Cosmos s více oblastmi](optimize-cost-regions.md)

