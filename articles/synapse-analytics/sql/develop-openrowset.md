---
title: Jak používat OPENROWSET na vyžádání SQL (Preview)
description: Tento článek popisuje syntaxi OPENROWSET v SQL na vyžádání (Preview) a vysvětluje, jak používat argumenty.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: e541a5620d4f263e5e1379b364d7c7dd9a97a331
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91289017"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Jak používat OPENROWSET s SQL na vyžádání (Preview)

`OPENROWSET(BULK...)`Funkce umožňuje přístup k souborům v Azure Storage. `OPENROWSET` funkce přečte obsah vzdáleného zdroje dat (například soubor) a vrátí obsah jako sadu řádků. V rámci prostředku SQL na vyžádání (Preview) je k dispozici zprostředkovatel hromadné sady řádků OPENROWSET voláním funkce OPENROWSET a určením hromadné možnosti.  

Na `OPENROWSET` funkci lze odkazovat v `FROM` klauzuli dotazu, jako by šlo o název tabulky `OPENROWSET` . Podporuje hromadné operace prostřednictvím integrovaného HROMADNÉho poskytovatele, který umožňuje číst data ze souboru a vracet je jako sadu řádků.

## <a name="data-source"></a>Zdroj dat

Funkce OPENROWSET v synapse SQL čte obsah souborů ze zdroje dat. Zdroj dat je účet úložiště Azure, ve kterém se dá explicitně odkazovat nebo se dá `OPENROWSET` dynamicky odvodit z adresy URL souborů, které chcete číst.
`OPENROWSET`Funkce může volitelně obsahovat `DATA_SOURCE` parametr pro určení zdroje dat, který obsahuje soubory.
- `OPENROWSET` bez toho se `DATA_SOURCE` dá použít k přímému čtení obsahu souborů z umístění adresy URL zadaného jako `BULK` možnost:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://<storage account>.dfs.core.windows.net/container/folder/*.parquet',
                    FORMAT = 'PARQUET') AS file
    ```

Toto je rychlý a snadný způsob, jak číst obsah souborů bez předchozí konfigurace. Tato možnost umožňuje použít možnost základního ověřování pro přístup k úložišti (Passthrough Azure AD pro přihlašovací údaje Azure AD a token SAS pro přihlašovací údaje SQL). 

- `OPENROWSET``DATA_SOURCE`k přístupu k souborům v zadaném účtu úložiště se dá použít:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    FORMAT = 'PARQUET') AS file
    ```


    Tato možnost umožňuje nakonfigurovat umístění účtu úložiště ve zdroji dat a zadat metodu ověřování, která se má použít pro přístup k úložišti. 
    
    > [!IMPORTANT]
    > `OPENROWSET` bez `DATA_SOURCE` poskytuje rychlý a snadný způsob, jak získat přístup k souborům úložiště, ale nabízí omezené možnosti ověřování. Například objekty zabezpečení Azure AD mají přístup k souborům jenom pomocí své [identity Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity) nebo veřejně dostupných souborů. Pokud potřebujete výkonnější možnosti ověřování, použijte `DATA_SOURCE` možnost a definujte přihlašovací údaje, které chcete použít pro přístup k úložišti.


## <a name="security"></a>Zabezpečení

Uživatel databáze musí mít `ADMINISTER BULK OPERATIONS` oprávnění k použití této `OPENROWSET` funkce.

Správce úložiště musí taky povolit uživateli přístup k souborům poskytnutím platného tokenu SAS nebo povolením objektu zabezpečení Azure AD pro přístup k souborům úložiště. Další informace o řízení přístupu k úložišti v [tomto článku](develop-storage-files-storage-access-control.md).

