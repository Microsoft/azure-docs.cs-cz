---
title: Dotazování na úložiště dat s neserverovým fondem SQL
description: Tento článek popisuje, jak zadat dotaz na Azure Storage pomocí prostředku fondu SQL bez serveru v rámci služby Azure synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: d299afca0bd8070a1da738e02812b64c41a7101c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675046"
---
# <a name="query-storage-files-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Dotazování souborů úložiště s neserverovým fondem SQL ve službě Azure synapse Analytics

Fond SQL bez serveru umožňuje dotazovat se na data ve službě Data Lake. Nabízí oblast dotazu T-SQL, která se vejde na částečně strukturované a nestrukturované datové dotazy. Pro dotazování jsou podporovány následující aspekty T-SQL:

- Celý [Výběr](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) oblasti plochy, včetně většiny [funkcí a operátorů SQL](overview-features.md)
- Možnost vytvořit externí tabulku jako SELECT ([CETAS](develop-tables-cetas.md)) vytvoří [externí tabulku](develop-tables-external-tables.md) a potom exportuje paralelně výsledky příkazu SELECT jazyka Transact-SQL pro Azure Storage.

Další informace o tom, co je vs. v současné době není podporováno, najdete v článku [Přehled fondu SQL bez serveru](on-demand-workspace-overview.md) nebo v následujících článcích:
- [Vývoj přístupu k úložišti](develop-storage-files-overview.md) , kde se dozvíte, jak používat [externí tabulku](develop-tables-external-tables.md) a funkci [OpenRowset](develop-openrowset.md) ke čtení dat ze služby Storage.
- [Řízení přístupu k úložišti](develop-storage-files-storage-access-control.md) , kde se dozvíte, jak povolit synapse SQL pro přístup k úložišti pomocí ověřování SAS nebo spravované identity pracovního prostoru.

## <a name="overview"></a>Přehled

Aby byla zajištěna podpora hladkého prostředí pro místo na místě dotazování na data umístěná v Azure Storage soubory, fond SQL bez serveru používá funkci [OpenRowset](develop-openrowset.md) s dalšími možnostmi:

