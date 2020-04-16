---
title: CETAS v Synapse SQL
description: Použití CETAS se Synapse SQL
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 71bc20680467d270436e28190bb49db5b9313ca0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424024"
---
# <a name="cetas-with-synapse-sql"></a>CETAS se Synapse SQL

V fondu SQL nebo SQL na vyžádání (preview) můžete k dokončení následujících úkolů použít příkaz VYTVOŘIT EXTERNÍ TABULKU JAKO SELECT (CETAS):  

- Vytvoření externí tabulky
- Souběžně exportujte výsledky příkazu Transact-SQL SELECT do

  - Hadoop
  - Objekt blob úložiště Azure
  - Azure Data Lake Storage Gen2

## <a name="cetas-in-sql-pool"></a>CETAS ve fondu SQL

Pro fond SQL, využití CETAS a syntaxi, zkontrolujte [vytvořit externí tabulka jako výběr](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) článku. Kromě toho pokyny pro CTAS pomocí fondu SQL naleznete v článku [vytvořit tabulku jako select.](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="cetas-in-sql-on-demand"></a>CETAS v SQL na vyžádání

Při použití prostředku SQL na vyžádání se CETAS používá k vytvoření externí tabulky a exportu výsledků dotazu do objektu Blob úložiště Azure nebo Azure Data Lake Storage Gen2.

## <a name="syntax"></a>Syntaxe

```syntaxsql
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name
    WITH (
        LOCATION = 'path_to_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
)
    AS <select_statement>  
[;]

<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>
```

## <a name="arguments"></a>Argumenty

*[ *database_name* . *[schema_name]* . ] | *schema_name* . ] *table_name**

Název tabulky jedné až tří částí, který chcete vytvořit. Pro externí tabulku sql na vyžádání ukládá pouze metadata tabulky. Žádná skutečná data jsou přesunuta nebo uložena v SQL na vyžádání.

UMÍSTĚNÍ = *"path_to_folder"*

Určuje, kam se mají zapisovat výsledky příkazu SELECT do externího zdroje dat. Kořenová složka je umístění dat zadané v externím zdroji dat. UMÍSTĚNÍ musí přejděte na složku a mají koncové /. Příklad: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Určuje název externího objektu zdroje dat, který obsahuje umístění, kde budou uložena externí data. Chcete-li vytvořit externí zdroj dat, použijte [create external data source (Transact-SQL).](develop-tables-external-tables.md#create-external-data-source)

FILE_FORMAT = *external_file_format_name*

Určuje název objektu ve formátu externího souboru, který obsahuje formát externího datového souboru. Chcete-li vytvořit externí formát souboru, použijte [příkaz CREATE EXTERNAL FILE FORMAT (Transact-SQL).](develop-tables-external-tables.md#create-external-file-format) V současné době jsou podporovány pouze externí formáty souborů s FORMÁTEM='PARKETY'.

S *<common_table_expression>*

Určuje dočasnou pojmenovanou sadu výsledků, známou jako výraz společné tabulky (CTE). Další informace naleznete [v tématu WITH common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

SELECT <select_criteria>

Naplní novou tabulku výsledky z příkazu SELECT. *select_criteria* je tělo příkazu SELECT, který určuje, která data se mají kopírovat do nové tabulky. Informace o příkazech SELECT naleznete [v tématu SELECT (Transact-SQL).](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="permissions"></a>Oprávnění

Musíte mít oprávnění k tomu, abyste mohli vypsat obsah složky a zapisovat do složky UMÍSTĚNÍ, aby cetas fungoval.

## <a name="examples"></a>Příklady

Tyto příklady používají CETAS k uložení celkového počtu agregovaných podle roku a stavu do aggregated_data složky, která je umístěna ve zdroji dat population_ds.

Tato ukázka závisí na pověření, zdroj dat a externí formát souboru vytvořené dříve. Viz [dokument externích tabulek.](develop-tables-external-tables.md) Chcete-li uložit výsledky dotazu do jiné složky ve stejném zdroji dat, změňte argument UMÍSTĚNÍ. Chcete-li uložit výsledky do jiného účtu úložiště, vytvořte a použijte jiný zdroj dat pro DATA_SOURCE argument.

> [!NOTE]
> Ukázky, které následují, používají veřejný účet úložiště Open Data Storage. Je jen pro čtení. Chcete-li provést tyto dotazy, je třeba zadat zdroj dat, pro který máte oprávnění k zápisu.

```sql
-- use CETAS to export select statement with OPENROWSET result to  storage
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM
    OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=*/*.parquet',
    FORMAT='PARQUET') AS [r]
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

Níže uvedený příklad používá externí tabulku jako zdroj pro CETAS. Spoléhá se na pověření, zdroj dat, externí formát souboru a externí tabulku vytvořenou dříve. Viz [dokument externích tabulek.](develop-tables-external-tables.md)

```sql
-- use CETAS with select from external table
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM census_external_table
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>Podporované datové typy

CETAS lze použít k ukládání sad výsledků s následujícími datovými typy SQL:

- binární
- Varbinary
- char
- varchar
- date
- time
- datetime2
- decimal
- numerické
- float
- reálná
- bigint
- int
- smallint
- tinyint
- bitové

Loby nelze použít s CETAS.

V části SELECT CETAS nelze použít následující datové typy:

- Nchar
- nvarchar
- datetime
- Smalldatetime
- Datetimeoffset
- Peníze
- Smallmoney
- uniqueidentifier

## <a name="next-steps"></a>Další kroky

Můžete zkusit dotazování [tabulek Spark](develop-storage-files-spark-tables.md).
