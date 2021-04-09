---
title: Osvědčené postupy pro fond SQL bez serveru
description: Doporučení a osvědčené postupy pro práci s fondem SQL bez serveru
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a47982012dcaa2eabda93c93508b23f30525812d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720385"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Osvědčené postupy pro fond SQL bez serveru ve službě Azure synapse Analytics

V tomto článku najdete kolekci osvědčených postupů pro použití fondu SQL bez serveru. Fond SQL bez serveru je prostředkem ve službě Azure synapse Analytics.

Fond SQL bez serveru umožňuje dotazování souborů ve vašich účtech úložiště Azure. Nemá místní úložiště ani možnosti ingestování. Všechny soubory, které dotaz cílí, jsou externí pro fond SQL bez serveru. Všechno, co souvisí s čtením souborů ze služby Storage, může mít dopad na výkon dotazů.

## <a name="client-applications-and-network-connections"></a>Klientské aplikace a síťová připojení

Ujistěte se, že je klientská aplikace připojená k nejbližšímu možnému pracovnímu prostoru synapse s optimálním připojením.
- Společně vyhledejte klientskou aplikaci v pracovním prostoru synapse. Pokud používáte aplikace, jako je Power BI nebo Azure Analysis Service, ujistěte se, že jsou ve stejné oblasti, do které jste umístili pracovní prostor synapse. V případě potřeby vytvořte samostatné pracovní prostory, které jsou spárovány s vašimi klientskými aplikacemi. Umístění klientské aplikace a pracovního prostoru synapse v jiné oblasti může způsobit větší latenci a zpomalit streamování výsledků.
- Pokud čtete data z místní aplikace, ujistěte se, že je pracovní prostor synapse v oblasti, která je blízko vašeho umístění.
- Ujistěte se, že nemáte k dispozici některé problémy se šířkou pásma sítě při čtení velkého množství dat.
- Nepoužívejte synapse Studio k vrácení velkého množství dat. Synapse Studio je webový nástroj, který používá protokol HTTPS k přenosu dat. Pomocí Azure Data Studio nebo SQL Server Management Studio si můžete přečíst velké množství dat.

## <a name="storage-and-content-layout"></a>Úložiště a rozložení obsahu

### <a name="colocate-your-storage-and-serverless-sql-pool"></a>Najděte úložiště a fond SQL bez serveru

Pokud chcete minimalizovat latenci, Najděte si účet Azure Storage nebo CosmosDB analytické úložiště a svůj koncový bod fondu SQL bez serveru. Účty úložiště a koncové body zřízené během vytváření pracovního prostoru se nacházejí ve stejné oblasti.

Pro zajištění optimálního výkonu, pokud přistupujete k jiným účtům úložiště s fondem SQL bez serveru, ujistěte se, že jsou ve stejné oblasti. Pokud nejsou ve stejné oblasti, dojde k vyšší latenci pro přenos dat v síti mezi vzdálenou oblastí a oblastí koncového bodu.

### <a name="azure-storage-throttling"></a>Omezení Azure Storage

K vašemu účtu úložiště může mít přístup více aplikací a služeb. K omezování úložiště dochází v případě, že kombinované IOPS nebo propustnost vygenerované aplikacemi, službami a úlohami fondu SQL bez serveru překračují omezení účtu úložiště. Výsledkem je, že budete mít výrazný negativní vliv na výkon dotazů.

Když se zjistí omezení, fond SQL bez serveru obsahuje vestavěnou manipulaci, která se má vyřešit. Fond SQL bez serveru bude podávat požadavky do úložiště pomalejším tempem, dokud se nevyřeší omezení.

> [!TIP]
> Pro optimální provádění dotazů nedělejte při provádění dotazů zdůraznit účet úložiště dalšími úlohami.

### <a name="azure-ad-pass-through-performance"></a>Předávací výkon Azure AD

Fond SQL bez serveru umožňuje přístup k souborům v úložišti pomocí předávacích a ověřovacích přihlašovacích údajů SAS Azure Active Directory (Azure AD). S předáváním Azure AD může docházet k nižšímu výkonu, než byste použili SAS.

Pokud potřebujete lepší výkon, zkuste použít přihlašovací údaje SAS pro přístup k úložišti.

### <a name="prepare-files-for-querying"></a>Příprava souborů pro dotazování

Pokud je to možné, můžete připravit soubory pro lepší výkon:

