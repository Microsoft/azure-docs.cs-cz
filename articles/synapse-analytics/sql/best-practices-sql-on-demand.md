---
title: Osvědčené postupy pro SQL na vyžádání (Preview)
description: Doporučení a osvědčené postupy, které byste měli znát při práci s SQL na vyžádání (Preview).
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 9de3e3503d63cf6dcaa98adc318d86df7700458d
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241867"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Osvědčené postupy pro SQL na vyžádání (Preview) ve službě Azure synapse Analytics

V tomto článku najdete kolekci osvědčených postupů pro používání SQL na vyžádání (Preview). SQL na vyžádání je prostředek ve službě Azure synapse Analytics.

## <a name="general-considerations"></a>Obecné aspekty

SQL na vyžádání umožňuje dotazování souborů ve vašich účtech úložiště Azure. Nemá místní úložiště ani možnosti ingestování. Takže všechny soubory, které dotaz cílí, jsou externí vůči SQL na vyžádání. Všechno, co souvisí s čtením souborů ze služby Storage, může mít dopad na výkon dotazů.

## <a name="colocate-your-azure-storage-account-and-sql-on-demand"></a>Vyhledání účtu úložiště Azure a SQL na vyžádání

Pokud chcete minimalizovat latenci, Najděte svůj účet služby Azure Storage a koncový bod SQL na vyžádání. Účty úložiště a koncové body zřízené během vytváření pracovního prostoru se nacházejí ve stejné oblasti.

Pro zajištění optimálního výkonu při přístupu k jiným účtům úložiště s SQL na vyžádání se ujistěte, že jsou ve stejné oblasti. Pokud nejsou ve stejné oblasti, dojde k vyšší latenci pro přenos dat v síti mezi vzdálenou oblastí a oblastí koncového bodu.

## <a name="azure-storage-throttling"></a>Omezení Azure Storage

K vašemu účtu úložiště může mít přístup více aplikací a služeb. K omezování úložiště dochází v případě, že kombinované IOPS nebo propustnost vygenerované aplikacemi, službami a úlohami SQL na vyžádání překračují limity účtu úložiště. Výsledkem je, že budete mít výrazný negativní vliv na výkon dotazů.

Při zjištění omezení má aplikace SQL na vyžádání vestavěnou manipulaci s jejich řešením. SQL na vyžádání bude podávat požadavky do úložiště pomalejším tempem, dokud se nevyřeší omezení.

> [!TIP]
> Pro optimální provádění dotazů nedělejte při provádění dotazů zdůraznit účet úložiště dalšími úlohami.

## <a name="prepare-files-for-querying"></a>Příprava souborů pro dotazování

Pokud je to možné, můžete připravit soubory pro lepší výkon:

