---
title: 'Kurz: Načtení dat taxíku v New Yorku'
description: Kurz používá portál Azure a SQL Server Management Studio k načtení dat taxislužby New York z globálního objektu blob Azure pro SQL Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: f1614538f6ab735720d090f66fee0e017e96cf72
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346725"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Kurz: Načtení datové sady Taxicab v New Yorku

Tento kurz používá PolyBase k načtení dat taxislužby v New Yorku z globálního účtu úložiště objektů blob Azure. Tento kurz používá [Azure Portal](https://portal.azure.com) a aplikaci [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) k: 

> [!div class="checklist"]
> * Vytvoření fondu SQL na webu Azure Portal
> * Vytvořit pravidlo brány firewall na úrovni serveru na webu Azure Portal
> * Připojení k datovému skladu pomocí SSMS
> * Vytvoření vyhrazeného uživatele pro načítání dat
> * Vytvoření externích tabulek pro data v úložišti objektů blob v Azure
> * Načtení dat do datového skladu pomocí příkazu T-SQL CTAS
> * Zobrazení průběhu nahrávání dat
> * Vytvoření statistik pro nově načtená data

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="before-you-begin"></a>Než začnete

Než začnete s tímto kurzem, stáhněte a nainstalujte nejnovější verzi aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).


## <a name="log-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="create-a-blank-database"></a>Vytvoření prázdné databáze

Fond SQL se vytvoří s definovanou sadou [výpočetních prostředků](memory-concurrency-limits.md). Databáze se vytvoří v rámci [skupiny prostředků Azure](../../azure-resource-manager/management/overview.md) a na [logickém serveru SQL Azure](../../sql-database/sql-database-features.md). 

Chcete-li vytvořit prázdnou databázi, postupujte takto. 

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.

2. Na stránce **Nový** vyberte **Databáze** a v části Doporučené na nové stránce vyberte **Azure Synapse Analytics** v části **Doporučené.** **New**

    ![vytvoření datového skladu](./media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Do formuláře zadejte následující informace: 

   | Nastavení            | Navrhovaná hodnota       | Popis                                                  |
   | ------------------ | --------------------- | ------------------------------------------------------------ |
   | *Jméno**            | mySampleDataWarehouse | Platné názvy databází najdete v tématu [Identifikátory databází](/sql/relational-databases/databases/database-identifiers). |
   | **Předplatné**   | Vaše předplatné     | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
   | **Skupina prostředků** | myResourceGroup       | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). |
   | **Vybrat zdroj**  | Prázdná databáze        | Určuje, že se má vytvořit prázdná databáze. Poznámka: datový sklad je jedním z typů databáze. |

    ![vytvoření datového skladu](./media/load-data-from-azure-blob-storage-using-polybase/create-data-warehouse.png)

