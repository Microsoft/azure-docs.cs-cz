---
title: Návrh strategie načítání základních dat pro vyhrazený fond SQL
description: Místo ETL Navrhněte proces extrakce, načítání a transformace (ELT) pro načítání dat s vyhrazeným SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 18a2cf0de94641c955ed72a48f28352d13115ef0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667573"
---
# <a name="design-a-polybase-data-loading-strategy-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Návrh strategie načítání základních dat pro vyhrazený fond SQL ve službě Azure synapse Analytics

Tradiční datové sklady SMP používají proces extrakce, transformace a načítání (ETL) pro načítání dat. Azure SQL Pool je rozsáhlá architektura paralelního zpracování (MPP), která využívá škálovatelnost a flexibilitu výpočetních prostředků a prostředků úložiště. Pomocí procesu extrakce, načítání a transformace (ELT) můžete využít integrované možnosti zpracování distribuovaných dotazů a eliminovat prostředky potřebné k transformaci dat před jejich načtením.

I když fond SQL podporuje mnoho metod načítání, včetně nezákladních možností, jako je BCP a SQL BulkCopy API, nejrychlejší a nejškálovatelný způsob načítání data je prostřednictvím základny.  Základem je technologie, která přistupuje k externím datům uloženým ve službě Azure Blob Storage nebo Azure Data Lake Store prostřednictvím jazyka T-SQL.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="extract-load-and-transform-elt"></a>Extrakce, načítání a transformace (ELT)

Extrahování, načítání a transformace (ELT) je proces, při kterém jsou data extrahována ze zdrojového systému, načtena do datového skladu a následně transformována.

Základní kroky pro implementaci ELT základního fondu SQL jsou následující:

1. Extrakce zdrojových dat do textových souborů
2. Nakládat data do služby Azure Blob Storage nebo Azure Data Lake Store.
3. Připravte data pro načtení.
4. Načtěte data do vyhrazených pracovních tabulek fondu SQL pomocí základu.
5. Transformujte data.
6. Vložení dat do produkčních tabulek

