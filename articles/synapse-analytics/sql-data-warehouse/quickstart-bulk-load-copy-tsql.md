---
title: 'Úvodní příručka: Hromadné načítání dat pomocí jediného příkazu T-SQL'
description: Hromadné načítání dat pomocí příkazu COPY
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
ms.openlocfilehash: d03c7d72a0adf02959badac758f94e47fd81de5c
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992225"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Úvodní příručka: Hromadné načítání dat pomocí příkazu COPY

V tomto rychlém startu budete hromadně načítat data do fondu SQL pomocí jednoduchého a [flexibilního příkazu COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) pro ingestování dat s vysokou propustností. Příkaz COPY je doporučený nástroj pro načítání, protože umožňuje bezproblémově a flexibilně načítat data tím, že poskytuje funkce pro:

- Povolit načtení uživatelů s nižšími oprávněními bez nutnosti přísných oprávnění CONTROL v datovém skladu
- Využijte pouze jeden příkaz T-SQL bez nutnosti vytvářet další databázové objekty
- Využití jemnějšího modelu oprávnění bez vystavení klíčů účtu úložiště pomocí podpisů share access (SAS)
- Zadejte jiný účet úložiště pro umístění ERRORFILE (REJECTED_ROW_LOCATION)
- Přizpůsobení výchozích hodnot pro každý cílový sloupec a určení zdrojových datových polí, která se mají načíst do určitých cílových sloupců
- Určení vlastního zakončení řádku pro soubory CSV
- Oddělovače únikových řetězců, polí a řádků pro soubory CSV
- Využití formátů data serveru SQL Server pro soubory CSV
- Určení zástupných znaků a více souborů v cestě umístění úložiště

## <a name="prerequisites"></a>Požadavky

Tento rychlý start předpokládá, že již máte fond SQL. Pokud fond SQL nebyl vytvořen, použijte rychlý start [Vytvořit a připojit - portál.](create-data-warehouse-portal.md)

## <a name="create-the-target-table"></a>Vytvoření cílové tabulky

V tomto příkladu budeme načítat data z datové sady taxi v New Yorku. naložíme stůl s názvem Trip, který představuje taxi výlety během jednoho roku. Chcete-li tabulku vytvořit, spusťte následující:

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

Spusťte následující příkaz COPY, který načte data z účtu úložiště objektů blob Azure do tabulky Výlet.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>Sledování zatížení

Zkontrolujte, zda vaše zatížení postupuje pravidelným spuštěním následujícího dotazu:

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

- Doporučené postupy při načítání dat najdete [v tématu Doporučené postupy pro načítání dat](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data).
- Informace o tom, jak spravovat prostředky pro zatížení dat, naleznete v [tématu Izolace pracovního vytížení](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql). 
