---
title: Jak používat OPENROWSET na vyžádání SQL (Preview)
description: Tento článek popisuje syntaxi OPENROWSET v SQL na vyžádání (Preview) a vysvětluje, jak používat argumenty.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 6325d5555b01373b148dce69731ec64896d6e1fd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81680497"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Jak používat OPENROWSET s SQL na vyžádání (Preview)

Funkce OPENROWSET (BULK...) umožňuje přístup k souborům v Azure Storage. V rámci prostředku SQL na vyžádání (Preview) je k dispozici zprostředkovatel hromadné sady řádků OPENROWSET voláním funkce OPENROWSET a určením hromadné možnosti.  

Na funkci OPENROWSET lze odkazovat v klauzuli FROM dotazu, jako by šlo o název tabulky OPENROWSET. Podporuje hromadné operace prostřednictvím integrovaného HROMADNÉho poskytovatele, který umožňuje číst data ze souboru a vracet je jako sadu řádků.

OPENROWSET ve fondu SQL aktuálně není podporováno.

## <a name="syntax"></a>Syntaxe

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row'  ]     
[ , FIELDQUOTE = 'quote_characters']
```

## <a name="arguments"></a>Argumenty

Máte dvě možnosti pro vstupní soubory, které obsahují cílová data pro dotazování. Platné hodnoty jsou:

- ' CSV ' – obsahuje libovolný oddělený textový soubor s oddělovači řádků nebo sloupců. Libovolný znak lze použít jako oddělovač pole, například TSV: FIELDTERMINATOR = TAB.

- ' PARQUET ' – binární soubor ve formátu Parquet 

**unstructured_data_path**

Unstructured_data_path, která vytváří cestu k datům, je strukturována takto:  
'\<prefix>://\<storage_account_path>/\<storage_path> '
 
 
 Níže najdete relevantní cesty k účtu úložiště, které budou propojit s vaším konkrétním externím zdrojem dat. 

| Externí zdroj dat       | Předpona | Cesta k účtu úložiště                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | HTTPS  | \<storage_account>. blob.core.windows.net             |
| Azure Data Lake Store Gen1 | HTTPS  | \<storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | HTTPS  | \<storage_account>. dfs.core.windows.net              |
||||

\<storage_path>

 Určuje cestu v rámci úložiště, která odkazuje na složku nebo soubor, který chcete číst. Pokud cesta odkazuje na kontejner nebo složku, všechny soubory budou načteny z konkrétního kontejneru nebo složky. Soubory v podsložkách nebudou zahrnuty. 
 
 Můžete použít zástupné znaky k zacílení na více souborů nebo složek. Je povoleno použití více zástupných znaků nejdoucích po sobě.
Níže je příklad, který čte všechny soubory *CSV* počínaje *plněním* ze všech složek začínajících na */CSV/Population*:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Pokud zadáte unstructured_data_path jako složku, dotaz na vyžádání SQL načte soubory z této složky. 

> [!NOTE]
> Na rozdíl od Hadoop a báze SQL na vyžádání nevrací podsložky. Na rozdíl od Hadoop a PloyBase vrátí SQL na vyžádání soubory, pro které název souboru začíná podtržítkem (_) nebo tečkou (.).

Pokud je v následujícím příkladu unstructured_data_path =`https://mystorageaccount.dfs.core.windows.net/webdata/`, dotaz SQL na vyžádání vrátí řádky z Mojedata. txt a _hidden. txt. Nevrátí mydata2. txt a mydata3. txt, protože jsou umístěné v podsložce.

![Rekurzivní data pro externí tabulky](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

Klauzule WITH umožňuje zadat sloupce, které chcete ze souborů číst.

- V případě datových souborů CSV můžete načíst všechny sloupce a zadat názvy sloupců a jejich datové typy. Pokud chcete podmnožinu sloupců, použijte řadové číslovky a vyberte sloupce z původních datových souborů podle pořadového čísla. Sloupce budou vázány podle ordinálního označení. 

> [!IMPORTANT]
> Klauzule WITH je povinná pro soubory CSV.
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

Určuje ukončovací znak řádku, který se má použít. Výchozí ukončovací znak řádku je znak nového řádku, například \r\n..

ESCAPE_CHAR = char

Určuje znak v souboru, který se používá k zaznamenání samotného řídicího panelu a všech hodnot oddělovače v souboru. Je-li řídicí znak následován jinou hodnotou než samotnou nebo kteroukoli z hodnot oddělovače, je řídicí znak při čtení hodnoty vynechán. 

Parametr ESCAPE_CHAR bude použit bez ohledu na to, zda je FIELDQUOTE nebo není povolen. Nepoužije se k řídicímu znaku pro uvozovky. Znak quotování je řídicím znakem s dvojitými uvozovkami v zarovnání s chováním CSV v Excelu.

FIRSTROW = ' first_row ' 

Určuje číslo prvního řádku, který se má načíst. Výchozí hodnota je 1. Určuje první řádek v zadaném datovém souboru. Čísla řádků se určují podle počtu zakončení řádků. FIRSTROW je založen na 1.

FIELDQUOTE = ' field_quote ' 

Určuje znak, který bude použit jako znak uvozovky v souboru CSV. Pokud není zadaný, použije se znak uvozovky ("). 

## <a name="examples"></a>Příklady

Následující příklad vrátí pouze dva sloupce se řadovými čísly 1 a 4 ze souborů Population*. csv. Vzhledem k tomu, že v souborech není žádný řádek záhlaví, začíná čtení z prvního řádku:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

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



Následující příklad vrátí všechny sloupce prvního řádku ze skupiny vydaných dat ve formátu Parquet bez zadání názvů sloupců a datových typů: 

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```



## <a name="next-steps"></a>Další kroky

Další ukázky najdete v [rychlých startech](query-data-storage.md) nebo ukládání výsledků dotazu do Azure Storage pomocí [CETAS](develop-tables-cetas.md).
