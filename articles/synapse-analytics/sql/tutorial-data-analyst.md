---
title: Kurz pro datového analytika – použití SQL na vyžádání (preview) k analýze otevřených datových sad Azure v Azure Synapse Studio (preview)
description: V tomto kurzu se dozvíte, jak snadno provádět průzkumnou analýzu dat kombinující různé datové sady Azure Open pomocí SQL na vyžádání (preview) a vizualizovat výsledky v Azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1080001cb222f91503080914d7fb253e5ee82626
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423226"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>Použití SQL na vyžádání (preview) k analýze otevřených datových sad Azure a vizualizaci výsledků v Azure Synapse Studio (preview)

V tomto kurzu se dozvíte, jak provádět analýzu průzkumných dat kombinací různých datových sad Azure Open pomocí SQL na vyžádání a pak vizualizaci výsledků v Azure Synapse Studio.

Zejména analyzujete datovou sadu taxi služby [New York City (NYC),](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) která zahrnuje data/časy vyzvednutí a předání, místa vyzvednutí a předání, vzdálenosti jízdy, rozepsanýtarify, typy sazeb, typy plateb a počty cestujících nahlášené řidičem.

Cílem analýzy je najít trendy ve změnách počtu jízd taxíkem v průběhu času. Analyzujete další dvě datové sady Azure Open Datasets[(Data o svátcích](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) a [počasí),](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)abyste porozuměli odlehlým hodnotám v počtu jízd taxíkem.

## <a name="create-credentials"></a>Vytvoření přihlašovacích údajů

```sql
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/nyctlc]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO
```

## <a name="automatic-schema-inference"></a>Odvození automatického schématu

Vzhledem k tomu, že data jsou uložena ve formátu parketového souboru, je k dispozici automatické odvození schématu, takže lze snadno zadat dotaz na data bez nutnosti vypsat datové typy všech sloupců v souborech. Kromě toho lze využít virtuální sloupcový mechanismus a funkci filepath k odfiltrování určité podmnožiny souborů.

Nejprve se seznámíme s daty NYC Taxi spuštěním následujícího dotazu:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Následující text ukazuje fragment výsledku pro data NYC Taxi:

![úryvek výsledků](./media/tutorial-data-analyst/1.png)

Podobně můžeme dotazovat datové sady státních svátků pomocí následujícího dotazu:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Následující text ukazuje fragment výsledku pro datovou sadu státních svátků:

![výsledek úryvek 2](./media/tutorial-data-analyst/2.png)

Nakonec můžeme také dotaz na datové sady počasí pomocí následujícího dotazu:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Následující text ukazuje fragment výsledku pro datovou sadu počasí:

![výsledek úryvek 3](./media/tutorial-data-analyst/3.png)

Další informace o významu jednotlivých sloupců naleznete v popisech datových sad [NYC Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), [Státní svátky](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)a [Data o počasí.](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)

## <a name="time-series-seasonality-and-outlier-analysis"></a>Časové řady, sezónnost a odlehlá analýza

Můžete snadno shrnout roční počet jízd taxíkem pomocí následujícího dotazu:

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

Následující text ukazuje fragment výsledku pro roční počet jízd taxíkem:

![výsledek úryvek 4](./media/tutorial-data-analyst/4.png)

Data lze vizualizovat v Synapse Studio přepnutím z tabulky do zobrazení grafu. Můžete si vybrat z různých typů grafů (Oblast, Pruh, Sloupec, Spojnicový, Výsečový a Bodový). V tomto případě vykreslete sloupcový graf se sloupcem Kategorie nastaveným na "current_year":

![vizualizace výsledků 5](./media/tutorial-data-analyst/5.png)

Z této vizualizace je jasně vidět trend klesajícího počtu jízd v průběhu let, pravděpodobně kvůli nedávné zvýšené popularitě společností pro sdílení jízd.

> [!NOTE]
> V době psaní tohoto kurzu jsou data pro rok 2019 neúplná, takže pro tento rok dochází k obrovskému poklesu počtu jízd.

Dále se zaměříme na naši analýzu na jeden rok, například 2016. Následující dotaz vrátí denní počet jízd během uvedeného roku:

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

Následující text ukazuje fragment výsledku pro tento dotaz:

![výsledek úryvek 6](./media/tutorial-data-analyst/6.png)

Opět můžeme snadno vizualizovat data vykreslením sloupcového grafu se sloupcem Kategorie "current_day" a Legend (série) "rides_per_day".

![vizualizace výsledků 7](./media/tutorial-data-analyst/7.png)

Z pozemku lze pozorovat, že existuje týdenní vzor se sobotním vrcholem. V letních měsících je kvůli prázdninovému období méně jízd taxíkem. Nicméně, tam jsou také některé významné poklesy v počtu jízd taxíkem bez jasného vzoru, kdy a proč k nim dochází.

Dále se podívejme, jestli tyto kapky jsou potenciálně koreluje s svátky spojením NYC taxi jízdy s datovou sadou státních svátků:

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

![vizualizace výsledků 8](./media/tutorial-data-analyst/8.png)

Tentokrát chceme upozornit na počet jízd taxíkem během státních svátků. Za tímto účelem zvolíme sloupce kategorie a sloupce "rides_per_day" a "holiday" jako Legend (série).

![vizualizace výsledků 9](./media/tutorial-data-analyst/9.png)

Z pozemku je jasně vidět, že během státních svátků je řada jízd taxíkem nižší. 23. ledna však stále dochází k jednomu nevysvětlitelnému obrovskému poklesu. Podívejme se na počasí v New Yorku v ten den dotazem na datové sady počasí:

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

![vizualizace výsledků 10](./media/tutorial-data-analyst/10.png)

Výsledky dotazu naznačují, že pokles počtu jízd taxíkem byl způsoben:

- blizzard na ten den v New Yorku, jak tam byl těžký sníh (~ 30 cm)
- byla zima (teplota pod nulou stupňů Celsia)
- a větrno (~ 10m / s)

Tento kurz ukázal, jak analytik dat můžete rychle provádět analýzu průzkumných dat, snadno kombinovat různé datové sady pomocí SQL na vyžádání a vizualizovat výsledky pomocí Azure Synapse Studio.

## <a name="next-steps"></a>Další kroky

Prohlédněte si [připojení SQL na vyžádání k Power BI Desktopu & vytvořte](tutorial-connect-power-bi-desktop.md) článek sestavy, kde se dozvíte, jak připojit SQL na vyžádání k Power BI Desktopu a vytvářet sestavy.
 
