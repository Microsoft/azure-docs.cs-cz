---
title: VYTVOŘENÍ externí tabulky jako SELECT (CETAS) v synapse SQL
description: Použití možnost vytvořit externí tabulku jako SELECT (CETAS) s synapse SQL
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 09/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 23b74c06ebd45092b9efe36a870eeac7f6fb828a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677561"
---
# <a name="cetas-with-synapse-sql"></a>CETAS s synapse SQL

Pokud chcete dokončit následující úlohy, můžete použít možnost vytvořit externí tabulku jako SELECT (CETAS) ve vyhrazeném fondu SQL nebo bez serveru SQL.  

- Vytvoření externí tabulky
- Exportujte paralelně výsledky příkazu SELECT jazyka Transact-SQL pro:

  - Hadoop
  - Azure Storage Blob
  - Azure Data Lake Storage Gen2

## <a name="cetas-in-dedicated-sql-pool"></a>CETAS ve vyhrazeném fondu SQL

V případě vyhrazeného fondu SQL CETAS použití a syntaxe zaškrtněte políčko [vytvořit externí tabulku jako](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) článek. Další informace o pokynech k CTAS s využitím vyhrazeného fondu SQL najdete v článku [CREATE TABLE AS Select](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) .

## <a name="cetas-in-serverless-sql-pool"></a>CETAS ve fondu SQL bez serveru

Pokud používáte fond SQL bez serveru, CETAS se používá k vytvoření externí tabulky a k exportu výsledků dotazu pro Azure Storage Blob nebo Azure Data Lake Storage Gen2.

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

*[[ *database_name* . [ *schema_name* ]. ] | *schema_name* . ] *TABLE_NAME**

Název první ze tří částí tabulky, která se má vytvořit. V případě externí tabulky je ve fondu SQL bez serveru uloženo pouze metadata tabulky. Ve fondu SQL bez serveru nejsou přesunutá ani uložená žádná skutečná data.

LOCATION = *' path_to_folder '*

Určuje, kam se mají zapsat výsledky příkazu SELECT na externím zdroji dat. Kořenová složka je umístění dat zadané v externím zdroji dat. UMÍSTĚNÍ musí ukazovat na složku a mít koncovou/. Příklad: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Určuje název objektu externího zdroje dat, který obsahuje umístění, kde budou externí data uložena. Chcete-li vytvořit externí zdroj dat, použijte příkaz [Create External data source (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source).

FILE_FORMAT = *external_file_format_name*

Určuje název objektu externího souboru formátu, který obsahuje formát pro externí datový soubor. Chcete-li vytvořit externí formát souboru, použijte příkaz [Create External File Format (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format). V současné době se podporují jenom formáty externích souborů s FORMAT_TYPE = PARQUET a FORMAT_TYPE = DELIMITEDTEXT. Komprese GZip pro formát DELIMITEDTEXT se nepodporuje.

S *<common_table_expression>*

Určuje dočasnou pojmenovanou sadu výsledků, která se označuje jako výraz běžné tabulky (CTE). Další informace naleznete v tématu [WITH common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?view=azure-sqldw-latest&preserve-view=true).

Vyberte <select_criteria>

Naplní novou tabulku výsledky příkazu SELECT. *select_criteria* je tělo příkazu SELECT, který určuje, která data se mají zkopírovat do nové tabulky. Informace o příkazech SELECT naleznete v tématu [Select (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true).

> [!NOTE]
> Klauzule ORDER BY v příkazu SELECT není pro CETAS podporována.

## <a name="permissions"></a>Oprávnění

Aby CETAS fungovalo, musíte mít oprávnění k výpisu obsahu složky a zápisu do složky umístění.

## <a name="examples"></a>Příklady

Tyto příklady používají CETAS k uložení celkového počtu obyvatel agregovaného po rocích a State do složky aggregated_data, která je umístěná ve zdroji dat population_ds.

Tato ukázka spoléhá na dříve vytvořené přihlašovací údaje, zdroj dat a externí formát souboru. Přečtěte si dokument [externí tabulky](develop-tables-external-tables.md) . Chcete-li uložit výsledky dotazu do jiné složky ve stejném zdroji dat, změňte argument umístění. 

Pokud chcete výsledky Uložit do jiného účtu úložiště, vytvořte a použijte jiný zdroj dat pro DATA_SOURCE argument.

> [!NOTE]
> Níže uvedené příklady používají veřejný účet úložiště Azure Open data. Je jen pro čtení. Chcete-li spustit tyto dotazy, je nutné zadat zdroj dat, pro který máte oprávnění k zápisu.

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

-- you can query the newly created external table
SELECT * FROM population_by_year_state
```

Následující příklad používá externí tabulku jako zdroj pro CETAS. Spoléhá se na přihlašovací údaje, zdroj dat, formát externích souborů a externí tabulku vytvořenou dříve. Přečtěte si dokument [externí tabulky](develop-tables-external-tables.md) .

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

-- you can query the newly created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>Podporované datové typy

CETAS lze použít k uložení sad výsledků s následujícími datovými typy SQL:

- binární
- varbinary
- char
- varchar
- nchar
- nvarchar
- smalldate
- date
- datetime
- datetime2
- DateTimeOffset
- time
- decimal
- numerické
- float
- real
- bigint
- tinyint
- smallint
- int
- bigint
- bit
- papír
- smallmoney
- uniqueidentifier

> [!NOTE]
> Objekty LOBs s větší než 1 MB nelze použít s CETAS.

## <a name="next-steps"></a>Další kroky

Zkuste zadat dotaz [na Apache Spark externích tabulek Azure synapse](develop-storage-files-spark-tables.md).
