---
title: Místo ETL, návrh ELT pro službu Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Místo ETL návrhu procesu extrakce, načítání a transformace (ELT) pro načítání dat nebo Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 8407fcdabecbb4f6ed9c0028a4a74916913591ba
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54199183"
---
# <a name="designing-a-polybase-data-loading-strategy-for-azure-sql-data-warehouse"></a>Navrhování dat pomocí PolyBase načítání strategie pro Azure SQL Data Warehouse

Tradiční SMP datových skladů pomocí procesu extrakce, transformace a načítání (ETL) pro načítání dat. Azure SQL Data Warehouse je architektura paralelního zpracování (MPP), která přijímá využívat škálovatelnost a flexibilitu výpočetních a úložných prostředků. Využívají procesu extrakce, načítání a transformace (ELT) můžete využít výhod MPP a eliminovat prostředky potřebné k transformaci dat před načtením. I když SQL Data Warehouse podporuje mnoho metod načítání, včetně možnosti technologie Polybase, jako je BCP a rozhraní API BulkCopy SQL, je nejvíce škálovatelným a nejrychlejší způsob, jak načíst data pomocí PolyBase.  PolyBase je technologie, která přistupuje k externí data uložená v Azure Blob storage nebo Azure Data Lake Store pomocí jazyka T-SQL.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>Co je ELT?

Extrakce, načítání, a transformace (ELT) je proces, podle kterého je extrahován ze zdrojového systému, načíst do datového skladu a pak transformovat data. 

Toto jsou základní kroky pro implementaci PolyBase ELT pro službu SQL Data Warehouse:

1. Extrakce zdrojových dat do textových souborů.
2. Dostat data do Azure Blob storage nebo Azure Data Lake Store.
3. Příprava dat pro načtení.
4. Načtení dat do SQL Data Warehouse pracovních tabulek při použití technologie PolyBase. 
5. Transformujte data.
6. Vložení dat do produkčních tabulek.


