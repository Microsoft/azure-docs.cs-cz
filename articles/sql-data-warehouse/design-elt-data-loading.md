---
title: Místo ETL ELT návrhu pro Azure SQL Data Warehouse | Microsoft Docs
description: Místo ETL Navrhněte proces extrakce, načítání a transformace (ELT) pro načítání dat nebo Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 07/28/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: c90deefba75cd8bbeda126c9da8a05e1069831d4
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597467"
---
# <a name="designing-a-polybase-data-loading-strategy-for-azure-sql-data-warehouse"></a>Návrh strategie načítání základních dat pro Azure SQL Data Warehouse

Tradiční datové sklady SMP používají proces extrakce, transformace a načítání (ETL) pro načítání dat. Azure SQL Data Warehouse je rozsáhlá architektura paralelního zpracování (MPP), která využívá škálovatelnost a flexibilitu výpočetních prostředků a prostředků úložiště. Použití procesu extrakce, načítání a transformace (ELT) může využít funkce MPP a eliminace prostředků potřebných k transformaci dat před jejich načtením. I když SQL Data Warehouse podporuje mnoho metod načítání, včetně nezákladních možností, jako je BCP a SQL BulkCopy API, nejrychlejší a nejškálovatelný způsob načítání data je prostřednictvím základny.  Základem je technologie, která přistupuje k externím datům uloženým ve službě Azure Blob Storage nebo Azure Data Lake Store prostřednictvím jazyka T-SQL.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>Co je ELT?

Extrahování, načítání a transformace (ELT) je proces, při kterém jsou data extrahována ze zdrojového systému, načtena do datového skladu a následně transformována. 

Základní kroky pro implementaci ELT základu pro SQL Data Warehouse jsou:

1. Extrahujte zdrojová data do textových souborů.
2. Nakládat data do služby Azure Blob Storage nebo Azure Data Lake Store.
3. Připravte data pro načtení.
4. Načtěte data do SQL Data Warehouse přípravných tabulek pomocí základu. 
5. Transformujte data.
6. Vložte data do provozních tabulek.


Kurz načítání najdete v tématu [použití základny k načtení dat z úložiště objektů BLOB v Azure do Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Další informace najdete v tématu [načítání vzorů na blogu](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/). 


## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrahovat zdrojová data do textových souborů

Získávání dat ze zdrojového systému závisí na umístění úložiště.  Cílem je přesunout data do podporovaných textových souborů s oddělovači. 

### <a name="polybase-external-file-formats"></a>Základní formáty externích souborů

Základ kódu načítá data z textových souborů s oddělovači v kódování UTF-8 a UTF-16. Kromě textových souborů s oddělovači načte soubory ze souboru Hadoop formáty RC, ORC a Parquet. Základna může také načítat data z gzip a s přichycením komprimovaných souborů. Základová databáze v současné době nepodporuje rozšířené kódování ASCII, formát s pevnou šířkou a vnořené formáty, jako je například WinZip, JSON a XML. Pokud exportujete z SQL Server, můžete pomocí [nástroje příkazového řádku BCP](/sql/tools/bcp-utility) exportovat data do textových souborů s oddělovači. Mapování datových typů Parquet na SQL DW je následující:

| **Datový typ Parquet** |                      **Datový typ SQL**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            float                             |
|         float         |                             real                             |
|        double         |                            money                             |
|        double         |                          smallmoney                          |
|        řetězec         |                            nchar                             |
|        řetězec         |                           nvarchar                           |
|        řetězec         |                             char                             |
|        řetězec         |                           varchar                            |
|        binary         |                            binary                            |
|        binary         |                          Varbinary                           |
|       timestamp       |                             date                             |
|       timestamp       |                        smalldatetime                         |
|       timestamp       |                          datetime2                           |
|       timestamp       |                           datetime                           |
|       timestamp       |                             time                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Využití dat do služby Azure Blob Storage nebo Azure Data Lake Store

Pokud chcete data z Azure Storage nakládat, můžete je přesunout do služby [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) nebo [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md). V obou umístěních by se data měla ukládat v textových souborech. Základna může být načtena z libovolného umístění.

Nástroje a služby, které můžete použít k přesunu dat do Azure Storage:

- Služba [Azure ExpressRoute](../expressroute/expressroute-introduction.md) vylepšuje propustnost, výkon a předvídatelnost sítě. ExpressRoute je služba, která směruje vašich dat pomocí vyhrazeného soukromého připojení k Azure. Připojení ExpressRoute nesměrují data prostřednictvím veřejného Internetu. Připojení nabízejí spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes veřejný Internet.
- [Nástroj AzCopy](../storage/common/storage-moving-data.md) přesouvá data Azure Storage přes veřejný Internet. To funguje, pokud jsou velikosti vašich dat menší než 10 TB. Pokud chcete pravidelně provádět zátěž s AZCopy, otestujte rychlost sítě a zjistěte, jestli je přijatelné. 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) má bránu, kterou můžete nainstalovat na svůj místní server. Pak můžete vytvořit kanál pro přesun dat z místního serveru až do Azure Storage. Pokud chcete použít Data Factory s SQL Data Warehouse, přečtěte si téma [načtení dat do SQL Data Warehouse](/azure/data-factory/load-azure-sql-data-warehouse).


## <a name="3-prepare-the-data-for-loading"></a>3. Příprava dat pro načtení

Před načtením do SQL Data Warehouse možná budete muset data v účtu úložiště připravit a vyčistit. Přípravu dat lze provést, pokud jsou data ve zdroji, protože exportujete data do textových souborů nebo když jsou data v Azure Storage.  Je nejjednodušší pracovat s daty co nejdříve v tomto procesu.  

