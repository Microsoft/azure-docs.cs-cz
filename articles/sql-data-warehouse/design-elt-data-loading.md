---
title: Místo ETL, návrh ELT pro službu Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Místo ETL návrhu procesu extrakce, načítání a transformace (ELT) pro načítání dat nebo Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 33e4a405547fcdd797ddfdf6aba6c6c1c126b742
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2018
ms.locfileid: "42055640"
---
# <a name="designing-extract-load-and-transform-elt-for-azure-sql-data-warehouse"></a>Navrhování extrakce, načítání a transformace (ELT) pro Azure SQL Data Warehouse

Místo extrakce, transformace a načítání (ETL) návrhu procesu extrakce, načítání a transformace (ELT) pro načítání dat do služby Azure SQL Data Warehouse. Tento článek představuje způsoby, jak navrhnout ELT proces, který přesouvá data do služby Azure data warehouse.

## <a name="what-is-elt"></a>Co je ELT?

Extrakce, načítání, a transformace (ELT) je proces, podle kterého se data přesune ze zdrojového systému do cílového datového skladu. Tento proces probíhá v pravidelných intervalech, například každou hodinu nebo každý den, chcete-li získat nově vygenerovala data do datového skladu. Ideální způsob, jak získat data ze zdroje do služby data warehouse je na vývoj procesu ELT využívající funkci PolyBase k načtení dat do SQL Data Warehouse.

ELT načten jako první a potom transformuje data, zatímco extrakce, transformace a načítání (ETL) transformuje data před načtením. Provádění ELT místo ETL šetří náklady na poskytování vlastních prostředků pro transformaci dat, než je načten. Při použití SQL Data Warehouse využívá ELT MPP systému k provádění těchto transformací.

I když existuje mnoho variant pro provádění ELT pro službu SQL Data Warehouse, jedná se o základní kroky:  

1. Extrakce zdrojových dat do textových souborů.
2. Dostat data do Azure Blob storage nebo Azure Data Lake Store.
3. Příprava dat pro načtení.
2. Načtení dat do SQL Data Warehouse pomocí PolyBase pracovních tabulek.
3. Transformujte data.
4. Vložení dat do produkčních tabulek.


Kurz načítání najdete v tématu [použití PolyBase k načítání dat z Azure blob storage do služby Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Další informace najdete v tématu [načítání vzorů blogu](http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/). 

## <a name="options-for-loading-with-polybase"></a>Možnosti pro načtení pomocí funkce PolyBase

PolyBase je technologie, která přistupuje k datům mimo databázi pomocí jazyka T-SQL. Je nejlepší způsob, jak načíst data do SQL Data Warehouse. Díky technologii PolyBase načítá data paralelně ze zdroje dat přímo do výpočetních uzlů. 

Pro načtení dat pomocí PolyBase, můžete použít některé z těchto možností načítání.

- [PolyBase pomocí jazyka T-SQL](load-data-from-azure-blob-storage-using-polybase.md) funguje dobře, pokud je vaše data v Azure Blob storage nebo Azure Data Lake Store. Poskytuje většinu kontrolu nad procesu načítání, ale také vyžaduje, abyste k definování externích datových objektů. Jiné metody definovat tyto objekty na pozadí jako mapování zdrojových tabulek do cílových tabulek.  K orchestraci zatížení T-SQL, můžete použít Azure Data Factory, služby SSIS nebo Azure functions. 
- [PolyBase pomocí služby SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) funguje dobře, pokud je zdroj dat v systému SQL Server, SQL Server na místní nebo v cloudu. SSIS definuje zdroj k určení mapování tabulky a také orchestruje zatížení. Pokud už máte balíčky služby SSIS, můžete upravit balíčky pro práci s nové cílové datového skladu. 
- [PolyBase s Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) je jiný nástroj pro orchestraci.  Definuje kanál a plány úloh. 
- [PolyBase s Azure DataBricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) přenosech dat z SQL Data Warehouse tabulku do datového rámce Databricks a/nebo zapisuje data z datového rámce Databricks do tabulky SQL Data Warehouse.

### <a name="polybase-external-file-formats"></a>PolyBase formáty externích souborů.

PolyBase načte data z kódování UTF-8 a UTF-16 textových souborů s oddělovači. Kromě textových souborů s oddělovači načte z RC souboru ORC a Parquet formáty souborů Hadoop. Technologie PolyBase můžete načítat data z Gzip a Snappy komprimované soubory. PolyBase aktuálně nepodporuje rozšířené ASCII, formátu s pevnou šířkou a vnořené formáty, jako je například WinZip, JSON a XML.

### <a name="non-polybase-loading-options"></a>Načítání PolyBase bez možnosti
Pokud vaše data není kompatibilní s technologií PolyBase, můžete použít [bcp](/sql/tools/bcp-utility) nebo [SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). BCP načte přímo do služby SQL Data Warehouse bez nutnosti kontaktovat úložiště objektů Blob v Azure a je určena pouze pro malé zatížení. Mějte na paměti, je výrazně pomalejší než PolyBase zatížení výkonu z těchto možností. 


## <a name="extract-source-data"></a>Extrakce zdrojových dat

Získání dat ze zdrojového systému závisí na zdroji.  Cílem je k přesunu dat do textových souborů s oddělovači. Pokud používáte SQL Server, můžete použít [nástroj příkazového řádku bcp](/sql/tools/bcp-utility) exportovat data.  

## <a name="land-data-to-azure-storage"></a>Příjem dat do služby Azure storage

Dostat data do úložiště Azure, můžete přesunout na [úložiště objektů Blob v Azure](../storage/blobs/storage-blobs-introduction.md) nebo [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md). V některém umístění ukládat data do textových souborů. Technologie Polybase můžete načíst z obou umístění.

Toto jsou nástroje a služby, které můžete použít pro přesun dat do služby Azure Storage.

- [Azure ExpressRoute](../expressroute/expressroute-introduction.md) služba zvyšuje propustnost sítě, výkonu a předvídatelnosti. ExpressRoute je služba, která směruje vašich dat pomocí vyhrazeného soukromého připojení k Azure. Připojení ExpressRoute není směrování dat prostřednictvím veřejného Internetu. Připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a lepší zabezpečení než Typická připojení přes veřejný internet.
- [Nástroj AZCopy](../storage/common/storage-moving-data.md) přesouvá data do služby Azure Storage prostřednictvím veřejného Internetu. Tento postup funguje, pokud jsou vaše data velikosti menší než 10 TB. K provedení zatížení v pravidelných intervalech pomocí nástroje AZCopy, test rychlost sítě, jestli je přijatelné. 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) má bránu, kterou lze nainstalovat na místním serveru. Potom můžete vytvořit kanál pro přesun dat z místního serveru do služby Azure Storage. Pomocí služby Data Factory s využitím SQL Data Warehouse, najdete v článku [načtení dat do SQL Data Warehouse](/azure/data-factory/load-azure-sql-data-warehouse).

