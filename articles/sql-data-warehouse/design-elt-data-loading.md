---
title: Místo ETL, návrh ELT pro Azure SQL Data Warehouse | Microsoft Docs
description: Místo ETL návrh o proces extrakce, načítání a transformace ELT () pro načtení dat nebo Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: jhubbard
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/11/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 13189bfe2e2e6db6185c798065dc3bea1fd3d537
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="designing-extract-load-and-transform-elt-for-azure-sql-data-warehouse"></a>Navrhování extrakce, načítání a transformace (ELT) pro Azure SQL Data Warehouse

Místo extrakce, transformace a načítání (ETL) návrh extrakce, načítání a transformace ELT () proces načítání dat do Azure SQL Data Warehouse. Tento článek představuje způsoby, jak navrhnout ELT proces, který přesouvá data do služby Azure data warehouse.

## <a name="what-is-elt"></a>Co je ELT?

Rozbalte, zatížení, a transformace ELT () je proces, podle kterého se data přesune ze zdrojového systému do cílového datového skladu. Tento proces probíhá v pravidelných intervalech, například hodinové nebo denní, chcete-li získat nově generované data do datového skladu. Nejvhodnější způsob získání dat ze zdroje do datového skladu je vyvinout ELT proces PolyBase k načtení dat do SQL Data Warehouse.

ELT nejprve načte a pak transformace dat, zatímco extrakce, transformace a načítání (ETL) transformuje data před načtením ho. Provádění ELT místo ETL šetří náklady na poskytování vlastních prostředků pro transformaci dat, než je načten. Při použití SQL Data Warehouse, ELT využívá systém MPP k provádění těchto transformací.

I když existuje mnoho variant pro implementaci ELT pro SQL Data Warehouse, toto jsou základní kroky:  

1. Extrahujte zdrojová data do textových souborů.
2. Zobrazovat data do úložiště objektů Blob v Azure nebo Azure Data Lake Store.
3. Příprava dat pro načtení.
2. Načítání dat do SQL Data Warehouse pomocí PolyBase pracovních tabulek.
3. Transformujte data.
4. Vložení dat do tabulky produkční.


Načítání kurzu, najdete v části [PolyBase používá k načtení dat z Azure blob storage do Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Další informace najdete v tématu [načítání vzorů blog](http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/). 

## <a name="options-for-loading-with-polybase"></a>Možnosti pro načítání pomocí funkce PolyBase

PolyBase je technologie, která přistupuje k datům mimo databázi prostřednictvím jazyka T-SQL. Je nejlepší způsob, jak načíst data do SQL Data Warehouse. Pomocí funkce PolyBase načte data paralelně ze zdroje dat přímo na výpočetní uzly. 

Načtení dat pomocí funkce PolyBase, můžete použít některý z těchto možností načítání.

- [PolyBase s T-SQL](load-data-from-azure-blob-storage-using-polybase.md) funguje dobře, pokud je vaše data v Azure Blob storage nebo Azure Data Lake Store. Poskytuje maximální kontrolu nad procesu načítání, ale také vyžaduje, abyste definice externích dat objektů. Jiné metody definujte tyto objekty na pozadí, jako je mapovat zdrojové tabulky do cílové tabulky.  K orchestraci zatížení T-SQL, můžete použít Azure Data Factory, SSIS nebo Azure functions. 
- [PolyBase se SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) funguje dobře, pokud je zdrojová data v systému SQL Server, SQL Server místně nebo v cloudu. SSIS definuje zdrojové do cílové tabulky mapování a také orchestruje zatížení. Pokud již máte balíčky SSIS, můžete upravit balíčky pro práci s nové cílové datového skladu. 
- [PolyBase s Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) je jiný nástroj orchestration.  Definuje kanálu a plány úloh. 
- [PolyBase s Azure DataBricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) přenosu dat z SQL Data Warehouse tabulku do Databricks dataframe a zapisuje data z Databricks dataframe do tabulky SQL Data Warehouse.

### <a name="polybase-external-file-formats"></a>Formáty PolyBase externích souborů

PolyBase načte data z UTF-8 a UTF-16 oddělený textových souborů. Kromě souborů text oddělený znaky načte ze souboru RC, ORC a Parquet formáty souborů Hadoop. PolyBase můžete načíst data z Gzip a Tenhle komprimovaných souborů. PolyBase aktuálně nepodporuje rozšířené ASCII, formát pevnou délkou a vnořené formáty například WinZip, JSON a XML.

### <a name="non-polybase-loading-options"></a>Načítání PolyBase bez možnosti
Pokud vaše data není kompatibilní s funkcí PolyBase, můžete použít [bcp](sql-data-warehouse-load-with-bcp.md) nebo [SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). BCP načte přímo do SQL Data Warehouse bez průchodu přes Azure Blob storage a je určena pouze pro malé zatížení. Všimněte si, zatížení výkonu z těchto možností je podstatně pomalejší než PolyBase. 


## <a name="extract-source-data"></a>Extrakce zdrojových dat

Získání dat ze zdrojového systému závisí na zdroji.  Cílem je přesunu dat do textových souborů s oddělovači. Pokud používáte systém SQL Server, můžete použít [nástroj příkazového řádku bcp](/sql/tools/bcp-utility) export dat.  

## <a name="land-data-to-azure-storage"></a>Krajině data do úložiště Azure

Pokud chcete zobrazovat data v úložišti Azure, můžete přesunout ho na [úložiště objektů Azure Blob](../storage/blobs/storage-blobs-introduction.md) nebo [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md). V některém umístění by měl být data uložena do textových souborů. Polybase můžete načíst z buď umístění.

Jedná se o nástrojích a službách, které můžete použít pro přesun dat do úložiště Azure.

- [Azure ExpressRoute](../expressroute/expressroute-introduction.md) služba zvyšuje propustnost sítě, výkonu a předvídatelnost. ExpressRoute je služba, která směruje data přes vyhrazené soukromé připojení do Azure. Připojení ExpressRoute není směrovat data prostřednictvím veřejného Internetu. Připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než Typická připojení prostřednictvím veřejného Internetu.
- [Nástroj AZCopy](../storage/common/storage-moving-data.md) přesouvá data do úložiště Azure prostřednictvím veřejného Internetu. Toto funguje, pokud jsou vaše data velikosti menší než 10 TB. Načítání v pravidelných intervalech s AZCopy provedete testovací rychlost sítě, jestli je přijatelné. 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) má bránu, kterou lze nainstalovat na místním serveru. Potom můžete vytvořit kanál pro přesun dat z místní server až Azure Storage. Pomocí objektu pro vytváření dat s datovým skladem SQL naleznete v části [načtení dat do SQL Data Warehouse](/azure/data-factory/load-azure-sql-data-warehouse).

