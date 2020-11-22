---
title: 'Kurz: zkoumání a analýza datových laků pomocí serveru SQL bez serveru synapse'
description: V tomto kurzu se dozvíte, jak snadno provést analýzu průzkumného data s kombinací různých otevřených datových sad Azure s využitím fondu SQL bez serveru (Preview) a vizualizace výsledků v synapse studiu pro Azure synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 11/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: af6fc75b5de22fc77313932ca17ce695e889dad3
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2020
ms.locfileid: "95237952"
---
# <a name="tutorial-explore-and-analyze-data-lakes-with-serverless-sql-pool-preview"></a>Kurz: zkoumání a analýza datových laků pomocí neserverového fondu SQL (Preview)

V tomto kurzu se dozvíte, jak provádět analýzu dat průzkumného zpracování. Budete kombinovat různé otevřené datové sady Azure s využitím fondu SQL bez serveru. Výsledky pak vizualizujete v synapse studiu pro Azure synapse Analytics.

Funkce OPENROWSET (BULK...) umožňuje přístup k souborům v Azure Storage. Funkce [OpenRowset](develop-openrowset.md) přečte obsah vzdáleného zdroje dat (například soubor) a vrátí obsah jako sadu řádků.

## <a name="automatic-schema-inference"></a>Automatické odvození schématu

Vzhledem k tomu, že jsou data uložena ve formátu souboru Parquet, je k dispozici automatické odvození schématu. Data můžete snadno dotazovat bez výpisu datových typů všech sloupců v souborech. Můžete také použít mechanismus virtuálního sloupce a funkci FilePath k odfiltrování určité podmnožiny souborů.

Nejdřív se podíváme na data taxislužby NYC spuštěním následujícího dotazu:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

[New York City (NYC) taxislužby datová sada](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) obsahuje:

- Data a časy vyzvednutí a vyřazení.
- Výběr a odkládací umístění. 
- Vzdálenosti cest.
- Tarify s položkou
- Typy přenosů.
- Typy plateb. 
- Počty cestujících hlášených ovladačem

Podobně můžete zadat dotaz na datovou sadu pro veřejné svátky pomocí následujícího dotazu:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

A Konečně můžete také zadat dotaz na datovou sadu dat počasí pomocí následujícího dotazu:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Další informace o významu jednotlivých sloupců najdete v popisech datových sad: 
- [NYC taxislužby](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)
- [Veřejné svátky](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
- [Data o počasí](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)

## <a name="time-series-seasonality-and-outlier-analysis"></a>Analýza časových řad, sezónnost a izolované

Můžete snadno shrnout roční počet taxislužby jezdí pomocí následujícího dotazu:

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

Následující fragment kódu ukazuje výsledek pro roční počet taxislužby jezdí:

![Roční počet fragmentů výsledku taxislužby jezdí](./media/tutorial-data-analyst/yearly-taxi-rides.png)

Data je možné vizuálně zobrazit v synapse studiu přepnutím z **tabulky** do zobrazení **grafu** . Můžete si vybrat mezi různými typy grafů, jako je **oblast**, **pruhový**, **sloupcový**, **spojnicový**, **výsečový** a **bodový**. V takovém případě znázorněte **sloupcový** graf se sloupcem **kategorie** nastaveným na **current_year**:

![Sloupcový graf zobrazující jezdí za rok](./media/tutorial-data-analyst/column-chart-rides-year.png)

Z této vizualizace uvidíte trend klesajících čísel v průběhu let. Důvodem je to, že tento pokles je v důsledku nedávného nárůstu počtu společností pro spolusdílení.

> [!NOTE]
> V době psaní tohoto kurzu nejsou data pro 2019 kompletní. Výsledkem je, že počet jezdí pro daný rok je velký.

Teď se podíváme na analýzu v jednom roce, například 2016. Následující dotaz vrátí denní počet jezdí během tohoto roku:

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

Následující fragment kódu ukazuje výsledek pro tento dotaz:

![Denní počet jezdí pro fragment výsledků 2016](./media/tutorial-data-analyst/daily-rides.png)

Data pak můžete snadno vizualizovat tak, že vykreslíte **sloupcový** graf se sloupcem **kategorie** nastaveným na **current_day** a sloupec **Legenda (řady)** nastavíte na hodnotu **rides_per_day**.

![Sloupcový graf zobrazující denní počet jezdí pro 2016](./media/tutorial-data-analyst/column-chart-daily-rides.png)

V grafu se zobrazí týdenní vzor s sobotu jako den špičky. Během letního měsíce je z důvodu dovolené méně taxislužby jezdí. Všimněte si také, že některé významné kapky v počtu taxislužby jezdí bez jasného vzoru, kdy a proč k nim dojde.

Teď se podívejme, jestli se jezdí ve vztahu k veřejným svátkům. V případě, že existuje korelace, můžete se spojit s datovou sadou NYC taxislužby jezdí s datovou sadou veřejných svátků:

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![Vizualizace výsledků datových sad NYC taxislužby jezdí a veřejné svátky](./media/tutorial-data-analyst/rides-public-holidays.png)

Tentokrát chceme během veřejných svátků zvýraznit počet taxislužby jezdí. Pro tento účel zvolíme **none** pro sloupec **Category** a **rides_per_day** a **svátky** jako sloupce **legendy (řady)** .

![Počet taxislužby jezdí během veřejných svátků v grafu](./media/tutorial-data-analyst/plot-chart-public-holidays.png)

Z diagramu vidíte, že během veřejných svátků je počet taxislužby jezdí nižší. Stále se jedná o jeden neobjasněný velký pokles od 23. ledna. Pojďme se podívat na NYC počasí v tomto dni dotazování datové sady dat o počasí:

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![Vizualizace výsledku datové sady dat počasí](./media/tutorial-data-analyst/weather-data-set-visualization.png)

Výsledky dotazu označují, že odkládací hodnota taxislužby jezdía vznikla z těchto důvodů:

- V NYC se za tento den Blizzard s velkým sněhem (~ 30 cm).
- Byla studena (teplota byla nižší než 0 stupňů Celsia).
- Došlo k větru (~ 10 m/s).

V tomto kurzu jsme si ukázali, jak může analytik dat rychle provádět analýzu dat, snadno kombinovat různé datové sady s využitím fondu SQL bez serveru a vizualizovat výsledky pomocí Azure synapse studia.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak připojit fond SQL bez serveru pro Power BI Desktop a vytváření sestav, najdete v tématu [připojení fondu SQL bez serveru pro Power BI Desktop a vytváření sestav](tutorial-connect-power-bi-desktop.md).

Další informace o tom, jak používat externí tabulky v neserverovém fondu SQL, najdete v tématu [použití externích tabulek s synapse SQL](develop-tables-external-tables.md?tabs=sql-pool) .
 