`OPENROWSET` k určení, jak ověřit úložiště, použijte následující pravidla:
- V nástroji `OPENROWSET` bez `DATA_SOURCE` mechanismu ověřování závisí na typu volajícího.
  - Všichni uživatelé můžou použít `OPENROWSET` bez `DATA_SOURCE` čtení veřejně dostupných souborů v Azure Storage.
  - Přihlášení Azure AD mají přístup k chráněným souborům pomocí vlastní [identity Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) , pokud Azure Storage umožňuje uživatelům Azure AD přístup k základním souborům (například pokud má volající `Storage Reader` oprávnění k úložišti Azure).
  - Přihlášení SQL můžete také použít `OPENROWSET` bez `DATA_SOURCE` přístupu k veřejně dostupným souborům, souborům chráněným pomocí tokenu SAS nebo spravované identitě synapse pracovního prostoru. Pro povolení přístupu k souborům úložiště by bylo potřeba [vytvořit přihlašovací údaje v oboru serveru](develop-storage-files-storage-access-control.md#examples) . 
- V `OPENROWSET` rámci `DATA_SOURCE` mechanismu ověřování je definována v přihlašovacích údajích, které jsou přiřazené k odkazovanému zdroji dat v rámci databáze. Tato možnost umožňuje přístup k veřejně dostupnému úložišti nebo přístup k úložišti pomocí tokenu SAS, spravované identity pracovního prostoru nebo [identity volajícího služby Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) (Pokud je volající objekt zabezpečení Azure AD). Pokud `DATA_SOURCE` odkazujete na úložiště Azure, které není veřejné, budete muset [vytvořit přihlašovací údaje v oboru databáze](develop-storage-files-storage-access-control.md#examples) a odkazovat na ni v `DATA SOURCE` , aby se povolil přístup k souborům úložiště.

Volající musí mít `REFERENCES` oprávnění k pověření, aby ho mohl použít k ověření úložiště.

## <a name="syntax"></a>Syntaxe

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
```

## <a name="arguments"></a>Argumenty

Máte dvě možnosti pro vstupní soubory, které obsahují cílová data pro dotazování. Platné hodnoty jsou:

- ' CSV ' – obsahuje libovolný oddělený textový soubor s oddělovači řádků nebo sloupců. Libovolný znak lze použít jako oddělovač pole, například TSV: FIELDTERMINATOR = TAB.

- ' PARQUET ' – binární soubor ve formátu Parquet 

**unstructured_data_path**

Unstructured_data_path, která vytváří cestu k datům, může být absolutní nebo relativní cesta:
- Absolutní cesta ve formátu \<prefix> :// \<storage_account_path> / \<storage_path> umožňuje uživateli přímo číst soubory.
- Relativní cesta ve formátu ' <storage_path> ', která musí být použita s `DATA_SOURCE` parametrem a popisuje vzor souboru v umístění <storage_account_path>, které je definováno v `EXTERNAL DATA SOURCE` . 

 Níže najdete relevantní <storage account path> hodnoty, které budou propojeny s konkrétním externím zdrojem dat. 

| Externí zdroj dat       | Předpona | Cesta k účtu úložiště                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | http [s]  | \<storage_account>. blob.core.windows.net/path/file   |
| Azure Blob Storage         | wasb [s]  | \<container>@\<storage_account>. blob.core.windows.net/path/file |
| Azure Data Lake Store Gen1 | http [s]  | \<storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | http [s]  | \<storage_account>dfs.core.windows.net/Path/File   |
| Azure Data Lake Store Gen2 | aufs [s]  | [\<file_system>@\<account_name>. dfs.core.windows.net/path/file](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

 Určuje cestu v rámci úložiště, která odkazuje na složku nebo soubor, který chcete číst. Pokud cesta odkazuje na kontejner nebo složku, všechny soubory budou načteny z konkrétního kontejneru nebo složky. Soubory v podsložkách nebudou zahrnuty. 

 Můžete použít zástupné znaky k zacílení na více souborů nebo složek. Je povoleno použití více zástupných znaků nejdoucích po sobě.
Níže je příklad, který čte všechny soubory *CSV* počínaje *plněním* ze všech složek začínajících na */CSV/Population*:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Pokud zadáte unstructured_data_path jako složku, dotaz na vyžádání SQL načte soubory z této složky. 

> [!NOTE]
> Na rozdíl od Hadoop a báze SQL na vyžádání nevrací podsložky. Na rozdíl od Hadoop a báze SQL na vyžádání vrátí také soubory, pro které název souboru začíná podtržítkem (_) nebo tečkou (.).

Pokud se v následujícím příkladu unstructured_data_path = `https://mystorageaccount.dfs.core.windows.net/webdata/` , dotaz SQL na vyžádání vrátí řádky z mydata.txt a _hidden.txt. Nevrátí mydata2.txt a mydata3.txt, protože jsou umístěné v podsložce.

![Rekurzivní data pro externí tabulky](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

Klauzule WITH umožňuje zadat sloupce, které chcete ze souborů číst.

- V případě datových souborů CSV můžete načíst všechny sloupce a zadat názvy sloupců a jejich datové typy. Pokud chcete podmnožinu sloupců, použijte řadové číslovky a vyberte sloupce z původních datových souborů podle pořadového čísla. Sloupce budou vázány podle ordinálního označení. 

    > [!IMPORTANT]
    > Klauzule WITH je povinná pro soubory CSV.
    >
    
- V případě datových souborů Parquet zadejte názvy sloupců, které odpovídají názvům sloupců v původních datových souborech. Sloupce budou vázány podle názvu. Pokud je klauzule WITH vynechána, budou vráceny všechny sloupce z Parquet souborů.

column_name = název výstupního sloupce. Pokud je tento název zadán, přepíše název sloupce ve zdrojovém souboru.

column_type = datový typ pro výstupní sloupec. Sem bude proveden převod implicitního datového typu.

column_ordinal = pořadové číslo sloupce ve zdrojových souborech. Tento argument se ignoruje u souborů Parquet, protože se vazba provádí podle názvu. Následující příklad vrátí druhý sloupec pouze ze souboru CSV:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

**\<bulk_options>**

FIELDTERMINATOR = ' field_terminator '

Určuje ukončovací znak pole, který se má použít. Výchozí ukončovací znak pole je čárka ("**,**").

ROWTERMINATOR = ' row_terminator ' '

Určuje ukončovací znak řádku, který se má použít. Pokud není zadán ukončovací znak řádku, bude použit jeden z výchozích zakončení. Výchozí zakončení pro PARSER_VERSION = "1,0" jsou \r\n, \n a \r. Výchozí zakončení pro PARSER_VERSION = "2,0" jsou \r\n a \n.

ESCAPE_CHAR = char

Určuje znak v souboru, který se používá k zaznamenání samotného řídicího panelu a všech hodnot oddělovače v souboru. Je-li řídicí znak následován jinou hodnotou než samotnou nebo kteroukoli z hodnot oddělovače, je řídicí znak při čtení hodnoty vynechán. 

Parametr ESCAPE_CHAR bude použit bez ohledu na to, zda je FIELDQUOTE nebo není povolen. Nepoužije se k řídicímu znaku pro uvozovky. Znak Quota musí být uvozen jiným znakem pro quotování. Znak quotace se může objevit v rámci hodnoty sloupce jenom v případě, že je hodnota zapouzdřená pomocí znaků quote.

FIRSTROW = ' first_row ' 

Určuje číslo prvního řádku, který se má načíst. Výchozí hodnota je 1 a určuje první řádek v zadaném datovém souboru. Čísla řádků se určují podle počtu zakončení řádků. FIRSTROW je založen na 1.

FIELDQUOTE = ' field_quote ' 

Určuje znak, který bude použit jako znak uvozovky v souboru CSV. Pokud není zadaný, použije se znak uvozovky ("). 

DATA_COMPRESSION = ' data_compression_method '

Určuje kompresní metodu. Je podporována následující metoda komprese:

- org. Apache. Hadoop. IO. Compress. GzipCodec

PARSER_VERSION = ' parser_version '

Určuje verzi analyzátoru, která se má použít při čtení souborů. Aktuálně podporované verze analyzátoru CSV jsou 1,0 a 2,0:

- PARSER_VERSION = ' 1,0 '
- PARSER_VERSION = ' 2,0 '

Analyzátor CSV verze 1,0 je výchozí a funkce je bohatá. Verze 2,0 je postavená na výkon a nepodporuje všechny možnosti a kódování. 

Specifické pro analyzátor CSV verze 2,0:

- Ne všechny datové typy jsou podporovány.
- Maximální omezení velikosti řádku je 8 MB.
- Následující možnosti nejsou podporovány: DATA_COMPRESSION.
- Prázdný řetězec v uvozovkách ("") je interpretován jako prázdný řetězec.

## <a name="examples"></a>Příklady

Následující příklad vrátí pouze dva sloupce se řadovými čísly 1 a 4 ze souborů Population*. csv. Vzhledem k tomu, že v souborech není žádný řádek záhlaví, začíná čtení z prvního řádku:

```sql
SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```

Následující příklad vrátí všechny sloupce prvního řádku ze sady sestavování dat ve formátu Parquet a bez zadání názvů sloupců a datových typů: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

## <a name="next-steps"></a>Další kroky

Další ukázky najdete v [rychlém startu pro dotaz na úložiště dat](query-data-storage.md) a Naučte se, jak používat `OPENROWSET` ke čtení formátů souborů [CSV](query-single-csv-file.md), [PARQUET](query-parquet-files.md)a [JSON](query-json-files.md) . Můžete se také dozvědět, jak uložit výsledky dotazu do Azure Storage pomocí [CETAS](develop-tables-cetas.md).