- [Dotazování na více souborů nebo složek](#query-multiple-files-or-folders)
- [Formát souboru PARQUET](#query-parquet-files)
- [Dotaz na CSV a text s oddělovači (ukončovací znak pole, ukončovací znak řádku, řídicí znak)](#query-csv-files)
- [Čtení zvolené podmnožiny sloupců](#read-a-chosen-subset-of-columns)
- [Odvození schématu](#schema-inference)
- [filename – funkce](#filename-function)
- [FilePath – funkce](#filepath-function)
- [Práce se složitými typy a vnořenými nebo opakovanými datovými strukturami](#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="query-parquet-files"></a>Dotazy na soubory PARQUET

K dotazování na zdrojová data Parquet použijte FORMAT = ' PARQUET '.

```syntaxsql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net//mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (C1 int, C2 varchar(20), C3 varchar(max)) as rows
```

Příklady použití najdete v článku o [souborech dotazů Parquet](query-parquet-files.md) .

## <a name="query-csv-files"></a>Dotazování souborů CSV

Dotaz na zdrojová data sdíleného svazku clusteru získáte pomocí FORMAT = CSV. Schéma souboru CSV můžete zadat jako součást `OPENROWSET` funkce při dotazování na soubory CSV:

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.csv', FORMAT = 'CSV', PARSER_VERSION='2.0') 
WITH (C1 int, C2 varchar(20), C3 varchar(max)) as rows
```

K dispozici jsou některé další možnosti, které lze použít k úpravě pravidel analýzy na vlastní formát CSv:
- ESCAPE_CHAR = char určuje znak v souboru, který se používá k zaznamenání samotného řídicího panelu a všech hodnot oddělovače v souboru. Je-li řídicí znak následován jinou hodnotou než samotnou nebo kteroukoli z hodnot oddělovače, je řídicí znak při čtení hodnoty vynechán.
Parametr ESCAPE_CHAR bude použit bez ohledu na to, jestli je FIELDQUOTE nebo není povolený. Nepoužije se k řídicímu znaku pro uvozovky. Znak Quota musí být uvozen jiným znakem pro quotování. Znak quotace se může objevit v rámci hodnoty sloupce jenom v případě, že je hodnota zapouzdřená pomocí znaků quote.
- FIELDTERMINATOR = ' field_terminator ' Určuje ukončovací znak pole, který se má použít. Výchozí ukončovací znak pole je čárka (**,**).
- ROWTERMINATOR = ' row_terminator ' Určuje ukončovací znak řádku, který má být použit. Výchozí ukončovací znak řádku je znak nového řádku: **\r\n**.

## <a name="file-schema"></a>Schéma souboru

Jazyk SQL v synapse SQL umožňuje definovat schéma souboru jako součást `OPENROWSET` funkce a číst všechny nebo podmnožiny sloupců nebo se pokusí automaticky určit typy sloupců ze souboru pomocí odvození schématu.

### <a name="read-a-chosen-subset-of-columns"></a>Čtení zvolené podmnožiny sloupců

Chcete-li určit sloupce, které chcete číst, můžete v rámci příkazu zadat volitelnou klauzuli WITH `OPENROWSET` .

- Pokud jsou k dispozici datové soubory CSV, pro čtení všech sloupců zadejte názvy sloupců a jejich datové typy. Pokud chcete podmnožinu sloupců, použijte řadové číslovky a vyberte sloupce z původních datových souborů podle pořadového čísla. Sloupce budou vázány podle ordinálního označení.
- Pokud jsou k dispozici datové soubory Parquet, zadejte názvy sloupců, které odpovídají názvům sloupců v původních datových souborech. Sloupce budou vázány podle názvu.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (
      C1 int, 
      C2 varchar(20),
      C3 varchar(max)
) as rows
```

U každého sloupce je třeba zadat název sloupce a `WITH` klauzuli Type.
Ukázky najdete v tématu [čtení souborů CSV bez zadání všech sloupců](query-single-csv-file.md#return-a-subset-of-columns).

## <a name="schema-inference"></a>Odvození schématu

Vyvoláním klauzule WITH z `OPENROWSET` příkazu můžete pokyn, aby služba automaticky zjišťovala (odvodit) schéma ze základních souborů.

> [!NOTE]
> Tato funkce aktuálně funguje pouze pro formát souboru PARQUET.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
```

Zajistěte, aby se pro optimální výkon používaly [příslušné odvozené datové typy](best-practices-sql-on-demand.md#check-inferred-data-types) . 

## <a name="query-multiple-files-or-folders"></a>Dotazování na více souborů nebo složek

Chcete-li spustit dotaz T-SQL nad sadou souborů v rámci složky nebo sady složek při jejich zpracování jako jednu entitu nebo sadu řádků, zadejte cestu ke složce nebo vzoru (pomocí zástupných znaků) prostřednictvím sady souborů nebo složek.

Platí následující pravidla:

- Vzory se můžou objevit buď v rámci cesty k adresáři, nebo v názvu souboru.
- V jednom kroku adresáře nebo názvu souboru se může zobrazit několik vzorů.
- Pokud je k dispozici více zástupných znaků, budou do výsledné sady souborů zahrnuty soubory v rámci všech vyhovujících cest.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/myroot/*/mysubfolder/*.parquet', FORMAT = 'PARQUET' ) as rows
```

Příklady použití najdete v tématu [složky dotazů a více souborů](query-folders-multiple-csv-files.md) .

## <a name="file-metadata-functions"></a>Funkce metadat souborů

### <a name="filename-function"></a>Filename – funkce

Tato funkce vrátí název souboru, ze kterého pochází řádek. 

Pokud chcete zadat dotaz na konkrétní soubory, přečtěte si oddíl filename v článku věnovaném [dotazům pro konkrétní soubory](query-specific-files.md#filename) .

Návratový typ dat je nvarchar (1024). Pro zajištění optimálního výkonu vždy přetypování výsledku funkce filename na vhodný datový typ. Použijete-li datový typ znaků, ujistěte se, že je použita odpovídající délka.

### <a name="filepath-function"></a>FilePath – funkce

Tato funkce vrací úplnou cestu nebo část cesty:

- Při volání bez parametru vrátí úplnou cestu k souboru, ze které řádek pochází.
- Při volání s parametrem vrátí část cesty, která odpovídá zástupnému znaku na pozici zadané v parametru. Například hodnota parametru 1 vrátí část cesty, která odpovídá prvnímu zástupnému znaku.

Další informace najdete v části FilePath v článku věnovaném [dotazům pro konkrétní soubory](query-specific-files.md#filepath) .

Návratový typ dat je nvarchar (1024). Pro zajištění optimálního výkonu vždy přetypování výsledku funkce FilePath na vhodný datový typ. Použijete-li datový typ znaků, ujistěte se, že je použita odpovídající délka.

## <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Práce se složitými typy a vnořenými nebo opakovanými datovými strukturami

Aby bylo možné zapnout hladké prostředí s daty uloženými ve vnořených nebo opakovaných datových typech, jako jsou například soubory [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) , přidají se následující rozšíření do fondu SQL bez serveru.

#### <a name="project-nested-or-repeated-data"></a>Vnořená nebo opakující se data projektu

Chcete-li data projektu, spusťte příkaz SELECT v souboru Parquet, který obsahuje sloupce vnořených datových typů. Ve výstupu budou vnořené hodnoty serializovány do formátu JSON a vráceny jako datový typ varchar (8000) SQL.

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Podrobnější informace najdete v části vnořený nebo opakovaná data v článku věnovaném [dotazům Parquet nesteded Types](query-parquet-nested-types.md#project-nested-or-repeated-data) .

#### <a name="access-elements-from-nested-columns"></a>Přístup k prvkům z vnořených sloupců

Pro přístup k vnořeným prvkům z vnořeného sloupce, jako je například struktura, použijte tečkovou notaci k zřetězení názvů polí do cesty. Zadejte cestu jako column_name v klauzuli WITH `OPENROWSET` funkce.

Příklad fragmentu syntaxe je následující:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Ve výchozím nastavení `OPENROWSET` funkce odpovídá názvu a cestě zdrojového pole s názvy sloupců, které jsou zadány v klauzuli WITH. Prvky obsažené v různých úrovních vnoření v rámci stejného zdrojového souboru Parquet lze použít prostřednictvím klauzule WITH.

**Vrácené hodnoty**

- Funkce vrací skalární hodnotu, jako je int, Decimal, a varchar, ze zadaného elementu a v zadané cestě pro všechny typy Parquet, které nejsou ve skupině vnořeného typu.
- Pokud cesta odkazuje na element, který je vnořeného typu, funkce vrátí fragment JSON od horního prvku na zadané cestě. Fragment kódu JSON je typu varchar (8000).
- Pokud vlastnost nebyla nalezena v zadaném column_name, funkce vrátí chybu.
- Pokud vlastnost nelze nalézt v zadaném column_path v závislosti na [režimu cesty](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true#PATHMODE), funkce vrátí chybu v režimu Strict nebo null v režimu Lax.

V případě ukázek dotazů si přečtěte část přístupové prvky z vnořených sloupců v článku [dotaz Parquet nesteded Types](query-parquet-nested-types.md#read-properties-from-nested-object-columns) .

#### <a name="access-elements-from-repeated-columns"></a>Přístup k prvkům z opakujících se sloupců

Chcete-li získat přístup k prvkům z opakujícího se sloupce, jako je například prvek pole nebo mapa, použijte funkci [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) pro každý skalární prvek, který potřebujete k projektu a poskytnout:

- Vnořený nebo opakovaný sloupec jako první parametr
- [Cesta JSON](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) , která určuje element nebo vlastnost, pro kterou má být přístup, jako druhý parametr

Chcete-li získat přístup k neskalárním prvkům z opakujícího se sloupce, použijte funkci [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?view=azure-sqldw-latest&preserve-view=true) pro každý neskalární prvek, který potřebujete k projektu a poskytnout:

- Vnořený nebo opakovaný sloupec jako první parametr
- [Cesta JSON](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) , která určuje element nebo vlastnost, pro kterou má být přístup, jako druhý parametr

Viz fragment syntaxe níže:

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Můžete najít Ukázky dotazů pro přístup k elementům z opakujících se sloupců v článku [dotaz Parquet nesteded Types](query-parquet-nested-types.md#access-elements-from-repeated-columns) .

## <a name="query-samples"></a>Ukázky dotazů

Můžete se dozvědět více o dotazování různých typů dat pomocí ukázkových dotazů.

### <a name="tools"></a>nástroje

Nástroje, které potřebujete k vydávání dotazů:
    - Azure Synapse Studio 
    - Azure Data Studio
    - SQL Server Management Studio

### <a name="demo-setup"></a>Nastavení ukázky

Prvním krokem je **Vytvoření databáze** , ve které budete spouštět dotazy. Pak inicializujete objekty spuštěním [instalačního skriptu](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) v této databázi. 

Tento instalační skript vytvoří zdroje dat, přihlašovací údaje v oboru databáze a formáty externích souborů, které se použijí pro čtení dat v těchto ukázkách.

> [!NOTE]
> Databáze se používají jenom pro zobrazení metadat, nikoli pro skutečná data.  Poznamenejte si název databáze, který používáte, budete ho potřebovat později.

```sql
CREATE DATABASE mydbname;
```

### <a name="provided-demo-data"></a>Poskytnutá ukázková data

Ukázková data obsahují následující sady dat:

- NYC taxislužby – žlutý záznam o cestách taxislužby – součást veřejné sady dat NYC ve formátu CSV a Parquet
- Sada dat naplnění ve formátu CSV
- Ukázkové soubory Parquet s vnořenými sloupci
- Knihy ve formátu JSON

| Cesta ke složce                                                  | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Formát                                                        | Nadřazená složka pro data ve formátu CSV                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Složky s datovými soubory populace v různých formátech CSV. |
| /csv/taxi/                                                   | Složka se soubory veřejných dat NYC ve formátu CSV              |
| Parquet                                                    | Nadřazená složka pro data ve formátu Parquet                     |
| /parquet/taxi                                                | NYC veřejné datové soubory ve formátu Parquet, rozdělené podle roku a měsíčně pomocí schématu dělení na oddíly (Hadoop). |
| /parquet/nested/                                             | Ukázkové soubory Parquet s vnořenými sloupci                     |
| JSON                                                       | Nadřazená složka pro data ve formátu JSON                        |
| /json/books/                                                 | Soubory JSON s daty z knih                                   |


## <a name="next-steps"></a>Další kroky

Další informace o tom, jak zadávat dotazy na různé typy souborů a vytvářet a používat zobrazení, najdete v následujících článcích:

- [Dotazování souborů CSV](query-single-csv-file.md)
- [Dotazování souborů Parquet](query-parquet-files.md)
- [Dotazování souborů JSON](query-json-files.md)
- [Dotaz na vnořené hodnoty](query-parquet-nested-types.md)
- [Složky dotazů a více souborů CSV](query-folders-multiple-csv-files.md)
- [Použití metadat souboru v dotazech](query-specific-files.md)
- [Vytváření a používání zobrazení](create-use-views.md)