- Převod velkých CSV a JSON na Parquet Parquet je sloupcový formát. Vzhledem k tomu, že jsou komprimované, jsou velikosti souborů menší než CSV nebo soubory JSON, které obsahují stejná data. Fond SQL bez serveru umožňuje přeskočit sloupce a řádky, které nejsou potřeba v dotazu, pokud čtete soubory Parquet. Fond SQL bez serveru bude potřebovat méně času a méně požadavků na úložiště pro jeho čtení.
- Pokud se dotaz zaměřuje na jeden velký soubor, budete ho moci rozdělit do několika menších souborů.
- Snažte se zachovat velikost souboru CSV mezi 100 MB a 10 GB.
- Je lepší mít soubory stejné velikosti pro jednu cestu OPENROWSET nebo externí umístění tabulky.
- Vytvořte oddíly dat uložením oddílů do různých složek nebo názvů souborů. V tématu [použití funkcí filename a FilePath pro cílení na konkrétní oddíly](#use-filename-and-filepath-functions-to-target-specific-partitions).

### <a name="colocate-your-cosmosdb-analytical-storage-and-serverless-sql-pool"></a>Vyhledání CosmosDB analytického úložiště a neserverového fondu SQL

Ujistěte se, že je vaše CosmosDB analytické úložiště umístěno ve stejné oblasti jako pracovní prostor synapse. Dotazy na různé oblasti můžou způsobit obrovský latenci.

## <a name="csv-optimizations"></a>Optimalizace sdílených svazků clusteru

### <a name="use-parser_version-20-to-query-csv-files"></a>Použití PARSER_VERSION 2,0 k dotazování na soubory CSV

Při dotazování na soubory CSV můžete použít analyzátor optimalizovaný pro výkon. Podrobnosti najdete v tématu [PARSER_VERSION](develop-openrowset.md).

### <a name="manually-create-statistics-for-csv-files"></a>Ruční vytváření statistik pro soubory CSV

Fond SQL bez serveru spoléhá na statistiku pro generování optimálních plánů spouštění dotazů. V případě potřeby budou statistiky automaticky vytvořeny pro sloupce v souborech Parquet. V současné době se Statistika nevytvoří automaticky pro sloupce v souborech CSV a měli byste je vytvořit ručně pro sloupce, které používáte v dotazech, zejména ty, které se používají v DISTINCT, spojení, kde, ORDER BY a GROUP BY. Podrobnosti najdete [v statistikách fondu SQL bez serveru](develop-tables-statistics.md#statistics-in-serverless-sql-pool) .


## <a name="data-types"></a>Typy dat

### <a name="use-appropriate-data-types"></a>Použití vhodných datových typů

Datové typy, které používáte v dotazu, mají vliv na výkon. Pokud budete postupovat podle těchto pokynů, můžete získat lepší výkon: 

- Použijte nejmenší velikost dat, která bude vyhovovat největší možné hodnotě.
  - Pokud je maximální délka znakové hodnoty 30 znaků, použijte datový typ znak o délce 30.
  - Pokud jsou všechny hodnoty sloupce znaků pevné velikosti, použijte **char** nebo **nchar**. V opačném případě použijte **varchar** nebo **nvarchar**.
  - Pokud je maximální hodnota sloupce Integer 500, použijte typ **smallint** , protože se jedná o nejmenší datový typ, který může být přizpůsoben této hodnotě. Rozsahy datových typů typu Integer můžete najít v [tomto článku](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true).
- Pokud je to možné, použijte typ **varchar** a **char** místo typu **nvarchar** a **nchar**.
- Pokud je to možné, použijte datové typy založené na celých číslech. Operace SORT, JOIN a GROUP BY se dokončí rychleji pomocí celých čísel než u dat znaků.
- Pokud používáte odvození schématu, [Prohlédněte si odvozené datové typy](#check-inferred-data-types).

### <a name="check-inferred-data-types"></a>Kontrolovat odvozené datové typy

[Odvození schématu](query-parquet-files.md#automatic-schema-inference) vám pomůže rychle napsat dotazy a prozkoumat data bez znalosti schémat souborů. Náklady na tyto pohodlí jsou, že odvozené datové typy můžou být větší než skutečné datové typy. K tomu dojde v případě, že ve zdrojových souborech není dostatek informací, aby bylo zajištěno, že bude použit vhodný datový typ. Například soubory Parquet neobsahují metadata o maximální délce sloupce znaků. Takže fond SQL bez serveru ho odvodí jako varchar (8000).

Můžete použít [sp_describe_first_results_set](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) ke kontrole výsledných datových typů dotazu.

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

## <a name="filter-optimization"></a>Optimalizace filtru

### <a name="push-wildcards-to-lower-levels-in-the-path"></a>Doručovat zástupné znaky na nižší úrovně v cestě

V cestě můžete použít zástupné znaky k [dotazování více souborů a složek](query-data-storage.md#query-multiple-files-or-folders). Fond SQL bez serveru obsahuje seznam souborů v účtu úložiště počínaje prvním * použitím rozhraní API úložiště. Eliminuje soubory, které neodpovídají zadané cestě. Zmenšení počátečního seznamu souborů může zlepšit výkon, pokud existuje mnoho souborů, které odpovídají zadané cestě až k prvnímu zástupnému znaku.

### <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Použití funkcí filename a FilePath k cílení na konkrétní oddíly

Data jsou často organizována v oddílech. SQL fondu bez serveru můžete dát k dotazování na konkrétní složky a soubory. Tím se sníží počet souborů a množství dat, které dotaz potřebuje ke čtení a zpracování. Přidaný bonus znamená, že dosáhnete lepšího výkonu.

Další informace najdete v tématu o funkcích [filename](query-data-storage.md#filename-function) a [FilePath](query-data-storage.md#filepath-function) a viz příklady pro [dotazování na konkrétní soubory](query-specific-files.md).

> [!TIP]
> Vždy přetypování výsledků funkcí FilePath a filename na příslušné datové typy. Pokud používáte znakové datové typy, ujistěte se, že použijete příslušnou délku.

> [!NOTE]
> Funkce používané pro odstraňování oddílů, FilePath a filename se aktuálně nepodporují pro externí tabulky, kromě těch, které se automaticky vytvořily pro každou tabulku vytvořenou v Apache Spark pro Azure synapse Analytics.

Pokud uložená data nejsou rozdělená na oddíly, zvažte jejich dělení. Tímto způsobem můžete použít tyto funkce k optimalizaci dotazů, které cílí na tyto soubory. Když se [dotazování na oddíly Apache Spark pro tabulky Azure synapse](develop-storage-files-spark-tables.md) z fondu SQL bez serveru, dotaz automaticky cílí jenom na nezbytné soubory.

### <a name="use-proper-collation-to-utilize-predicate-pushdown-for-character-columns"></a>Použití správné kolace k využití predikátu směrem dolů pro sloupce znaků

Data v souboru Parquet jsou uspořádaná do skupin řádků. Fond SQL bez serveru přeskočí skupiny řádků na základě zadaného predikátu v klauzuli WHERE, a tím snižuje v/v, což vede ke zvýšení výkonu dotazů. 

Všimněte si, že u predikátů pro sloupce znaků v souborech Parquet je Latin1_General_100_BIN2_UTF8 podporováno pouze řazení v predikátech. Můžete zadat kolaci pro konkrétní sloupec pomocí klauzule WITH. Pokud tuto kolaci pomocí klauzule WITH nezadáte, použije se kolace databáze.

## <a name="optimize-repeating-queries"></a>Optimalizovat opakované dotazy

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Použití CETAS ke zvýšení výkonu a spojení dotazů

[CETAS](develop-tables-cetas.md) je jednou z nejdůležitějších funkcí dostupných v neserverovém fondu SQL. CETAS je paralelní operace, která vytváří externí metadata tabulky a exportuje výsledky dotazu SELECT do sady souborů v účtu úložiště.

CETAS můžete použít k vyhodnotit často používaných částí dotazů, jako jsou připojené referenční tabulky, k nové sadě souborů. Pak se můžete k této jedné externí tabulce připojit místo opakujících se běžných spojení ve více dotazech.

Když CETAS generuje soubory Parquet, Statistika se automaticky vytvoří, když se první dotaz zaměřuje na tuto externí tabulku, což vede k lepšímu výkonu pro následující dotazy cílené na tabulku vygenerované pomocí CETAS.

## <a name="next-steps"></a>Další kroky

Řešení běžných problémů najdete v článku [věnovaném řešení potíží](resources-self-help-sql-on-demand.md) . Pokud pracujete s vyhrazeným fondem SQL a nikoli bez serveru SQL, přečtěte si téma [osvědčené postupy vyhrazených fondů SQL](best-practices-dedicated-sql-pool.md) pro konkrétní doprovodné materiály.