## <a name="prepare-data"></a>Příprava dat

Možná budete muset připravit a vyčistit data ve vašem účtu úložiště před načtením do SQL Data Warehouse. Příprava dat lze provést, když vaše data jsou ve zdroji, jak exportovat data do textových souborů, nebo po data ve službě Azure Storage.  Je to nejjednodušší provádět pracovat s daty jako dříve v procesu míře.  

### <a name="define-external-tables"></a>Zadejte externí tabulky
Před načtením dat, budete muset definovat externí tabulky v datovém skladu. PolyBase používá k definování a přístupu k datům v Azure Storage externí tabulky. Externí tabulky je podobná o běžnou tabulku. Hlavní rozdíl je externí tabulka odkazuje na data, která je uložená mimo datového skladu. 

Definování externí tabulky vyžaduje určení zdroj dat, formát textové soubory a definice tabulky. Toto jsou témata syntaxe T-SQL, které budete potřebovat:
- [VYTVOŘENÍ EXTERNÍHO ZDROJE DAT](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

Příklad vytvoření externích objektů, najdete v článku [vytvoření externí tabulky](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) krok v tomto kurzu načítání.

### <a name="format-text-files"></a>Formát textové soubory

Jakmile externí objekty jsou definovány, budete muset zarovnává řádky textových souborů s externí tabulky a definice formátu souboru. Data v jednotlivých řádcích textového souboru musí zarovnané s definici tabulky.

K formátování textu soubory:

- Pokud vaše data pochází z nerelační zdroje, musíte převést je na řádky a sloupce. Jestli se data z relační nebo nerelační zdroj, musí data transformaci souladu s definicí sloupce pro tabulku, do kterého chcete načíst data. 
- Formátování dat v textovém souboru souladu s typy sloupců a data v cílové tabulce SQL Data Warehouse. Řádků, které mají být odmítnuta během zatížení způsobí, že chybné zarovnání mezi datových typů v externích textových souborů a tabulky datového skladu.
- Samostatné pole v textovém souboru a ukončení.  Nezapomeňte použít znak nebo posloupnost znaků, který nebyl nalezen v zdrojová data. Použít nebyl zadán s [vytvořit EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql).

## <a name="load-to-a-staging-table"></a>Načítání do pracovní tabulky
Pokud chcete získat data do datového skladu, funguje dobře pro první zatížení data do pracovní tabulky. Pomocí pracovní tabulky bez zasahování výrobní tabulky může zpracovávat chyby, a můžete spouštění operace vrácení zpět na výrobní tabulce. Pracovní tabulka taky dává příležitost se použije ke spuštění transformace před vložením dat do produkčního tabulek SQL Data Warehouse.

Chcete-li načíst pomocí T-SQL, spusťte [vytvořit tabulku AS vyberte funkce CTAS ()](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md) příkaz T-SQL. Tento příkaz vloží výsledky příkazu select do nové tabulky. Pokud příkaz vybere z externí tabulku, naimportuje externí data. 

V následujícím příkladu ext. Datum je externí tabulky. Všechny řádky jsou importovány do nové tabulky dbo. Datum.

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
Když jsou data v přípravné tabulce, proveďte transformace, které vaše úlohy vyžadují. Pak přesuňte data do tabulky produkční.

## <a name="insert-data-into-production-table"></a>Vložení dat do produkční tabulky

INSERT INTO... Příkaz SELECT se přesouvají data z pracovní tabulky do trvalé tabulky. 

Při navrhování o proces ETL, spusťte proces na malý zkušební vzorek. Zkuste extrahování 1 000 řádků z tabulky do souboru, přesuňte jej do Azure a opakujte načtení do pracovní tabulky. 

## <a name="partner-loading-solutions"></a>Partnerských řešení načítání
Mnoho našich partnerů má načítání řešení. Další informace, podívejte se do seznamu naše [partnery poskytujícími řešení](sql-data-warehouse-partner-business-intelligence.md). 

## <a name="next-steps"></a>Další postup
Načítání pokyny, najdete v části [pokyny pro načítání dat](guidance-for-loading-data.md).


