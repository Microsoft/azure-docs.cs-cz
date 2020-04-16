---
title: Namísto ETL, design ELT pro Synapse SQL pool | Dokumenty společnosti Microsoft
description: Namísto ETL navrhněte proces extrakce, načtení a transformace (ELT) pro načítání dat nebo fondu SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 596f4bcf2e3f829430fdc90eb1806a44a84b2bc5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429587"
---
# <a name="designing-a-polybase-data-loading-strategy-for-azure-synapse-sql-pool"></a>Návrh strategie načítání dat PolyBase pro fond SQL Azure Synapse

Tradiční datové sklady SMP používají proces extrakce, transformace a načtení (ETL) pro načítání dat. Fond Azure SQL je architektura masivně paralelního zpracování (MPP), která využívá výhod škálovatelnosti a flexibility výpočetních prostředků a prostředků úložiště. S využitím procesu extrakce, zatížení a transformace (ELT) můžete využít MPP a eliminovat prostředky potřebné k transformaci dat před načtením.

Zatímco fond SQL podporuje mnoho metod načítání, včetně možností mimo Polybase, jako je například Rozhraní BCP a SQL BulkCopy API, nejrychlejší a nejškálovatelnější způsob, jak načíst datum je prostřednictvím PolyBase.  PolyBase je technologie, která přistupuje k externím datům uloženým v úložišti objektů Blob Azure nebo v Azure Data Lake Store prostřednictvím jazyka T-SQL.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>Co je ELT?

Extrakce, načtení a transformace (ELT) je proces, při kterém jsou data extrahována ze zdrojového systému, načtena do datového skladu a poté transformována.

Základní kroky pro implementaci PolyBase ELT pro fond SQL jsou:

1. Extrakce zdrojových dat do textových souborů
2. Přiěte data do úložiště objektů blob Azure nebo Azure Data Lake Store.
3. Připravte data pro načtení.
4. Načtěte data do pracovních tabulek fondu SQL pomocí polybase.
5. Transformujte data.
6. Vložení dat do produkčních tabulek