- Převeďte CSV a JSON na Parquet. Parquet je sloupcový formát. Vzhledem k tomu, že jsou komprimované, jsou velikosti souborů menší než CSV nebo soubory JSON, které obsahují stejná data. SQL na vyžádání bude potřebovat méně času a méně požadavků na úložiště ke čtení.
- Pokud se dotaz zaměřuje na jeden velký soubor, budete ho moci rozdělit do několika menších souborů.
- Snažte se zachovat velikost souboru CSV pod 10 GB.
- Je lepší mít soubory stejné velikosti pro jednu cestu OPENROWSET nebo externí umístění tabulky.
- Vytvořte oddíly dat uložením oddílů do různých složek nebo názvů souborů. V tématu [použití funkcí filename a FilePath pro cílení na konkrétní oddíly](#use-filename-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-the-path"></a>Doručovat zástupné znaky na nižší úrovně v cestě

V cestě můžete použít zástupné znaky k [dotazování více souborů a složek](query-data-storage.md#query-multiple-files-or-folders). SQL na vyžádání obsahuje seznam souborů v účtu úložiště počínaje prvním * použitím rozhraní API úložiště. Eliminuje soubory, které neodpovídají zadané cestě. Zmenšení počátečního seznamu souborů může zlepšit výkon, pokud existuje mnoho souborů, které odpovídají zadané cestě až k prvnímu zástupnému znaku.

## <a name="use-appropriate-data-types"></a>Použití vhodných datových typů

Datové typy, které používáte v dotazu, mají vliv na výkon. Pokud budete postupovat podle těchto pokynů, můžete získat lepší výkon: 

- Použijte nejmenší velikost dat, která bude vyhovovat největší možné hodnotě.
  - Pokud je maximální délka znakové hodnoty 30 znaků, použijte datový typ znak o délce 30.
  - Pokud jsou všechny hodnoty sloupce znaků pevné velikosti, použijte **char** nebo **nchar** . V opačném případě použijte **varchar** nebo **nvarchar** .
  - Pokud je maximální hodnota sloupce Integer 500, použijte typ **smallint** , protože se jedná o nejmenší datový typ, který může být přizpůsoben této hodnotě. Rozsahy datových typů typu Integer můžete najít v [tomto článku](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15&preserve-view=true).
- Pokud je to možné, použijte typ **varchar** a **char** místo typu **nvarchar** a **nchar** .
- Pokud je to možné, použijte datové typy založené na celých číslech. Operace SORT, JOIN a GROUP BY se dokončí rychleji pomocí celých čísel než u dat znaků.
- Pokud používáte odvození schématu, [Prohlédněte si odvozené datové typy](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Kontrolovat odvozené datové typy

[Odvození schématu](query-parquet-files.md#automatic-schema-inference) vám pomůže rychle napsat dotazy a prozkoumat data bez znalosti schémat souborů. Náklady na tyto pohodlí jsou, že odvozené datové typy můžou být větší než skutečné datové typy. K tomu dojde v případě, že ve zdrojových souborech není dostatek informací, aby bylo zajištěno, že bude použit vhodný datový typ. Například soubory Parquet neobsahují metadata o maximální délce sloupce znaků. Takže SQL na vyžádání ho odvodí jako varchar (8000).

Můžete použít [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) ke kontrole výsledných datových typů dotazu.

Následující příklad ukazuje, jak lze optimalizovat odvozené datové typy. Tento postup slouží k zobrazení odvozených datových typů: 
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

Tady je sada výsledků dotazu:

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar (8000)|8000|
|0|2|pickup_datetime|datetime2 (7)|8|
|0|3|passenger_count|int|4|

Až budete znát odvozené datové typy pro dotaz, můžete zadat příslušné datové typy:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Použití funkcí filename a FilePath k cílení na konkrétní oddíly

Data jsou často organizována v oddílech. SQL na vyžádání můžete dát zadat dotaz na konkrétní složky a soubory. Tím se sníží počet souborů a množství dat, které dotaz potřebuje ke čtení a zpracování. Přidaný bonus znamená, že dosáhnete lepšího výkonu.

Další informace najdete v tématu o funkcích [filename](query-data-storage.md#filename-function) a [FilePath](query-data-storage.md#filepath-function) a viz příklady pro [dotazování na konkrétní soubory](query-specific-files.md).

> [!TIP]
> Vždy přetypování výsledků funkcí FilePath a filename na příslušné datové typy. Pokud používáte znakové datové typy, ujistěte se, že použijete příslušnou délku.

> [!NOTE]
> Funkce používané pro odstraňování oddílů, FilePath a filename se aktuálně nepodporují pro externí tabulky, kromě těch, které se automaticky vytvořily pro každou tabulku vytvořenou v Apache Spark pro Azure synapse Analytics.

Pokud uložená data nejsou rozdělená na oddíly, zvažte jejich dělení. Tímto způsobem můžete použít tyto funkce k optimalizaci dotazů, které cílí na tyto soubory. Při [dotazování na oddíly Apache Spark pro tabulky Azure synapse](develop-storage-files-spark-tables.md) z SQL na vyžádání bude dotaz automaticky cílit jenom na nezbytné soubory.

## <a name="use-parser_version-20-to-query-csv-files"></a>Použití PARSER_VERSION 2,0 k dotazování na soubory CSV

Při dotazování na soubory CSV můžete použít analyzátor optimalizovaný pro výkon. Podrobnosti najdete v tématu [PARSER_VERSION](develop-openrowset.md).

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Použití CETAS ke zvýšení výkonu a spojení dotazů

[CETAS](develop-tables-cetas.md) je jednou z nejdůležitějších funkcí, které jsou k dispozici v SQL na vyžádání. CETAS je paralelní operace, která vytváří externí metadata tabulky a exportuje výsledky dotazu SELECT do sady souborů v účtu úložiště.

Pomocí CETAS můžete ukládat často používané části dotazů, jako jsou připojené referenční tabulky, do nové sady souborů. Pak se můžete k této jedné externí tabulce připojit místo opakujících se běžných spojení ve více dotazech.

Když CETAS generuje soubory Parquet, Statistika se automaticky vytvoří, když první dotaz cílí na tuto externí tabulku, což vede k lepšímu výkonu.

## <a name="azure-ad-pass-through-performance"></a>Předávací výkon Azure AD

SQL na vyžádání umožňuje přístup k souborům v úložišti pomocí předávacích a ověřovacích přihlašovacích údajů SAS Azure Active Directory (Azure AD). S předáváním Azure AD může docházet k nižšímu výkonu, než byste použili SAS.

Pokud potřebujete lepší výkon, zkuste použít přihlašovací údaje SAS pro přístup do úložiště, dokud se zlepší výkon předávacího systému Azure AD.

## <a name="next-steps"></a>Další kroky

Řešení běžných problémů najdete v článku [věnovaném řešení potíží](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) . Pokud pracujete s fondy SQL místo SQL na vyžádání, přečtěte si téma [osvědčené postupy pro fondy SQL](best-practices-sql-pool.md) pro konkrétní pokyny.
