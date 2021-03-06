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
ms.openlocfilehash: 0def1f957842417c3936e3f1c7bb5bc023109818
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536327"
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
    IF NOT EXISTS (SELECT * FROM sys.objects O JOIN sys.schemas S ON O.schema_id = S.schema_id WHERE O.NAME = 'NYCTaxiTripSmall' AND O.TYPE = 'U' AND S.NAME = 'dbo')
    CREATE TABLE dbo.NYCTaxiTripSmall
        (
         [DateID] int,
         [MedallionID] int,
         [HackneyLicenseID] int,
         [PickupTimeID] int,
         [DropoffTimeID] int,
         [PickupGeographyID] int,
         [DropoffGeographyID] int,
         [PickupLatitude] float,
         [PickupLongitude] float,
         [PickupLatLong] nvarchar(4000),
         [DropoffLatitude] float,
         [DropoffLongitude] float,
         [DropoffLatLong] nvarchar(4000),
         [PassengerCount] int,
         [TripDurationSeconds] int,
         [TripDistanceMiles] float,
         [PaymentType] nvarchar(4000),
         [FareAmount] numeric(19,4),
         [SurchargeAmount] numeric(19,4),
         [TaxAmount] numeric(19,4),
         [TipAmount] numeric(19,4),
         [TollsAmount] numeric(19,4),
         [TotalAmount] numeric(19,4)
        )
    WITH
        (
        DISTRIBUTION = ROUND_ROBIN,
         CLUSTERED COLUMNSTORE INDEX
         -- HEAP
        )
    GO

    COPY INTO dbo.NYCTaxiTripSmall
    (DateID 1, MedallionID 2, HackneyLicenseID 3, PickupTimeID 4, DropoffTimeID 5,
    PickupGeographyID 6, DropoffGeographyID 7, PickupLatitude 8, PickupLongitude 9, 
    PickupLatLong 10, DropoffLatitude 11, DropoffLongitude 12, DropoffLatLong 13, 
    PassengerCount 14, TripDurationSeconds 15, TripDistanceMiles 16, PaymentType 17, 
    FareAmount 18, SurchargeAmount 19, TaxAmount 20, TipAmount 21, TollsAmount 22, 
    TotalAmount 23)
    FROM 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet'
    WITH
    (
        FILE_TYPE = 'PARQUET'
        ,MAXERRORS = 0
        ,IDENTITY_INSERT = 'OFF'
    )
    ```
1. Kliknutím na tlačítko Spustit spusťte skript.
1. Tento skript se dokončí za méně než 60 sekund. Načte 2 000 000 řádků taxislužby dat NYC do tabulky nazvané **dbo. Cesta**.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Prozkoumat data taxislužby NYC ve vyhrazeném fondu SQL

1. V synapse studiu přejdete do centra **dat** .
1. Přejít na   >  **tabulky** SQLPOOL1. 
3. Klikněte pravým tlačítkem na **dbo. NYCTaxiTripSmall** tabulku a vyberte **Nový skript SQL**  >  **Vybrat horní 100 řádků**.
4. Počkejte, než se vytvoří a spustí nový skript SQL.
5. Všimněte si, že v horní části SQL Script **Connect** se automaticky nastaví fond SQL s názvem **SQLPOOL1**.
6. Nahraďte text skriptu SQL tímto kódem a spusťte ho.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.NYCTaxiTripSmall
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    Tento dotaz ukazuje, jak celková vzdálenost cest a Průměrná doba odezvy se vztahují k počtu cestujících.
1. V okně výsledek skriptu SQL změňte **zobrazení** na **graf** , aby se zobrazila vizualizace výsledků jako spojnicový graf.
    
## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza dat v účtu Azure Storage](get-started-analyze-storage.md)
