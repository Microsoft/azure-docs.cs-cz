---
title: 'Kurz: Taxicab New Yorku zatížení dat do Azure SQL Data Warehouse | Microsoft Docs'
description: Kurz používá Azure portal a SQL Server Management Studio načíst New Yorku Taxicab data z veřejné Azure blob do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: fb918cc70a3a3d21e86c9d530e264199794886f1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-load-new-york-taxicab-data-to-azure-sql-data-warehouse"></a>Kurz: Taxicab New Yorku zatížení dat do Azure SQL Data Warehouse

Tento kurz používá PolyBase k načtení dat New Yorku Taxicab z veřejné blob Azure do Azure SQL Data Warehouse. Tento kurz používá [Azure Portal](https://portal.azure.com) a aplikaci [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) k: 

> [!div class="checklist"]
> * Vytvoření datového skladu na webu Azure Portal
> * Vytvořit pravidlo brány firewall na úrovni serveru na webu Azure Portal
> * Připojení k datovému skladu pomocí SSMS
> * Vytvoření vyhrazeného uživatele pro načítání dat
> * Vytvoření externích tabulek pro data v úložišti objektů blob v Azure
> * Načtení dat do datového skladu pomocí příkazu T-SQL CTAS
> * Zobrazení průběhu nahrávání dat
> * Vytvoření statistik pro nově načtená data

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="before-you-begin"></a>Než začnete

Než začnete s tímto kurzem, stáhněte a nainstalujte nejnovější verzi aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).


## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-blank-sql-data-warehouse"></a>Vytvoření prázdného datového skladu SQL

Datový sklad SQL Azure se vytvoří s definovanou sadou [výpočetních prostředků](memory-and-concurrency-limits.md). Databáze se vytvoří v rámci [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) a na [logickém serveru SQL Azure](../sql-database/sql-database-features.md). 

Pomocí následujících kroků vytvořte prázdný datový sklad SQL. 

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