### <a name="define-external-tables"></a>Definovat externí tabulky

Předtím, než budete moci načíst data, je třeba definovat externí tabulky v datovém skladu. Základ používá pro definování a přístup k datům v Azure Storage externí tabulky. Externí tabulka je podobná zobrazení databáze. Externí tabulka obsahuje schéma tabulky a odkazuje na data, která jsou uložená mimo datový sklad. 

Definování externích tabulek zahrnuje určení zdroje dat, formátu textových souborů a definic tabulek. Jedná se o témata syntaxe T-SQL, která budete potřebovat:
- [VYTVOŘIT EXTERNÍ ZDROJ DAT](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [VYTVOŘIT EXTERNÍ TABULKU](/sql/t-sql/statements/create-external-table-transact-sql)

Příklad vytváření externích objektů naleznete v kroku [Vytvoření externích tabulek](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) v kurzu načítání.

### <a name="format-text-files"></a>Formátování textových souborů

Po definování externích objektů je nutné zarovnat řádky textových souborů s externí tabulkou a definicí formátu souboru. Data v každém řádku textového souboru musí být zarovnána s definicí tabulky.
Formátování textových souborů:

- Pokud vaše data pocházejí z nerelačního zdroje, je nutné je transformovat na řádky a sloupce. Bez ohledu na to, jestli jsou data z relačního nebo nerelačního zdroje, musí být data transformovaná tak, aby odpovídala definicím sloupců pro tabulku, do které plánujete načíst data. 
- Naformátujte data v textovém souboru tak, aby odpovídala sloupcům a datovým typům v cílové tabulce SQL Data Warehouse. Chybné zarovnání mezi datovými typy v externích textových souborech a v tabulce datového skladu způsobí, že se řádky během načítání odmítnou.
- Oddělte pole v textovém souboru ukončovacím znakem.  Nezapomeňte použít znak nebo sekvenci znaků, které se ve zdrojových datech nenašly. Použijte ukončovací znak, který jste zadali pomocí nástroje [Create External File Format](/sql/t-sql/statements/create-external-file-format-transact-sql).


## <a name="4-load-the-data-into-sql-data-warehouse-staging-tables-using-polybase"></a>4. Načtení dat do SQL Data Warehouse přípravných tabulek pomocí základu

Osvědčeným postupem je načíst data do pracovní tabulky. Pracovní tabulky umožňují zpracovávat chyby bez rušivého vlivu na provozní tabulky. Pracovní tabulka vám také umožní použít SQL Data Warehouse MPP pro transformaci dat před vložením dat do provozních tabulek.

### <a name="options-for-loading-with-polybase"></a>Možnosti načítání s použitím základny

Chcete-li načíst data pomocí základu, můžete použít některou z těchto možností načítání:

- [Základ T-SQL](load-data-from-azure-blob-storage-using-polybase.md) funguje dobře, když jsou vaše data ve službě Azure Blob storage nebo Azure Data Lake Store. Poskytuje vám největší kontrolu nad procesem načítání, ale také vyžaduje, abyste definovali externí datové objekty. Ostatní metody definují tyto objekty na pozadí při mapování zdrojových tabulek na cílové tabulky.  K orchestraci načtení T-SQL můžete použít Azure Data Factory, SSIS nebo Azure Functions. 
- [Základna s SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) funguje dobře, když jsou vaše zdrojová data v SQL Server, a to buď SQL Server místně, nebo v cloudu. SSIS definuje mapování zdrojového do cílové tabulky a také toto zatížení orchestruje. Pokud již máte balíčky SSIS, můžete je upravit tak, aby fungovaly s novým cílem datového skladu. 
- [Základem s Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) je další nástroj orchestrace.  Definuje kanál a plánuje úlohy. 
- [Základna s Azure](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) datacihly přenáší data z SQL Data Warehouse tabulky do datacihly datacihly nebo zapisuje data z datového rámce datacihly do tabulky SQL Data Warehouse pomocí základu.

### <a name="non-polybase-loading-options"></a>Možnosti načítání nezaložených na základech

Pokud vaše data nejsou kompatibilní s základnu, můžete použít [BCP](/sql/tools/bcp-utility) nebo [rozhraní SqlBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). BCP se načítá přímo do SQL Data Warehouse bez přechodu přes úložiště objektů BLOB v Azure a je určený jenom pro malé zátěže. Všimněte si, že výkon zatížení těchto možností je výrazně pomalejší než základ. 


## <a name="5-transform-the-data"></a>5. Transformace dat

Data jsou v pracovní tabulce a umožňují transformace, které vaše zatížení vyžaduje. Pak data přesuňte do provozní tabulky.


## <a name="6-insert-the-data-into-production-tables"></a>6. Vložení dat do provozních tabulek

VLOŽIT do... Příkaz SELECT přesune data z pracovní tabulky do trvalé tabulky. 

Při navrhování procesu ETL zkuste proces spustit na malém vzorku testu. Zkuste extrahovat řádky 1000 z tabulky do souboru, přesunout je do Azure a pak je zkusit načíst do pracovní tabulky. 


## <a name="partner-loading-solutions"></a>Řešení pro načítání partnerů

Mnohé z našich partnerů načítají řešení. Pokud se chcete dozvědět víc, podívejte se na seznam našich [partnerů pro řešení](sql-data-warehouse-partner-business-intelligence.md). 


## <a name="next-steps"></a>Další postup

Pokyny k načtení najdete v tématu [doprovodné materiály k načtení dat](guidance-for-loading-data.md).


