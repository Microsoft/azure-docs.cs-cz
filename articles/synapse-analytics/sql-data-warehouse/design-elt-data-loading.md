---
title: Namísto ETL, design ELT
description: Implementace flexibilních strategií načítání dat pro fond Synapse SQL v rámci Azure Synapse Analytics
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 87b33e91076f8f7f31740795f0ec05cea49a1e83
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631193"
---
# <a name="data-loading-strategies-for-synapse-sql-pool"></a>Strategie načítání dat pro fond Synapse SQL

Tradiční fondy SMP SQL používají proces extrakce, transformace a zatížení (ETL) pro načítání dat. Fond SYNAPse SQL v rámci Azure Synapse Analytics má architekturu masivně paralelního zpracování (MPP), která využívá škálovatelnost a flexibilitu výpočetních prostředků a prostředků úložiště.

Pomocí procesu extrakce, zatížení a transformace (ELT) využívá MPP a eliminuje prostředky potřebné pro transformaci dat před načtením.

Zatímco fond SQL podporuje mnoho metod načítání, včetně oblíbených možností serveru SQL Server, jako je [například bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) a [sqlbulkcopy api](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), nejrychlejší a nejškálovatelnější způsob, jak načíst data je prostřednictvím polybase externí tabulky a [příkaz COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (preview).

S PolyBase a příkazem COPY můžete přistupovat k externím datům uloženým v úložišti objektů Blob Azure nebo v Úložišti Datových jezer Azure prostřednictvím jazyka T-SQL. Pro co největší flexibilitu při načítání doporučujeme použít příkaz COPY.

> [!NOTE]  
> Příkaz COPY je aktuálně ve verzi Public Preview. Chcete-li poskytnout zpětnou vazbu, sqldwcopypreview@service.microsoft.comodešlete e-mail do následujícího distribučního seznamu: .

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>Co je ELT?

Extrakce, načtení a transformace (ELT) je proces, kterým jsou data extrahována ze zdrojového systému, načtena do fondu SQL a poté transformována.

Základní kroky pro implementaci ELT jsou:

1. Extrakce zdrojových dat do textových souborů
2. Přiěte data do úložiště objektů blob Azure nebo Azure Data Lake Store.
3. Připravte data pro načtení.
4. Načtěte data do pracovních tabulek pomocí příkazu PolyBase nebo příkazu COPY.
5. Transformujte data.
6. Vložení dat do produkčních tabulek

Kurz načítání PolyBase najdete v [tématu Použití PolyBase k načtení dat z úložiště objektů blob Azure](load-data-from-azure-blob-storage-using-polybase.md).

Další informace naleznete v [tématu Načítání vzorů blogu](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrahujte zdrojová data do textových souborů

Získání dat ze zdrojového systému závisí na umístění úložiště.  Cílem je přesunout data do PolyBase a COPY podporované oddělené text nebo CSV soubory.

### <a name="polybase-and-copy-external-file-formats"></a>Formáty externích souborů PolyBase a COPY

S PolyBase a COPY prohlášení, můžete načíst data z UTF-8 a UTF-16 kódovaný oddělený text nebo CSV soubory. Kromě oddělených textnebo CSV souborů se načte z formátů souborů Hadoop, jako jsou ORC a Parkety. PolyBase a příkaz COPY mohou také načíst data z komprimovaných souborů Gzip a Snappy.

Rozšířené ASCII, formát s pevnou šířkou a vnořené formáty, jako je WinZip nebo XML, nejsou podporovány. Pokud exportujete ze serveru SQL Server, můžete data exportovat do oddělených textových souborů pomocí [nástroje příkazového řádku bcp.](/sql/tools/bcp-utility?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Přiložte data do úložiště objektů blob Azure nebo azure data lake store

Pokud chcete data přistát v úložišti Azure, můžete je přesunout do [úložiště objektů blob Azure](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) nebo Do Úložiště [datových jezer Azure Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). V obou umístěních by měla být data uložena v textových souborech. PolyBase a příkaz COPY lze načíst z obou umístění.

Nástroje a služby, které můžete použít k přesunu dat do Azure Storage:

- [Služba Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) zvyšuje propustnost sítě, výkon a předvídatelnost. ExpressRoute je služba, která směruje vaše data prostřednictvím vyhrazeného privátního připojení do Azure. Připojení ExpressRoute nesměrují data přes veřejný internet. Připojení nabízejí vyšší spolehlivost, vyšší rychlosti, nižší latenci a vyšší zabezpečení než běžná připojení přes veřejný internet.
- [Nástroj AZCopy](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) přesouvá data do služby Azure Storage přes veřejný internet. To funguje, pokud jsou velikosti dat menší než 10 TB. Chcete-li pravidelně provádět zatížení se společností AZCopy, otestujte rychlost sítě, abyste zjistili, zda je přijatelná.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) má bránu, kterou můžete nainstalovat na místní server. Pak můžete vytvořit kanál pro přesun dat z místního serveru do Azure Storage. Informace o použití data factory s SQL Analytics, najdete [v tématu Načítání dat pro SQL Analytics](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Připravte data pro načítání

Možná budete muset připravit a vyčistit data v účtu úložiště před načtením. Přípravu dat lze provést, když jsou vaše data ve zdroji, při exportu dat do textových souborů nebo po data je ve službě Azure Storage.  Nejjednodušší je pracovat s daty co nejdříve v procesu, jak je to možné.  

### <a name="define-external-tables"></a>Definování externích tabulek

Pokud používáte PolyBase, je třeba definovat externí tabulky ve fondu SQL před načtením. Externí tabulky nejsou vyžadovány příkazem COPY. PolyBase používá externí tabulky k definování a přístupu k datům ve službě Azure Storage.

Externí tabulka je podobná zobrazení databáze. Externí tabulka obsahuje schéma tabulky a odkazuje na data, která jsou uložena mimo fond SQL.

Definování externích tabulek zahrnuje určení zdroje dat, formátu textových souborů a definic tabulek. T-SQL odkaz na syntaxi články, které budete potřebovat, jsou:

- [VYTVOŘENÍ EXTERNÍHO ZDROJE DAT](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [VYTVOŘIT EXTERNÍ TABULKU](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Při načítání parket je mapování datového typu SQL:

| **Datový typ parket** | **Datový typ SQL** |
| :-------------------: | :---------------: |
|        tinyint        |      tinyint      |
|       smallint        |     smallint      |
|          int          |        int        |
|        bigint         |      bigint       |
|        Boolean        |        bitové        |
|        double         |       float       |
|         float         |       reálná        |
|        double         |       Peníze       |
|        double         |    Smallmoney     |
|        řetězec         |       Nchar       |
|        řetězec         |     nvarchar      |
|        řetězec         |       char        |
|        řetězec         |      varchar      |
|        binární         |      binární       |
|        binární         |     Varbinary     |
|       časové razítko       |       date        |
|       časové razítko       |   Smalldatetime   |
|       časové razítko       |     datetime2     |
|       časové razítko       |     datetime      |
|       časové razítko       |       time        |
|         date          |       date        |
|        decimal        |      decimal      |

Příklad vytváření externích objektů najdete v kroku [Vytvoření externích tabulek](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) v kurzu načítání.

### <a name="format-text-files"></a>Formátování textových souborů

Pokud používáte PolyBase, definované externí objekty musí zarovnat řádky textových souborů s definicí externí tabulky a formátu souboru. Data v každém řádku textového souboru musí být zarovnána s definicí tabulky.
Formátování textových souborů:

- Pokud data pocházejí z nerelačního zdroje, je třeba je převést na řádky a sloupce. Bez ohledu na to, zda data pocházejí z relačního nebo nerelačního zdroje, musí být data transformována tak, aby byla zarovnána s definicemi sloupců pro tabulku, do které chcete data načíst.
- Formátováním dat v textovém souboru zarovnáte sloupce a datové typy v cílové tabulce. Nesouosost mezi datovými typy v externích textových souborech a tabulkové tabulce SQL způsobí, že řádky budou během načítání odmítnuty.
- Oddělte pole v textovém souboru zakončením.  Nezapomeňte použít znak nebo posloupnost znaků, která nebyla nalezena ve zdrojových datech. Použijte zakončení, které jste zadali pomocí [funkce CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. Načíst data pomocí Příkazu PolyBase nebo příkazu COPY

Je vhodné načíst data do pracovní tabulky. Pracovní tabulky umožňují zpracovávat chyby bez zasahování do produkčních tabulek. Pracovní tabulka také umožňuje použít MPP fondu SQL pro transformace dat před vložením dat do produkčních tabulek.

Tabulka bude muset být předem vytvořena při načítání do pracovní tabulky s COPY.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>Možnosti načítání s příkazem PolyBase a COPY

Chcete-li načíst data pomocí funkce PolyBase, můžete použít některou z těchto možností načítání:

- [PolyBase s T-SQL](load-data-from-azure-blob-storage-using-polybase.md) funguje dobře, když vaše data jsou v úložišti objektů Blob Azure nebo Azure Data Lake Store. Poskytuje největší kontrolu nad procesem načítání, ale také vyžaduje definování externích datových objektů. Ostatní metody definují tyto objekty na pozadí při mapování zdrojových tabulek na cílové tabulky.  Chcete-li organizovat zatížení T-SQL, můžete použít Azure Data Factory, SSIS nebo Azure funkce.
- [PolyBase s SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) funguje dobře, když jsou zdrojová data na SQL Serveru, buď místně sql servernebo v cloudu. SSIS definuje zdroj mapování cílové tabulky a také orchestruje zatížení. Pokud již máte balíčky SSIS, můžete upravit balíčky pro práci s novým cílem datového skladu.
- [Příkaz PolyBase a COPY s Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) je další nástroj orchestrace.  Definuje kanál a plánuje úlohy.
- [PolyBase s Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) přenáší data z tabulky do datového rámce Databricks a/nebo zapisuje data z datového rámce Databricks do tabulky pomocí PolyBase.

### <a name="other-loading-options"></a>Další možnosti načítání

Kromě PolyBase a příkazu COPY můžete použít [bcp](https://docs.microsoft.com/sql/tools/bcp-utility?view=sql-server-ver15) nebo [SqlBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). bcp načte přímo do databáze bez procházení úložiště objektů Blob Azure a je určen pouze pro malé zatížení.

> [!NOTE]
> Všimněte si, že výkon zatížení těchto možností je pomalejší než PolyBase a příkaz COPY.

## <a name="5-transform-the-data"></a>5. Transformace dat

Zatímco data jsou v pracovní tabulce, proveďte transformace, které vaše úloha vyžaduje. Potom přesuňte data do produkční tabulky.

## <a name="6-insert-the-data-into-production-tables"></a>6. Vložení dat do výrobních tabulek

Vložit do ... Příkaz SELECT přesune data z pracovní tabulky do trvalé tabulky.

Při navrhování procesu ETL zkuste proces spustit na malém testovacím vzorku. Zkuste extrahovat 1000 řádků z tabulky do souboru, přesuňte ho do Azure a zkuste ho načíst do pracovní tabulky.

## <a name="partner-loading-solutions"></a>Řešení pro nakládku partnerů

Mnoho našich partnerů má řešení pro nakládání. Další informace naleznete v seznamu našich [partnerů pro řešení](sql-data-warehouse-partner-business-intelligence.md).

## <a name="next-steps"></a>Další kroky

Pokyny k načítání najdete v tématu [Doprovodné materiály k načítání dat](guidance-for-loading-data.md).
