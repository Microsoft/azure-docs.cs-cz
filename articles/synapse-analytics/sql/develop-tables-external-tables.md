---
title: Použití externích tabulek s synapse SQL
description: Čtení nebo zápis datových souborů pomocí synapse SQL
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 6c76fcc0fefdf8aa3ae97a4c131481f7ea6ada81
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91288847"
---
# <a name="use-external-tables-with-synapse-sql"></a>Použití externích tabulek s synapse SQL

Externí tabulka odkazuje na data umístěná v Hadoop, Azure Storage BLOB nebo Azure Data Lake Storage. Externí tabulky se používají ke čtení dat ze souborů nebo zápisu dat do souborů v Azure Storage. Pomocí synapse SQL můžete použít externí tabulky ke čtení a zápisu dat do fondu SQL nebo na vyžádání SQL (Preview).

## <a name="external-tables-in-synapse-sql-pool-and-on-demand"></a>Externí tabulky ve fondu SQL synapse a na vyžádání

### <a name="sql-pool"></a>[Fond SQL](#tab/sql-pool) 

Ve fondu SQL můžete použít externí tabulku k těmto akcím:

- Dotazování Azure Blob Storage a Azure Data Lake Gen2 pomocí příkazů jazyka Transact-SQL.
- Importuje a ukládá data z Azure Blob Storage a Azure Data Lake Storage do fondu SQL.

Při použití ve spojení s příkazem [Create Table jako SELECT](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) , výběr z externí tabulky importuje data do tabulky v rámci fondu SQL. Kromě [příkazu copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)jsou externí tabulky užitečné pro načítání dat. 