Kurz načítání najdete v tématu [použití základny k načtení dat z úložiště objektů BLOB v Azure do služby Azure synapse Analytics](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

Další informace najdete v tématu [načítání vzorů na blogu](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies).

## <a name="1-extract-the-source-data-into-text-files"></a>1. extrahuje zdrojová data do textových souborů.

Získávání dat ze zdrojového systému závisí na umístění úložiště.  Cílem je přesunout data do podporovaných textových souborů s oddělovači.

### <a name="polybase-external-file-formats"></a>Základní formáty externích souborů

Základ kódu načítá data z textových souborů s oddělovači v kódování UTF-8 a UTF-16. Kromě textových souborů s oddělovači načte soubory ze souboru Hadoop formáty RC, ORC a Parquet. Základna může také načítat data z gzip a s přichycením komprimovaných souborů. Základová databáze v současné době nepodporuje rozšířené kódování ASCII, formát s pevnou šířkou a vnořené formáty, jako je například WinZip, JSON a XML.

Pokud exportujete z SQL Server, můžete pomocí [nástroje příkazového řádku BCP](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) exportovat data do textových souborů s oddělovači. Mapování datových typů Parquet na Azure synapse Analytics je následující:

| **Datový typ Parquet** |                      **Datový typ SQL**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            float                             |
|         float         |                             real                             |
|        double         |                            papír                             |
|        double         |                          smallmoney                          |
|        řetězec         |                            nchar                             |
|        řetězec         |                           nvarchar                           |
|        řetězec         |                             char                             |
|        řetězec         |                           varchar                            |
|        binární         |                            binární                            |
|        binární         |                          varbinary                           |
|       časové razítko       |                             date                             |
|       časové razítko       |                        smalldatetime                         |
|       časové razítko       |                          datetime2                           |
|       časové razítko       |                           datetime                           |
|       časové razítko       |                             time                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. překládat data do služby Azure Blob Storage nebo Azure Data Lake Store

Pokud chcete data z Azure Storage nakládat, můžete je přesunout do služby [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md) nebo [Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md). V obou umístěních by se data měla ukládat v textových souborech. Základna může být načtena z libovolného umístění.

Nástroje a služby, které můžete použít k přesunu dat do Azure Storage:

- Služba [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) vylepšuje propustnost, výkon a předvídatelnost sítě. ExpressRoute je služba, která směruje vaše data prostřednictvím vyhrazeného privátního připojení k Azure. Připojení ExpressRoute nesměrují data prostřednictvím veřejného Internetu. Připojení nabízejí spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes veřejný Internet.
- [Nástroj AzCopy](../../storage/common/storage-use-azcopy-v10.md) přesouvá data Azure Storage přes veřejný Internet. To funguje, pokud jsou velikosti vašich dat menší než 10 TB. Pokud chcete pravidelně provádět zátěž s AZCopy, otestujte rychlost sítě a zjistěte, jestli je přijatelné.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) má bránu, kterou můžete nainstalovat na svůj místní server. Pak můžete vytvořit kanál pro přesun dat z místního serveru až do Azure Storage. Pokud chcete použít Data Factory s vyhrazeným fondem SQL, přečtěte si téma [načtení dat do vyhrazeného fondu SQL](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Příprava dat pro načtení

Před načtením do vyhrazeného fondu SQL může být nutné připravit a vyčistit data v účtu úložiště. Přípravu dat lze provést, pokud jsou data ve zdroji, protože exportujete data do textových souborů nebo když jsou data v Azure Storage.  Je nejjednodušší pracovat s daty co nejdříve v tomto procesu.  

### <a name="define-external-tables"></a>Definovat externí tabulky

Předtím, než budete moci načíst data, je třeba definovat externí tabulky v datovém skladu. Základ používá pro definování a přístup k datům v Azure Storage externí tabulky. Externí tabulka je podobná zobrazení databáze. Externí tabulka obsahuje schéma tabulky a odkazuje na data, která jsou uložená mimo datový sklad.

Definování externích tabulek zahrnuje určení zdroje dat, formátu textových souborů a definic tabulek. Níže jsou uvedená témata syntaxe T-SQL, která budete potřebovat:

- [VYTVOŘIT EXTERNÍ ZDROJ DAT](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [VYTVOŘIT EXTERNÍ TABULKU](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="format-text-files"></a>Formátování textových souborů

Po definování externích objektů je nutné zarovnat řádky textových souborů s externí tabulkou a definicí formátu souboru. Data v každém řádku textového souboru musí být zarovnána s definicí tabulky.
Formátování textových souborů:

- Pokud vaše data pocházejí z nerelačního zdroje, je nutné je transformovat na řádky a sloupce. Bez ohledu na to, jestli jsou data z relačního nebo nerelačního zdroje, musí být data transformovaná tak, aby odpovídala definicím sloupců pro tabulku, do které plánujete načíst data.
- Naformátujte data v textovém souboru tak, aby byla v souladu se sloupci a datovými typy v cílové tabulce fondu SQL. Chybné zarovnání mezi datovými typy v externích textových souborech a v tabulce datového skladu způsobí, že se řádky během načítání odmítnou.
- Oddělte pole v textovém souboru ukončovacím znakem.  Nezapomeňte použít znak nebo sekvenci znaků, které se ve zdrojových datech nenašly. Použijte ukončovací znak, který jste zadali pomocí nástroje [Create External File Format](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="4-load-the-data-into-dedicated-sql-pool-staging-tables-using-polybase"></a>4. načtěte data do pracovních tabulek vyhrazeného fondu SQL pomocí základu.

Osvědčeným postupem je načíst data do pracovní tabulky. Pracovní tabulky umožňují zpracovávat chyby bez rušivého vlivu na provozní tabulky. Přípravná tabulka vám také nabídne možnost použít integrované funkce distribuovaného zpracování dotazů ve fondu SQL pro transformaci dat před vložením dat do provozních tabulek.

### <a name="options-for-loading-with-polybase"></a>Možnosti načítání s použitím základny

Chcete-li načíst data pomocí základu, můžete použít některou z těchto možností načítání:

- [Základ T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) funguje dobře, když jsou vaše data ve službě Azure Blob storage nebo Azure Data Lake Store. Poskytuje vám největší kontrolu nad procesem načítání, ale také vyžaduje, abyste definovali externí datové objekty. Ostatní metody definují tyto objekty na pozadí při mapování zdrojových tabulek na cílové tabulky.  K orchestraci načtení T-SQL můžete použít Azure Data Factory, SSIS nebo Azure Functions.
- [Základ SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) funguje dobře, když jsou zdrojová data v SQL Server. SSIS definuje mapování zdrojového do cílové tabulky a také toto zatížení orchestruje. Pokud již máte balíčky SSIS, můžete je upravit tak, aby fungovaly s novým cílem datového skladu.
- [Základem s Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) je další nástroj orchestrace.  Definuje kanál a plánuje úlohy.
- [Základna s Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) přenáší data z tabulky Azure synapse Analytics do dataframe datacihly nebo zapisuje data z datového rámce datacihly do tabulky Azure synapse Analytics pomocí základu.

### <a name="non-polybase-loading-options"></a>Možnosti načítání nezaložených na základech

Pokud vaše data nejsou kompatibilní s základnu, můžete použít [BCP](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) nebo [rozhraní SqlBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy). BCP se načítá přímo do vyhrazeného fondu SQL bez přechodu přes úložiště objektů BLOB v Azure a je určený jenom pro malé zátěže. Všimněte si, že výkon zatížení těchto možností je výrazně pomalejší než základ.

## <a name="5-transform-the-data"></a>5. Transformujte data

Data jsou v pracovní tabulce a umožňují transformace, které vaše zatížení vyžaduje. Pak data přesuňte do provozní tabulky.

## <a name="6-insert-the-data-into-production-tables"></a>6. Vložte data do provozních tabulek.

VLOŽIT do... Příkaz SELECT přesune data z pracovní tabulky do trvalé tabulky.

Při navrhování procesu ETL zkuste proces spustit na malém vzorku testu. Zkuste extrahovat řádky 1000 z tabulky do souboru, přesunout je do Azure a pak je zkusit načíst do pracovní tabulky.

## <a name="partner-loading-solutions"></a>Řešení pro načítání partnerů

Mnohé z našich partnerů načítají řešení. Pokud se chcete dozvědět víc, podívejte se na seznam našich [partnerů pro řešení](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="next-steps"></a>Další kroky

Pokyny k načtení najdete v tématu [doprovodné materiály k načtení dat](data-loading-best-practices.md).