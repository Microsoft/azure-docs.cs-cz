---
title: 'Kurz: načtení dat New York taxislužby města'
description: Kurz používá Azure Portal a SQL Server Management Studio k načtení dat New York taxislužby města z objektu blob Azure pro synapse SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/31/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: d2c2673e6863725e064f3ad8561ab77eb1b051eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371520"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Kurz: načtení datové sady New York taxislužby města

V tomto kurzu se pomocí [příkazu copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) načte datová sada New York taxislužby města z účtu služby Azure Blob Storage. Tento kurz používá [Azure Portal](https://portal.azure.com) a aplikaci [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) k:

> [!div class="checklist"]
>
> * Vytvořte ve Azure Portal fond SQL.
> * Vytvořit pravidlo brány firewall na úrovni serveru na webu Azure Portal
> * Připojení k datovému skladu pomocí SSMS
> * Vytvoření vyhrazeného uživatele pro načítání dat
> * Vytvoření tabulek pro ukázkovou datovou sadu 
> * Načtení dat do datového skladu pomocí příkazu Kopírovat T-SQL
> * Zobrazení průběhu nahrávání dat

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="before-you-begin"></a>Než začnete

Než začnete s tímto kurzem, stáhněte a nainstalujte nejnovější verzi aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [Azure Portal](https://portal.azure.com/).

## <a name="create-a-blank-database"></a>Vytvoření prázdné databáze

Vytvoří se fond SQL s definovanou sadou [výpočetních prostředků](memory-concurrency-limits.md). Databáze se vytvoří v rámci [skupiny prostředků Azure](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a na [logickém SQL serveru](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Pomocí těchto kroků vytvořte prázdnou databázi.

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.

2. Na stránce **Nový** vyberte **databáze** a v části **Doporučené** na **nové** stránce vyberte **Azure synapse Analytics** .

    ![vytvoření datového skladu](./media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Do formuláře zadejte následující informace:

   | Nastavení            | Navrhovaná hodnota       | Popis                                                  |
   | ------------------ | --------------------- | ------------------------------------------------------------ |
   | *Jméno**            | mySampleDataWarehouse | Platné názvy databází najdete v tématu [Identifikátory databází](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). |
   | **Předplatné**   | Vaše předplatné     | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
   | **Skupina prostředků** | myResourceGroup       | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |
   | **Zvolit zdroj**  | Prázdná databáze        | Určuje, že se má vytvořit prázdná databáze. Poznámka: datový sklad je jedním z typů databáze. |

    ![vytvoření datového skladu](./media/load-data-from-azure-blob-storage-using-polybase/create-data-warehouse.png)

4. Vyberte **Server** a vytvořte a nakonfigurujte nový server pro novou databázi. Do **formuláře Nový server** zadejte následující informace:

    | Nastavení                | Navrhovaná hodnota          | Popis                                                  |
    | ---------------------- | ------------------------ | ------------------------------------------------------------ |
    | **Název serveru**        | Libovolný globálně jedinečný název | Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |
    | **Přihlašovací jméno správce serveru** | Libovolné platné jméno           | Platná přihlašovací jména najdete v tématu [Identifikátory databází](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). |
    | **Heslo**           | Libovolné platné heslo       | Heslo musí mít alespoň osm znaků a musí obsahovat znaky ze tří z následujících kategorií: velká písmena, malá písmena, číslice a jiné než alfanumerické znaky. |
    | **Umístění**           | Libovolné platné umístění       | Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/). |

    ![vytvořit server](./media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Vyberte **Vybrat**.

6. Vyberte **úroveň výkonu** , abyste určili, jestli je datový sklad Gen1 nebo Gen2, a kolik jednotek datového skladu.

7. Pro tento kurz vyberte **Gen2**fond SQL. Ve výchozím nastavení je posuvník nastaven na **DW1000c** .  Zkuste jeho posouváním hodnotu zvýšit a snížit a podívejte se, jak funguje.

    ![konfigurace výkonu](./media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Vyberte **Použít**.
9. V okně zřizování vyberte **kolaci** pro prázdnou databázi. Pro účely tohoto kurzu použijte výchozí hodnotu. Další informace o kolacích najdete v tématu [Kolace](/sql/t-sql/statements/collations?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

10. Teď, když jste dokončili formulář, vyberte **vytvořit** a zřiďte databázi. Zřizování trvá několik minut.

11. Na panelu nástrojů vyberte **Oznámení**, abyste mohli sledovat proces nasazení.
  
     ![Snímek obrazovky ukazuje Azure Portal podokna oznámení otevřeném s probíhajícím nasazením.](./media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Vytvoření pravidla brány firewall na úrovni serveru

Brána firewall na úrovni serveru, která zabraňuje externím aplikacím a nástrojům v připojení k serveru nebo jakékoli databázi na serveru. Pokud chcete umožnit připojení, můžete přidat pravidla brány firewall, která povolí připojení z konkrétních IP adres.  Postupujte podle těchto pokynů a vytvořte [pravidlo brány firewall na úrovni serveru](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) pro IP adresu vašeho klienta.

> [!NOTE]
> Azure synapse Analytics komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud ano, nemůžete se připojit k serveru, dokud vaše IT oddělení neotevře port 1433.

1. Po dokončení nasazení v nabídce na levé straně vyberte **databáze SQL** a na stránce **databáze SQL** vyberte **mySampleDatabase** . Otevře se stránka s přehledem pro vaši databázi, na které se zobrazí plně kvalifikovaný název serveru (například **MyNewServer-20180430.Database.Windows.NET**), který poskytuje možnosti pro další konfiguraci.

2. Zkopírujte tento plně kvalifikovaný název serveru, abyste ho mohli použít pro připojení k serveru a jeho databázím v následujících rychlých startech. Pak vyberte název serveru a otevřete nastavení serveru.

    ![vyhledání názvu serveru](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)

3. Vyberte název serveru a otevřete nastavení serveru.

    ![nastavení serveru](./media/load-data-from-azure-blob-storage-using-polybase/server-settings.png)

4. Vyberte **Zobrazit nastavení brány firewall**. Otevře se stránka **nastavení brány firewall** pro server.

    ![pravidlo brány firewall serveru](./media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png)

5. Na panelu nástrojů vyberte **Přidat IP adresu klienta** a přidejte tak aktuální IP adresu do nového pravidla brány firewall. Pravidlo brány firewall může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

6. Vyberte **Uložit**. Vytvoří se pravidlo brány firewall na úrovni serveru pro vaši aktuální IP adresu pro otevření portu 1433 na serveru.

7. Vyberte **OK** a pak zavřete stránku **nastavení brány firewall** .

Nyní se můžete připojit k serveru a jeho datovým skladům pomocí této IP adresy. Připojení funguje z aplikace SQL Server Management Studio nebo jiného nástroje podle vašeho výběru. Při připojování použijte účet správce serveru, který jste předtím vytvořili.  

> [!IMPORTANT]
> Standardně je přístup přes bránu firewall služby SQL Database povolený pro všechny služby Azure. Na této stránce vyberte **vypnuto** a pak výběrem **Uložit** zakažte bránu firewall pro všechny služby Azure.

## <a name="get-the-fully-qualified-server-name"></a>Získání plně kvalifikovaného názvu serveru

Získejte plně kvalifikovaný název serveru pro váš server v Azure Portal. Tento plně kvalifikovaný název použijete později při připojování k serveru.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
2. V nabídce na levé straně vyberte **Azure synapse Analytics** a na stránce **Azure synapse Analytics** vyberte svou databázi.
3. V podokně **Základy** na stránce webu Azure Portal pro vaši databázi vyhledejte a potom zkopírujte **Název serveru**. V tomto příkladu je plně kvalifikovaný název mynewserver-20180430.database.windows.net.

    ![informace o připojení](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Připojení k serveru jako správce serveru

V této části se k navázání připojení k serveru používá [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).

1. Otevřete sadu SQL Server Management Studio.

2. V dialogovém okně **Připojení k serveru** zadejte následující informace:

    | Nastavení        | Navrhovaná hodnota                            | Popis                                                  |
    | -------------- | ------------------------------------------ | ------------------------------------------------------------ |
    | Typ serveru    | Databázový stroj                            | Tato hodnota se vyžaduje.                                       |
    | Název serveru    | Plně kvalifikovaný název serveru            | Název by měl být podobný tomuto: **MyNewServer-20180430.Database.Windows.NET**. |
    | Ověřování | Ověřování SQL Serveru                  | Ověřování SQL je jediný typ ověřování, který jsme v tomto kurzu nakonfigurovali. |
    | Přihlásit          | Účet správce serveru                   | Jedná se o účet, který jste zadali při vytváření serveru. |
    | Heslo       | Heslo pro účet správce serveru | Jedná se o heslo, které jste zadali při vytváření serveru. |

    ![Připojení k serveru](./media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

3. Vyberte **Připojit**. V aplikaci SSMS se otevře okno Průzkumníka objektů.

4. V Průzkumníku objektů rozbalte **Databáze**. Pak rozbalte **Systémové databáze** a uzel **master** a zobrazte objekty v hlavní databázi.  Rozbalte **mySampleDatabase** a zobrazte objekty v nové databázi.

    ![databázové objekty](./media/load-data-from-azure-blob-storage-using-polybase/connected.png)

## <a name="create-a-user-for-loading-data"></a>Vytvoření uživatele pro načítání dat

Účet správce serveru slouží k provádění operací správy a není vhodný pro spouštění dotazů na uživatelská data. Načítání dat je operace s vysokými nároky na paměť. Maximální velikost paměti je definována v závislosti na nakonfigurovaných [jednotkách datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md) a [třídě prostředků](resource-classes-for-workload-management.md) .

Doporučujeme vytvořit účet a uživatele vyhrazeného pro načítání dat. Pak přidejte uživatele načítání do [třídy prostředků](resource-classes-for-workload-management.md), která umožňuje odpovídající maximální přidělení paměti.

Vzhledem k tomu, že jste aktuálně připojeni jako správce serveru, můžete vytvářet účty a uživatele. Pomocí následujících kroků vytvořte účet a uživatele **LoaderRC20**. Pak uživatele přiřaďte k třídě prostředků **staticrc20**.

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

3. Vyberte **Připojit**.

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

V této části se používá [příkaz Copy pro načtení](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) ukázkových dat z Azure Storage BLOB.  

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

5. Pokud chcete odebrat skupinu prostředků, vyberte **myResourceGroup**a pak vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili vytvořit datový sklad a uživatele pro načítání dat. Použili jste jednoduchý [příkaz Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#examples) k načtení dat do datového skladu.

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

- [Dokumentace k příkazu COPY reference](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [ZKOPÍROVAT příklady pro každou metodu ověřování](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)
- [Rychlé spuštění kopírování pro jednu tabulku](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql)