2. Na stránce **Nový** vyberte **Databáze** a v části **Doporučené** na stránce **Nový** vyberte **SQL Data Warehouse**.

    ![vytvoření datového skladu](media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Do formuláře služby SQL Data Warehouse zadejte následující informace:   

   | Nastavení | Navrhovaná hodnota | Popis | 
   | ------- | --------------- | ----------- | 
   | **Název databáze** | mySampleDataWarehouse | Platné názvy databází najdete v tématu [Identifikátory databází](/sql/relational-databases/databases/database-identifiers). | 
   | **Předplatné** | Vaše předplatné  | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
   | **Skupina prostředků** | myResourceGroup | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Výběr zdroje** | Prázdná databáze | Určuje, že se má vytvořit prázdná databáze. Poznámka: datový sklad je jedním z typů databáze.|

    ![vytvoření datového skladu](media/load-data-from-azure-blob-storage-using-polybase/create-data-warehouse.png)

4. Klikněte na **Server** a vytvořte a nakonfigurujte nový server pro novou databázi. Do **formuláře Nový server** zadejte následující informace: 

    | Nastavení | Navrhovaná hodnota | Popis | 
    | ------- | --------------- | ----------- |
    | **Název serveru** | Libovolný globálně jedinečný název | Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). | 
    | **Přihlašovací jméno správce serveru** | Libovolné platné jméno | Platná přihlašovací jména najdete v tématu [Identifikátory databází](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers).|
    | **Heslo** | Libovolné platné heslo | Heslo musí mít alespoň osm znaků a musí obsahovat znaky ze tří z následujících kategorií: velká písmena, malá písmena, číslice a jiné než alfanumerické znaky. |
    | **Umístění** | Libovolné platné umístění | Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/). |

    ![vytvoření databázového serveru](media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Klikněte na **Vybrat**.

6. Klikněte na **Úroveň výkonu** a určete, jestli je datový sklad optimalizovaný pro elasticitu nebo výpočetní výkon, a počet jednotek datového skladu. 

7. Pro účely tohoto kurzu vyberte úroveň služby **Optimalizováno pro elasticitu**. Posuvník je ve výchozím nastavení nastavený na hodnotu **DW400**.  Zkuste jeho posouváním hodnotu zvýšit a snížit a podívejte se, jak funguje. 

    ![konfigurace výkonu](media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Klikněte na tlačítko **Použít**.
9. Na stránce služby SQL Data Warehouse vyberte **kolaci** pro prázdnou databázi. Pro účely tohoto kurzu použijte výchozí hodnotu. Další informace o kolacích najdete v tématu [Kolace](/sql/t-sql/statements/collations).

11. Po vyplnění formuláře pro SQL Database klikněte na **Vytvořit** a databázi zřiďte. Zřizování trvá několik minut. 

    ![kliknutí na Vytvořit](media/load-data-from-azure-blob-storage-using-polybase/click-create.png)

12. Na panelu nástrojů klikněte na **Oznámení** a sledujte proces nasazení.
    
     ![oznámení](media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Vytvoření pravidla brány firewall na úrovni serveru

Služba SQL Data Warehouse vytvoří bránu firewall na úrovni serveru, aby zabránila externím aplikacím a nástrojům v připojení k serveru nebo ke kterékoli databázi na serveru. Pokud chcete umožnit připojení, můžete přidat pravidla brány firewall, která povolí připojení z konkrétních IP adres.  Postupujte podle těchto pokynů a vytvořte [pravidlo brány firewall na úrovni serveru](../sql-database/sql-database-firewall-configure.md) pro IP adresu vašeho klienta. 

> [!NOTE]
> SQL Data Warehouse komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud je to tak, nebudete se moct připojit k serveru Azure SQL Database, dokud vaše IT oddělení neotevře port 1433.
>

1. Po dokončení nasazení klikněte na **Databáze SQL** z nabídky na levé straně a klikněte na **mySampleDatabase** na stránce **Databáze SQL**. Otevře se stránka s přehledem pro vaši databázi, na které se zobrazí plně kvalifikovaný název serveru (například **mynewserver-20171113.database.windows.net**) a možnosti pro další konfiguraci. 

2. Zkopírujte tento plně kvalifikovaný název serveru, abyste ho mohli použít pro připojení k serveru a jeho databázím v následujících rychlých startech. Pak kliknutím na název serveru otevřete nastavení serveru.

    ![vyhledání názvu serveru](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png) 

3. Kliknutím na název serveru otevřete nastavení serveru.

    ![nastavení serveru](media/load-data-from-azure-blob-storage-using-polybase/server-settings.png) 

5. Klikněte na **Zobrazit nastavení brány firewall**. Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database. 

    ![pravidlo brány firewall serveru](media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png) 

4. Klikněte na **Přidat IP adresu klienta** na panelu nástrojů a přidejte svoji aktuální IP adresu do nového pravidla brány firewall. Pravidlo brány firewall může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

5. Klikněte na **Uložit**. Vytvoří se pravidlo brány firewall na úrovni serveru pro vaši aktuální IP adresu, které otevře port 1433 na logickém serveru.

6. Klikněte na **OK** a pak zavřete stránku **Nastavení brány firewall**.

Pomocí této IP adresy se teď můžete připojit k serveru SQL a jeho datovým skladům. Připojení funguje z aplikace SQL Server Management Studio nebo jiného nástroje podle vašeho výběru. Při připojování použijte účet správce serveru, který jste předtím vytvořili.  

> [!IMPORTANT]
> Standardně je přístup přes bránu firewall služby SQL Database povolený pro všechny služby Azure. Pokud chcete bránu firewall zakázat pro všechny služby Azure, klikněte na této stránce na **VYPNUTO** pak klikněte na **Uložit**.

## <a name="get-the-fully-qualified-server-name"></a>Získání plně kvalifikovaného názvu serveru

Na webu Azure Portal získejte plně kvalifikovaný název vašeho serveru SQL. Tento plně kvalifikovaný název použijete později při připojování k serveru.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo vyberte **SQL Database** a na stránce **Databáze SQL** klikněte na vaši databázi. 
3. V podokně **Základy** na stránce webu Azure Portal pro vaši databázi vyhledejte a potom zkopírujte **Název serveru**. V tomto příkladu je plně kvalifikovaný název mynewserver-20171113.database.windows.net. 

    ![informace o připojení](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Připojení k serveru jako správce serveru

V této části se pomocí aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) naváže připojení k serveru SQL Azure.

1. Otevřete SQL Server Management Studio.

2. V dialogovém okně **Připojení k serveru** zadejte následující informace:

    | Nastavení      | Navrhovaná hodnota | Popis | 
    | ------------ | --------------- | ----------- | 
    | Typ serveru | Databázový stroj | Tato hodnota se vyžaduje. |
    | Název serveru | Plně kvalifikovaný název serveru | Název by měl vypadat přibližně takto: **mynewserver-20171113.database.windows.net**. |
    | Authentication | Ověřování SQL Serveru | Ověřování SQL je jediný typ ověřování, který jsme v tomto kurzu nakonfigurovali. |
    | Přihlásit | Účet správce serveru | Jedná se o účet, který jste zadali při vytváření serveru. |
    | Heslo | Heslo pro účet správce serveru | Jedná se o heslo, které jste zadali při vytváření serveru. |

    ![Připojení k serveru](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

4. Klikněte na **Připojit**. V aplikaci SSMS se otevře okno Průzkumníka objektů. 

5. V Průzkumníku objektů rozbalte **Databáze**. Pak rozbalte **Systémové databáze** a uzel **master** a zobrazte objekty v hlavní databázi.  Rozbalte **mySampleDatabase** a zobrazte objekty v nové databázi.

    ![databázové objekty](media/load-data-from-azure-blob-storage-using-polybase/connected.png) 

## <a name="create-a-user-for-loading-data"></a>Vytvoření uživatele pro načítání dat

Účet správce serveru slouží k provádění operací správy a není vhodný pro spouštění dotazů na uživatelská data. Načítání dat je operace s vysokými nároky na paměť. Maximální paměť hodnoty jsou definovány podle [úroveň výkonu](memory-and-concurrency-limits.md#performance-tiers), [datového skladu jednotky](what-is-a-data-warehouse-unit-dwu-cdwu.md), a [Třída prostředků](resource-classes-for-workload-management.md). 

Doporučujeme vytvořit účet a uživatele vyhrazeného pro načítání dat. Pak přidejte uživatele načítání do [třídy prostředků](resource-classes-for-workload-management.md), která umožňuje odpovídající maximální přidělení paměti.

Vzhledem k tomu, že jste aktuálně připojeni jako správce serveru, můžete vytvářet účty a uživatele. Pomocí následujících kroků vytvořte účet a uživatele **LoaderRC20**. Pak uživatele přiřaďte k třídě prostředků **staticrc20**. 

1.  V SSMS kliknutím pravým tlačítkem na uzel **master** zobrazte rozevírací nabídku a zvolte **Nový dotaz**. Otevře se nové okno dotazu.

    ![Nový dotaz v uzlu master](media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. V okně dotazu zadejte následující příkazy T-SQL, které vytvoří účet a uživatele LoaderRC20, a heslo a123STRONGpassword nahraďte vlastním heslem. 

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Klikněte na tlačítko **Spustit**.

4. Klikněte pravým tlačítkem na **mySampleDataWarehouse** a zvolte **Nový dotaz**. Otevře se nové okno dotazu.  

    ![Nový dotaz na ukázkový datový sklad](media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)
 
5. Zadejte následující příkazy T-SQL, které pro účet LoaderRC20 vytvoří uživatele databáze LoaderRC20. Na druhém řádku se novému uživateli přidělí oprávnění CONTROL k novému datovému skladu.  Tato oprávnění jsou podobná, jako kdybyste z uživatele udělali vlastníka databáze. Na třetím řádku se nový uživatel přidá jako člen [třídy prostředků](resource-classes-for-workload-management.md) staticrc20.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Klikněte na tlačítko **Spustit**.

## <a name="connect-to-the-server-as-the-loading-user"></a>Připojení k serveru jako uživatel načítání

Prvním krokem k načítání dat je přihlášení jako LoaderRC20.  

1. V Průzkumníku objektů klikněte na rozevírací nabídku **Připojit** a vyberte **Databázový stroj**. Zobrazí se dialogové okno **Připojení k serveru**.

    ![Přihlášení pomocí nového účtu](media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Zadejte plně kvalifikovaný název serveru a jako Účet zadejte **LoaderRC20**.  Zadejte své heslo k účtu LoaderRC20.

3. Klikněte na **Připojit**.

4. Až bude vaše připojení připravené, v Průzkumníku objektů se zobrazí dvě připojení k serveru. Jedno připojení jako ServerAdmin a druhé jako MedRCLogin.

    ![Úspěšné připojení](media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-external-tables-for-the-sample-data"></a>Vytvoření externích tabulek pro ukázková data

Teď jste připraveni zahájit proces načítání dat do svého nového datového skladu. V tomto kurzu se dozvíte, jak používat externí tabulky k načtení New Yorku taxíkem souboru cab dat z objektu blob úložiště Azure. Informace o přesunu dat do Azure Blob Storage nebo jejich načtení přímo ze zdroje do služby SQL Data Warehouse najdete pro budoucí použití v části s [přehledem načítání](sql-data-warehouse-overview-load.md).

Spuštěním následujících skriptů SQL zadejte informace o datech, která chcete načíst. Tyto informace zahrnují umístění dat, formát obsahu dat a definici tabulky pro data. 

1. V předchozí části jste se do svého datového skladu přihlásili jako LoaderRC20. V SSMS klikněte pravým tlačítkem na připojení LoaderRC20 a vyberte **Nový dotaz**.  Zobrazí se nové okno dotazu. 

    ![Nové okno dotazu načítání](media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Porovnejte své okno dotazu s předchozím obrázkem.  Ověřte, že je vaše okno dotazu spuštěné pod účtem LoaderRC20 a provádí dotazy na vaši databázi MySampleDataWarehouse. Toto okno dotazu použijte k provedení všech kroků načítání.

3. Vytvořte hlavní klíč pro databázi MySampleDataWarehouse. Pro každou databázi je nutné vytvořit hlavní klíč pouze jednou. 

    ```sql
    CREATE MASTER KEY;
    ```

4. Spuštěním následujícího příkazu [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) definujte umístění objektu blob v Azure. Toto je umístění externích dat taxislužby.  Pokud chcete spustit příkaz, který jste připojili k oknu dotazu, zvýrazněte příkazy, které chcete spustit, a klikněte na **Provést**.

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

7. Vytvořte externí tabulky. Definice tabulek se ukládají ve službě SQL Data Warehouse, ale tabulky odkazují na data uložená v úložišti objektů blob v Azure. Spuštěním následujících příkazů T-SQL vytvořte několik externích tabulek odkazujících na objekt blob Azure, který jsme dříve definovali v externím zdroji dat.

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

    ![Zobrazení externích tabulek](media/load-data-from-azure-blob-storage-using-polybase/view-external-tables.png)

## <a name="load-the-data-into-your-data-warehouse"></a>Načtení dat do datového skladu

V této části se použijí externí tabulky, které jste právě definovali, k načtení ukázkových dat z Azure Storage Blob do služby SQL Data Warehouse.  

> [!NOTE]
> V tomto kurzu se data načítají přímo do konečné tabulky. V produkčním prostředí budete obvykle používat příkaz CREATE TABLE AS SELECT k načtení dat do pracovní tabulky. Zatímco jsou data v pracovní tabulce, můžete provést všechny potřebné transformace. K připojení dat v pracovní tabulce do provozní tabulky můžete použít příkaz INSERT...SELECT. Další informace najdete v tématu popisujícím [vkládání dat do provozní tabulky](guidance-for-loading-data.md#inserting-data-into-a-production-table).
> 

Tento skript pomocí příkazu T-SQL [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) načítá data z Azure Storage Blob do nových tabulek ve vašem datovém skladu. Příkaz CTAS vytvoří novou tabulku na základě výsledků příkazu SELECT. Nová tabulka obsahuje stejné sloupce a datové typy jako výsledky příkazu SELECT. Když příkaz SELECT provádí výběr z externí tabulky, SQL Data Warehouse importuje data do relační tabulky v datovém skladu. 

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

2. Zobrazte data během načítání. Provedete načtení několika GB dat a jejich kompresi do vysoce výkonných clusterovaných indexů columnstore. Spuštěním následujícího dotazu, který používá zobrazení dynamické správy, zobrazíte stav načítání. Po spuštění dotazu si udělejte přestávku na kávu, zatímco SQL Data Warehouse bude dělat namáhavou práci.

    ```sql
    SELECT
        r.command,
        s.request_id,
        r.status,
        count(distinct input_name) as nbr_files,
        sum(s.bytes_processed)/1024/1024/1024 as gb_processed
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

    ![Zobrazení načtených tabulek](media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="create-statistics-on-newly-loaded-data"></a>Vytvoření statistik pro nově načtená data

SQL Data Warehouse nevytváří ani neaktualizuje statistiku automaticky. Pro dosažení vysokého výkonu dotazu je proto důležité vytvořit statistiku pro každý sloupec každé tabulky po prvním načtení. Důležité je také aktualizovat statistiku po důležitých změnách v datech.

Spuštěním následujících příkazů vytvořte statistiky pro sloupce, které se pravděpodobně budou používat v příkazech JOIN.

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Účtují se vám výpočetní prostředky a data, která načtete do svého datového skladu. Ta se účtují zvlášť. 

- Pokud chcete zachovat data v úložišti, můžete pozastavit výpočetní prostředky v době, kdy datový sklad nepoužíváte. Když pozastavíte výpočetní prostředky, bude se vám účtovat pouze úložiště dat, a kdykoli budete připraveni s daty pracovat, můžete výpočetní prostředky zase obnovit.
- Pokud chcete zamezit budoucím poplatkům, můžete datový sklad odstranit. 

Pomocí tohoto postupu podle potřeby vyčistěte prostředky.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a klikněte na váš datový sklad.

    ![Vyčištění prostředků](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Pokud chcete pozastavit výpočetní prostředky, klikněte na tlačítko **Pozastavit**. Když je datový sklad pozastavený, zobrazí se tlačítko **Spustit**.  Pokud chcete obnovit výpočetní prostředky, klikněte na **Spustit**.

3. Pokud chcete odebrat datový sklad, aby se vám neúčtovaly výpočetní prostředky ani prostředky úložiště, klikněte na **Odstranit**.

4. Pokud chcete odstranit server SQL, který jste vytvořili, klikněte na **mynewserver-20171113.database.windows.net**, jak je znázorněno na předchozím obrázku, a pak klikněte na **Odstranit**.  Buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

5. Pokud chcete odebrat skupinu prostředků, klikněte na **myResourceGroup** a pak klikněte na **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup 
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

Pokračujte k přehledu migrace a zjistěte, jak do služby SQL Data Warehouse migrovat existující databázi.

> [!div class="nextstepaction"]
>[Zjistěte, jak do služby SQL Data Warehouse migrovat existující databázi](sql-data-warehouse-overview-migrate.md).