Kurz načítání najdete v tématu [použití PolyBase k načítání dat z Azure blob storage do služby Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Další informace najdete v tématu [načítání vzorů blogu](https://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/). 


## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrakce zdrojových dat do textových souborů

Získání dat ze zdrojového systému závisí na umístění úložiště.  Cílem je k přesunu dat do PolyBase nepodporuje textových souborů s oddělovači. 

### <a name="polybase-external-file-formats"></a>PolyBase formáty externích souborů.

PolyBase načte data z kódování UTF-8 a UTF-16 textových souborů s oddělovači. Kromě textových souborů s oddělovači načte z RC souboru ORC a Parquet formáty souborů Hadoop. Technologie PolyBase také může načítat data z Gzip a Snappy komprimované soubory. PolyBase aktuálně nepodporuje rozšířené ASCII, formátu s pevnou šířkou a vnořené formáty, jako je například WinZip, JSON a XML. Pokud exportujete z SQL serveru, můžete použít [nástroj příkazového řádku bcp](/sql/tools/bcp-utility) exportovat data do textových souborů s oddělovači.


## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Dostat data do Azure Blob storage nebo Azure Data Lake Store

Dostat data do úložiště Azure, můžete přesunout na [úložiště objektů Blob v Azure](../storage/blobs/storage-blobs-introduction.md) nebo [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md). V některém umístění by se data ukládají v textových souborech. Technologie PolyBase můžete načíst z obou umístění.

Nástroje a služby, které můžete použít pro přesun dat do služby Azure Storage:

- [Azure ExpressRoute](../expressroute/expressroute-introduction.md) služba zvyšuje propustnost sítě, výkonu a předvídatelnosti. ExpressRoute je služba, která směruje vašich dat pomocí vyhrazeného soukromého připojení k Azure. Připojení ExpressRoute není směrování dat prostřednictvím veřejného Internetu. Připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a lepší zabezpečení než Typická připojení přes veřejný internet.
- [Nástroj AZCopy](../storage/common/storage-moving-data.md) přesouvá data do služby Azure Storage prostřednictvím veřejného Internetu. Tento postup funguje, pokud jsou vaše data velikosti menší než 10 TB. K provedení zatížení v pravidelných intervalech pomocí nástroje AZCopy, test rychlost sítě, jestli je přijatelné. 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) má bránu, kterou lze nainstalovat na místním serveru. Potom můžete vytvořit kanál pro přesun dat z místního serveru do služby Azure Storage. Pomocí služby Data Factory s využitím SQL Data Warehouse, najdete v článku [načtení dat do SQL Data Warehouse](/azure/data-factory/load-azure-sql-data-warehouse).


## <a name="3-prepare-the-data-for-loading"></a>3. Příprava dat pro načtení

Můžete potřebovat k přípravě a vyčistit data ve vašem účtu úložiště před jejich načtením do SQL Data Warehouse. Příprava dat lze provést, zatímco vaše data jsou ve zdroji, jak exportovat data do textových souborů, nebo po data ve službě Azure Storage.  Je pro práci s daty v procesu co nejjednodušší.  

### <a name="define-external-tables"></a>Definování externích tabulek

Před načtením dat, budete muset definovat externích tabulek v datovém skladu. PolyBase používá k definování a přístup k datům ve službě Azure Storage externí tabulky. Externí tabulky je podobné zobrazení databáze. Externí tabulka obsahuje schéma tabulky a odkazuje na data, která je uložená mimo datového skladu. 

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


## <a name="4-load-the-data-into-sql-data-warehouse-staging-tables-using-polybase"></a>4. Načtení dat do SQL Data Warehouse pracovních tabulek pomocí PolyBase

Je osvědčeným postupem, jak načíst data do pracovní tabulky. Pracovní tabulky umožňují zpracování chyb, aniž by zasahovala do produkčních tabulek. Pracovní tabulky také nabízí možnost používat SQL Data Warehouse MPP pro transformace dat před vložením dat do produkčních tabulek.

### <a name="options-for-loading-with-polybase"></a>Možnosti pro načtení pomocí funkce PolyBase

Pro načtení dat pomocí PolyBase, můžete použít některé z těchto možností načítání:

- [PolyBase pomocí jazyka T-SQL](load-data-from-azure-blob-storage-using-polybase.md) funguje dobře, pokud je vaše data v Azure Blob storage nebo Azure Data Lake Store. Poskytuje většinu kontrolu nad procesu načítání, ale také vyžaduje, abyste k definování externích datových objektů. Jiné metody definovat tyto objekty na pozadí jako mapování zdrojových tabulek do cílových tabulek.  K orchestraci zatížení T-SQL, můžete použít Azure Data Factory, služby SSIS nebo Azure functions. 
- [PolyBase pomocí služby SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) funguje dobře, pokud je zdroj dat v systému SQL Server, SQL Server na místní nebo v cloudu. SSIS definuje zdroj k určení mapování tabulky a také orchestruje zatížení. Pokud už máte balíčky služby SSIS, můžete upravit balíčky pro práci s nové cílové datového skladu. 
- [PolyBase s Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) je jiný nástroj pro orchestraci.  Definuje kanál a plány úloh. 
- [PolyBase s Azure DataBricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) přenosech dat z SQL Data Warehouse tabulku do datového rámce Databricks a/nebo zapisuje data z datového rámce Databricks do tabulky SQL Data Warehouse.

### <a name="non-polybase-loading-options"></a>Načítání PolyBase bez možnosti

Pokud vaše data není kompatibilní s technologií PolyBase, můžete použít [bcp](/sql/tools/bcp-utility) nebo [SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). BCP načte přímo do služby SQL Data Warehouse bez nutnosti kontaktovat úložiště objektů Blob v Azure a je určena pouze pro malé zatížení. Mějte na paměti, je výrazně pomalejší než PolyBase zatížení výkonu z těchto možností. 


## <a name="5-transform-the-data"></a>5. Transformace dat

I když jsou data v pracovní tabulce, provedení transformace, které vaše úloha vyžaduje. Potom přesunu dat do provozní tabulky.


## <a name="6-insert-the-data-into-production-tables"></a>6. Vložení dat do produkční tabulky

INSERT INTO... Příkaz SELECT se přesouvají data z pracovní tabulky na trvalou tabulku. 

Při návrhu procesu ETL, spusťte proces na malé vzorku. Extrahování 1000 řádků z tabulky do souboru, přesuňte ho do Azure a potom to zkusit načítání do pracovní tabulky. 


## <a name="partner-loading-solutions"></a>Partnerská řešení načítání

Máte spoustu našich partnerů, načítání řešení. Další informace najdete v tématu seznam našich [partneři řešení](sql-data-warehouse-partner-business-intelligence.md). 


## <a name="next-steps"></a>Další postup

Doprovodné materiály k načítání, najdete v části [pokyny k načítání dat](guidance-for-loading-data.md).