4. Vyberte **Server** a vytvořte a nakonfigurujte nový server pro novou databázi. Do **formuláře Nový server** zadejte následující informace: 

    | Nastavení                | Navrhovaná hodnota          | Popis                                                  |
    | ---------------------- | ------------------------ | ------------------------------------------------------------ |
    | **Název serveru**        | Libovolný globálně jedinečný název | Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). |
    | **Přihlášení správce serveru** | Libovolné platné jméno           | Platná přihlašovací jména najdete v tématu [Identifikátory databází](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
    | **Heslo**           | Libovolné platné heslo       | Heslo musí mít alespoň osm znaků a musí obsahovat znaky ze tří z následujících kategorií: velká písmena, malá písmena, číslice a jiné než alfanumerické znaky. |
    | **Umístění**           | Libovolné platné umístění       | Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/). |

    ![vytvoření databázového serveru](./media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Vyberte **Vybrat**.

6. Vyberte **úroveň výkonu,** chcete-li určit, zda je datový sklad Gen1 nebo Gen2 a počet jednotek datového skladu. 

7. Pro účely tohoto kurzu vyberte fond SQL **Gen2**. Jezdec je ve výchozím nastavení nastaven na **DW1000c.**  Zkuste jeho posouváním hodnotu zvýšit a snížit a podívejte se, jak funguje. 

    ![konfigurace výkonu](./media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Vyberte **Použít**.
9. V okně zřizování vyberte **řazení** pro prázdnou databázi. Pro účely tohoto kurzu použijte výchozí hodnotu. Další informace o kolacích najdete v tématu [Kolace](/sql/t-sql/statements/collations).

11. Teď, když jste vyplnili formulář, vyberte **Vytvořit** pro zřízení databáze. Zřizování trvá několik minut. 

12. Na panelu nástrojů vyberte **Oznámení,** chcete-li sledovat proces nasazení.
  
     ![oznámení](./media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Vytvoření pravidla brány firewall na úrovni serveru

Brána firewall na úrovni serveru, která brání externím aplikacím a nástrojům v připojení k serveru nebo k databázím na serveru. Pokud chcete umožnit připojení, můžete přidat pravidla brány firewall, která povolí připojení z konkrétních IP adres.  Postupujte podle těchto pokynů a vytvořte [pravidlo brány firewall na úrovni serveru](../../sql-database/sql-database-firewall-configure.md) pro IP adresu vašeho klienta. 

> [!NOTE]
> SQL Data Warehouse komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud je to tak, nebudete se moct připojit k serveru služby Azure SQL Database, dokud vaše IT oddělení neotevře port 1433.

1. Po dokončení nasazení vyberte **databáze SQL** z nabídky na levé straně a potom vyberte **mySampleDatabase** na stránce **databáze SQL.** Otevře se stránka s přehledem databáze, která zobrazuje plně kvalifikovaný název serveru (například **mynewserver-20180430.database.windows.net)** a poskytuje možnosti pro další konfiguraci. 

2. Zkopírujte tento plně kvalifikovaný název serveru, abyste ho mohli použít pro připojení k serveru a jeho databázím v následujících rychlých startech. Pak vyberte na názvu serveru otevřít nastavení serveru.

    ![vyhledání názvu serveru](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png) 

3. Vyberte název serveru, který chcete otevřít nastavení serveru.

    ![nastavení serveru](./media/load-data-from-azure-blob-storage-using-polybase/server-settings.png) 

5. Vyberte **možnost Zobrazit nastavení brány firewall**. Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database. 

    ![pravidlo brány firewall serveru](./media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png) 

4. **Chcete-li** přidat aktuální adresu IP do nového pravidla brány firewall, vyberte na panelu nástrojů přidat adresu IP klienta. Pravidlo brány firewall může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

5. Vyberte **Uložit**. Vytvoří se pravidlo brány firewall na úrovni serveru pro vaši aktuální IP adresu, které otevře port 1433 na logickém serveru.

6. Vyberte **OK** a zavřete stránku **Nastavení brány firewall.**

Pomocí této IP adresy se teď můžete připojit k serveru SQL a jeho datovým skladům. Připojení funguje z aplikace SQL Server Management Studio nebo jiného nástroje podle vašeho výběru. Při připojování použijte účet správce serveru, který jste předtím vytvořili.  

> [!IMPORTANT]
> Standardně je přístup přes bránu firewall služby SQL Database povolený pro všechny služby Azure. Na této stránce vyberte **OFF** a pak vyberte **Uložit,** chcete-li zakázat bránu firewall pro všechny služby Azure.

## <a name="get-the-fully-qualified-server-name"></a>Získání plně kvalifikovaného názvu serveru

Na webu Azure Portal získejte plně kvalifikovaný název vašeho serveru SQL. Tento plně kvalifikovaný název použijete později při připojování k serveru.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. V yberte **Azure Synapse Analytics** z nabídky na levé straně a vyberte databázi na stránce **Azure Synapse Analytics.** 
3. V podokně **Základy** na stránce webu Azure Portal pro vaši databázi vyhledejte a potom zkopírujte **Název serveru**. V tomto příkladu je mynewserver-20180430.database.windows.net plně kvalifikovaný název. 

    ![informace o připojení](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Připojení k serveru jako správce serveru

V této části se pomocí aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) naváže připojení k serveru SQL Azure.

1. Otevřete SQL Server Management Studio.

2. V dialogovém okně **Připojení k serveru** zadejte následující informace:

    | Nastavení        | Navrhovaná hodnota                            | Popis                                                  |
    | -------------- | ------------------------------------------ | ------------------------------------------------------------ |
    | Typ serveru    | Databázový stroj                            | Tato hodnota se vyžaduje.                                       |
    | Název serveru    | Plně kvalifikovaný název serveru            | Název by měl být něco takového: **mynewserver-20180430.database.windows.net**. |
    | Ověřování | Ověřování SQL Serveru                  | Ověřování SQL je jediný typ ověřování, který jsme v tomto kurzu nakonfigurovali. |
    | Přihlásit          | Účet správce serveru                   | Jedná se o účet, který jste zadali při vytváření serveru. |
    | Heslo       | Heslo pro účet správce serveru | Jedná se o heslo, které jste zadali při vytváření serveru. |

    ![Připojení k serveru](./media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

4. Vyberte **Connect** (Připojit). V aplikaci SSMS se otevře okno Průzkumníka objektů. 

5. V Průzkumníku objektů rozbalte **Databáze**. Pak rozbalte **Systémové databáze** a uzel **master** a zobrazte objekty v hlavní databázi.  Rozbalte **mySampleDatabase** a zobrazte objekty v nové databázi.

    ![databázové objekty](./media/load-data-from-azure-blob-storage-using-polybase/connected.png) 

## <a name="create-a-user-for-loading-data"></a>Vytvoření uživatele pro načítání dat

Účet správce serveru slouží k provádění operací správy a není vhodný pro spouštění dotazů na uživatelská data. Načítání dat je operace s vysokými nároky na paměť. Maximální kapacita paměti je definována podle [jednotek datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md) a nakonfigurované [třídy prostředků](resource-classes-for-workload-management.md) . 

Doporučujeme vytvořit účet a uživatele vyhrazeného pro načítání dat. Pak přidejte uživatele načítání do [třídy prostředků](resource-classes-for-workload-management.md), která umožňuje odpovídající maximální přidělení paměti.

Vzhledem k tomu, že jste aktuálně připojeni jako správce serveru, můžete vytvářet účty a uživatele. Pomocí následujících kroků vytvořte účet a uživatele **LoaderRC20**. Pak uživatele přiřaďte k třídě prostředků **staticrc20**. 

1.  V SSMS vyberte **hlavní položku** vpravo, chcete-li zobrazit rozevírací nabídku, a zvolte **Nový dotaz**. Otevře se nové okno dotazu.

    ![Nový dotaz v uzlu master](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. V okně dotazu zadejte následující příkazy T-SQL, které vytvoří účet a uživatele LoaderRC20, a heslo a123STRONGpassword nahraďte vlastním heslem. 

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Vyberte **Provést**.

4. Klikněte pravým tlačítkem na **mySampleDataWarehouse** a zvolte **Nový dotaz**. Otevře se nové okno dotazu.  

    ![Nový dotaz na ukázkový datový sklad](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. Zadejte následující příkazy T-SQL, které pro účet LoaderRC20 vytvoří uživatele databáze LoaderRC20. Na druhém řádku se novému uživateli přidělí oprávnění CONTROL k novému datovému skladu.  Tato oprávnění jsou podobná, jako kdybyste z uživatele udělali vlastníka databáze. Na třetím řádku se nový uživatel přidá jako člen [třídy prostředků](resource-classes-for-workload-management.md) staticrc20.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Vyberte **Provést**.

## <a name="connect-to-the-server-as-the-loading-user"></a>Připojení k serveru jako uživatel načítání

Prvním krokem k načítání dat je přihlášení jako LoaderRC20.  

1. V Průzkumníku objektů vyberte rozevírací nabídku **Připojit** a vyberte **Database Engine**. Zobrazí se dialogové okno **Připojení k serveru**.

    ![Přihlášení pomocí nového účtu](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Zadejte plně kvalifikovaný název serveru a jako Účet zadejte **LoaderRC20**.  Zadejte své heslo k účtu LoaderRC20.

3. Vyberte **Connect** (Připojit).

4. Až bude vaše připojení připravené, v Průzkumníku objektů se zobrazí dvě připojení k serveru. Jedno připojení jako ServerAdmin a druhé jako MedRCLogin.

    ![Úspěšné připojení](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-external-tables-for-the-sample-data"></a>Vytvoření externích tabulek pro ukázková data

Teď jste připraveni zahájit proces načítání dat do svého nového datového skladu. Tento kurz ukazuje, jak používat externí tabulky k načtení dat taxi taxi v New Yorku z objektu blob úložiště Azure. Další informace o tom, jak získat data do úložiště objektů blob Azure nebo je načíst přímo ze zdroje, najdete v přehledu [načítání](design-elt-data-loading.md).

Spusťte následující skripty SQL a zadejte informace o datech, která chcete načíst. Tyto informace zahrnují umístění dat, formát obsahu dat a definici tabulky pro data. 

1. V předchozí části jste se do svého datového skladu přihlásili jako LoaderRC20. V SSMS klikněte pravým tlačítkem na připojení LoaderRC20 a vyberte **Nový dotaz**.  Zobrazí se nové okno dotazu. 

    ![Nové okno dotazu načítání](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Porovnejte své okno dotazu s předchozím obrázkem.  Ověřte, že je vaše okno dotazu spuštěné pod účtem LoaderRC20 a provádí dotazy na vaši databázi MySampleDataWarehouse. Toto okno dotazu použijte k provedení všech kroků načítání.

3. Vytvořte hlavní klíč pro databázi MySampleDataWarehouse. Pro každou databázi je nutné vytvořit hlavní klíč pouze jednou. 

    ```sql
    CREATE MASTER KEY;
    ```

4. Spuštěním následujícího příkazu [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) definujte umístění objektu blob v Azure. Toto je umístění externích dat taxislužby.  Chcete-li spustit příkaz, který jste připojili k oknu dotazu, zvýrazněte příkazy, které chcete spustit, a vyberte **příkaz Spustit**.

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
        TYPE = Hadoop,
        LOCATION = 'wasbs://2013@nytaxiblob.blob.core.windows.net/'
    );
    ```

5. Spuštěním následujícího příkazu T-SQL [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql) určete charakteristiky a možnosti formátování pro externí datový soubor. Tento příkaz určuje, že jsou externí data uložená jako text a hodnoty jsou oddělené znakem roury („|“). Externí soubor je komprimovaný pomocí Gzip. 

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( 
            FIELD_TERMINATOR = ',',
            STRING_DELIMITER = '',
            DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        )
    );
    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH ( 
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = '|',
            STRING_DELIMITER = '',
        DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        ),
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

6.  Spuštěním následujícího příkazu [CREATE SCHEMA](/sql/t-sql/statements/create-schema-transact-sql) vytvořte schéma pro formát vašeho externího souboru. Schéma představuje způsob uspořádání externích tabulek, které se chystáte vytvořit.

    ```sql
    CREATE SCHEMA ext;
    ```

7. Vytvořte externí tabulky. Definice tabulek jsou uloženy v datovém skladu, ale tabulky odkazují na data, která jsou uložená v úložišti objektů blob Azure. Spuštěním následujících příkazů T-SQL vytvořte několik externích tabulek odkazujících na objekt blob Azure, který jsme dříve definovali v externím zdroji dat.

    ```sql
    CREATE EXTERNAL TABLE [ext].[Date] 
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
        LOCATION = 'Date',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    ); 
    CREATE EXTERNAL TABLE [ext].[Geography]
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
        LOCATION = 'Geography',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0 
    );      
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'HackneyLicense',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Medallion',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;  
    CREATE EXTERNAL TABLE [ext].[Time]
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
        LOCATION = 'Time',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Trip]
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
        LOCATION = 'Trip2013',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = compressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        LOCATION = 'Weather',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
    ```

8. V Průzkumníku objektů rozbalte mySampleDataWarehouse a zobrazte seznam externích tabulek, které jste právě vytvořili.

    ![Zobrazení externích tabulek](./media/load-data-from-azure-blob-storage-using-polybase/view-external-tables.png)

## <a name="load-the-data-into-your-data-warehouse"></a>Načtení dat do datového skladu

Tato část používá externí tabulky, které jste právě definovali k načtení ukázkových dat z objektu blob úložiště Azure.  

> [!NOTE]
> V tomto kurzu se data načítají přímo do konečné tabulky. V produkčním prostředí budete obvykle používat příkaz CREATE TABLE AS SELECT k načtení dat do pracovní tabulky. Zatímco jsou data v pracovní tabulce, můžete provést všechny potřebné transformace. K připojení dat v pracovní tabulce do provozní tabulky můžete použít příkaz INSERT...SELECT. Další informace najdete v tématu popisujícím [vkládání dat do provozní tabulky](guidance-for-loading-data.md#inserting-data-into-a-production-table).

Tento skript pomocí příkazu T-SQL [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) načítá data z Azure Storage Blob do nových tabulek ve vašem datovém skladu. Příkaz CTAS vytvoří novou tabulku na základě výsledků příkazu SELECT. Nová tabulka obsahuje stejné sloupce a datové typy jako výsledky příkazu SELECT. Když příkaz select vybere z externí tabulky, data se importují do relační tabulky v datovém skladu. 

1. Spuštěním následujícího skriptu načtěte data do nových tabulek ve svém datovém skladu.

    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    CREATE TABLE [dbo].[Geography]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    CREATE TABLE [dbo].[Medallion]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    CREATE TABLE [dbo].[Time]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    CREATE TABLE [dbo].[Weather]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    CREATE TABLE [dbo].[Trip]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

2. Zobrazte data během načítání. Načítáte několik GBs dat a jejich kompresi do vysoce výkonných clusterovaných indexů columnstore. Spuštěním následujícího dotazu, který používá zobrazení dynamické správy, zobrazíte stav načítání. 

    ```sql
    SELECT
        r.command,
        s.request_id,
        r.status,
        count(distinct input_name) as nbr_files,
        sum(s.bytes_processed)/1024/1024/1024.0 as gb_processed
    FROM 
        sys.dm_pdw_exec_requests r
        INNER JOIN sys.dm_pdw_dms_external_work s
        ON r.request_id = s.request_id
    WHERE
        r.[label] = 'CTAS : Load [dbo].[Date]' OR
        r.[label] = 'CTAS : Load [dbo].[Geography]' OR
        r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
        r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
        r.[label] = 'CTAS : Load [dbo].[Time]' OR
        r.[label] = 'CTAS : Load [dbo].[Weather]' OR
        r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
        r.command,
        s.request_id,
        r.status
    ORDER BY
        nbr_files desc, 
        gb_processed desc;
    ```

3. Zobrazte všechny systémové dotazy.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Užívejte si pohled na to, jak se data krásně načítají do vašeho datového skladu.

    ![Zobrazení načtených tabulek](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="authenticate-using-managed-identities-to-load-optional"></a>Ověření pomocí spravovaných identit k načtení (volitelné)
Načítání pomocí PolyBase a ověřování prostřednictvím spravovaných identit je nejbezpečnější mechanismus a umožňuje využít koncové body virtuální síťové služby s Azure Storage. 

### <a name="prerequisites"></a>Požadavky
1.    Nainstalujte Azure PowerShell pomocí této [příručky](https://docs.microsoft.com/powershell/azure/install-az-ps).
2.    Pokud máte účet úložiště pro obecné účely v1 nebo objektblo, musíte nejprve upgradovat na obecný účel v2 pomocí této [příručky](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
3.  Musíte mít **možnost Povolit důvěryhodným službám Microsoftu přístup k tomuto účtu úložiště** zapnuté v nabídce Firewally účtů Azure Storage a Nastavení **virtuálních sítí.** Další informace naleznete v této [příručce.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)

#### <a name="steps"></a>Kroky
1. V PowerShellu **zaregistrujte sql server** pomocí Azure Active Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```
   
   1. Vytvořte **účet úložiště pro obecné účely v2** pomocí této [příručky](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

   > [!NOTE]
   >
   > - Pokud máte účet úložiště pro obecné účely v1 nebo objektblo, musíte **nejprve upgradovat na v2** pomocí této [příručky](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
   
1. V části účet úložiště přejděte na **příkaz Řízení přístupu (IAM)** a vyberte **Přidat přiřazení role**. Přiřaďte databázovému serveru SQL roli RBAC **přispěvatele dat objektů blob úložiště.**

   > [!NOTE] 
   > Tento krok mohou provést pouze členové s oprávněním vlastníka. Různé předdefinované role pro prostředky Azure najdete v této [příručce](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  
1. **Připojení Polybase k účtu Azure Storage:**
  
   1. Vytvořte pověření s rozsahem databáze s **identitou IDENTITY = Identita spravované služby**:

       ```SQL
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```
       > [!NOTE] 
       > - Není nutné zadávat SECRET s klíčem přístupu k úložišti Azure Storage, protože tento mechanismus používá [spravovanou identitu](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pod kryty.
       > - Název identity by měl být **"Identita spravované služby"** pro připojení PolyBase pro práci s účtem Azure Storage.
   
   1. Vytvořte zdroj externích dat určující pověření s rozsahem databáze pomocí identity spravované služby.
     
   1. Dotaz jako obvykle pomocí [externích tabulek](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql).

Pokud chcete nastavit koncové body služby virtuální sítě pro Azure Synapse Analytics, podívejte se do následující [dokumentace.](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Účtují se vám výpočetní prostředky a data, která načtete do svého datového skladu. Ta se účtují zvlášť. 

- Pokud chcete zachovat data v úložišti, můžete pozastavit výpočetní prostředky v době, kdy datový sklad nepoužíváte. Když pozastavíte výpočetní prostředky, bude se vám účtovat pouze úložiště dat, a kdykoli budete připraveni s daty pracovat, můžete výpočetní prostředky zase obnovit.
- Pokud chcete zamezit budoucím poplatkům, můžete datový sklad odstranit. 

Pomocí tohoto postupu podle potřeby vyčistěte prostředky.

1. Přihlaste se na [portál Azure](https://portal.azure.com), vyberte svůj datový sklad.

    ![Vyčištění prostředků](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Chcete-li pozastavit výpočetní výkon, vyberte tlačítko **Pozastavit.** Když je datový sklad pozastavený, zobrazí se tlačítko **Spustit**.  Chcete-li pokračovat v výpočtu, vyberte **možnost Start**.

3. Pokud chcete datový sklad odebrat, aby vám neúčtovali poplatky za výpočetní prostředky nebo úložiště, vyberte **Odstranit**.

4. Chcete-li odebrat vytvořený server SQL, vyberte **mynewserver-20180430.database.windows.net** v předchozím obrázku a pak vyberte **Odstranit**.  Buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

5. Chcete-li skupinu prostředků odebrat, vyberte **položku myResourceGroup**a potom vyberte **odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky 
V tomto kurzu jste se naučili vytvořit datový sklad a uživatele pro načítání dat. Vytvořili jste externí tabulky pro definici struktury dat uložených v Azure Storage Blob a pak jste pomocí příkazu PolyBase CREATE TABLE AS SELECT načetli data do svého datového skladu. 

Provedli jste tyto akce:
> [!div class="checklist"]
> * Vytvoření datového skladu na webu Azure Portal
> * Vytvořit pravidlo brány firewall na úrovni serveru na webu Azure Portal
> * Připojení k datovému skladu pomocí SSMS
> * Vytvoření vyhrazeného uživatele pro načítání dat
> * Vytvoření externích tabulek pro data v Azure Storage Blob
> * Načtení dat do datového skladu pomocí příkazu T-SQL CTAS
> * Zobrazení průběhu nahrávání dat
> * Vytvoření statistik pro nově načtená data

Přejdete k přehledu vývoje a zjistěte, jak migrovat existující databázi do Azure Synapse Analytics.

> [!div class="nextstepaction"]
> [Rozhodnutí o návrhu migrace existující databáze do Azure Synapse Analytics](sql-data-warehouse-overview-develop.md)
