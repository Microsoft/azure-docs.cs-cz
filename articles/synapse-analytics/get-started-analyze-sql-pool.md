---
title: 'Kurz: Začínáme analyzovat data pomocí vyhrazených fondů SQL'
description: V tomto kurzu použijete ukázková data NYC taxislužby k prozkoumání možností analytického fondu SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: a1f15330a912c8a8a93fe1f74e88ef8d117441c2
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047894"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Analýza dat pomocí vyhrazených fondů SQL

V tomto kurzu použijete data NYC taxislužby k prozkoumání možností vyhrazeného fondu SQL.

## <a name="create-a-dedicated-sql-pool"></a>Vytvoření vyhrazeného fondu SQL

1. V synapse studiu v levém podokně vyberte **Spravovat**  >  **fondy SQL**.
1. Vybrat **Nový**
1. Pro **název fondu SQL** vyberte **SQLPOOL1**
1. Pro **úroveň výkonu** vyberte **DW100C**
1. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**. Vyhrazený fond SQL bude připravený během několika minut. 

Váš vyhrazený fond SQL je přidružený k SQL Database, která se také označuje jako **SQLPOOL1**.
1. Přejděte do   >  **pracovního prostoru** data.
1. Měla by se zobrazit databáze s názvem **SQLPOOL1**. Pokud ji nevidíte, klikněte na **aktualizovat**.

Vyhrazený fond SQL spotřebovává Fakturovatelné prostředky, pokud je aktivní. Fond můžete později pozastavit a snížit tak náklady.

> [!NOTE] 
> Při vytváření nového vyhrazeného fondu SQL (dříve SQL DW) ve vašem pracovním prostoru se otevře vyhrazená stránka pro zřizování fondu SQL. Zřizování bude provedeno na logickém SQL serveru.
## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>Načtení dat taxislužby NYC do SQLPOOL1

1. V synapse studiu přejděte do centra pro **vývoj** , kliknutím na **+** tlačítko přidejte nový prostředek a pak vytvořte nový skript SQL.
1. V rozevíracím seznamu připojit k výše uvedeném skriptu vyberte fond SQLPOOL1 (vytvořený v [kroku 1](./get-started-create-workspace.md) tohoto kurzu).
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
1. Kliknutím na tlačítko Spustit spusťte skript.
1. Tento skript se dokončí za méně než 60 sekund. Načte 2 000 000 řádků taxislužby dat NYC do tabulky nazvané **dbo. Cesta**.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Prozkoumat data taxislužby NYC ve vyhrazeném fondu SQL

1. V synapse studiu přejdete do centra **dat** .
1. Přejít na   >  **tabulky** SQLPOOL1. 
3. Klikněte pravým tlačítkem na **dbo. Tabulka cest** a výběr **nového skriptu SQL**  >  **Vyberte horní 100 řádků**.
4. Počkejte, než se vytvoří a spustí nový skript SQL.
5. Všimněte si, že v horní části SQL Script **Connect** se automaticky nastaví fond SQL s názvem **SQLPOOL1**.
6. Nahraďte text skriptu SQL tímto kódem a spusťte ho.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    Tento dotaz ukazuje, jak celková vzdálenost cest a Průměrná doba odezvy se vztahují k počtu cestujících.
1. V okně výsledek skriptu SQL změňte **zobrazení** na **graf** , aby se zobrazila vizualizace výsledků jako spojnicový graf.
    
## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza dat v účtu Azure Storage](get-started-analyze-storage.md)
