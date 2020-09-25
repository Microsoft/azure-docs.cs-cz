---
title: 'Kurz: Začínáme analyzovat data pomocí vyhrazených fondů SQL'
description: V tomto kurzu použijete ukázková data NYC taxislužby k prozkoumání možností analytického fondu SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 3df0959003923a9a4ff2347330fd5a4f22e52245
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91300118"
---
# <a name="analyze-data-with-sql-dedicated-pools"></a>Analýza dat pomocí vyhrazených fondů SQL

Azure synapse Analytics poskytuje možnost analyzovat data pomocí vyhrazeného fondu SQL. V tomto kurzu použijete data NYC taxislužby k prozkoumávání schopností dedicted fondu SQL.

## <a name="load-the-nyc-taxi-data-into-sqldb1"></a>Načtení dat taxislužby NYC do SQLDB1

1. V synapse studiu přejděte do centra pro **vývoj** a pak vytvořte nový skript SQL.
1. Zadejte následující kód:
    ```
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );

    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/QID6392_20171107_05910_0.txt.gz'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```
1. Spuštění tohoto skriptu bude trvat přibližně 1 minutu. Načte 2 000 000 řádků taxislužby dat NYC do tabulky nazvané **dbo. Cesta**

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Prozkoumat data taxislužby NYC ve vyhrazeném fondu SQL

1. V synapse studiu přejdete do centra **dat** .
1. Přejít na **SQLDB1**  >  **tabulky**SQLDB1. Zobrazí se několik načtených tabulek.
1. Klikněte pravým tlačítkem na **dbo. Tabulka cest** a výběr **nového skriptu SQL**  >  **Vyberte horní 100 řádků**.
1. Počkejte, než se vytvoří a spustí nový skript SQL.
1. Všimněte si, že v horní části SQL Script **Connect** se automaticky nastaví fond SQL s názvem **SQLDB1**.
1. Nahraďte text skriptu SQL tímto kódem a spusťte ho.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    Tento dotaz ukazuje, jak celková vzdálenost cest a Průměrná doba odezvy se vztahují k počtu cestujících.
1. V okně výsledek skriptu SQL změňte **zobrazení** na **graf** , aby se zobrazila vizualizace výsledků jako spojnicový graf.



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza pomocí Sparku](get-started-analyze-spark.md)

