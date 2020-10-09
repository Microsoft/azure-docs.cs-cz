---
title: Místo ETL ELT design
description: Implementace flexibilních strategií načítání dat pro synapse fond SQL ve službě Azure synapse Analytics
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/13/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 1b73b82b4367d50cc5fbe9881a67e0afa041db86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201154"
---
# <a name="data-loading-strategies-for-synapse-sql-pool"></a>Strategie načítání dat do fondu Synapse SQL

Tradiční fondy SQL SMP používají pro načítání dat proces extrakce, transformace a načítání (ETL). Synapse fond SQL v rámci služby Azure synapse Analytics má architekturu MPP (COMPUTE Parallel Processing), která využívá škálovatelnost a flexibilitu výpočetních a úložných prostředků.

Použití procesu extrakce, načítání a transformace (ELT) využívá MPP a eliminuje prostředky potřebné pro transformaci dat před jejich načtením.

I když fond SQL podporuje mnoho metod načítání, včetně oblíbených SQL Server možností, jako je [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) a [rozhraní SqlBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), nejrychlejší a nejškálovatelný způsob načítání dat je prostřednictvím základních externích tabulek a [příkazu copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (Preview).

Pomocí základu a příkazu Kopírovat můžete přistupovat k externím datům uloženým ve službě Azure Blob Storage nebo Azure Data Lake Store prostřednictvím jazyka T-SQL. Pro největší flexibilitu při nasazování doporučujeme použít příkaz COPY.

> [!NOTE]  
> Příkaz COPY je aktuálně ve verzi Public Preview. Pokud chcete poskytnout zpětnou vazbu, odešlete e-mail na následující distribuční seznam: sqldwcopypreview@service.microsoft.com .

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>Co je ELT?

Extrahování, načítání a transformace (ELT) je proces, při kterém se data extrahují ze zdrojového systému, načtou do fondu SQL a pak se transformují.

Základní kroky pro implementaci ELT jsou:

1. Extrakce zdrojových dat do textových souborů
2. Nakládat data do služby Azure Blob Storage nebo Azure Data Lake Store.
3. Připravte data pro načtení.
4. Načtěte data do pracovních tabulek pomocí základu nebo příkazu pro kopírování.
5. Transformujte data.
6. Vložení dat do produkčních tabulek

Kurz načítání najdete v tématu [načtení dat z úložiště objektů BLOB v Azure](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-extract-the-source-data-into-text-files"></a>1. extrahuje zdrojová data do textových souborů.

Získávání dat ze zdrojového systému závisí na umístění úložiště. Cílem je přesunout data do podporovaného textu nebo souborů CSV s oddělovači.

### <a name="supported-file-formats"></a>Podporované formáty souborů

Pomocí základny a příkazu COPY můžete načíst data z textového souboru nebo souborů CSV s oddělovači UTF-8 a UTF-16. Kromě souborů s oddělovači textu nebo CSV se načte z formátů souborů Hadoop, jako jsou ORC a Parquet. Základní a příkaz COPY mohou také načítat data z gzip a s přichycením komprimovaných souborů.

Rozšířené formáty ASCII, formát s pevnou šířkou a vnořené formáty, jako je například WinZip nebo XML, nejsou podporovány. Pokud exportujete z SQL Server, můžete použít [Nástroj příkazového řádku BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) k exportu dat do textových souborů s oddělovači.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. překládat data do služby Azure Blob Storage nebo Azure Data Lake Store

Pokud chcete data z Azure Storage nakládat, můžete je přesunout do služby [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) nebo [Azure Data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). V obou umístěních by se data měla ukládat v textových souborech. Základ a příkaz COPY lze načíst z libovolného umístění.

Nástroje a služby, které můžete použít k přesunu dat do Azure Storage:

- Služba [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) vylepšuje propustnost, výkon a předvídatelnost sítě. ExpressRoute je služba, která směruje vaše data prostřednictvím vyhrazeného privátního připojení k Azure. Připojení ExpressRoute nesměrují data prostřednictvím veřejného Internetu. Připojení nabízejí spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes veřejný Internet.
- [Nástroj AzCopy](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) přesouvá data Azure Storage přes veřejný Internet. To funguje, pokud jsou velikosti vašich dat menší než 10 TB. Pokud chcete pravidelně provádět zátěž s AZCopy, otestujte rychlost sítě a zjistěte, jestli je přijatelné.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) má bránu, kterou můžete nainstalovat na svůj místní server. Pak můžete vytvořit kanál pro přesun dat z místního serveru až do Azure Storage. Pokud chcete použít Data Factory s fondem SQL, přečtěte si téma [načítání dat pro fond SQL](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Příprava dat pro načtení

Možná budete muset před načtením připravit a vyčistit data v účtu úložiště. Přípravu dat lze provést, pokud jsou data ve zdroji, protože exportujete data do textových souborů nebo když jsou data v Azure Storage.  Je nejjednodušší pracovat s daty co nejdříve v tomto procesu.  

### <a name="define-the-tables"></a>Definování tabulek

Při použití příkazu COPY musíte nejprve definovat tabulky, do kterých načítáte, do fondu SQL.

Pokud používáte základnu, musíte před načtením definovat externí tabulky ve svém fondu SQL. Základ používá pro definování a přístup k datům v Azure Storage externí tabulky. Externí tabulka je podobná zobrazení databáze. Externí tabulka obsahuje schéma tabulky a odkazuje na data, která jsou uložená mimo fond SQL.

Definování externích tabulek zahrnuje určení zdroje dat, formátu textových souborů a definic tabulek. Odkazy jazyka T-SQL odkazují na články, které budete potřebovat:

- [VYTVOŘIT EXTERNÍ ZDROJ DAT](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [VYTVOŘIT EXTERNÍ TABULKU](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Při načítání souborů Parquet použít následující mapování typů dat SQL:

|                         Typ Parquet                         |   Logický typ Parquet (anotace)   |  Datový typ SQL   |
| :----------------------------------------------------------: | :-----------------------------------: | :--------------: |
|                           DATOVÉHO                            |                                       |       bit        |
|                     BINÁRNÍ/BYTE_ARRAY                      |                                       |    varbinary     |
|                            KLEPAT                            |                                       |      float       |
|                            Plovák                             |                                       |       real       |
|                            UVEDENA                             |                                       |       int        |
|                            INT64                             |                                       |      bigint      |
|                            INT96                             |                                       |    datetime2     |
|                     FIXED_LEN_BYTE_ARRAY                     |                                       |      binární      |
|                            TVARU                            |                 UTF                  |     nvarchar     |
|                            TVARU                            |                ŘETEZCE                 |     nvarchar     |
|                            TVARU                            |                 VYTVÁŘENÍ                  |     nvarchar     |
|                            TVARU                            |                 IDENTIFIKÁTOR                  | uniqueidentifier |
|                            TVARU                            |                NOTACI                |     decimal      |
|                            TVARU                            |                 JSON                  |  nvarchar(MAX)   |
|                            TVARU                            |                 BSON                  |  varbinary (max)  |
|                     FIXED_LEN_BYTE_ARRAY                     |                NOTACI                |     decimal      |
|                          BYTE_ARRAY                          |               DOBA                |  varchar (max);   |
|                            UVEDENA                             |             INT (8, true)              |     smallint     |
|                            UVEDENA                             |            INT (16, true)            |     smallint     |
|                            UVEDENA                             |             INT (32, true)             |       int        |
|                            UVEDENA                             |            INT (8, false)            |     tinyint      |
|                            UVEDENA                             |            INT (16, false)             |       int        |
|                            UVEDENA                             |           INT (32, false)            |      bigint      |
|                            UVEDENA                             |                 DATE                  |       date       |
|                            UVEDENA                             |                NOTACI                |     decimal      |
|                            UVEDENA                             |            ČAS (LISOVNY)             |       time       |
|                            INT64                             |            INT (64; true)            |      bigint      |
|                            INT64                             |           INT (64, false)            |  desetinné číslo (20, 0)   |
|                            INT64                             |                NOTACI                |     decimal      |
|                            INT64                             |         ČAS (MIKROČASU A NANO)         |       time       |
|                            INT64                             | ČASOVÉ RAZÍTKO (LISOVNY//NANO) |    datetime2     |
| [Komplexní typ](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 SEZNAMU                  |   varchar(max)   |
| [Komplexní typ](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  MAPY                  |   varchar(max)   |



Příklad vytváření externích objektů najdete v tématu [Vytvoření externích tabulek](https://docs.microsoft.com/azure/synapse-analytics/sql/develop-tables-external-tables?tabs=sql-pool).

### <a name="format-text-files"></a>Formátování textových souborů

Pokud používáte základnu, externí objekty definovány musí zarovnat řádky textových souborů s externí tabulkou a definicí formátu souboru. Data v každém řádku textového souboru musí být zarovnána s definicí tabulky.
Formátování textových souborů:

- Pokud vaše data pocházejí z nerelačního zdroje, je nutné je transformovat na řádky a sloupce. Bez ohledu na to, jestli jsou data z relačního nebo nerelačního zdroje, musí být data transformovaná tak, aby odpovídala definicím sloupců pro tabulku, do které plánujete načíst data.
- Umožňuje formátovat data v textovém souboru tak, aby odpovídala sloupcům a datovým typům v cílové tabulce. Chybné zarovnání mezi datovými typy v externích textových souborech a v tabulce fondu SQL způsobuje, že během načítání budou řádky odmítnuty.
- Oddělte pole v textovém souboru ukončovacím znakem.  Nezapomeňte použít znak nebo sekvenci znaků, které se ve zdrojových datech nenašly. Použijte ukončovací znak, který jste zadali pomocí nástroje [Create External File Format](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. načtěte data pomocí základu nebo příkazu COPY.

Osvědčeným postupem je načíst data do pracovní tabulky. Pracovní tabulky umožňují zpracovávat chyby bez rušivého vlivu na provozní tabulky. Přípravná tabulka vám také nabídne možnost použít architekturu paralelního zpracování fondu SQL pro transformaci dat před vložením dat do provozních tabulek.

### <a name="options-for-loading"></a>Možnosti načítání

Chcete-li načíst data, můžete použít kteroukoli z těchto možností načítání:

- [Příkaz Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) je doporučeným nástrojem pro načítání, protože umožňuje hladce a pružně načítat data. Příkaz má mnoho dalších možností načítání, které základ neposkytuje. 
- [Základna s T-SQL](load-data-from-azure-blob-storage-using-polybase.md) vyžaduje, abyste definovali externí datové objekty.
- [Příkaz Base a copy s Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) je další nástroj Orchestration.  Definuje kanál a plánuje úlohy.
- [Základ SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) funguje dobře, když jsou zdrojová data v SQL Server. SSIS definuje mapování zdrojového do cílové tabulky a také toto zatížení orchestruje. Pokud již máte balíčky SSIS, můžete je upravit tak, aby fungovaly s novým cílem datového skladu.
- [Základna s Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) přenáší data z tabulky do datacihly datacihly nebo zapisuje data z datového rámce datacihly do tabulky pomocí základu.

### <a name="other-loading-options"></a>Další možnosti načítání

Kromě základu a příkazu COPY můžete použít [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) nebo [rozhraní SqlBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). BCP se načítá přímo do databáze bez přechodu přes úložiště objektů BLOB v Azure a je určený jenom pro malé zátěže.

> [!NOTE]
> Výkon zatížení těchto možností je pomalejší než základ a příkaz COPY.

## <a name="5-transform-the-data"></a>5. Transformujte data

Data jsou v pracovní tabulce a umožňují transformace, které vaše zatížení vyžaduje. Pak data přesuňte do provozní tabulky.

## <a name="6-insert-the-data-into-production-tables"></a>6. Vložte data do provozních tabulek.

VLOŽIT do... Příkaz SELECT přesune data z pracovní tabulky do trvalé tabulky.

Při navrhování procesu ETL zkuste proces spustit na malém vzorku testu. Zkuste extrahovat řádky 1000 z tabulky do souboru, přesunout je do Azure a pak je zkusit načíst do pracovní tabulky.

## <a name="partner-loading-solutions"></a>Řešení pro načítání partnerů

Mnohé z našich partnerů načítají řešení. Pokud se chcete dozvědět víc, podívejte se na seznam našich [partnerů pro řešení](sql-data-warehouse-partner-business-intelligence.md).

## <a name="next-steps"></a>Další kroky

Pokyny k načítání najdete v tématu [Doprovodné materiály k načítání dat](guidance-for-loading-data.md).
