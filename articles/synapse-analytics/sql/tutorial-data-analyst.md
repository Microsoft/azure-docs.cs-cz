---
title: Kurz pro analytiky dat – použití SQL na vyžádání (Preview) k analýze otevřených datových sad Azure v Azure synapse Studio (Preview)
description: V tomto kurzu se naučíte, jak snadno provádět analýzu průzkumného data s kombinací různých otevřených datových sad Azure pomocí SQL na vyžádání (Preview) a vizualizovat výsledky v Azure synapse studiu.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 5084867d3db2da6718935f2af85e6148f2adbff8
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84258908"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>Použití SQL na vyžádání (Preview) k analýze otevřených datových sad Azure a vizualizaci výsledků v Azure synapse Studio (Preview)

V tomto kurzu se dozvíte, jak provádět analýzu průzkumnéch dat kombinací různých otevřených datových sad Azure pomocí SQL na vyžádání a pak vizualizace výsledků v Azure synapse studiu.

Konkrétně analyzujete [datovou sadu taxislužby New York City (NYC)](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) , která zahrnuje datum a časy vybírání a oddálení, umístění pro výběr a odkládací umístění, vzdálenosti cest, vypočítané tarify, typy sazeb, typy plateb a počty spolupracujících strojvedoucího.

Tato analýza se zaměřuje na hledání trendů ve změnách počtu taxislužby jezdí v průběhu času. Analyzujete dvě další služby Azure Open DataSets ([veřejné svátky](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) a [data o počasí](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)), abyste pochopili odlehlé hodnoty taxislužby jezdí.


## <a name="automatic-schema-inference"></a>Automatické odvození schématu

Vzhledem k tomu, že jsou data uložená ve formátu souboru Parquet, je k dispozici automatické odvození schématu, takže jeden může snadno dotazovat data bez nutnosti vypsat datové typy všech sloupců v souborech. Kromě toho může jeden použít mechanizmus virtuálního sloupce a funkci FilePath k odfiltrování určité podmnožiny souborů.

Nejprve se seznamte s daty NYC taxislužby spuštěním následujícího dotazu:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Následující příklad ukazuje fragment výsledku pro data NYC taxislužby:

![fragment výsledku](./media/tutorial-data-analyst/1.png)

Podobně je možné zadat dotaz na datovou sadu veřejných svátků pomocí následujícího dotazu:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Následující příklad ukazuje fragment výsledku pro datovou sadu pro veřejné svátky:

![fragment výsledku 2](./media/tutorial-data-analyst/2.png)

Nakonec můžete také zadat dotaz na datovou sadu počasí pomocí následujícího dotazu:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Následující příklad ukazuje fragment výsledku pro datovou sadu počasí:

![fragment výsledku 3](./media/tutorial-data-analyst/3.png)

Další informace o významu jednotlivých sloupců najdete v popisech datových sad [NYC taxislužby](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), [veřejných svátků](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)a [počasí](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) .

## <a name="time-series-seasonality-and-outlier-analysis"></a>Analýza časových řad, sezónnost a izolované

Pomocí následujícího dotazu můžete snadno shrnout roční počet taxislužby jezdí:

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

Následující příklad ukazuje fragment výsledku pro roční počet taxislužby jezdí:

![fragment výsledku 4](./media/tutorial-data-analyst/4.png)

Data je možné vizuálně zobrazit v synapse studiu přepnutím z tabulky na zobrazení grafu. Můžete si vybrat z různých typů grafů (plošný, pruhový, sloupcový, spojnicový, výsečový a bodový). V tomto případě vykreslíme sloupcový graf s sloupcem Category nastaveným na "current_year":

![vizualizace výsledku 5](./media/tutorial-data-analyst/5.png)

Z této vizualizace se může jasně zobrazit trend klesajícího počtu jezdíů za rok, který je pravděpodobně způsobený nedávným nárůstem počtu společností pro sdílení jedoucího.

> [!NOTE]
> V době psaní tohoto kurzu jsou data pro 2019 nekompletní, takže se za tento rok bude hodně jezdí velký počet.

Nyní se budeme soustředit na naši analýzu na jeden rok, například 2016. Následující dotaz vrátí denní počet jezdí během tohoto roku:

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

Následující příklad ukazuje fragment výsledků pro tento dotaz:

![fragment výsledku 6](./media/tutorial-data-analyst/6.png)

Data můžeme snadno vizualizovat tak, že vykreslíte sloupcový graf se sloupcem kategorie current_day a legenda (řady) sloupec "rides_per_day".

![vizualizace výsledku 7](./media/tutorial-data-analyst/7.png)

Z grafu může být zjištěno, že existuje týdenní vzor s špičkou sobotu. Během letního měsíce je z důvodu období dovolené méně taxislužby jezdí. Existují však také významná množství taxislužby jezdí bez jasného vzoru, kdy a proč k nim dojde.

Teď se podívejme na to, jestli se tyto kapky můžou vzájemně korelovat s veřejnými svátky, a to spojením NYC taxislužby jezdí s datovou sadou veřejných svátků:

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

![vizualizace výsledku 8](./media/tutorial-data-analyst/8.png)

Tentokrát chceme během veřejných svátků zvýraznit počet taxislužby jezdí. Pro tento účel vybereme "none" pro sloupec Category a "rides_per_day" a "svátek" jako sloupce legendy (řady).

![vizualizace výsledku 9](./media/tutorial-data-analyst/9.png)

Z diagramu může být jasně vidět, že během veřejných svátků je nižší počet taxislužby jezdí. Stále je však k dispozici jeden neobjasněný obrovský pokles od 23. ledna. Pojďme se podívat na NYC počasí v tomto dni tím, že se dotazuje na datovou sadu počasí:

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

![vizualizace výsledku 10](./media/tutorial-data-analyst/10.png)

Výsledky dotazu označují, že pokles počtu taxislužby jezdí byl v důsledku:

- Blizzard se za tento den v NYC, protože došlo k velkému sněhu (~ 30 cm).
- bylo studené (teplota nižší než 0 stupňů Celsia).
- a vítr (~ 10 milionů/s)

V tomto kurzu jsme si ukázali, jak může datový analytik rychle provádět analýzu dat, snadno kombinovat různé datové sady pomocí SQL na vyžádání a vizualizovat výsledky pomocí Azure synapse studia.

## <a name="next-steps"></a>Další kroky

Přečtěte si článek [připojení SQL na vyžádání a Power BI Desktop & vytvoření sestavy](tutorial-connect-power-bi-desktop.md) , kde se dozvíte, jak propojit SQL na vyžádání Power BI Desktop a vytvořit sestavy.
 
