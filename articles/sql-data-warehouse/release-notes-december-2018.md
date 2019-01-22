---
title: Azure SQL Data Warehouse poznámky k verzi z prosince 2018 | Dokumentace Microsoftu
description: Zpráva k vydání verze pro Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: b897b50edf4d5a7eeabacc6da1505e165f2bb21a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431737"
---
# <a name="whats-new-in-azure-sql-data-warehouse-december-2018"></a>Co je nového ve službě Azure SQL Data Warehouse? Prosinec 2018
Azure SQL Data Warehouse neustále obdrží vylepšení. Tento článek popisuje nové funkce a změny, které byly zavedeny v prosince 2018.

## <a name="virtual-network-service-endpoints-generally-available"></a>Obecná dostupnost koncových bodech virtuální sítě
Tato verze obsahuje obecné dostupnosti koncových bodů služby Virtual Network (VNet) pro Azure SQL Data Warehouse ve všech oblastech Azure. Koncové body služby virtuální sítě umožňují izolovat připojení ke svému logickému serveru z dané podsítě nebo sadu podsítí v rámci vaší virtuální sítě. Provoz do služby Azure SQL Data Warehouse z vaší virtuální sítě se vždy neopustí páteřní sítě Azure. Toto přímé trasy se bude upřednostňovat přes všechny konkrétní trasy, která směrují provoz na Internet přes virtuální zařízení nebo v místním. Přístup k virtuální síti prostřednictvím koncových bodů služby se budou účtovat žádné další fakturace. Aktuální cenový model pro [Azure SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/) podobě.

