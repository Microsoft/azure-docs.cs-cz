---
title: Dotazování na soubory CSV pomocí neserverového fondu SQL (Preview)
description: V tomto článku se dozvíte, jak zadávat dotazy na samostatné soubory CSV s různými formáty souborů pomocí SQL fondu bez serveru (Preview).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: b9896b62ab347ec3b4751eb517c00222f00ddb1c
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579398"
---
# <a name="query-csv-files"></a>Dotazování souborů CSV

V tomto článku se dozvíte, jak zadat dotaz na jeden soubor CSV s použitím fondu SQL bez serveru (Preview) ve službě Azure synapse Analytics. Soubory CSV můžou mít různé formáty: 

- S a bez řádku záhlaví
- Hodnoty oddělené čárkami a tabulátory
- Konce řádků ve stylu Windows a UNIX
- Hodnoty, které nejsou v uvozovkách a uvozovkách, a znaky pro uvozovací znaky

Všechny výše uvedené variace budou uvedené níže.

## <a name="quickstart-example"></a>Příklad rychlého startu

`OPENROWSET` funkce umožňuje číst obsah souboru CSV zadáním adresy URL souboru.

### <a name="read-a-csv-file"></a>Čtení souboru CSV

Nejjednodušší způsob, jak zobrazit obsah `CSV` souboru, je poskytnout URL souboru pro `OPENROWSET` funkci, zadat csv `FORMAT` a 2,0 `PARSER_VERSION` . Pokud je soubor veřejně dostupný nebo pokud vaše identita Azure AD může získat přístup k tomuto souboru, měli byste být schopni zobrazit obsah souboru pomocí dotazu, jako je ten, který je znázorněn v následujícím příkladu:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    format = 'csv',
    parser_version = '2.0',
    firstrow = 2 ) as rows
```

Možnost `firstrow` slouží k přeskočení prvního řádku v souboru CSV, který představuje hlavičku v tomto případě. Ujistěte se, že máte přístup k tomuto souboru. Pokud je soubor chráněný klíčem SAS nebo vlastní identitou, bude potřeba nastavit [přihlašovací údaje na úrovni serveru pro přihlášení SQL](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential).

> [!IMPORTANT]
> Pokud soubor CSV obsahuje znaky znakové sady UTF-8, ujistěte se, že používáte určitou databázovou kolaci UTF-8 (například `Latin1_General_100_CI_AS_SC_UTF8` ).
> Neshoda mezi kódováním textu v souboru a kolaci může způsobit neočekávané chyby při konverzi.
> Výchozí kolaci aktuální databáze můžete snadno změnit pomocí následujícího příkazu T-SQL: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`

### <a name="data-source-usage"></a>Využití zdroje dat

Předchozí příklad používá úplnou cestu k souboru. Jako alternativu můžete vytvořit externí zdroj dat s umístěním, které odkazuje na kořenovou složku úložiště:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
```

Po vytvoření zdroje dat můžete tento zdroj dat a relativní cestu k souboru ve `OPENROWSET` funkci použít:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) as rows
```

Pokud je zdroj dat chráněný pomocí klíče SAS nebo vlastní identity, můžete nakonfigurovat [zdroj dat s použitím přihlašovacích údajů s rozsahem databáze](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

### <a name="explicitly-specify-schema"></a>Explicitně zadat schéma

`OPENROWSET` umožňuje explicitně určit sloupce, které chcete číst z klauzule File using `WITH` :

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) with (
        date_rep date 1,
        cases int 5,
        geo_id varchar(6) 8
    ) as rows
```

Čísla po datovém typu v `WITH` klauzuli reprezentují index sloupce v souboru CSV.

> [!IMPORTANT]
> Pokud Váš soubor CSV obsahuje znaky znakové sady UTF-8, ujistěte se, že jste explicilty určení některé kolace UTF-8 (například `Latin1_General_100_CI_AS_SC_UTF8` ) pro všechny sloupce v `WITH` klauzuli nebo nastavte určitou kolaci UTF-8 na úrovni databáze.
> Neshoda mezi kódováním textu v souboru a kolaci může způsobit neočekávané chyby při konverzi.
> Výchozí kolaci aktuální databáze můžete snadno změnit pomocí následujícího příkazu T-SQL: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`
> Kolaci pro typy slo můžete snadno nastavit pomocí následující definice: `geo_id varchar(6) collate Latin1_General_100_CI_AI_SC_UTF8 8`

V následujících částech se můžete podívat, jak se dotazovat na různé typy souborů CSV.

## <a name="prerequisites"></a>Požadavky

Prvním krokem je **Vytvoření databáze** , ve které budou vytvořeny tabulky. Pak inicializujte objekty spuštěním [instalačního skriptu](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) v této databázi. Tento instalační skript vytvoří zdroje dat, přihlašovací údaje v oboru databáze a formáty externích souborů, které jsou použity v těchto ukázkách.

## <a name="windows-style-new-line"></a>Nový řádek stylu Windows

Následující dotaz ukazuje, jak číst soubor CSV bez řádku záhlaví, s novým řádkem ve stylu systému Windows a se sloupci oddělenými čárkami.