Kurz načítání najdete v [článku Použití PolyBase k načtení dat z úložiště objektů blob Azure do Azure SQL Data Warehouse](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Další informace naleznete v [tématu Načítání vzorů blogu](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrahujte zdrojová data do textových souborů

Získání dat ze zdrojového systému závisí na umístění úložiště.  Cílem je přesunout data do textových souborů podporovaných oddělovači PolyBase.

### <a name="polybase-external-file-formats"></a>Formáty externích souborů PolyBase

PolyBase načte data z UTF-8 a UTF-16 kódované oddělené textové soubory. Kromě oddělených textových souborů se načte z formátů souborů Hadoop RC File, ORC a Parquet. PolyBase může také načíst data z komprimovaných souborů Gzip a Snappy. PolyBase aktuálně nepodporuje rozšířené ASCII, formát s pevnou šířkou a vnořené formáty, jako je WinZip, JSON a XML.

Pokud exportujete ze serveru SQL Server, můžete data exportovat do oddělených textových souborů pomocí [nástroje příkazového řádku bcp.](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Mapování datového typu Parkety na SQL DW je následující:

| **Datový typ parket** |                      **Datový typ SQL**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        Boolean        |                             bitové                              |
|        double         |                            float                             |
|         float         |                             reálná                             |
|        double         |                            Peníze                             |
|        double         |                          Smallmoney                          |
|        řetězec         |                            Nchar                             |
|        řetězec         |                           nvarchar                           |
|        řetězec         |                             char                             |
|        řetězec         |                           varchar                            |
|        binární         |                            binární                            |
|        binární         |                          Varbinary                           |
|       časové razítko       |                             date                             |
|       časové razítko       |                        Smalldatetime                         |
|       časové razítko       |                          datetime2                           |
|       časové razítko       |                           datetime                           |
|       časové razítko       |                             time                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Přiložte data do úložiště objektů blob Azure nebo azure data lake store

Pokud chcete data přistát ve službě Azure, můžete je přesunout do [úložiště objektů blob Azure](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) nebo Azure Data Lake [Store](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). V obou umístěních by měla být data uložena v textových souborech. PolyBase lze načíst z obou umístění.

Nástroje a služby, které můžete použít k přesunu dat do Azure Storage:

- [Služba Azure ExpressRoute](../../expressroute/expressroute-introduction.md) zvyšuje propustnost sítě, výkon a předvídatelnost. ExpressRoute je služba, která směruje vaše data prostřednictvím vyhrazeného privátního připojení do Azure. Připojení ExpressRoute nesměrují data přes veřejný internet. Připojení nabízejí vyšší spolehlivost, vyšší rychlosti, nižší latenci a vyšší zabezpečení než běžná připojení přes veřejný internet.
- [Nástroj AZCopy](../../storage/common/storage-use-azcopy-v10.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) přesouvá data do služby Azure Storage přes veřejný internet. To funguje, pokud jsou velikosti dat menší než 10 TB. Chcete-li pravidelně provádět zatížení se společností AZCopy, otestujte rychlost sítě, abyste zjistili, zda je přijatelná.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) má bránu, kterou můžete nainstalovat na místní server. Pak můžete vytvořit kanál pro přesun dat z místního serveru do Azure Storage. Informace o použití data factory s fondem SQL, najdete v [tématu Načítání dat do fondu SQL](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Připravte data pro načítání

Možná budete muset připravit a vyčistit data v účtu úložiště před načtením do fondu SQL. Přípravu dat lze provést, když jsou vaše data ve zdroji, při exportu dat do textových souborů nebo po data je ve službě Azure Storage.  Nejjednodušší je pracovat s daty co nejdříve v procesu, jak je to možné.  

### <a name="define-external-tables"></a>Definování externích tabulek

Před načtením dat je třeba definovat externí tabulky v datovém skladu. PolyBase používá externí tabulky k definování a přístupu k datům ve službě Azure Storage. Externí tabulka je podobná zobrazení databáze. Externí tabulka obsahuje schéma tabulky a odkazuje na data uložená mimo datový sklad.

Definování externích tabulek zahrnuje určení zdroje dat, formátu textových souborů a definic tabulek. Jedná se o témata syntaxe T-SQL, která budete potřebovat:

- [VYTVOŘENÍ EXTERNÍHO ZDROJE DAT](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [VYTVOŘIT EXTERNÍ TABULKU](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

Příklad vytváření externích objektů najdete v kroku [Vytvoření externích tabulek](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-external-tables-for-the-sample-data) v kurzu načítání.

### <a name="format-text-files"></a>Formátování textových souborů

Jakmile jsou externí objekty definovány, je třeba zarovnat řádky textových souborů s definicí externí tabulky a formátu souboru. Data v každém řádku textového souboru musí být zarovnána s definicí tabulky.
Formátování textových souborů:

- Pokud data pocházejí z nerelačního zdroje, je třeba je převést na řádky a sloupce. Bez ohledu na to, zda data pocházejí z relačního nebo nerelačního zdroje, musí být data transformována tak, aby byla zarovnána s definicemi sloupců pro tabulku, do které chcete data načíst.
- Formátováním dat v textovém souboru zarovnáte sloupce a datové typy v cílové tabulce fondu SQL. Nesouosost mezi datovými typy v externích textových souborech a tabulkou datového skladu způsobí, že řádky budou během načítání odmítnuty.
- Oddělte pole v textovém souboru zakončením.  Ujistěte se, že používáte znak nebo znakovou sekvenci, která nebyla nalezena ve zdrojových datech. Použijte zakončení, které jste zadali pomocí [funkce CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-into-sql-pool-staging-tables-using-polybase"></a>4. Načtení dat do pracovních tabulek fondu SQL pomocí polybase

Je vhodné načíst data do pracovní tabulky. Pracovní tabulky umožňují zpracovávat chyby bez zasahování do produkčních tabulek. Pracovní tabulka také umožňuje použít MPP fondu SQL pro transformace dat před vložením dat do produkčních tabulek.

### <a name="options-for-loading-with-polybase"></a>Možnosti načítání pomocí PolyBase

Chcete-li načíst data pomocí funkce PolyBase, můžete použít některou z těchto možností načítání:

- [PolyBase s T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) funguje dobře, když vaše data jsou v úložišti objektů Blob Azure nebo Azure Data Lake Store. Poskytuje největší kontrolu nad procesem načítání, ale také vyžaduje definování externích datových objektů. Ostatní metody definují tyto objekty na pozadí při mapování zdrojových tabulek na cílové tabulky.  Chcete-li organizovat zatížení T-SQL, můžete použít Azure Data Factory, SSIS nebo Azure funkce.
- [PolyBase s SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) funguje dobře, když jsou zdrojová data na SQL Serveru, buď místně sql servernebo v cloudu. SSIS definuje zdroj mapování cílové tabulky a také orchestruje zatížení. Pokud již máte balíčky SSIS, můžete upravit balíčky pro práci s novým cílem datového skladu.
- Další nástroj orchestrace [Je PolyBase with Azure Data Factory (ADF).](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)  Definuje kanál a plánuje úlohy.
- [PolyBase s Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) přenáší data z tabulky datového skladu SQL do datového rámce Databricks a/nebo zapisuje data z datového rámce Databricks do tabulky DATOVÉHO Skladu SQL pomocí PolyBase.

### <a name="non-polybase-loading-options"></a>Jiné možnosti načítání mimo PolyBase

Pokud vaše data nejsou kompatibilní s PolyBase, můžete použít [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) nebo [SQLBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). bcp načte přímo do fondu SQL bez procházení úložiště objektů Blob Azure a je určen pouze pro malé zatížení. Všimněte si, že výkon zatížení těchto možností je výrazně pomalejší než PolyBase.

## <a name="5-transform-the-data"></a>5. Transformace dat

Zatímco data jsou v pracovní tabulce, proveďte transformace, které vaše úloha vyžaduje. Potom přesuňte data do produkční tabulky.

## <a name="6-insert-the-data-into-production-tables"></a>6. Vložení dat do výrobních tabulek

Vložit do ... Příkaz SELECT přesune data z pracovní tabulky do trvalé tabulky.

Při navrhování procesu ETL zkuste proces spustit na malém testovacím vzorku. Zkuste extrahovat 1000 řádků z tabulky do souboru, přesuňte ho do Azure a zkuste ho načíst do pracovní tabulky.

## <a name="partner-loading-solutions"></a>Řešení pro nakládku partnerů

Mnoho našich partnerů má řešení pro nakládání. Další informace naleznete v seznamu našich [partnerů pro řešení](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="next-steps"></a>Další kroky

Pokyny k načtení naleznete v [tématu Pokyny pro načtení dat](data-loading-best-practices.md).
