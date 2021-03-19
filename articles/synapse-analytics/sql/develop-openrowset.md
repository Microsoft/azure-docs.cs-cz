---
title: Použití funkce OPENROWSET v SQL fondu bez serveru
description: Tento článek popisuje syntaxi funkce OPENROWSET v SQL fondu bez serveru a vysvětluje, jak používat argumenty.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 987b850d569c238902f57baaed4a2178f51bd79d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589612"
---
# <a name="how-to-use-openrowset-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Použití funkce OPENROWSET s použitím fondu SQL bez serveru ve službě Azure synapse Analytics

`OPENROWSET(BULK...)`Funkce umožňuje přístup k souborům v Azure Storage. `OPENROWSET` funkce přečte obsah vzdáleného zdroje dat (například soubor) a vrátí obsah jako sadu řádků. V rámci prostředku fondu SQL bez serveru je k dispozici zprostředkovatel hromadné sady řádků OPENROWSET voláním funkce OPENROWSET a určením hromadné možnosti.  

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
WITH ( {'column_name' 'column_type' [ 'column_ordinal' | 'json_path'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
[ , HEADER_ROW = { TRUE | FALSE } ]
[ , DATAFILETYPE = { 'char' | 'widechar' } ]
[ , CODEPAGE = { 'ACP' | 'OEM' | 'RAW' | 'code_page' } ]
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
| Azure Data Lake Store Gen2 | ABFS [s]  | [\<file_system>@\<account_name>. dfs.core.windows.net/path/file](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

Určuje cestu v rámci úložiště, která odkazuje na složku nebo soubor, který chcete číst. Pokud cesta odkazuje na kontejner nebo složku, všechny soubory budou načteny z konkrétního kontejneru nebo složky. Soubory v podsložkách nebudou zahrnuty. 

Můžete použít zástupné znaky k zacílení na více souborů nebo složek. Je povoleno použití více zástupných znaků nejdoucích po sobě.
Níže je příklad, který čte všechny soubory *CSV* počínaje *plněním* ze všech složek začínajících na */CSV/Population*:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Pokud zadáte unstructured_data_path jako složku, dotaz na fond SQL bez serveru načte soubory z této složky. 

SQL fondu bez serveru můžete dát pokyn k procházení složek zadáním/* na konci cesty jako v příkladu: `https://sqlondemandstorage.blob.core.windows.net/csv/population/**`

> [!NOTE]
> Na rozdíl od Hadoop a báze SQL bez serveru nevrátí podsložky, pokud nezadáte/* * na konci cesty. I na rozdíl od Hadoop a báze SQL bez serveru vrátí soubory, pro které název souboru začíná podtržením (_) nebo tečku (.).

V následujícím příkladu, pokud unstructured_data_path = `https://mystorageaccount.dfs.core.windows.net/webdata/` , dotaz fondu SQL bez serveru vrátí řádky z mydata.txt a _hidden.txt. Nevrátí mydata2.txt a mydata3.txt, protože jsou umístěné v podsložce.

![Rekurzivní data pro externí tabulky](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

Klauzule WITH umožňuje zadat sloupce, které chcete ze souborů číst.

- V případě datových souborů CSV můžete načíst všechny sloupce a zadat názvy sloupců a jejich datové typy. Pokud chcete podmnožinu sloupců, použijte řadové číslovky a vyberte sloupce z původních datových souborů podle pořadového čísla. Sloupce budou vázány podle ordinálního označení. Je-li použita HEADER_ROW = TRUE, je vazba sloupce provedena pomocí názvu sloupce namísto ordinálního umístění.
    > [!TIP]
    > Můžete také vynechat klauzuli WITH pro soubory CSV. Datové typy budou automaticky odvozeny z obsahu souboru. Argument HEADER_ROW lze použít k určení existence řádku záhlaví, ve kterém budou názvy sloupců případu načteny z řádku záhlaví. Podrobnosti najdete v podrobnostech o [automatickém zjišťování schématu](#automatic-schema-discovery).
    
- V případě datových souborů Parquet zadejte názvy sloupců, které odpovídají názvům sloupců v původních datových souborech. Sloupce se budou svázat podle názvu a rozlišuje velká a malá písmena. Pokud je klauzule WITH vynechána, budou vráceny všechny sloupce z Parquet souborů.
    > [!IMPORTANT]
    > Názvy sloupců v souborech Parquet rozlišují velká a malá písmena. Pokud v souboru Parquet zadáte název sloupce, který se liší od názvu sloupce a velikost písmen, vrátí se pro tento sloupec hodnoty NULL.


column_name = název výstupního sloupce. Pokud je tento název zadán, přepíše název sloupce ve zdrojovém souboru a názvu sloupce zadaného v cestě JSON, pokud je nějaký. Pokud není zadaný json_path, automaticky se přidá jako $ .column_name. Pro chování ověřte json_path argument.

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

json_path = [výraz cesty JSON](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) na vlastnost Column nebo Nested. Výchozí [režim cesty](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true#PATHMODE) je Lax.

> [!NOTE]
> Dotaz v striktním režimu se nezdaří s chybou, pokud zadaná cesta neexistuje. Dotaz v režimu LAX bude úspěšný a výraz cesty JSON se vyhodnotí na hodnotu NULL.

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

Určuje kompresní metodu. Podporováno pouze v PARSER_VERSION = ' 1.0 '. Je podporována následující metoda komprese:

- GZIP

PARSER_VERSION = ' parser_version '

Určuje verzi analyzátoru, která se má použít při čtení souborů. Aktuálně podporované verze analyzátoru CSV jsou 1,0 a 2,0:

- PARSER_VERSION = ' 1,0 '
- PARSER_VERSION = ' 2,0 '

Analyzátor CSV verze 1,0 je výchozí a funkce je bohatá. Verze 2,0 je postavená na výkon a nepodporuje všechny možnosti a kódování. 

Specifické pro analyzátor CSV verze 1,0:

- Následující možnosti nejsou podporovány: HEADER_ROW.

Specifické pro analyzátor CSV verze 2,0:

- Ne všechny datové typy jsou podporovány.
- Maximální délka sloupce znaků je 8000.
- Maximální omezení velikosti řádku je 8 MB.
- Následující možnosti nejsou podporovány: DATA_COMPRESSION.
- Prázdný řetězec v uvozovkách ("") je interpretován jako prázdný řetězec.
- Podporovaný formát pro datový typ DATE: RRRR-MM-DD
- Podporovaný formát pro datový typ TIME: HH: MM: SS [. zlomky sekund]
- Podporovaný formát pro datový typ DATETIME2: RRRR-MM-DD HH: MM: SS [. zlomky sekund]

HEADER_ROW = {TRUE | CHYBNÉ

Určuje, zda soubor CSV obsahuje řádek záhlaví. Výchozí hodnota je FALSE. Podporováno v PARSER_VERSION = ' 2.0 '. Pokud má hodnotu TRUE, názvy sloupců se načtou z prvního řádku podle argumentu FIRSTROW. Je-li nastavena hodnota TRUE a v WITH je zadáno schéma, vytvoří se vazba názvů sloupců podle názvu sloupce, nikoli podle pořadových míst.

Datatyp_souboru = {' char ' | ' widechar '}

Určuje kódování: char se používá pro UTF8, widechar se používá pro soubory UTF16.

CODEPAGE = {' ACP ' | VÝROBCE OEM | RAW | code_page}

Určuje znakovou stránku dat v datovém souboru. Výchozí hodnota je 65001 (kódování UTF-8). Další podrobnosti o této možnosti najdete [tady](/sql/t-sql/functions/openrowset-transact-sql?view=sql-server-ver15#codepage).

## <a name="fast-delimited-text-parsing"></a>Analýza textu s rychlým oddělovačem

Existují dvě verze analyzátoru s oddělovači textu, které můžete použít. Analyzátor CSV verze 1,0 je výchozí a funkce bohatá, zatímco analyzátor verze 2,0 je sestaven pro výkon. Vylepšení výkonu v analyzátoru 2,0 pochází z pokročilých technik analýzy a multithreading. Rozdíl v rychlosti bude při zvětšování velikosti souboru větší.

## <a name="automatic-schema-discovery"></a>Automatické zjišťování schématu

Můžete snadno dotazovat soubory CSV i Parquet bez znalosti nebo zadání schématu vyvoláním klauzule WITH. Názvy sloupců a datové typy budou odvozeny ze souborů.

Soubory Parquet obsahují metadata sloupců, která se budou číst, mapování typů se dá najít v [mapování typů pro Parquet](#type-mapping-for-parquet). Podívejte se na [čtení souborů Parquet bez zadání schématu](#read-parquet-files-without-specifying-schema) pro ukázky.

Názvy sloupců pro soubory CSV lze číst z řádku záhlaví. Můžete určit, zda řádek záhlaví existuje pomocí argumentu HEADER_ROW. Pokud HEADER_ROW = FALSE, použijí se názvy obecných sloupců: C1, C2,... CN, kde n je počet sloupců v souboru. Datové typy budou odvozeny z prvních 100 datových řádků. Kontroluje [čtení souborů CSV bez zadání schématu](#read-csv-files-without-specifying-schema) pro ukázky.

> [!IMPORTANT]
> Existují případy, kdy se vhodný datový typ nedá odvodit z důvodu nedostatku informací a místo toho se použije větší datový typ. To přináší nároky na výkon a je zvláště důležité pro sloupce znaků, které budou odvozeny jako varchar (8000). Pro zajištění optimálního výkonu [Zkontrolujte odvozené datové typy](best-practices-sql-on-demand.md#check-inferred-data-types) a [použijte příslušné datové typy](best-practices-sql-on-demand.md#use-appropriate-data-types).

### <a name="type-mapping-for-parquet"></a>Mapování typů pro Parquet

Soubory Parquet obsahují popisy typů pro každý sloupec. Následující tabulka popisuje, jak jsou typy Parquet mapovány na nativní typy SQL.

| Typ Parquet | Logický typ Parquet (anotace) | Datový typ SQL |
| --- | --- | --- |
| DATOVÉHO | | bit |
| BINÁRNÍ/BYTE_ARRAY | | varbinary |
| KLEPAT | | float |
| Plovák | | real |
| UVEDENA | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binární |
| TVARU |UTF |varchar \* (řazení UTF8) |
| TVARU |ŘETEZCE |varchar \* (řazení UTF8) |
| TVARU |VYTVÁŘENÍ|varchar \* (řazení UTF8) |
| FIXED_LEN_BYTE_ARRAY |IDENTIFIKÁTOR |uniqueidentifier |
| TVARU |NOTACI |decimal |
| TVARU |JSON |varchar (8000) \* (kolace UTF8) |
| TVARU |BSON | Nepodporováno |
| FIXED_LEN_BYTE_ARRAY |NOTACI |decimal |
| BYTE_ARRAY |DOBA | Nepodporováno |
| UVEDENA |INT (8, true) |smallint |
| UVEDENA |INT (16, true) |smallint |
| UVEDENA |INT (32, true) |int |
| UVEDENA |INT (8, false) |tinyint |
| UVEDENA |INT (16, false) |int |
| UVEDENA |INT (32, false) |bigint |
| UVEDENA |DATE |date |
| UVEDENA |NOTACI |decimal |
| UVEDENA |ČAS (LISOVNY)|time |
| INT64 |INT (64; true) |bigint |
| INT64 |INT (64, false) |desetinné číslo (20, 0) |
| INT64 |NOTACI |decimal |
| INT64 |ČAS (MIKRO) |časový čas (NANO) není podporován. |
|INT64 |ČASOVÉ RAZÍTKO (LISOVNY/MIKROORGANISMY) |datetime2 – časové RAZÍTKo (NANO) není podporované. |
|[Komplexní typ](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |SEZNAMU |varchar (8000), serializováno do formátu JSON |
|[Komplexní typ](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAPY|varchar (8000), serializováno do formátu JSON |

## <a name="examples"></a>Příklady

### <a name="read-csv-files-without-specifying-schema"></a>Čtení souborů CSV bez zadání schématu

Následující příklad přečte soubor CSV, který obsahuje řádek záhlaví bez zadání názvů sloupců a datových typů: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0',
    HEADER_ROW = TRUE) as [r]
```

Následující příklad přečte soubor CSV, který neobsahuje řádek záhlaví bez zadání názvů sloupců a datových typů: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0') as [r]
```

### <a name="read-parquet-files-without-specifying-schema"></a>Čtení souborů Parquet bez zadání schématu

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

### <a name="read-specific-columns-from-csv-file"></a>Číst konkrétní sloupce ze souboru CSV

Následující příklad vrátí pouze dva sloupce se řadovými čísly 1 a 4 ze souborů Population*. csv. Vzhledem k tomu, že v souborech není žádný řádek záhlaví, začíná čtení z prvního řádku:

```sql
SELECT 
    * 
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

### <a name="read-specific-columns-from-parquet-file"></a>Číst konkrétní sloupce ze souboru Parquet

Následující příklad vrátí pouze dva sloupce prvního řádku ze sady oddaných dat ve formátu Parquet: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    [stateName] VARCHAR (50),
    [population] bigint
) AS [r]
```

### <a name="specify-columns-using-json-paths"></a>Určení sloupců pomocí cest JSON

Následující příklad ukazuje, jak lze použít [výrazy cesty JSON](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) v klauzuli with a ukazuje rozdíl mezi striktními a LAX cestami: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    --lax path mode samples
    [stateName] VARCHAR (50), -- this one works as column name casing is valid - it targets the same column as the next one
    [stateName_explicit_path] VARCHAR (50) '$.stateName', -- this one works as column name casing is valid
    [COUNTYNAME] VARCHAR (50), -- STATEname column will contain NULLs only because of wrong casing - it targets the same column as the next one
    [countyName_explicit_path] VARCHAR (50) '$.COUNTYNAME', -- STATEname column will contain NULLS only because of wrong casing and default path mode being lax

    --strict path mode samples
    [population] bigint 'strict $.population' -- this one works as column name casing is valid
    --,[population2] bigint 'strict $.POPULATION' -- this one fails because of wrong casing and strict path mode
)
AS [r]
```

## <a name="next-steps"></a>Další kroky

Další ukázky najdete v [rychlém startu pro dotaz na úložiště dat](query-data-storage.md) a Naučte se, jak používat `OPENROWSET` ke čtení formátů souborů [CSV](query-single-csv-file.md), [PARQUET](query-parquet-files.md)a [JSON](query-json-files.md) . Podívejte se na [osvědčené postupy](best-practices-sql-on-demand.md) pro dosažení optimálního výkonu. Můžete se také dozvědět, jak uložit výsledky dotazu do Azure Storage pomocí [CETAS](develop-tables-cetas.md).