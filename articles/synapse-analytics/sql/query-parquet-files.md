---
title: Dotazy na soubory Parquet s využitím fondu SQL bez serveru (Preview)
description: V tomto článku se naučíte, jak zadávat dotazy na soubory Parquet pomocí neserverového fondu SQL (Preview).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 97b34d85e4628c0ef01dd02d3a9be85da7f8291e
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685609"
---
# <a name="query-parquet-files-using-serverless-sql-pool-preview-in-azure-synapse-analytics"></a>Dotazování souborů Parquet pomocí SQL fondu bez serveru (Preview) ve službě Azure synapse Analytics

V tomto článku se dozvíte, jak napsat dotaz pomocí neserverového fondu SQL (Preview), který načte soubory Parquet.

## <a name="quickstart-example"></a>Příklad rychlého startu

`OPENROWSET` funkce umožňuje číst obsah souboru Parquet zadáním adresy URL souboru.

### <a name="read-parquet-file"></a>Přečíst soubor Parquet

Nejjednodušší způsob, jak zobrazit obsah `PARQUET` souboru, je poskytnout URL souboru pro `OPENROWSET` funkci a zadat Parquet `FORMAT` . Pokud je soubor veřejně dostupný nebo pokud vaše identita Azure AD může získat přístup k tomuto souboru, měli byste být schopni zobrazit obsah souboru pomocí dotazu, jako je ten, který je znázorněn v následujícím příkladu:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet',
    format = 'parquet') as rows
```

Ujistěte se, že máte přístup k tomuto souboru. Pokud je soubor chráněný pomocí klíče SAS nebo vlastní identity Azure, budete muset nastavit [přihlašovací údaje na úrovni serveru pro přihlášení SQL](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential).

> [!IMPORTANT]
> Ujistěte se, že používáte určitou databázovou kolaci UTF-8 (například `Latin1_General_100_CI_AS_SC_UTF8` ), protože řetězcové hodnoty v souborech PARQUET jsou kódovány pomocí kódování UTF-8.
> Neshoda mezi kódováním textu v souboru PARQUET a kolaci může způsobit neočekávané chyby při konverzi.
> Výchozí kolaci aktuální databáze můžete snadno změnit pomocí následujícího příkazu T-SQL: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`

### <a name="data-source-usage"></a>Využití zdroje dat

Předchozí příklad používá úplnou cestu k souboru. Jako alternativu můžete vytvořit externí zdroj dat s umístěním, které odkazuje na kořenovou složku úložiště, a použít tento zdroj dat a relativní cestu k souboru ve `OPENROWSET` funkci:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) as rows
```

Pokud je zdroj dat chráněný pomocí klíče SAS nebo vlastní identity, můžete nakonfigurovat [zdroj dat s použitím přihlašovacích údajů s rozsahem databáze](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

### <a name="explicitly-specify-schema"></a>Explicitně zadat schéma

`OPENROWSET` umožňuje explicitně určit sloupce, které chcete číst z klauzule File using `WITH` :

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) with ( date_rep date, cases int, geo_id varchar(6) ) as rows
```

> [!IMPORTANT]
> Ujistěte se, že jste explicilty určení některých řazení v kódování UTF-8 (například `Latin1_General_100_CI_AS_SC_UTF8` ) pro všechny řetězcové sloupce v `WITH` klauzuli, nebo nastavte určitou kolaci UTF-8 na úrovni databáze.
> Neshoda mezi kódováním textu v souboru a kolaci sloupce String může způsobit neočekávané chyby při konverzi.
> Výchozí kolaci aktuální databáze můžete snadno změnit pomocí následujícího příkazu T-SQL: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`
> Kolaci pro typy slo můžete snadno nastavit pomocí následující definice: `geo_id varchar(6) collate Latin1_General_100_CI_AI_SC_UTF8`

V následujících částech se můžete podívat, jak se dotazovat na různé typy souborů PARQUET.

## <a name="prerequisites"></a>Požadavky

Prvním krokem je **Vytvoření databáze** se zdrojem dat, který odkazuje na účet úložiště [NYC Yellow taxislužby](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) . Pak inicializujte objekty spuštěním [instalačního skriptu](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) v této databázi. Tento instalační skript vytvoří zdroje dat, přihlašovací údaje v oboru databáze a formáty externích souborů, které jsou použity v těchto ukázkách.

## <a name="dataset"></a>Datová sada

V této ukázce se používá [NYC Yellow taxislužby](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) DataSet. Soubory Parquet můžete dotazovat stejným způsobem jako [soubory CSV](query-parquet-files.md). Jediným rozdílem je, že `FILEFORMAT` parametr by měl být nastaven na hodnotu `PARQUET` . Příklady v tomto článku znázorňují konkrétní soubory pro čtení Parquet.

## <a name="query-set-of-parquet-files"></a>Dotaz sady souborů Parquet

Při dotazování souborů Parquet můžete zadat pouze sloupce s oznámením.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>Automatické odvození schématu

Při čtení souborů Parquet není nutné používat klauzuli OPENROWSET WITH. Názvy sloupců a datové typy se automaticky čtou ze souborů Parquet.

Následující ukázka ukazuje možnosti automatického odvození schématu pro soubory Parquet. Vrátí počet řádků v září 2017 bez zadání schématu.

> [!NOTE]
> Při čtení souborů Parquet není nutné zadávat sloupce v klauzuli OPENROWSET WITH. V takovém případě služba dotazů na SQL fond bez serveru použije metadata v souboru Parquet a sváže sloupce podle názvu.

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>Dotazování na dělená data

Datová sada uvedená v této ukázce je rozdělená (rozdělená do oddílů) na samostatné podsložky. Pomocí funkce FilePath můžete cílit na konkrétní oddíly. V tomto příkladu se zobrazuje množství tarifů podle roku, měsíce a payment_type v prvních třech měsících od 2017.

> [!NOTE]
> Dotaz na fond SQL bez serveru je kompatibilní se schématem dělení na oddíly/Hadoop.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>Mapování typů

Pro mapování typu Parquet na typ mapování nativního typu SQL [pro Parquet](develop-openrowset.md#type-mapping-for-parquet).

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku a Naučte se, jak [zadávat dotazy na vnořené typy Parquet](query-parquet-nested-types.md).
