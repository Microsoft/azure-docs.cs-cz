---
title: 'Kurz: načtení dat New York taxislužby města'
description: Kurz používá Azure Portal a SQL Server Management Studio k načtení dat New York taxislužby města z objektu blob Azure pro synapse SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: ec577e2a70e2b354b8d2013fe259aa9ea622c50e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120134"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Kurz: načtení datové sady New York taxislužby města

V tomto kurzu se pomocí [příkazu copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) načte datová sada New York taxislužby města z účtu služby Azure Blob Storage. Tento kurz používá [Azure Portal](https://portal.azure.com) a aplikaci [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) k:

> [!div class="checklist"]
>
> * Vytvoření vyhrazeného uživatele pro načítání dat
> * Vytvoření tabulek pro ukázkovou datovou sadu 
> * Načtení dat do datového skladu pomocí příkazu Kopírovat T-SQL
> * Zobrazení průběhu nahrávání dat

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="before-you-begin"></a>Než začnete

Než začnete s tímto kurzem, stáhněte a nainstalujte nejnovější verzi aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).  

V tomto kurzu se předpokládá, že jste už vytvořili vyhrazený fond SQL z následujícího [kurzu](./create-data-warehouse-portal.md#connect-to-the-server-as-server-admin).

## <a name="create-a-user-for-loading-data"></a>Vytvoření uživatele pro načítání dat

Účet správce serveru slouží k provádění operací správy a není vhodný pro spouštění dotazů na uživatelská data. Načítání dat je operace s vysokými nároky na paměť. Maximální velikost paměti je definována v závislosti na nakonfigurovaných [jednotkách datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md) a [třídě prostředků](resource-classes-for-workload-management.md) .

Doporučujeme vytvořit účet a uživatele vyhrazeného pro načítání dat. Pak přidejte uživatele načítání do [třídy prostředků](resource-classes-for-workload-management.md), která umožňuje odpovídající maximální přidělení paměti.

Připojte se jako správce serveru, abyste mohli vytvářet přihlašovací jména a uživatele. Pomocí následujících kroků vytvořte účet a uživatele **LoaderRC20**. Pak uživatele přiřaďte k třídě prostředků **staticrc20**.

1. V SSMS klikněte pravým tlačítkem myši na možnost **Hlavní** a zobrazte rozevírací nabídku a zvolte možnost **Nový dotaz**. Otevře se nové okno dotazu.

    ![Nový dotaz v uzlu master](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. V okně dotazu zadejte následující příkazy T-SQL, které vytvoří účet a uživatele LoaderRC20, a heslo a123STRONGpassword nahraďte vlastním heslem.

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Vyberte **Execute** (Provést).

4. Klikněte pravým tlačítkem na **mySampleDataWarehouse** a zvolte **Nový dotaz**. Otevře se nové okno dotazu.  

    ![Nový dotaz na ukázkový datový sklad](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. Zadejte následující příkazy T-SQL, které pro účet LoaderRC20 vytvoří uživatele databáze LoaderRC20. Na druhém řádku se novému uživateli přidělí oprávnění CONTROL k novému datovému skladu.  Tato oprávnění jsou podobná, jako kdybyste z uživatele udělali vlastníka databáze. Na třetím řádku se nový uživatel přidá jako člen [třídy prostředků](resource-classes-for-workload-management.md) staticrc20.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Vyberte **Execute** (Provést).

## <a name="connect-to-the-server-as-the-loading-user"></a>Připojení k serveru jako uživatel načítání

Prvním krokem k načítání dat je přihlášení jako LoaderRC20.  

1. V Průzkumník objektů vyberte rozevírací nabídku **připojit** a vyberte možnost **databázový stroj**. Zobrazí se dialogové okno **Připojení k serveru**.

    ![Přihlášení pomocí nového účtu](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Zadejte plně kvalifikovaný název serveru a jako Účet zadejte **LoaderRC20**.  Zadejte své heslo k účtu LoaderRC20.

3. Vyberte **Connect** (Připojit).

4. Až bude vaše připojení připravené, v Průzkumníku objektů se zobrazí dvě připojení k serveru. Jedno připojení jako ServerAdmin a druhé jako MedRCLogin.

    ![Úspěšné připojení](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-tables-for-the-sample-data"></a>Vytvořit tabulky pro ukázková data

Teď jste připraveni zahájit proces načítání dat do svého nového datového skladu. V této části kurzu se dozvíte, jak pomocí příkazu COPY načíst datovou sadu CAB New York City taxislužby z objektu blob Azure Storage. Informace o tom, jak získat data do služby Azure Blob Storage nebo jak je načíst přímo ze zdroje, najdete v tématu [Přehled načítání](design-elt-data-loading.md).

Spusťte následující skripty SQL a zadejte informace o datech, která chcete načíst. Tyto informace zahrnují umístění dat, formát obsahu dat a definici tabulky pro data.

1. V předchozí části jste se do svého datového skladu přihlásili jako LoaderRC20. V SSMS klikněte pravým tlačítkem na připojení LoaderRC20 a vyberte **Nový dotaz**.  Zobrazí se nové okno dotazu.

    ![Nové okno dotazu načítání](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Porovnejte své okno dotazu s předchozím obrázkem.  Ověřte, že je vaše okno dotazu spuštěné pod účtem LoaderRC20 a provádí dotazy na vaši databázi MySampleDataWarehouse. Toto okno dotazu použijte k provedení všech kroků načítání.

7. Pro vytvoření tabulek spusťte následující příkazy T-SQL:

    ```sql
    CREATE TABLE [dbo].[Date]
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
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
    
    CREATE TABLE [dbo].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    ```
    

## <a name="load-the-data-into-your-data-warehouse"></a>Načtení dat do datového skladu

V této části se používá [příkaz Copy pro načtení](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) ukázkových dat z Azure Storage BLOB.  

> [!NOTE]
> V tomto kurzu se data načítají přímo do konečné tabulky. Obvykle byste se načetli do pracovní tabulky pro produkční úlohy. Zatímco jsou data v pracovní tabulce, můžete provést všechny potřebné transformace. 

1. Pro načtení dat spusťte následující příkazy:

    ```sql
    COPY INTO [dbo].[Date]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Date'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Date] - Taxi dataset');
    
    
    COPY INTO [dbo].[Geography]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Geography'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Geography] - Taxi dataset');
    
    COPY INTO [dbo].[HackneyLicense]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/HackneyLicense'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset');
    
    COPY INTO [dbo].[Medallion]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Medallion'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Medallion] - Taxi dataset');
    
    COPY INTO [dbo].[Time]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Time'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Time] - Taxi dataset');
    
    COPY INTO [dbo].[Weather]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Weather'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Weather] - Taxi dataset');
    
    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013'
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

2. Zobrazte data během načítání. Načítáte několik GB dat a komprimujete je do vysoce výkonných clusterovaných indexů columnstore. Spuštěním následujícího dotazu, který používá zobrazení dynamické správy, zobrazíte stav načítání.

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
    WHERE [label] = 'COPY : Load [dbo].[Date] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Geography] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Medallion] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Time] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Weather] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Trip] - Taxi dataset' 
    and session_id <> session_id() and type = 'WRITER'
    GROUP BY r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command;
    ```
    
3. Zobrazte všechny systémové dotazy.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Užívejte si pohled na to, jak se data krásně načítají do vašeho datového skladu.

    ![Zobrazení načtených tabulek](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Účtují se vám výpočetní prostředky a data, která načtete do svého datového skladu. Ta se účtují zvlášť.

* Pokud chcete zachovat data v úložišti, můžete pozastavit výpočetní prostředky v době, kdy datový sklad nepoužíváte. Když pozastavíte výpočetní prostředky, bude se vám účtovat pouze úložiště dat, a kdykoli budete připraveni s daty pracovat, můžete výpočetní prostředky zase obnovit.
* Pokud chcete zamezit budoucím poplatkům, můžete datový sklad odstranit.

Pomocí tohoto postupu podle potřeby vyčistěte prostředky.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a vyberte svůj datový sklad.

    ![Vyčištění prostředků](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Pokud chcete pozastavit výpočetní prostředky, vyberte tlačítko **pozastavit** . Když je datový sklad pozastavený, zobrazí se tlačítko **Spustit**.  Pokud chcete pokračovat v výpočetních prostředích, vyberte **Spustit**.

3. Pokud chcete odebrat datový sklad, aby se vám neúčtovaly výpočetní výkon nebo úložiště, vyberte **Odstranit**.

4. Pokud chcete odebrat server, který jste vytvořili, vyberte na předchozím obrázku **MyNewServer-20180430.Database.Windows.NET** a pak vyberte **Odstranit**.  Buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

5. Pokud chcete odebrat skupinu prostředků, vyberte **myResourceGroup** a pak vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili vytvořit datový sklad a uživatele pro načítání dat. Použili jste jednoduchý [příkaz Copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#examples) k načtení dat do datového skladu.

Provedli jste tyto akce:
> [!div class="checklist"]
>
> * Vytvoření datového skladu na webu Azure Portal
> * Vytvořit pravidlo brány firewall na úrovni serveru na webu Azure Portal
> * Připojení k datovému skladu pomocí SSMS
> * Vytvoření vyhrazeného uživatele pro načítání dat
> * Vytváření tabulek pro ukázková data
> * Použití příkazu Kopírovat T-SQL k načtení dat do datového skladu
> * Zobrazení průběhu nahrávání dat

Přejděte na přehled vývoje, kde se dozvíte, jak migrovat existující databázi do Azure synapse Analytics:

> [!div class="nextstepaction"]
> [Rozhodnutí o návrhu migrace stávající databáze do Azure synapse Analytics](sql-data-warehouse-overview-develop.md)

Další příklady a odkazy pro načítání naleznete v následující dokumentaci:

- [Dokumentace k příkazu COPY reference](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [ZKOPÍROVAT příklady pro každou metodu ověřování](./quickstart-bulk-load-copy-tsql-examples.md)
- [Rychlé spuštění kopírování pro jednu tabulku](./quickstart-bulk-load-copy-tsql.md)