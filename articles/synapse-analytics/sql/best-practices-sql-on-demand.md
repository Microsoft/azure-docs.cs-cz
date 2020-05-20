---
title: Osvědčené postupy pro SQL na vyžádání (Preview) ve službě Azure synapse Analytics
description: Doporučení a osvědčené postupy, které byste měli znát při práci s SQL na vyžádání (Preview).
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 0792dd98bb554b7b430676472c27274f3312d381
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701365"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Osvědčené postupy pro SQL na vyžádání (Preview) ve službě Azure synapse Analytics

V tomto článku najdete kolekci osvědčených postupů pro používání SQL na vyžádání (Preview). SQL na vyžádání je další prostředek ve službě Azure synapse Analytics.

## <a name="general-considerations"></a>Obecné aspekty

SQL na vyžádání umožňuje dotazování souborů ve vašich účtech úložiště Azure. Nemá místní úložiště ani možnosti ingestování. V takovém případě všechny soubory, které dotaz cílí, jsou externí pro SQL na vyžádání. Všechno, co souvisí s čtením souborů ze služby Storage, může mít dopad na výkon dotazů.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Vyhledání účtu Azure Storage a SQL na vyžádání

Pokud chcete minimalizovat latenci, Najděte svůj účet služby Azure Storage a koncový bod SQL na vyžádání. Účty úložiště a koncové body zřízené během vytváření pracovního prostoru se nacházejí ve stejné oblasti.

Pro zajištění optimálního výkonu při přístupu k jiným účtům úložiště s SQL na vyžádání se ujistěte, že jsou ve stejné oblasti. Pokud nejsou ve stejné oblasti, dojde k vyšší latenci pro přenos dat v síti mezi oblastmi vzdáleného a koncového bodu.

## <a name="azure-storage-throttling"></a>Omezení Azure Storage

K vašemu účtu úložiště může mít přístup více aplikací a služeb. K omezování úložiště dochází v případě, že kombinované IOPS nebo propustnost vygenerované aplikacemi, službami a úlohami SQL na vyžádání překračují limity účtu úložiště. Výsledkem je, že budete mít výrazný negativní vliv na výkon dotazů.

Po zjištění omezení má na vyžádání SQL na vyžádání vestavěnou manipulaci s tímto scénářem. SQL na vyžádání bude podávat požadavky do úložiště pomalejším tempem, dokud se nevyřeší omezení.

> [!TIP]
> Pro optimální provádění dotazů byste během provádění dotazů neměli nastrese účet úložiště dalšími úlohami.

## <a name="prepare-files-for-querying"></a>Příprava souborů pro dotazování

Pokud je to možné, můžete připravit soubory pro lepší výkon:

- Převod CSV a JSON na Parquet-Parquet je sloupcový formát. Vzhledem k tomu, že je komprimovaná, jsou velikosti souborů menší než CSV nebo soubory JSON se stejnými daty. SQL na vyžádání bude potřebovat kratší dobu a požadavky na úložiště pro jeho čtení.
- Pokud se dotaz zaměřuje na jeden velký soubor, budete ho moci rozdělit do několika menších souborů.
- Zkuste zachovat velikost souboru CSV pod 10 GB.
- Je lepší mít soubory stejné velikosti pro jednu cestu OPENROWSET nebo externí umístění tabulky.
- Rozdělení dat do oddílů ukládáním oddílů do různých složek nebo názvů souborů – Pokud [chcete cílit na konkrétní oddíly, ověřte použití funkcí filename a FilePath](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-path"></a>Vložení zástupných znaků na nižší úrovně v cestě

V cestě můžete použít zástupné znaky k [dotazování více souborů a složek](develop-storage-files-overview.md#query-multiple-files-or-folders). SQL na vyžádání vypíše soubory ve vašem účtu úložiště počínaje prvním * pomocí rozhraní API úložiště a eliminuje soubory, které se neshodují s určenou cestou. Zmenšení počátečního seznamu souborů může zvýšit výkon, pokud existuje mnoho souborů, které odpovídají zadané cestě k prvnímu zástupnému znaku.

## <a name="use-appropriate-data-types"></a>Použití vhodných datových typů

Datové typy, které používáte ve svém vlivu na výkon dotazu. Můžete získat lepší výkon, pokud: 

- Použijte nejmenší velikost dat, která bude vyhovovat největší možné hodnotě.
  - Pokud je maximální délka znakové hodnoty 30 znaků, použijte datový typ znak o délce 30.
  - Pokud jsou všechny hodnoty sloupce znaků pevné velikosti, použijte char nebo nchar. V opačném případě použijte varchar nebo nvarchar.
  - Pokud je maximální hodnota sloupce celé číslo 500, použijte typ smallint, protože se jedná o nejmenší datový typ, který může být přizpůsoben této hodnotě. [Zde](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15)můžete najít rozsahy datových typů typu Integer.
- Pokud je to možné, použijte typ varchar a char místo typu nvarchar a nchar.
- Pokud je to možné, použijte datové typy založené na celých číslech. Operace řazení, spojování a seskupit podle jsou prováděny rychleji na základě celých čísel než u znaků.
- Pokud používáte odvození schématu, ověřte, zda se jedná o [typ dat s odvozeným datovým typem](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Kontrolovat odvozené datové typy

[Odvození schématu](query-parquet-files.md#automatic-schema-inference) vám pomůže rychle napsat dotazy a prozkoumat data bez znalosti schématu souborů. Tato pohodlí se dodává na úkor odvozených datových typů, které jsou větší než ve skutečnosti. K tomu dochází, když ve zdrojových souborech není dostatek informací, aby se zajistilo, že se použije vhodný datový typ. Například soubory Parquet neobsahují metadata o maximální délce sloupce znaků a SQL na vyžádání odvodí jako varchar (8000). 

Můžete kontrolovat výsledné datové typy dotazu pomocí [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15).

Následující příklad ukazuje, jak lze optimalizovat odvozené datové typy. Procedura slouží k zobrazení odvozených datových typů. 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

Zde je sada výsledků.

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar (8000)|8000|
|0|2|pickup_datetime|datetime2 (7)|8|
|0|3|passenger_count|int|4|

Jakmile víme o odvozených datových typech pro dotaz, můžeme specifikovat vhodné datové typy:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Použití funkcí FileInfo a FilePath k cílení na konkrétní oddíly

Data jsou často organizována v oddílech. SQL na vyžádání můžete dát zadat dotaz na konkrétní složky a soubory. Tato funkce omezí počet souborů a množství dat, které musí dotaz číst a zpracovat. Přidaný bonus znamená, že dosáhnete lepšího výkonu.

Další informace najdete v tématu funkce [filename](develop-storage-files-overview.md#filename-function) a [FilePath](develop-storage-files-overview.md#filepath-function) a příklady, jak [zadávat dotazy na konkrétní soubory](query-specific-files.md).

> [!TIP]
> Vždy přetypujte výsledek FilePath a funkce FileInfo na příslušné datové typy. Pokud používáte znakové datové typy, ujistěte se, že je použita odpovídající délka.

> [!NOTE]
> Funkce používané pro vyloučení oddílu, FilePath a FileInfo nejsou v současné době podporovány u externích tabulek, které nejsou vytvořeny automaticky pro každou tabulku vytvořenou v Apache Spark pro Azure synapse Analytics.

Pokud vaše uložená data nejsou rozdělená na oddíly, zvažte jejich dělení, aby bylo možné použít tyto funkce k optimalizaci dotazů, které cílí na tyto soubory. Při [dotazování na dělené Apache Spark tabulek Azure synapse](develop-storage-files-spark-tables.md) z SQL na vyžádání bude dotaz automaticky cílit jenom na potřebné soubory.

## <a name="use-parser_version-20-for-querying-csv-files"></a>Použití PARSER_VERSION 2,0 pro dotazování na soubory CSV

Při dotazování na soubory CSV můžete použít analyzátor optimalizované pro výkon. Podrobnosti najdete [PARSER_VERSION](develop-openrowset.md) .

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Použití CETAS ke zvýšení výkonu a spojení dotazů

[CETAS](develop-tables-cetas.md) je jednou z nejdůležitějších funkcí, které jsou k dispozici v SQL na vyžádání. CETAS je paralelní operace, která vytváří externí metadata tabulky a exportuje výsledky dotazu SELECT do sady souborů v účtu úložiště.

Pomocí CETAS můžete ukládat často používané části dotazů, jako jsou připojené referenční tabulky, do nové sady souborů. V dalším kroku se můžete k této jedné externí tabulce připojit místo opakujících se běžných spojení ve více dotazech.

Když CETAS generuje soubory Parquet, Statistika se automaticky vytvoří, když první dotaz cílí na tuto externí tabulku, což vede k lepšímu výkonu.

## <a name="aad-pass-through-performance"></a>Předávací výkon AAD

SQL na vyžádání umožňuje přístup k souborům v úložišti pomocí pověření AAD Pass-through nebo SAS. Pomocí předávacího průchodu AAD, který se porovnává s SAS, může docházet k nižšímu výkonu. 

Pokud potřebujete lepší výkon, vyzkoušejte přihlašovací údaje SAS pro přístup do úložiště, dokud se nezlepší výkon předávacího průchodu AAD.

## <a name="next-steps"></a>Další kroky

Běžné problémy a řešení najdete v článku [věnovaném řešení potíží](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) . Pokud pracujete s fondem SQL místo SQL na vyžádání, přečtěte si článek [osvědčené postupy pro fond SQL](best-practices-sql-pool.md) , kde najdete konkrétní pokyny.
