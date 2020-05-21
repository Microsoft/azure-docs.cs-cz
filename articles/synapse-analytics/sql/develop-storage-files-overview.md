---
title: Dotazování souborů úložiště pomocí SQL na vyžádání (Preview) v synapse SQL
description: Popisuje dotazování souborů úložiště pomocí prostředků SQL na vyžádání (ve verzi Preview) v rámci synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 204fd1b1a0a2984886684bbabf33dc7e73c1b45c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653534"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Dotazování souborů úložiště pomocí prostředků SQL na vyžádání (ve verzi Preview) v synapse SQL

SQL na vyžádání (Preview) umožňuje dotazovat data v Data Lake. Nabízí oblast dotazu T-SQL, která se vejde na částečně strukturované a nestrukturované datové dotazy.

Pro dotazování jsou podporovány následující aspekty T-SQL:

- Celý [Výběr](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) oblasti plochy, včetně většiny funkcí SQL, operátorů a tak dále.
- Možnost vytvořit externí tabulku jako SELECT ([CETAS](develop-tables-cetas.md)) vytvoří [externí tabulku](develop-tables-external-tables.md) a potom exportuje paralelně výsledky příkazu SELECT jazyka Transact-SQL pro Azure Storage.

Další informace o tom, co je vs. v současné době není podporováno, najdete v článku [Přehled SQL na vyžádání](on-demand-workspace-overview.md) .

Když uživatelé Azure AD spouštějí dotazy, výchozí hodnota je pro účty úložiště, pro které se má přístup provést pomocí předávacího ověřovacího protokolu Azure AD. V takovém případě budou uživatelé zosobněni a oprávnění zkontrolována na úrovni úložiště. Přístup k [úložišti můžete řídit](develop-storage-files-storage-access-control.md) podle svých potřeb.

## <a name="extensions"></a>Rozšíření

Za účelem podpory hladkého prostředí pro účely dotazování na data umístěná v Azure Storage soubory používá SQL na vyžádání funkci [OpenRowset](develop-openrowset.md) s dalšími možnostmi:

- [Dotazování na více souborů nebo složek](#query-multiple-files-or-folders)
- [Formát souboru PARQUET](#parquet-file-format)
- [Další možnosti pro práci s odděleným textem (ukončovací znak pole, ukončovací znak řádku, řídicí znak)](#additional-options-for-working-with-delimited-text)
- [Čtení zvolené podmnožiny sloupců](#read-a-chosen-subset-of-columns)
- [Odvození schématu](#schema-inference)
- [filename – funkce](#filename-function)
- [FilePath – funkce](#filepath-function)
- [Práce se složitými typy a vnořenými nebo opakovanými datovými strukturami](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>Dotazování na více souborů nebo složek

Chcete-li spustit dotaz T-SQL nad sadou souborů v rámci složky nebo sady složek při jejich zpracování jako jednu entitu nebo sadu řádků, zadejte cestu ke složce nebo vzoru (pomocí zástupných znaků) prostřednictvím sady souborů nebo složek.

Platí následující pravidla:

- Vzory se můžou objevit buď v rámci cesty k adresáři, nebo v názvu souboru.
- V jednom kroku adresáře nebo názvu souboru se může zobrazit několik vzorů.
- Pokud je k dispozici více zástupných znaků, budou do výsledné sady souborů zahrnuty soubory v rámci všech vyhovujících cest.

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

Příklady použití najdete v tématu [složky dotazů a více souborů](query-folders-multiple-csv-files.md) .

### <a name="parquet-file-format"></a>Formát souboru PARQUET

K dotazování na zdrojová data Parquet použijte FORMAT = ' PARQUET '.

```syntaxsql
OPENROWSET
(
    { BULK 'data_file' ,
    { FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
)
AS table_alias(column_alias,...n)
<bulk_options> ::=
...
[ , FORMAT = {'CSV' | 'PARQUET'} ]
```

Příklady použití najdete v článku o [souborech dotazů Parquet](query-parquet-files.md) .

### <a name="additional-options-for-working-with-delimited-text"></a>Další možnosti pro práci s odděleným textem

Tyto další parametry jsou představené pro práci se soubory CSV (s oddělovači textu):

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- ESCAPE_CHAR = char určuje znak v souboru, který se používá k zaznamenání samotného řídicího panelu a všech hodnot oddělovače v souboru. Je-li řídicí znak následován buď jinou hodnotou než samotná, nebo jakoukoli hodnotou oddělovače, je řídicí znak při čtení hodnoty vynechán.
Parametr ESCAPE_CHAR bude použit bez ohledu na to, jestli je FIELDQUOTE nebo není povolený. Nepoužije se k řídicímu znaku pro uvozovky. Znak quotování je řídicím znakem s dvojitými uvozovkami v zarovnání s chováním CSV v Excelu.
- FIELDTERMINATOR = ' field_terminator ' Určuje ukončovací znak pole, který se má použít. Výchozí ukončovací znak pole je čárka (**,**).
- ROWTERMINATOR = ' row_terminator ' Určuje ukončovací znak řádku, který má být použit. Výchozí ukončovací znak řádku je znak nového řádku: **\r\n**.

### <a name="read-a-chosen-subset-of-columns"></a>Čtení zvolené podmnožiny sloupců

Chcete-li určit sloupce, které chcete číst, můžete zadat volitelnou klauzuli WITH v rámci příkazu OPENROWSET.

- Pokud jsou k dispozici datové soubory CSV, pro čtení všech sloupců zadejte názvy sloupců a jejich datové typy. Pokud chcete podmnožinu sloupců, použijte řadové číslovky a vyberte sloupce z původních datových souborů podle pořadového čísla. Sloupce budou vázány podle ordinálního označení.
- Pokud jsou k dispozici datové soubory Parquet, zadejte názvy sloupců, které odpovídají názvům sloupců v původních datových souborech. Sloupce budou vázány podle názvu.

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

Ukázky najdete v tématu [čtení souborů CSV bez zadání všech sloupců](query-single-csv-file.md#returning-subset-of-columns).

### <a name="schema-inference"></a>Odvození schématu

Vyvoláním klauzule WITH z příkazu OPENROWSET můžete dát službě pokyn k automatickému zjištění (odvodit) schématu ze základních souborů.

> [!NOTE]
> Tato funkce aktuálně funguje pouze pro formát souboru PARQUET.

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');
```

Zajistěte, aby se pro optimální výkon používaly [příslušné odvozené datové typy](best-practices-sql-on-demand.md#check-inferred-data-types) . 

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

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Práce se složitými typy a vnořenými nebo opakovanými datovými strukturami

Aby bylo možné zajistit hladké prostředí při práci s daty uloženými ve vnořených nebo opakovaných datových typech, jako jsou například soubory [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) , přidají se na vyžádání SQL na vyžádání rozšíření níže.

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

Pro přístup k vnořeným prvkům z vnořeného sloupce, jako je například struktura, použijte tečkovou notaci k zřetězení názvů polí do cesty. Zadejte cestu jako column_name v klauzuli WITH funkce OPENROWSET.

Příklad fragmentu syntaxe je následující:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Ve výchozím nastavení funkce OPENROWSET odpovídá názvu a cestě zdrojového pole s názvy sloupců, které jsou zadány v klauzuli WITH. Prvky obsažené v různých úrovních vnoření v rámci stejného zdrojového souboru Parquet lze použít prostřednictvím klauzule WITH.

**Vrácené hodnoty**

- Funkce vrací skalární hodnotu, například int, Decimal, a varchar, ze zadaného elementu a v zadané cestě pro všechny typy Parquet, které nejsou ve skupině vnořeného typu.
- Pokud cesta odkazuje na element, který je vnořeného typu, funkce vrátí fragment JSON od horního prvku na zadané cestě. Fragment kódu JSON je typu varchar (8000).
- Pokud vlastnost nebyla nalezena v zadaném column_name, funkce vrátí chybu.
- Pokud vlastnost nelze nalézt v zadaném column_path v závislosti na [režimu cesty](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE), funkce vrátí chybu v režimu Strict nebo null v režimu Lax.

V případě ukázek dotazů si přečtěte část přístupové prvky z vnořených sloupců v článku [dotaz Parquet nesteded Types](query-parquet-nested-types.md#access-elements-from-nested-columns) .

#### <a name="access-elements-from-repeated-columns"></a>Přístup k prvkům z opakujících se sloupců

Chcete-li získat přístup k prvkům z opakujícího se sloupce, jako je například prvek pole nebo mapa, použijte funkci [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) pro každý skalární prvek, který potřebujete k projektu a poskytnout:

- Vnořený nebo opakovaný sloupec jako první parametr
- [Cesta JSON](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) , která určuje element nebo vlastnost, pro kterou má být přístup, jako druhý parametr

Chcete-li získat přístup k neskalárním prvkům z opakujícího se sloupce, použijte funkci [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) pro každý neskalární prvek, který potřebujete k projektu a poskytnout:

- Vnořený nebo opakovaný sloupec jako první parametr
- [Cesta JSON](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) , která určuje element nebo vlastnost, pro kterou má být přístup, jako druhý parametr

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

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak zadávat dotazy na různé typy souborů a vytvářet a používat zobrazení, najdete v následujících článcích:

- [Dotaz na jeden soubor CSV](query-single-csv-file.md)
- [Dotazování souborů Parquet](query-parquet-files.md)
- [Dotazování souborů JSON](query-json-files.md)
- [Dotazování vnořených typů Parquet](query-parquet-nested-types.md)
- [Složky dotazů a více souborů CSV](query-folders-multiple-csv-files.md)
- [Použití metadat souboru v dotazech](query-specific-files.md)
- [Vytváření a používání zobrazení](create-use-views.md)