V této verzi jsme taky povolili PolyBase připojení k [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (ADLS) prostřednictvím [systém souborů Azure Blob](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver) ovladače (ABFS). Azure Data Lake Storage Gen2 přináší všechny vlastnosti, které jsou vyžadovány pro kompletní životní cyklus analytická data do služby Azure Storage. Funkce dva existující služby Azure storage, Azure Blob Storage a Azure Data Lake Storage Gen1 se sloučí. Funkce z [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), například sémantiku systému souborů, souboru úroveň zabezpečení a škálování se sloučí s vrstveného úložiště s nízkými náklady a funkcím pro obnovení vysoká dostupnost/zotavení po havárii z [ Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). 

Při použití technologie Polybase můžete také importovat data do Azure SQL Data Warehouse z Azure Storage zabezpečené virtuální síti. Obdobně export dat z Azure SQL Data Warehouse do služby Azure Storage zabezpečené virtuální síti je také podporována prostřednictvím Polybase. 

Další informace o koncových bodů služby virtuální sítě ve službě Azure SQL Data Warehouse, najdete [blogový příspěvek](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/) nebo [dokumentaci](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json).

## <a name="automatic-performance-monitoring-preview"></a>Automatické Performance Monitoring (Náhled)
[Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017) je teď dostupná ve verzi Preview pro Azure SQL Data Warehouse. Query Store slouží vám pomůže při řešení potíží pomocí sledování dotazy, plány dotazů, statistické údaje a historie dotazu vám pomohou při monitorování činnosti a výkonu služby data warehouse výkon dotazů. Query Store je sada interního úložiště a zobrazení dynamické správy (DMV), které vám umožní:

- Identifikovat a vyladit hlavní dotazy využívající prostředky
- Identifikujte a Zlepšete ad hoc úlohy
- Změny v statistiky, indexy nebo velikost systému (nastavení jednotek datového skladu) a vyhodnocení výkonu dotazů a dopad na plán
- Zobrazit celý dotaz v textu pro všechny dotazy spouštěné

Query Store obsahuje tři skutečné úložišť:  
- Plánování úložiště pro trvalé informace o spuštění plánu 
- Modul runtime Statistika úložiště pro trvalé provádění statistické informace
- Statistiky čekání úložiště pro trvalé počkejte statistiky informace. 

SQL Data Warehouse automaticky spravuje tyto obchody a poskytuje neomezený počet dotazů storied za posledních sedm dnů bez dalších poplatků. Povolení Query Store je stejně jednoduché jako spuštění příkazu ALTER DATABASE T-SQL:

```sql
ALTER DATABASE [Database Name] SET QUERY_STORE = ON;
```
Další informace o Query Store ve službě Azure SQL Data Warehouse, najdete v článku, [monitorování výkonu pomocí Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017)a zobrazení dynamické správy Query Store jako [sys.query_store_query](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?view=sql-server-2017). Tady je [blogový příspěvek](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/) oznamujeme vydání.

## <a name="lower-compute-tiers-for-azure-sql-data-warehouse-gen2"></a>Nižší výpočetní úrovně pro Azure SQL Data Warehouse Gen2
Azure SQL Data Warehouse Gen2 teď podporuje nižší výpočetní úrovně. Zákazníci mohou špičkového výkonu Azure SQL Data Warehouse, flexibilitu a funkce zabezpečení od 100 cDWU ([jednotky datového skladu](https://docs.microsoft.com/azure/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu)) a škálování až na 30 000 cDWU během několika minut. Od poloviny prosince 2018, zákazníci využívat Gen2 výkon a flexibilitu s nižší výpočetní úrovně v [oblastech](https://docs.microsoft.com/azure/sql-data-warehouse/gen2-lower-tier-regions), se zbytkem oblasti k dispozici během 2019.

Přetažením vstupní bod pro další generace datových skladů Microsoft otevře dveře řízené hodnotu zákazníkům, kteří chcete vyhodnotit všechny výhody zabezpečené, vysoce výkonné datového skladu bez opakovaně uhodnout zkušební prostředí, ve kterém je pro ně nejvhodnější. Může spustit zákazníků v rozsahu od 100 cDWU dolů z aktuální 500 cDWU vstupní bod. SQL Data Warehouse Gen2 nadále podporují pozastavení a obnovení operací a jenom flexibilitu v výpočetní prostředky. Gen2 také podporuje neomezené úložiště sloupce kapacita úložiště spolu s 2,5 × více paměti na jeden dotaz, až do 128 souběžných dotazů a [adaptivní ukládání do mezipaměti](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/) funkce. Tyto funkce v průměru přenést pětkrát větší výkon ve srovnání s stejné jednotka datového skladu na Gen1 za stejnou cenu. Geograficky redundantní zálohy jsou standardní pro Gen2 integrované zaručené ochranu. Azure SQL Data Warehouse Gen2 je připravené ke škálování po.

## <a name="columnstore-background-merge"></a>Pozadí Columnstore slučovací
Ve výchozím nastavení, Azure SQL Data Warehouse (Azure SQL data Warehouse) ukládá data ve sloupcovém formátu, s názvem micro oddíly [rowgroups](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression). V některých případech z důvodu paměti omezuje na indexu dobu načítání sestavení nebo data, může rowgroups zkomprimují s méně než optimální velikost jednoho milionu řádků. Rowgroups může také dojít k fragmentaci z důvodu odstranění. Malé nebo fragmentovaný rowgroups za následek vyšší využití paměti a také provádění dotazu neefektivní. Této verze služby Azure SQL data Warehouse sloučí úlohu údržbu na pozadí columnstore malé komprimované rowgroups vytvořit větší rowgroups lepší využití paměti a zrychlení provádění dotazu.

## <a name="next-steps"></a>Další postup
Teď, když už víte o něco o SQL Data Warehouse, zjistěte, jak rychle [vytvořit SQL Data Warehouse][create a SQL Data Warehouse]. Pokud jste ještě do Azure, můžete zjistit [Glosář Azure] [ Azure glossary] užitečné při učení, nový terminologií. Můžete se také podívat na některé z těchto dalších zdrojů ke službě SQL Data Warehouse.  

* [Úspěšné zákaznické implementace]
* [Blogy]
* [Žádosti o funkce]
* [Videa]
* [Blogy zákaznického poradního týmu]
* [Fórum Stack Overflow]
* [Twitter]


[Blogy]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogy zákaznického poradního týmu]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Úspěšné zákaznické implementace]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Žádosti o funkce]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videa]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
