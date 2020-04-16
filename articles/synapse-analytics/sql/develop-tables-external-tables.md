---
title: Použití externích tabulek se synapse SQL
description: Čtení nebo zápis datových souborů pomocí Synapse SQL
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 4d13d15fe950c89687acfca355d4ed183756536a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423975"
---
# <a name="use-external-tables-with-synapse-sql"></a>Použití externích tabulek se synapse SQL

Externí tabulka odkazuje na data umístěná v Hadoopu, objektu blob azure storage nebo Azure Data Lake Storage. Externí tabulky se používají ke čtení dat ze souborů nebo zápisu dat do souborů ve službě Azure Storage. S Synapse SQL můžete použít externí tabulky ke čtení a zápisu dat do fondu SQL nebo SQL na vyžádání (náhled).

## <a name="external-tables-in-sql-pool"></a>Externí tabulky ve fondu SQL

Ve fondu SQL můžete použít externí tabulku k:

- Dotaz Azure Blob Storage a Azure Data Lake Gen2 s příkazy Transact-SQL.
- Importujte a uklápěte data z Azure Blob Storage a Azure Data Lake Storage do fondu SQL.

Při použití ve spojení s [příkazem CREATE TABLE AS SELECT](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) importuje výběr z externí tabulky data do tabulky v rámci fondu SQL. Kromě [příkazu COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)jsou externí tabulky užitečné pro načítání dat. Kurz načítání najdete v [tématu Použití PolyBase k načtení dat z azure blob storage](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="external-tables-in-sql-on-demand-preview"></a>Externí tabulky v SQL na vyžádání (náhled)

Pro SQL na vyžádání, budete používat externí tabulku:

- Dotazování na data ve službě Azure Blob Storage nebo Azure Data Lake Storage pomocí příkazů Transact-SQL
- Ukládejte výsledky dotazů SQL na vyžádání do souborů v Azure Blob Storage nebo Azure Data Lake Storage pomocí [CETAS](develop-tables-cetas.md).

Externí tabulky můžete vytvářet pomocí sql na vyžádání pomocí následujících kroků:

1. VYTVOŘENÍ EXTERNÍHO ZDROJE DAT
2. CREATE EXTERNAL FILE FORMAT
3. VYTVOŘIT EXTERNÍ TABULKU

## <a name="create-external-data-source"></a>VYTVOŘENÍ EXTERNÍHO ZDROJE DAT

Externí zdroje dat se používají k připojení k účtům úložiště. Kompletní dokumentace je popsána [zde](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="syntax-for-create-external-data-source"></a>Syntaxe pro vytvoření externího datového zdroje

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>' )
[;]
```

## <a name="arguments-for-create-external-data-source"></a>Argumenty pro vytvoření externího datového zdroje

data_source_name -Určuje uživatelem definovaný název zdroje dat. Název musí být jedinečný v rámci databáze.

UMÍSTĚNÍ `'<prefix>://<path>'` = - Poskytuje protokol připojení a cestu k externímu zdroji dat. Cesta může obsahovat kontejner ve `'<prefix>://<path>/container'`formě aplikace a složku `'<prefix>://<path>/container/folder'`ve formě aplikace .

| Externí zdroj dat        | Předpona umístění | Cesta k umístění                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Blob Storage          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Úložiště azure datových jezer Gen 1 | `adl`           | `<storage_account>.azuredatalake.net`                 |
| Úložiště Azure Data Lake Gen 2 | `abfs[s]`       | `<container>@<storage_account>.dfs.core.windows.net`  |

## <a name="example-for-create-external-data-source"></a>Příklad pro VYTVOŘENÍ EXTERNÍHO ZDROJE DAT

Následující příklad vytvoří externí zdroj dat pro Azure Data Lake Gen2 ukazující na newyorskou datovou sadu:

```sql
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://newyorktaxidataset.azuredatalakestore.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Vytvoří externí objekt ve formátu souboru, který definuje externí data uložená v Azure Blob Storage nebo Azure Data Lake Storage. Vytvoření externího formátu souboru je předpokladem pro vytvoření externí tabulky. Kompletní dokumentace je [zde](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Vytvořením externího formátu souboru určíte skutečné rozložení dat, na která externí tabulka odkazuje.

## <a name="syntax-for-create-external-file-format"></a>Syntaxe pro vytvoření externího formátu souboru

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
}
```

## <a name="arguments-for-create-external-file-format"></a>Argumenty pro vytvoření externího formátu souboru

file_format_name- Určuje název pro externí formát souboru.

FORMAT_TYPE = [ PARKETY | DELIMITEDTEXT]- Určuje formát externích dat.

- PARKETy - Určuje formát parket.
- DELIMITEDTEXT - Určuje textový formát s oddělovači sloupců, nazývanými také zakončení polí.

FIELD_TERMINATOR = *field_terminator* - Platí pouze pro oddělené textové soubory. Zakončení pole určuje jeden nebo více znaků, které označují konec každého pole (sloupce) v souboru odděleném textem. Výchozí hodnota je znak kanálu (|?).

Příklady:

- FIELD_TERMINATOR = |.
- FIELD_TERMINATOR = '
- FIELD_TERMINATOR =

STRING_DELIMITER = *string_delimiter* - Určuje zakončení pole pro data typu řetězce v souboru odděleném textem. Oddělovač řetězců má délku jeden nebo více znaků a je uzavřen s jednoduchými uvozovkami. Výchozí hodnota je prázdný řetězec ("").

Příklady:

- STRING_DELIMITER = "".
- STRING_DELIMITER = '*'
- STRING_DELIMITER =

FIRST_ROW = *First_row_int* - Určuje číslo řádku, který je přečten jako první a platí pro všechny soubory. Nastavení hodnoty na dva způsobí, že první řádek v každém souboru (řádek záhlaví) přeskočen, když je načtena data. Řádky jsou přeskočeny na základě existence zakončení řádku (/r/n, /r, /n, /n).

USE_TYPE_DEFAULT = { TRUE | **FALSE** } - Určuje, jak zacházet s chybějícími hodnotami v oddělených textových souborech při načítání dat z textového souboru.

PRAVDA – Pokud načítáte data z textového souboru, uložte každou chybějící hodnotu pomocí datového typu výchozí hodnoty pro odpovídající sloupec v definici externí tabulky. Nahraďte například chybějící hodnotu:

- 0, pokud je sloupec definován jako číselný sloupec. Desítkové sloupce nejsou podporovány a způsobí chybu.
- Prázdný řetězec (""), pokud je sloupec sloupec řetězec.
- 1900-01-01, pokud je sloupec sloupec datum.

NEPRAVDA - Všechny chybějící hodnoty uloží jako null. Všechny hodnoty NULL, které jsou uloženy pomocí slova NULL v textovém souboru s oddělovači, jsou importovány jako řetězec NULL.

Kódování = {'UTF8' | 'UTF16'} - SQL on-demand může číst UTF8 a UTF16 kódované oddělené textové soubory.

DATA_COMPRESSION = *data_compression_method* - Tento argument určuje metodu komprese dat pro externí data. Při čtení z externích tabulek je ignorována. Používá se pouze při zápisu do externích tabulek pomocí [CETAS](develop-tables-cetas.md).

Typ formátu parketového souboru podporuje následující metody komprese:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

## <a name="example-for-create-external-file-format"></a>Příklad pro VYTVOŘENÍ EXTERNÍHO FORMÁTU SOUBORU

Následující příklad vytvoří externí formát souboru pro soubory sčítání lidu:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>VYTVOŘIT EXTERNÍ TABULKU

Příkaz CREATE EXTERNAL TABLE vytvoří externí tabulku pro Synapse SQL pro přístup k datům uloženým v Azure Blob Storage nebo Azure Data Lake Storage. 

## <a name="syntax-for-create-external-table"></a>Syntaxe pro vytvoření externí tabulky

```syntaxsql
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH (
        LOCATION = 'folder_or_filepath',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name
    )  
[;]  

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]
```

## <a name="arguments-create-external-table"></a>Argumenty VYTVOŘIT EXTERNÍ TABULKU

*{ database_name.schema_name.table_name | schema_name.table_name | table_name }*

Název tabulky jedné až tří částí, který chcete vytvořit. Pro externí tabulku sql na vyžádání ukládá pouze metadata tabulky. Žádná skutečná data jsou přesunuta nebo uložena v SQL na vyžádání.

<column_definition>, ... *n* ]

CREATE EXTERNAL TABLE podporuje možnost konfigurovat název sloupce, datový typ, nullability a řazení. Výchozí omezení nelze použít u externích tabulek.

>[!IMPORTANT]
>Definice sloupců, včetně datových typů a počtu sloupců, musí odpovídat datům v externích souborech. Pokud dojde k neshodě, řádky souboru budou odmítnuty při dotazování na skutečná data.

Při čtení z parketových souborů můžete zadat pouze sloupce, které chcete číst, a zbytek přeskočit.

UMÍSTĚNÍ = '*folder_or_filepath*'

Určuje složku nebo cestu k souboru a název souboru pro skutečná data v úložišti objektů blob Azure. Umístění začíná z kořenové složky. Kořenová složka je umístění dat zadané v externím zdroji dat.

Pokud zadáte složku UMÍSTĚNÍ, dotaz SQL na vyžádání vybere z externí tabulky a načte soubory ze složky.

> [!NOTE]
> Na rozdíl od Hadoop a PolyBase SQL na vyžádání nevrací podsložky. Vrátí soubory, pro které název souboru začíná podtržením (_) nebo tečkou (.).

V tomto příkladu pokud LOCATION='/webdata/', dotaz SQL na vyžádání, vrátí řádky z mydata.txt a _hidden.txt. Nevrátí mydata2.txt a mydata3.txt, protože jsou umístěny v podsložce.

![Rekurzivní data pro externí tabulky](./media/develop-tables-external-tables/folder-traversal.png)

DATA_SOURCE = *external_data_source_name* - Určuje název externího zdroje dat, který obsahuje umístění externích dat. Chcete-li vytvořit externí zdroj dat, použijte [použít create external data source](#create-external-data-source).

FILE_FORMAT = *external_file_format_name* - Určuje název objektu ve formátu externího souboru, který ukládá typ souboru a metodu komprese pro externí data. Chcete-li vytvořit externí formát souboru, použijte [příkaz VYTVOŘIT EXTERNÍ FORMÁT SOUBORU](#create-external-file-format).

## <a name="permissions-create-external-table"></a>Oprávnění vytvořit externí tabulku

Chcete-li vybrat z externí tabulky, potřebujete správná pověření se seznamem a oprávněními ke čtení.

## <a name="example-create-external-table"></a>Příklad VYTVOŘENÍ EXTERNÍ TABULKY

Následující příklad vytvoří externí tabulku. Vrátí první řádek:

```sql
CREATE EXTERNAL TABLE census_external_table
(
    decennialTime varchar(20),
    stateName varchar(100),
    countyName varchar(100),
    population int,
    race varchar(50),
    sex    varchar(10),
    minAge int,
    maxAge int
)  
WITH (
    LOCATION = '/parquet/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)
GO

SELECT TOP 1 * FROM census_external_table
```

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Vytváření a dotazování externích tabulek ze souboru v Azure Data Lake

Pomocí možností průzkumu datového jezera můžete nyní vytvořit a zadat dotaz na externí tabulku pomocí fondu SQL nebo SQL na vyžádání s jednoduchým kliknutím pravým tlačítkem myši na soubor.

## <a name="prerequisites"></a>Požadavky

- Musíte mít přístup k pracovnímu prostoru s alespoň rolí přístupu k přístupu k serveru ADLS Gen2 s rolí přístupu k přístupu k datům objektu BLOB

- Musíte mít alespoň [oprávnění k vytváření](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#permissions-2) a dotazování externích tabulek ve fondu SQL nebo SQL OD

- Propojená služba přidružená k účtu ADLS Gen2 **musí mít přístup k souboru**. Pokud je například mechanismus ověřování propojené služby Spravovaná identita, musí mít spravovaná identita pracovního prostoru alespoň oprávnění pro čtení objektů blob úložiště v účtu úložiště.

Na panelu Data vyberte soubor, ze kterého chcete vytvořit externí tabulku:
> [!div class="mx-imgBorder"]
>![externaltable1](./media/develop-tables-external-tables/external-table-1.png)

Otevře se dialogové okno. Vyberte fond SQL nebo SQL na vyžádání, pojmenujte tabulku a vyberte otevřít skript:

> [!div class="mx-imgBorder"]
>![externaltable2](./media/develop-tables-external-tables/external-table-2.png)

Skript SQL je automaticky generován a vyvozuje schéma ze souboru:
> [!div class="mx-imgBorder"]
>![externaltable3](./media/develop-tables-external-tables/external-table-3.png)

Spusťte skript. Skript se automaticky spustí Select Top 100 *.:
> [!div class="mx-imgBorder"]
>![externaltable4](./media/develop-tables-external-tables/external-table-4.png)

Externí tabulka je nyní vytvořena, pro budoucí zkoumání obsahu této externí tabulky může uživatel zadat dotaz přímo z podokna Data:
> [!div class="mx-imgBorder"]
>![externaltable5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>Další kroky

Podívejte se do článku [CETAS,](develop-tables-cetas.md) jak uložit výsledky dotazu do externí tabulky ve službě Azure Storage. Nebo můžete začít dotazovat [tabulek Spark](develop-storage-files-spark-tables.md).
