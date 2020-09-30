---
title: 'Kurz: použití SQL na vyžádání (Preview) k analýze otevřených datových sad Azure v Azure synapse Studio (Preview)'
description: V tomto kurzu se dozvíte, jak snadno provádět analýzu průzkumného data s kombinací různých otevřených datových sad Azure pomocí SQL na vyžádání (Preview) a vizualizovat výsledky v Azure synapse studiu.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 0471bd594dcf1b0654d6b4c496e5d39ade2739fd
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91539516"
---
# <a name="tutorial-use-sql-on-demand-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio"></a>Kurz: použití SQL na vyžádání k analýze otevřených datových sad Azure a vizualizaci výsledků v Azure synapse studiu

V tomto kurzu se dozvíte, jak provádět analýzu průzkumnéch dat kombinací různých otevřených datových sad Azure pomocí SQL na vyžádání a pak vizualizace výsledků v Azure synapse studiu.

Konkrétně analyzujete [datovou sadu taxislužby New York City (NYC)](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) , která zahrnuje:

- Data a časy vyzvednutí a vyřazení.
- Výběr a odkládací umístění. 
- Vzdálenosti cest.
- Tarify s položkou
- Typy přenosů.
- Typy plateb. 
- Počty cestujících hlášených ovladačem

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

Následující fragment kódu ukazuje výsledek pro data taxislužby NYC:

![Fragment výsledku dat NYC taxislužby](./media/tutorial-data-analyst/1.png)

Podobně můžete zadat dotaz na datovou sadu pro veřejné svátky pomocí následujícího dotazu:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Následující fragment kódu ukazuje výsledek pro datovou sadu veřejných svátků:

![Fragment výsledku datové sady veřejných svátků](./media/tutorial-data-analyst/2.png)

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

Následující fragment kódu ukazuje výsledek pro datovou sadu dat počasí:

![Fragment výsledku datové sady dat počasí](./media/tutorial-data-analyst/3.png)

Další informace o významu jednotlivých sloupců najdete v popisech datových sad [NYC taxislužby](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), [veřejných svátků](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)a [počasí](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) .

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

![Roční počet fragmentů výsledku taxislužby jezdí](./media/tutorial-data-analyst/4.png)

Data je možné vizuálně zobrazit v synapse studiu přepnutím z **tabulky** do zobrazení **grafu** . Můžete si vybrat mezi různými typy grafů, jako je **oblast**, **pruhový**, **sloupcový**, **spojnicový**, **výsečový**a **bodový**. V takovém případě znázorněte **sloupcový** graf se sloupcem **kategorie** nastaveným na **current_year**:

![Sloupcový graf zobrazující jezdí za rok](./media/tutorial-data-analyst/5.png)

Z této vizualizace se dá jasně zobrazit trend klesajícího počtu jezdíů za rok. Důvodem je to, že tento pokles je v důsledku nedávného nárůstu počtu společností pro spolusdílení.

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

![Denní počet jezdí pro fragment výsledků 2016](./media/tutorial-data-analyst/6.png)

Data pak můžete snadno vizualizovat tak, že vykreslíte **sloupcový** graf se sloupcem **kategorie** nastaveným na **current_day** a sloupec **Legenda (řady)** nastavíte na hodnotu **rides_per_day**.

![Sloupcový graf zobrazující denní počet jezdí pro 2016](./media/tutorial-data-analyst/7.png)

V grafu se zobrazí týdenní vzor s sobotu jako den špičky. Během letního měsíce je z důvodu dovolené méně taxislužby jezdí. Všimněte si také, že některé významné kapky v počtu taxislužby jezdí bez jasného vzoru, kdy a proč k nim dojde.

Teď se podívejme, jestli se akce v souvislosti s veřejnými svátky připojí k datové sadě NYC taxislužby jezdí s datovou sadou veřejných svátků:

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

![Vizualizace výsledků datových sad NYC taxislužby jezdí a veřejné svátky](./media/tutorial-data-analyst/8.png)

Tentokrát chceme během veřejných svátků zvýraznit počet taxislužby jezdí. Pro tento účel zvolíme **none** pro sloupec **Category** a **rides_per_day** a **svátky** jako sloupce **legendy (řady)** .

![Počet taxislužby jezdí během veřejných svátků v grafu](./media/tutorial-data-analyst/9.png)

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

![Vizualizace výsledku datové sady dat počasí](./media/tutorial-data-analyst/10.png)

Výsledky dotazu označují, že odkládací hodnota taxislužby jezdía vznikla z těchto důvodů:

- V NYC se za tento den Blizzard s velkým sněhem (~ 30 cm).
- Byla studena (teplota byla nižší než 0 stupňů Celsia).
- Došlo k větru (~ 10 m/s).

V tomto kurzu jsme si ukázali, jak může analytik dat rychle provádět analýzu dat průzkumné, snadno kombinovat různé datové sady pomocí SQL na vyžádání a vizualizovat výsledky pomocí Azure synapse studia.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak připojit SQL na vyžádání pro Power BI Desktop a vytváření sestav, najdete v tématu [připojení SQL na vyžádání pro Power BI Desktop a vytváření sestav](tutorial-connect-power-bi-desktop.md).
 