## <a name="prepare-data"></a>Příprava dat

Můžete potřebovat k přípravě a vyčistit data ve vašem účtu úložiště před jejich načtením do SQL Data Warehouse. Příprava dat lze provést, zatímco vaše data jsou ve zdroji, jak exportovat data do textových souborů, nebo po data ve službě Azure Storage.  Je pro práci s daty v procesu co nejjednodušší.  

### <a name="define-external-tables"></a>Definování externích tabulek
Před načtením dat, budete muset definovat externích tabulek v datovém skladu. PolyBase používá k definování a přístup k datům ve službě Azure Storage externí tabulky. Externí tabulky je podobný o běžnou tabulku. Hlavní rozdíl je externí tabulka odkazuje na data, která je uložená mimo datového skladu. 

Definování externích tabulek vyžaduje určení zdroje dat, formát textové soubory a definici tabulky. Následují témata syntaxi T-SQL, které budete muset:
- [VYTVOŘENÍ EXTERNÍHO ZDROJE DAT](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

Příklad vytvoření externí objekty, najdete v článku [vytvoření externích tabulek](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) krok v kurzu načítání.

### <a name="format-text-files"></a>Soubory ve formátu textu

Jakmile externí objekty jsou definovány, budete muset zarovnává řádky textové soubory s externí tabulky a definici formátu souboru. Data v jednotlivých řádcích textového souboru musí být v souladu s definicí tabulky.

K formátování textu soubory:

- V případě vaše data pochází z jiné relačního zdroje, potřebujete transformovat na řádky a sloupce. Jestli se data ze zdroje relačních nebo nerelačních, musí být data transformována souladu s definicemi sloupců pro tabulku, do které chcete načíst data. 
- Formátování dat v textovém souboru, aby odpovídaly sloupců a datové typy v cílové tabulce SQL Data Warehouse. Chybné mezi datovými typy v externí textových souborů a tabulka data warehouse způsobí, že řádky zamítnutí v průběhu načítání.
- Oddělovače v textovém souboru se zakončením.  Nezapomeňte použít znak nebo posloupnost znaků, který se nenachází ve zdrojových datech. Použít ukončovací znak zadán s [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql).

## <a name="load-to-a-staging-table"></a>Načítání do pracovní tabulky
Jak vložit data do datového skladu, je dobře funguje při prvním načtení dat do pracovní tabulky. S použitím pracovní tabulky, aniž by zasahovala do produkčních tabulek můžete zpracovávat chyby a vám vyhnout se spouštění operací vrácení zpět na provozní tabulky. Pracovní tabulky také nabízí možnost, která se použije ke spuštění transformace před vložením dat do produkčních tabulek SQL Data Warehouse.

Pokud chcete načíst data pomocí jazyka T-SQL, spusťte [vytvořit TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) příkazu T-SQL. Tento příkaz vloží do nové tabulky výsledky příkazu select. Když příkaz vybere z externí tabulky, importuje externí data. 

V následujícím příkladu externí Datum je externí tabulky. Všechny řádky jsou importovány do nové tabulky nazvané dbo. Datum.

```sql
CREATE TABLE [dbo].[Date]
WITH
( 
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[Date]
;
```

## <a name="transform-the-data"></a>Transformace dat
I když jsou data v pracovní tabulce, provedení transformace, které vaše úloha vyžaduje. Potom přesunu dat do provozní tabulky.

## <a name="insert-data-into-production-table"></a>Vložení dat do produkční tabulky

INSERT INTO... Příkaz SELECT se přesouvají data z pracovní tabulky na trvalou tabulku. 

Při návrhu procesu ETL, spusťte proces na malé vzorku. Extrahování 1000 řádků z tabulky do souboru, přesuňte ho do Azure a potom to zkusit načítání do pracovní tabulky. 

## <a name="partner-loading-solutions"></a>Partnerská řešení načítání
Máte spoustu našich partnerů, načítání řešení. Další informace najdete v tématu seznam našich [partneři řešení](sql-data-warehouse-partner-business-intelligence.md). 

## <a name="next-steps"></a>Další postup
Doprovodné materiály k načítání, najdete v části [pokyny k načítání dat](guidance-for-loading-data.md).