Kurz načítání najdete v tématu [použití základny k načtení dat z Azure Blob Storage](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="sql-on-demand"></a>[SQL na vyžádání](#tab/sql-on-demand)

V případě SQL na vyžádání použijete externí tabulku k těmto akcím:

- Dotazování na data ve službě Azure Blob Storage nebo Azure Data Lake Storage pomocí příkazů jazyka Transact-SQL
- Uložení výsledků dotazů na vyžádání SQL do souborů v Azure Blob Storage nebo Azure Data Lake Storage pomocí [CETAS](develop-tables-cetas.md)

Externí tabulky můžete vytvořit pomocí SQL na vyžádání pomocí následujících kroků:

1. VYTVOŘIT EXTERNÍ ZDROJ DAT
2. CREATE EXTERNAL FILE FORMAT
3. VYTVOŘIT EXTERNÍ TABULKU

---

### <a name="security"></a>Zabezpečení

Uživatel musí mít `SELECT` oprávnění k externí tabulce pro čtení dat.
Externí tabulka přistupuje k základní službě Azure Storage pomocí oboru pověření definovaného ve zdroji dat s použitím následujících pravidel:
- Zdroj dat bez přihlašovacích údajů umožňuje externím tabulkám přistupovat k veřejně dostupným souborům v Azure Storage.
- Zdroj dat může mít přihlašovací údaje, které umožňuje externím tabulkám přistupovat k souborům v Azure Storage pomocí tokenu SAS nebo spravované identity v pracovním prostoru. Příklady najdete [v článku vývoj souborů úložiště pro přístup do úložiště](develop-storage-files-storage-access-control.md#examples) .

> [!IMPORTANT]
> V rámci fondu SQL umožňuje DataSource bez creadential uživateli Azure AD přístup k souborům úložiště pomocí své identity Azure AD. V SQL na vyžádání potřebujete vytvořit zdroj dat s přihlašovacími údaji pro databáze s rozsahem, který obsahuje `IDENTITY='User Identity'` vlastnost – [tady najdete příklady](develop-storage-files-storage-access-control.md#examples).

## <a name="create-external-data-source"></a>VYTVOŘIT EXTERNÍ ZDROJ DAT

Externí zdroje dat slouží k připojení k účtům úložiště. [Tady](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)je popsaný kompletní dokumentace.

### <a name="syntax-for-create-external-data-source"></a>Syntaxe pro vytvoření externího zdroje dat

#### <a name="sql-pool"></a>[Fond SQL](#tab/sql-pool)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
     , TYPE = HADOOP
)
[;]
```

#### <a name="sql-on-demand"></a>[SQL na vyžádání](#tab/sql-on-demand)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
)
[;]
```

---

### <a name="arguments-for-create-external-data-source"></a>Argumenty pro vytvoření externího zdroje dat

data_source_name

Určuje uživatelsky definovaný název pro zdroj dat. Název musí být v rámci databáze jedinečný.

#### <a name="location"></a>Umístění
LOCATION = `'<prefix>://<path>'`   – poskytuje protokol připojení a cestu k externímu zdroji dat. V umístění lze použít následující vzory:

| Externí zdroj dat        | Předpona umístění | Cesta k umístění                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Blob Storage          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Azure Blob Storage          | `http[s]`       | `<storage_account>.blob.core.windows.net/<container>/subfolders` |
| Azure Data Lake Store Gen 1 | `http[s]`       | `<storage_account>.azuredatalakestore.net/webhdfs/v1` |
| Azure Data Lake Store Gen 2 | `http[s]`       | `<storage_account>.dfs.core.windows.net/<container>/subfolders`  |

`https:` prefix umožňuje použít v cestě podsložku.

#### <a name="credential"></a>Přihlašovací údaj
CREDENTIAL = `<database scoped credential>` je volitelné přihlašovací údaje, které se použijí k ověření v Azure Storage. Externí zdroj dat bez přihlašovacích údajů má přístup ke veřejnému účtu úložiště. 

Externí zdroje dat bez přihlašovacích údajů ve fondu SQL můžou k přístupu k souborům v úložišti používat taky identitu Azure AD. Externí zdroj dat s přihlašovacími údaji, který je zadaný v přihlašovacích údajích pro přístup k souborům
- V rámci fondu SQL může pověření v oboru databáze určovat identitu vlastní aplikace, spravovanou identitu pracovního prostoru nebo SAK klíč. 
- V SQL na vyžádání můžou přihlašovací údaje vymezené databází určovat identitu Azure AD s rozsahem, spravovanou identitu nebo klíč SAS volajícího. 

#### <a name="type"></a>TYP
TYPE = `HADOOP` je povinná možnost ve fondu SQL a určuje, že se pro přístup k podkladovým souborům používá technologie založené na základech. Tento parametr se nedá použít ve službě SQL na vyžádání, která používá vestavěnou nativní čtečku.

### <a name="example-for-create-external-data-source"></a>Příklad pro vytvoření externího zdroje dat

#### <a name="sql-pool"></a>[Fond SQL](#tab/sql-pool)

Následující příklad vytvoří externí zdroj dat pro Azure Data Lake Gen2 odkazuje na datovou sadu New York:

```sql
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://data@newyorktaxidataset.dfs.core.windows.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

#### <a name="sql-on-demand"></a>[SQL na vyžádání](#tab/sql-on-demand)

Následující příklad vytvoří externí zdroj dat pro Azure Data Lake Gen2, ke kterému lze přistupovat pomocí pověření SAS:

```sql
CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO

CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

Následující příklad vytvoří externí zdroj dat, který Azure Data Lake Gen2 odkazuje na veřejně dostupnou datovou sadu New York:

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
```
---

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Vytvoří objekt externího formátu souboru, který definuje externí data uložená v Azure Blob Storage nebo Azure Data Lake Storage. Vytvoření externího formátu souboru je předpokladem pro vytvoření externí tabulky. Kompletní dokumentaci [najdete tady](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Vytvořením formátu externího souboru zadáte skutečné rozložení dat, na která odkazuje externí tabulka.

### <a name="syntax-for-create-external-file-format"></a>Syntaxe pro formát vytvoření externího souboru

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
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' ]
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

### <a name="arguments-for-create-external-file-format"></a>Argumenty pro formát vytvoření externího souboru

file_format_name – Určuje název formátu externího souboru.

FORMAT_TYPE = [PARQUET | DELIMITEDTEXT] – Určuje formát externích dat.

- PARQUET – určuje formát Parquet.
- DELIMITEDTEXT – určuje textový formát s oddělovači sloupců, označovaný také jako ukončovací znaky polí.

FIELD_TERMINATOR = *field_terminator* – platí pouze pro textové soubory s oddělovači. Ukončovací znak pole určuje jeden nebo více znaků, které označují konec každého pole (sloupec) v souboru s hodnotami oddělenými textem. Výchozí hodnota je znak svislé čáry (ꞌ | ꞌ).

Příklady:

- FIELD_TERMINATOR = |
- FIELD_TERMINATOR = ' '
- FIELD_TERMINATOR = ꞌ \ t ꞌ

STRING_DELIMITER = *string_delimiter* – Určuje ukončovací znak pole pro data typu String v souboru s hodnotami oddělenými textem. Oddělovač řetězců je jeden nebo více znaků a je uzavřený s jednoduchými uvozovkami. Výchozí hodnota je prázdný řetězec ("").

Příklady:

- STRING_DELIMITER = ""
- STRING_DELIMITER = *
- STRING_DELIMITER = ꞌ, ꞌ

FIRST_ROW = *First_row_int* – Určuje číslo řádku, který se načte jako první a vztahuje se na všechny soubory. Nastavením hodnoty na hodnotu dvě dojde při načtení dat k přeskočení prvního řádku v každém souboru (řádek záhlaví). Řádky se přeskočí na základě existence zakončení řádku (/r/n,/r,/n).

USE_TYPE_DEFAULT = {TRUE | **False** } -Určuje, jak se mají zpracovat chybějící hodnoty v textových souborech s oddělovači při načítání dat z textového souboru.

TRUE – Pokud načítáte data z textového souboru, uložte všechny chybějící hodnoty pomocí datového typu výchozí hodnota pro odpovídající sloupec v definici externí tabulky. Například nahraďte chybějící hodnotu hodnotou:

- 0, pokud je sloupec definován jako číselný sloupec. Sloupce Decimal nejsou podporovány a způsobí chybu.
- Prázdný řetězec (""), pokud je sloupec sloupcem typu řetězec.
- 1900-01-01, pokud se jedná o sloupec data.

FALSE – uloží všechny chybějící hodnoty jako NULL. Všechny hodnoty NULL, které jsou uloženy pomocí slova NULL v textovém souboru s oddělovači, jsou importovány jako řetězec "NULL".

Encoding = {' UTF8 ' | ' UTF16 '} – SQL na vyžádání může číst textové soubory s oddělovači v kódování UTF8 a UTF16.

DATA_COMPRESSION = *data_compression_method* – tento argument určuje metodu komprese dat pro externí data. 

Typ formátu souboru PARQUET podporuje následující metody komprese:

- DATA_COMPRESSION = ' org. Apache. Hadoop. IO. Compress. GzipCodec '
- DATA_COMPRESSION = ' org. Apache. Hadoop. IO. Compress. SnappyCodec '

Při čtení z externích tabulek PARQUET je tento argument ignorován, ale používá se při zápisu do externích tabulek pomocí [CETAS](develop-tables-cetas.md).

Typ formátu souboru DELIMITEDTEXT podporuje následující kompresní metodu:

- DATA_COMPRESSION = ' org. Apache. Hadoop. IO. Compress. GzipCodec '

### <a name="example-for-create-external-file-format"></a>Příklad pro vytvoření formátu externího souboru

Následující příklad vytvoří externí formát souboru pro soubory sčítání:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>VYTVOŘIT EXTERNÍ TABULKU

Příkaz vytvořit externí tabulku vytvoří externí tabulku pro synapse SQL pro přístup k datům uloženým v Azure Blob Storage nebo Azure Data Lake Storage. 

### <a name="syntax-for-create-external-table"></a>Syntaxe pro vytvoření externí tabulky

```sql
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

### <a name="arguments-create-external-table"></a>Argumenty vytvoří externí tabulku.

*{název_databáze. schema_name. TABLE_NAME | schema_name. TABLE_NAME | TABLE_NAME}*

Název první ze tří částí tabulky, která se má vytvořit. V případě externí tabulky ukládá SQL na vyžádání pouze metadata tabulky. V SQL na vyžádání se nepřesunou ani neukládají žádná skutečná data.

<column_definition>,... *n* ]

Možnost vytvořit externí tabulku podporuje konfiguraci názvu sloupce, datového typu, možnosti použití hodnoty null a kolace. VÝCHOZÍ omezení nemůžete použít u externích tabulek.

>[!IMPORTANT]
>Definice sloupců, včetně datových typů a počtu sloupců, musí odpovídat datům v externích souborech. Pokud dojde k neshodě, řádky souboru budou odmítnuty při dotazování na skutečná data.

Při čtení ze souborů Parquet můžete zadat pouze sloupce, které chcete číst, a přeskočit zbytek.

LOCATION = '*folder_or_filepath*'

Určuje složku, cestu k souboru a název souboru pro skutečná data v Azure Blob Storage. Umístění začíná od kořenové složky. Kořenová složka je umístění dat zadané v externím zdroji dat.

Pokud zadáte umístění složky, dotaz na vyžádání SQL se vybere z externí tabulky a načte soubory ze složky.

> [!NOTE]
> Na rozdíl od Hadoop a báze SQL na vyžádání nevrací podsložky. Vrátí soubory, pro které název souboru začíná podtržítkem (_) nebo tečkou (.).

V tomto příkladu, pokud LOCATION = '/WebData/', dotaz na vyžádání SQL, vrátí řádky z mydata.txt a _hidden.txt. Nevrátí mydata2.txt a mydata3.txt, protože jsou umístěné v podsložce.

![Rekurzivní data pro externí tabulky](./media/develop-tables-external-tables/folder-traversal.png)

DATA_SOURCE = *external_data_source_name* – Určuje název externího zdroje dat, který obsahuje umístění externích dat. Chcete-li vytvořit externí zdroj dat, použijte příkaz [vytvořit externí zdroj dat](#create-external-data-source).

FILE_FORMAT = *external_file_format_name* – Určuje název objektu externího souboru formátu, který ukládá typ souboru a kompresní metodu pro externí data. Chcete-li vytvořit externí formát souboru, použijte příkaz [Create External File Format](#create-external-file-format).

### <a name="permissions-create-external-table"></a>Oprávnění vytvořit externí tabulku

Chcete-li vybrat z externí tabulky, budete potřebovat správné přihlašovací údaje se seznamem a oprávněními ke čtení.

### <a name="example-create-external-table"></a>Příklad vytvoření externí tabulky

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

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Vytvoření a dotazování externích tabulek ze souboru v Azure Data Lake

Pomocí Data Lake možností průzkumu teď můžete vytvořit a zadat dotaz na externí tabulku pomocí fondu SQL nebo SQL na vyžádání s jednoduchým kliknutím pravým tlačítkem myši na soubor.

### <a name="prerequisites"></a>Předpoklady

- Musíte mít přístup k pracovnímu prostoru, který má alespoň roli přístupu pro přispěvatele dat objektů BLOB úložiště k účtu ADLS Gen2.

- Musíte mít aspoň [oprávnění k vytváření](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#permissions-2&preserve-view=true) a dotazování externích tabulek ve fondu SQL nebo v SQL z.

- Propojená služba přidružená k ADLS Gen2mu účtu **musí mít přístup k tomuto souboru**. Pokud má například mechanismus ověřování propojených služeb spravovanou identitu, musí mít spravovaná identita v pracovním prostoru aspoň oprávnění pro čtení objektů BLOB úložiště v účtu úložiště.

Z panelu data vyberte soubor, ze kterého chcete vytvořit externí tabulku:
> [!div class="mx-imgBorder"]
>![externaltable1](./media/develop-tables-external-tables/external-table-1.png)

Otevře se dialogové okno. Vyberte možnost fond SQL nebo SQL na vyžádání, zadejte název tabulky a vyberte otevřít skript:

> [!div class="mx-imgBorder"]
>![externaltable2](./media/develop-tables-external-tables/external-table-2.png)

Skript SQL automaticky vygeneroval odvození schématu ze souboru:
> [!div class="mx-imgBorder"]
>![externaltable3](./media/develop-tables-external-tables/external-table-3.png)

Spusťte skript. Skript automaticky spustí výběr Top 100 *.:
> [!div class="mx-imgBorder"]
>![externaltable4](./media/develop-tables-external-tables/external-table-4.png)

Externí tabulka je teď vytvořená, aby ji uživatel mohl příště dotazovat na obsah této externí tabulky, a to přímo z podokna data:
> [!div class="mx-imgBorder"]
>![externaltable5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>Další kroky

V článku [CETAS](develop-tables-cetas.md) najdete informace o tom, jak uložit výsledky dotazu do externí tabulky v Azure Storage. Můžete také spustit dotazování [Apache Spark pro externí tabulky Azure synapse](develop-storage-files-spark-tables.md).
