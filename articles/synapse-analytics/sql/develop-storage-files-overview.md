---
title: Dotazovat se na soubory úložiště pomocí SQL na vyžádání (preview) v rámci Synapse SQL
description: Popisuje dotazování souborů úložiště pomocí prostředků SQL na vyžádání (náhled) v rámci Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8e6fd4955d6d68764895619efd968a957bcb5184
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460592"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Dotazovat se na soubory úložiště pomocí prostředků SQL na vyžádání (preview) v rámci Synapse SQL

SQL na vyžádání (preview) umožňuje dotazovat data v datovém jezeře. Nabízí plochu dotazu T-SQL, která vyhovuje polostrukturovaným a nestrukturovaným datovým dotazům.

Pro dotazování jsou podporovány následující aspekty T-SQL:

- Úplná [plocha SELECT,](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) včetně většiny funkcí SQL, operátorů a tak dále.
- VYTVOŘIT EXTERNÍ TABULKU JAKO SELECT[(CETAS)](develop-tables-cetas.md)vytvoří [externí tabulku](develop-tables-external-tables.md) a pak exportuje, paralelně, výsledky Příkaz Transact-SQL SELECT do Služby Azure Storage.

Další informace o tom, co je vs. co není aktuálně podporováno, najdete v článku [přehledu na vyžádání SQL.](on-demand-workspace-overview.md)

Když uživatelé Azure AD spouštějí dotazy, výchozí je pro účty úložiště, které mají být přístupné pomocí předávacího ověřovacího protokolu Azure AD. Jako takové budou uživatelé zosobněni a oprávnění zkontrolována na úrovni úložiště. Můžete [řídit přístup k úložišti](develop-storage-files-storage-access-control.md) tak, aby vyhovoval vašim potřebám.

## <a name="extensions"></a>Rozšíření

Pro podporu hladkého prostředí pro dotazování na místě na základě dotazů na data, která se nacházejí v souborech Azure Storage, sql na vyžádání používá funkci [OPENROWSET](develop-openrowset.md) s dalšími funkcemi:

- [Dotaz na více souborů nebo složek](#query-multiple-files-or-folders)
- [FORMÁT SOUBORU PARKET](#parquet-file-format)
- [Další možnosti pro práci s odděleným textem (zakončení pole, zakončení řádku, escape char)](#additional-options-for-working-with-delimited-text)
- [Čtení vybrané podmnožiny sloupců](#read-a-chosen-subset-of-columns)
- [Odvození schématu](#schema-inference)
- [funkce název souboru](#filename-function)
- [funkce cesta souboru](#filepath-function)
- [Práce se složitými typy a vnořenými nebo opakovanými datovými strukturami](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>Dotaz na více souborů nebo složek

Chcete-li spustit dotaz T-SQL přes sadu souborů ve složce nebo sadě složek při jejich zacházení jako s jednou entitou nebo sadou řádků, zadejte cestu ke složce nebo vzoru (pomocí zástupných znaků) přes sadu souborů nebo složek.

Platí následující pravidla:

- Vzorky se mohou objevit buď v části cesty k adresáři, nebo v názvu souboru.
- Ve stejném kroku adresáře nebo názvu souboru se může zobrazit několik vzorků.
- Pokud existuje více zástupných znaků, budou do výsledné sady souborů zahrnuty soubory ve všech odpovídajících cestách.

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

Příklady použití naleznete ve [složkách dotazu a více souborech.](query-folders-multiple-csv-files.md)

### <a name="parquet-file-format"></a>FORMÁT SOUBORU PARKET

Chcete-li zadat dotaz na zdrojová data parket, použijte FORMÁT = "PARKETy"

```sql
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

Projděte si článek Soubory [parket y dotazu](query-parquet-files.md) pro příklady použití.

### <a name="additional-options-for-working-with-delimited-text"></a>Další možnosti pro práci s odděleným textem

Tyto další parametry jsou zavedeny pro práci se soubory CSV (oddělený text):

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- ESCAPE_CHAR = znak Určuje znak v souboru, který se používá k samotnému úniku, a všechny hodnoty oddělovače v souboru. Pokud je za řídicím znakem následována jiná hodnota než sama nebo některá z hodnot oddělovače, je při čtení hodnoty vynechán řídicí znak.
Parametr ESCAPE_CHAR bude použit bez ohledu na to, zda je nebo není povolena fieldquote. Nebude použit k úniku znaky citace. Znak citace je uvozen s dvojitými uvozovkami v souladu s chováním CSV aplikace Excel.
- FIELDTERMINATOR ='field_terminator' Určuje zakončení pole, které má být použito. Výchozí zakončení pole je čárka ("**,**")
- ROWTERMINATOR ='row_terminator' Určuje zakončení řádku, které má být použito. Výchozí zakončení řádku je znak nového řádku: **\r\n**.

### <a name="read-a-chosen-subset-of-columns"></a>Čtení vybrané podmnožiny sloupců

Chcete-li zadat sloupce, které chcete číst, můžete zadat volitelnou klauzuli WITH v rámci příkazu OPENROWSET.

- Pokud existují datové soubory CSV, chcete-li číst všechny sloupce, zadejte názvy sloupců a jejich datové typy. Pokud chcete podmnožinu sloupců, použijte řadová čísla k výběru sloupců z původních datových souborů podle počtu. Sloupce budou vázány pořitozovacím označením.
- Pokud existují datové soubory parket, zadejte názvy sloupců, které odpovídají názvům sloupců v původních datových souborech. Sloupce budou vázány názvem.

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

Ukázky naleznete v read [csv soubory bez zadání všech sloupců](query-single-csv-file.md#returning-subset-of-columns).

### <a name="schema-inference"></a>Odvození schématu

Vynecháním with klauzule z OPENROWSET prohlášení, můžete pokyn služby automaticky detekovat (odvodit) schéma z podkladových souborů.

> [!NOTE]
> To v současné době funguje pouze pro formát souboru PARKET.

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');

```

### <a name="filename-function"></a>Funkce Název souboru

Tato funkce vrátí název souboru, ze kterého řádek pochází.

Chcete-li zadat dotaz na určité soubory, přečtěte si oddíl Název_souboru v článku [Soubory specifické pro dotaz.](query-specific-files.md#filename)

### <a name="filepath-function"></a>Funkce Cesta souboru

Tato funkce vrátí úplnou cestu nebo část cesty:

- Při volání bez parametru vrátí úplnou cestu k souboru, ze které řádek pochází.
- Při volání s parametrem vrátí část cesty, která odpovídá zástupné znakové na pozici zadané v parametru. Například hodnota parametru 1 vrátí část cesty, která odpovídá prvnímu zástupný znak.

Další informace naleznete v části Filepath v článku [Soubory specifické pro dotaz.](query-specific-files.md#filepath)

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Práce se složitými typy a vnořenými nebo opakovanými datovými strukturami

Chcete-li povolit plynulé prostředí při práci s daty uloženými v vnořených nebo opakovaných datových typech, například v [parketových](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) souborech, sql na vyžádání přidal níže uvedená rozšíření.

#### <a name="project-nested-or-repeated-data"></a>Vnořená nebo opakovaná data projektu

Chcete-li promítnout data, spusťte příkaz SELECT přes soubor parket, který obsahuje sloupce vnořených datových typů. Na výstupu budou vnořené hodnoty serializovány do JSON a vráceny jako datový typ SQL varchar(8000).

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Podrobnější informace naleznete v části Project vnořená nebo opakovaná data v článku [Vnořené pole dotazu.](query-parquet-nested-types.md#project-nested-or-repeated-data)

#### <a name="access-elements-from-nested-columns"></a>Přístup k prvkům z vnořených sloupců

Chcete-li získat přístup k vnořeným prvkům z vnořeného sloupce, například Struct, použijte "dot notace" pro zřetězení názvů polí do cesty. Zadejte cestu jako column_name v with klauzuli openrowset funkce.

Příklad fragmentu syntaxe je následující:

```sql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Ve výchozím nastavení funkce OPENROWSET odpovídá názvu zdrojového pole a cestě s názvy sloupců uvedenými v klauzuli WITH. Prvky obsažené na různých úrovních vnoření v rámci stejného zdrojového souboru parket lze přistupovat prostřednictvím with klauzule.

**Vrácené hodnoty**

- Funkce vrátí skalární hodnotu, například int, decimal a varchar, ze zadaného prvku a na zadané cestě pro všechny typy parket, které nejsou ve skupině Vnořený typ.
- Pokud cesta odkazuje na prvek, který je vnořený typ, funkce vrátí fragment JSON od horního prvku na zadané cestě. Fragment JSON je typu varchar(8000).
- Pokud vlastnost nelze nalézt na zadané mcolumn_name, funkce vrátí chybu.
- Pokud vlastnost nelze nalézt na zadané m column_path v závislosti na [režimu Cesta](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE), funkce vrátí chybu, když v přísném režimu nebo null, když v laxním režimu.

Ukázky dotazů najdete v části Access elementy z vnořených sloupců v článku [Vnořené typy par dotazů.](query-parquet-nested-types.md#access-elements-from-nested-columns)

#### <a name="access-elements-from-repeated-columns"></a>Přístup k prvkům z opakovaných sloupců

Chcete-li získat přístup k prvkům z opakovaného sloupce, například prvku array nebo map, použijte [funkci JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) pro každý skalární prvek, který potřebujete promítat a zajišťovat:

- Vnořený nebo opakovaný sloupec jako první parametr
- [Cesta JSON,](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) která určuje prvek nebo vlastnost pro přístup, jako druhý parametr

Chcete-li získat přístup k neskalárním prvkům z opakovaného sloupce, použijte funkci [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) pro každý neskalární prvek, který potřebujete promítat, a zadejte:

- Vnořený nebo opakovaný sloupec jako první parametr
- [Cesta JSON,](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) která určuje prvek nebo vlastnost pro přístup, jako druhý parametr

Viz fragment syntaxe níže:

```sql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Ukázky dotazů pro přístup k prvkům z opakovaných sloupců najdete v článku [vnořené typy par dotazu.](query-parquet-nested-types.md#access-elements-from-repeated-columns)

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak dotazovat různé typy souborů a vytvářet a používat zobrazení, naleznete v následujících článcích:

- [Dotaz na jeden soubor CSV](query-single-csv-file.md)
- [Soubory parket dotazu](query-parquet-files.md)
- [Dotaz na soubory JSON](query-json-files.md)
- [Typy vnořených par dotazů](query-parquet-nested-types.md)
- [Složky dotazů a více souborů CSV](query-folders-multiple-csv-files.md)
- [Použití metadat souborů v dotazech](query-specific-files.md)
- [Vytváření a používání zobrazení](create-use-views.md)
