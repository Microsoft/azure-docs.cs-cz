---
title: Jak používat OPENROWSET v SQL on-demand (preview)
description: Tento článek popisuje syntaxi OPENROWSET v SQL on-demand (preview) a vysvětluje, jak používat argumenty.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 25eb93a01c59225b6d9e64db5d08b954adb4f8ab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424066"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Jak používat OPENROWSET s SQL na vyžádání (preview)

Funkce OPENROWSET(BULK...) umožňuje přístup k souborům ve službě Azure Storage. V rámci prostředku SQL on-demand (preview) je poskytovatel hromadné sady řádků OPENROWSET přístupný voláním funkce OPENROWSET a zadáním možnosti BULK.  

Funkce OPENROWSET může být odkazována v klauzuli FROM dotazu, jako by to byl název tabulky OPENROWSET. Podporuje hromadné operace prostřednictvím integrovaného zprostředkovatele BULK, který umožňuje čtení a vrácení dat ze souboru jako sady řádků.

OPENROWSET není aktuálně podporován ve fondu SQL.

## <a name="syntax"></a>Syntaxe

```
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

- 'CSV' - Zahrnuje všechny oddělené textové soubory s oddělovači řádků/sloupců. Libovolný znak lze použít jako oddělovač polí, například TSV: FIELDTERMINATOR = karta.

- 'PARKETy' - Binární soubor ve formátu parket 

"unstructured_data_path"

Unstructured_data_path, která vytváří cestu k datům, je strukturována takto: "<storage_account_path>/<storage_path>"<prefix> 
 
 
 Níže najdete příslušné cesty účtu úložiště, které budou propojeny s konkrétním externím zdrojem dat. 

| Externí zdroj dat       | Předpona | Cesta k účtu úložiště                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | HTTPS  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Store Gen1 | HTTPS  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | HTTPS  | <storage_account>.dfs.core.windows.net              |

 "<storage_path>" 

 Určuje cestu v úložišti, která odkazuje na složku nebo soubor, který chcete číst. Pokud cesta odkazuje na kontejner nebo složku, budou všechny soubory číst z tohoto konkrétního kontejneru nebo složky. Soubory v podsložkách nebudou zahrnuty. 
 
 Zástupné znaky můžete použít k cílení na více souborů nebo složek. Použití více nepo sobě jdoucích zástupných znaků je povoleno.
Níže je uveden příklad, který čte všechny soubory *csv* počínaje *plnění* zehttps://sqlondemandstorage.blob.core.windows.net/csv/populationvšech složek začínající */csv/population*: '*/population*.csv'

Pokud zadáte unstructured_data_path jako složku, dotaz NA vyžádání sql načte soubory z této složky. 

> [!NOTE]
> Na rozdíl od Hadoop a PolyBase SQL na vyžádání nevrací podsložky. Také na rozdíl od Hadoop a PloyBase, SQL na vyžádání vrátí soubory, pro které název souboru začíná podtržení (_) nebo tečka (.).

V níže uvedeném příkladu,https://mystorageaccount.dfs.core.windows.net/webdata/pokud unstructured_data_path=' ', dotaz SQL na vyžádání vrátí řádky z mydata.txt a _hidden.txt. Nevrátí mydata2.txt a mydata3.txt, protože jsou umístěny v podsložce.

![Rekurzivní data pro externí tabulky](./media/develop-openrowset/folder-traversal.png)

[S ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]

Klauzule WITH umožňuje zadat sloupce, které chcete číst ze souborů.

- Pro datové soubory CSV, chcete-li číst všechny sloupce, zadejte názvy sloupců a jejich datové typy. Pokud chcete podmnožinu sloupců, použijte řadová čísla k výběru sloupců z původních datových souborů podle počtu. Sloupce budou vázány pořitozovacím označením. 

> [!IMPORTANT]
> Klauzule WITH je povinná pro soubory CSV.
- Pro datové soubory parket zadejte názvy sloupců, které odpovídají názvům sloupců v původních datových souborech. Sloupce budou vázány názvem. Pokud je klauzule WITH vynechána, budou vráceny všechny sloupce ze souborů parket.

column_name = Název výstupního sloupce. Pokud je zadán, tento název přepíše název sloupce ve zdrojovém souboru.

column_type = Datový typ pro výstupní sloupec. Implicitní převod datového typu proběhne zde.

column_ordinal = Číslo sloupce ve zdrojovém souboru (zdrojových souborech). Tento argument je ignorován a soubory parket, protože vazba se provádí podle názvu. Následující příklad vrátí druhý sloupec pouze ze souboru CSV:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```
<bulk_options>

FIELDTERMINATOR ='field_terminator'

Určuje zakončení pole, které má být použito. Výchozí zakončení pole je čárka ("**,**").

ROWTERMINATOR ='row_terminator''

Určuje zakončení řádku, které má být použito. Výchozí zakončení řádku je znak nového řádku, například \r\n.

ESCAPE_CHAR = 'char'

Určuje znak v souboru, který se používá k samotnému úniku, a všechny hodnoty oddělovače v souboru. Pokud je za řídicím znakem následována jiná hodnota než sama nebo některá z hodnot oddělovače, je při čtení hodnoty vynechán řídicí znak. 

Parametr ESCAPE_CHAR bude použit bez ohledu na to, zda je nebo není povolena fieldquote. Nebude použit k úniku citaci charakteru. Znak citace je uvozen s dvojitými uvozovkami v souladu s chováním CSV aplikace Excel.

FIRSTROW = 'first_row' 

Určuje číslo prvního řádku, který se má načíst. Výchozí hodnota je 1. Označuje první řádek v zadaném datovém souboru. Čísla řádků jsou určena počítáním zakončení řádku. FIRSTROW je založen na 1.

FIELDQUOTE = 'field_quote' 

Určuje znak, který bude použit jako znak nabídky v souboru CSV. Pokud není zadán, bude použit znak nabídky ("). 

## <a name="examples"></a>Příklady

Následující příklad vrátí pouze dva sloupce s číselnými čísly 1 a 4 ze souborů populace*.csv. Vzhledem k tomu, že v souborech není žádný řádek záhlaví, začne číst z prvního řádku:

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



Následující příklad vrátí všechny sloupce prvního řádku ze sady dat sčítání ve formátu Parkety bez zadání názvů sloupců a datových typů: 

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

Další ukázky najdete na [rychlých startech](query-data-storage.md) nebo výsledky dotazu uložte do služby Azure Storage pomocí [cetasu](develop-tables-cetas.md).
