---
title: 'Rychlý Start: hromadné načtení dat pomocí jednoho příkazu T-SQL'
description: Hromadné načtení dat pomocí příkazu COPY
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/08/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d39b3085a802ca0ff745ab1f63f4a8fba966ea48
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81115008"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Rychlý Start: hromadné načtení dat pomocí příkazu COPY

V tomto rychlém startu budete hromadně načítat data do fondu SQL pomocí jednoduchého a flexibilního [příkazu kopírování](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) pro příjem dat s vysokou propustností. Příkaz COPY je doporučeným nástrojem pro načítání, protože umožňuje hladce a pružně pružně načíst data poskytnutím funkčnosti:

- Povolení načtení nižších privilegovaných uživatelů bez nutnosti přísného řízení pro datový sklad
- Využijte jenom jeden příkaz T-SQL, aniž byste museli vytvářet žádné další databázové objekty.
- Využijte model s jemnějším oprávněním bez odhalení klíčů účtu úložiště pomocí sdílených přístupových podpisů (SAS).
- Zadejte jiný účet úložiště pro umístění ERRORFILE (REJECTED_ROW_LOCATION).
- Přizpůsobení výchozích hodnot pro každý cílový sloupec a zadání zdrojových datových polí pro načtení do konkrétních cílových sloupců
- Zadání vlastního ukončovacího znaku řádku pro soubory CSV
- Řídicí řetězec, pole a oddělovače řádků pro soubory CSV
- Využití SQL Serverch formátů data pro soubory CSV
- Zadejte v cestě umístění úložiště zástupné znaky a více souborů.

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu se předpokládá, že už máte fond SQL. Pokud se fond SQL nevytvořil, použijte rychlý Start pro [Vytvoření a připojení – portál](create-data-warehouse-portal.md) .

## <a name="create-the-target-table"></a>Vytvoření cílové tabulky

V tomto příkladu budeme načítat data z datové sady taxislužby New York. Načteme tabulku s názvem Trip, která představuje taxislužby TRIPS pořízeného v jednom roce. Vytvořte tabulku spuštěním následujícího:

```sql
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
```

## <a name="run-the-copy-statement"></a>Spuštění příkazu COPY

Spusťte následující příkaz COPY, který načte data z účtu služby Azure Blob Storage do tabulky Trip.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>Monitorování zatížení

Pravidelným spuštěním následujícího dotazu ověřte, zda probíhá zátěž:

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>Další kroky

- Osvědčené postupy při načítání dat najdete v tématu [osvědčené postupy pro načítání dat](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data).
- Informace o tom, jak spravovat prostředky pro načtení dat, najdete v tématu věnovaném [izolaci úloh](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql). 