Náhled souboru:

![Prvních 10 řádků souboru CSV bez záhlaví, nový řádek stylu Windows](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Nový řádek ve stylu systému UNIX

Následující dotaz ukazuje, jak číst soubor bez řádku záhlaví, s novým řádkem ve stylu systému UNIX a sloupci oddělenými čárkami. Všimněte si, že se v porovnání s ostatními příklady liší umístění souboru.

Náhled souboru:

![Prvních 10 řádků souboru CSV bez řádku záhlaví a Unix-Style nový řádek](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>Řádek záhlaví

Následující dotaz ukazuje, jak číst soubor s řádkem záhlaví, s novým řádkem ve stylu systému UNIX a sloupci oddělenými čárkami. Všimněte si, že se v porovnání s ostatními příklady liší umístění souboru.

Náhled souboru:

![Prvních 10 řádků souboru CSV s řádkem záhlaví a Unix-Style nový řádek.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>Vlastní znak uvozovky

Následující dotaz ukazuje, jak přečíst soubor s řádkem záhlaví s novým řádkem ve stylu systému UNIX, sloupci s oddělovači a hodnotami v uvozovkách. Všimněte si, že se v porovnání s ostatními příklady liší umístění souboru.

Náhled souboru:

![Prvních 10 řádků souboru CSV s řádkem záhlaví a Unix-Style nový řádek a hodnoty v uvozovkách.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> Tento dotaz vrátí stejné výsledky, pokud jste vynechali parametr FIELDQUOTE, protože výchozí hodnota pro FIELDQUOTE je dvojité uvozovky.

## <a name="escape-characters"></a>Řídicí znaky

Následující dotaz ukazuje, jak číst soubor s řádkem záhlaví s novým řádkem ve stylu systému UNIX, sloupci s oddělovači a řídicím znakem použitým pro oddělovač pole (čárka) v rámci hodnot. Všimněte si, že se v porovnání s ostatními příklady liší umístění souboru.

Náhled souboru:

![Prvních 10 řádků souboru CSV s řádkem záhlaví a Unix-Style nový řádek a řídicí znak, který se používá pro oddělovač polí.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Tento dotaz by se nezdařil, pokud není zadán parametr ESCAPECHAR, protože čárka v "slov, enia" by byla považována za oddělovač polí namísto části názvu země nebo oblasti. "Slov, enia" by se považovat za dva sloupce. Proto bude mít konkrétní řádek jeden sloupec více než ostatní řádky a jeden sloupec je více, než jste definovali v klauzuli WITH.

### <a name="escape-quoting-characters"></a>Řídicí znaky pro uvozovky

Následující dotaz ukazuje, jak číst soubor s řádkem záhlaví s novým řádkem ve stylu UNIX, s oddělovači sloupců a s řídicím znakem dvojitých uvozovek v rámci hodnot. Všimněte si, že se v porovnání s ostatními příklady liší umístění souboru.

Náhled souboru:

![Následující dotaz ukazuje, jak číst soubor s řádkem záhlaví s novým řádkem ve stylu UNIX, s oddělovači sloupců a s řídicím znakem dvojitých uvozovek v rámci hodnot.](./media/query-single-csv-file/population-unix-hdr-escape-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Znak Quota musí být uvozen jiným znakem pro quotování. Znak quotace se může objevit v rámci hodnoty sloupce jenom v případě, že je hodnota zapouzdřená pomocí znaků quote.

## <a name="tab-delimited-files"></a>Soubory s hodnotami oddělenými tabulátory

Následující dotaz ukazuje, jak číst soubor s řádkem záhlaví, s novým řádkem ve stylu systému UNIX a sloupci oddělenými tabulátory. Všimněte si, že se v porovnání s ostatními příklady liší umístění souboru.

Náhled souboru:

![Prvních 10 řádků souboru CSV s řádkem záhlaví a Unix-Style nový řádek a oddělovač tabulátoru.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="return-a-subset-of-columns"></a>Vrácení podmnožiny sloupců

Zatím jste určili schéma souboru CSV pomocí nástroje s a seznamem všech sloupců. V dotazu můžete zadat pouze sloupce, které skutečně potřebujete, a to pomocí pořadového čísla pro každý požadovaný sloupec. Vynecháte také sloupce bez zájmu.

Následující dotaz vrátí počet jedinečných názvů zemí nebo oblastí v souboru, přičemž určí pouze sloupce, které jsou nutné:

> [!NOTE]
> Podívejte se na klauzuli WITH v následujícím dotazu a Všimněte si, že na konci řádku, ve kterém definujete sloupec *[country_name]* , se nachází znak "2" (bez uvozovek). To znamená, že sloupec *[country_name]* je druhým sloupcem v souboru. Dotaz bude ignorovat všechny sloupce v souboru s výjimkou druhé.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Další kroky

V dalších článcích se dozvíte, jak:

- [Dotazování na soubory Parquet](query-parquet-files.md)
- [Dotazování na složky a více souborů](query-folders-multiple-csv-files.md)
