---
title: Sestavit a nasadit model pomocí SQL Data Warehouse – vědecké zpracování týmových dat
description: Sestavujte a nasazujte pomocí SQL Data Warehouse s veřejně dostupnou datovou sadou model strojového učení.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/24/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8d9e59a11957e8d108ae86ebba157a06129efa58
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111539"
---
# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>Vědecké zpracování týmových dat v akci: použití služby SQL Data Warehouse
V tomto kurzu jsme vás provede procesem vytvoření a nasazení modelu strojového učení pomocí SQL Data Warehouse (SQL data Warehouse) veřejně dostupné datové sady – [cesty taxíkem NYC](http://www.andresmh.com/nyctaxitrips/) datové sady. Binární klasifikační model vytvořený předpovídá, zda tip platí pro cesty a které předpovědět distribuce pro tip částky zaplacené jsou popsány také pro víc tříd klasifikačních a regresních modelů.

Následující postup [vědecké zpracování týmových dat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) pracovního postupu. Vám ukážeme, jak nastavit prostředí pro datové vědy jak načíst data do SQL data Warehouse a použití SQL data Warehouse nebo IPython Notebook pro zkoumání dat a inženýr funkce do modelu. Potom ukážeme, jak sestavit a nasadit model s využitím Azure Machine Learning.

## <a name="dataset"></a>Cesty taxíkem NYC datové sady
Data o jízdách taxislužby NYC se skládá z přibližně 20GB komprimované soubory CSV (nekomprimovaný ~ 48GB), záznam 173 milionů jednotlivé trips a tarify placené pro každou cestu. Každý záznam o jízdách zahrnuje sbírat míčky a dropoff umístění a čas, anonymizované hack (ovladač) číslo licence a číslo Medailon (jedinečné id taxislužby.). Data v roce 2013 zahrnuje všechny cesty a je dostupné pro každý měsíc následující dvě datové sady:

1. **Trip_data.csv** soubor obsahuje podrobnosti o jízdách, jako je třeba počet cestujících, sbírat míčky a dropoff body, doba trvání cesty a délka cesty. Tady je několik ukázkových záznamů:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. **Trip_fare.csv** soubor obsahuje podrobné informace o tarif placené pro každou cestu, například typ platby, velikost tarif, příplatek za a daní, tipy a mýtné a celkové částky zaplacené. Tady je několik ukázkových záznamů:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

**Jedinečný klíč** použije k připojení k cestě\_a dat o jízdách\_tarif se skládá z následujících tří polí:

* medailonu,
* hack\_licence a
* vyzvednutí\_data a času.

## <a name="mltasks"></a>Adresa tři druhy úkonů predikcí
Jsme formulovali tři problémy předpovědi na základě *tip\_částka* pro ilustraci tři druhy modelování úlohy:

1. **Binární klasifikace**: Předpovědět, zda je či není tip byla zaplacena cesty, to znamená *tip\_velikost* , který je větší než 0 USD je kladné příklad, zatímco *tip\_velikost* $ 0 je záporná příklad.
2. **Klasifikace víc tříd**: K předpovědi rozsahu tip placené pro cestu. Doporučujeme rozdělit *tip\_částka* do pěti přihrádky nebo třídy:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Úloha regrese**: Odhadnout množství tip placené cesty.

## <a name="setup"></a>Nastavení prostředí Azure data science pro pokročilou analýzu
Nastavení prostředí Azure pro datové vědy, postupujte podle těchto kroků.

**Vytvořte si vlastní účet úložiště objektů blob v Azure**

* Když si zřídíte Azure blob storage, zvolte geografické polohy pro Azure blob storage v nebo co nejblíže k **střed USA – jih**, data taxislužby NYC uložené. Data se zkopírují pomocí AzCopy z veřejného kontejneru objektů blob storage do kontejneru v účtu úložiště. Čím blíž je službě Azure blob storage na střed USA – Jih, tím rychleji dokončení tohoto úkolu (krok 4).
* K vytvoření účtu úložiště Azure, postupujte podle kroků uvedených v [účty Azure storage](../../storage/common/storage-create-storage-account.md). Nezapomeňte si dělat poznámky na hodnoty pro následující přihlašovací údaje účtu úložiště, jako je budou potřebovat později v tomto názorném postupu.

  * **Název účtu úložiště**
  * **Klíč účtu úložiště**
  * **Název kontejneru** (které chcete data, která mají být uloženy ve službě Azure blob storage)

**Zřízení vaše instance Azure SQL data Warehouse.**
Postupujte podle dokumentace na [vytvořit SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) zřídit instanci SQL Data Warehouse. Ujistěte se, že provedete zápisy na následující přihlašovací údaje SQL Data Warehouse, které se budou používat v dalších krocích.

* **Server Name**: <server Name>.database.windows.net
* **Název SQLDW (databáze)**
* **Uživatelské jméno**
* **Heslo**

**Instalace sady Visual Studio a SQL Server Data Tools.** Pokyny najdete v tématu [instalace sady Visual Studio 2015 a SSDT (SQL Server Data Tools) pro službu SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Připojte se k vaší Azure SQL data Warehouse pomocí sady Visual Studio.** Pokyny najdete v tématu kroky 1 a 2 v [připojení k Azure SQL Data Warehouse pomocí sady Visual Studio](../../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

> [!NOTE]
> Spusťte následující dotaz SQL na databázi vytvořené ve službě SQL Data Warehouse (místo dotazu zadaného v kroku 3 tématu připojit) do **vytvořte hlavní klíč**.
>
>

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Vytvoření pracovního prostoru Azure Machine Learning v rámci vašeho předplatného Azure.** Pokyny najdete v tématu [vytvoření pracovního prostoru Azure Machine Learning](../studio/create-workspace.md).

## <a name="getdata"></a>Načtení dat do SQL Data Warehouse
Otevřete konzolu příkazového prostředí Windows PowerShell. Spusťte následující příkaz Powershellu příkazy ke stažení příkladu SQL skriptu soubory, které sdílíme s vámi na Githubu do místního adresáře, který zadáte s parametrem *- DestDir*. Můžete změnit hodnotu parametru *- DestDir* do libovolného místního adresáře. Pokud *- DestDir* buď neexistuje, vytvoří se skript prostředí PowerShell.

> [!NOTE]
> Možná budete muset **spustit jako správce** při spuštění následujícího skriptu prostředí PowerShell, pokud vaše *DestDir* directory potřebuje správce oprávnění k vytvoření nebo do ní zapisovat.
>
>

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Po úspěšném spuštění, změní aktuální pracovní adresář na *- DestDir*. Byste měli vidět obrazovka podobná níže uvedenému příkladu:

![Změny v aktuálním pracovním adresáři][19]

Ve vaší *- DestDir*, spusťte následující skript prostředí PowerShell v režimu správce:

    ./SQLDW_Data_Import.ps1

Při prvním spuštění skriptu prostředí PowerShell, zobrazí výzva k zadání informace z vašeho datového skladu SQL Azure a účtu úložiště objektů blob v Azure. Po dokončení tento skript Powershellu s prvním, přihlašovací údaje je vstup bude mít byl zapsán do konfiguračního souboru SQLDW.conf v aktuální pracovní adresář. Budoucí spuštění tohoto souboru skriptu prostředí PowerShell je možnost číst že všechny potřebné parametry z tohoto konfiguračního souboru. Pokud potřebujete změnit některé parametry, můžete vybrat vstupní parametry na obrazovce na řádku odstraněním tohoto konfiguračního souboru a po zobrazení výzvy zadejte hodnoty parametrů nebo změně hodnot parametrů tak, že upravíte soubor SQLDW.conf ve vašich *- DestDir* adresáře.

> [!NOTE]
> Pokud se chcete vyhnout schématu název je v konfliktu s těmi, které již existují v Azure SQL DW, při čtení parametry přímo ze souboru SQLDW.conf náhodné číslo 3 číslice se přidá k názvu schématu ze souboru SQLDW.conf jako výchozí název schématu pro každé spuštění. Skript prostředí PowerShell může vyzve k zadání názvu schématu: může být zadán název na uvážení uživatelů.
>
>

To **skript prostředí PowerShell** soubor dokončí následující úkoly:

* **Stáhne a nainstaluje AzCopy**, pokud ještě nemáte nainstalovaný nástroj AzCopy

        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
               Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }
* **Zkopíruje data do účtu úložiště objektů blob v privátní** z veřejných objektů blob pomocí nástroje AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* **Načtení dat pomocí Polybase (spuštěním LoadDataToSQLDW.sql) do Azure SQL DW** z vašeho účtu úložiště objektů blob v privátní pomocí následujících příkazů.

  * Vytvořte schéma

          EXEC (''CREATE SCHEMA {schemaname};'');
  * Vytvoření přihlašovacích údajů s rozsahem databáze

          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Vytvoření externího zdroje dat pro objektu blob služby Azure storage

          CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;

          CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;
  * Vytvořte externí soubor formát souboru .csv. Nekomprimovaných dat a pole jsou oddělené znakem svislé čáry.

          CREATE EXTERNAL FILE FORMAT {csv_file_format}
          WITH
          (
              FORMAT_TYPE = DELIMITEDTEXT,
              FORMAT_OPTIONS
              (
                  FIELD_TERMINATOR ='','',
                  USE_TYPE_DEFAULT = TRUE
              )
          )
          ;
  * Vytvoření externí tarif a o jízdách tabulek pro sadu dat taxislužby NYC ve službě Azure blob storage.

          CREATE EXTERNAL TABLE {external_nyctaxi_fare}
          (
              medallion varchar(50) not null,
              hack_license varchar(50) not null,
              vendor_id char(3),
              pickup_datetime datetime not null,
              payment_type char(3),
              fare_amount float,
              surcharge float,
              mta_tax float,
              tip_amount float,
              tolls_amount float,
              total_amount float
          )
          with (
              LOCATION    = ''/nyctaxifare/'',
              DATA_SOURCE = {nyctaxi_fare_storage},
              FILE_FORMAT = {csv_file_format},
              REJECT_TYPE = VALUE,
              REJECT_VALUE = 12
          )

            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                   medallion varchar(50) not null,
                   hack_license varchar(50)  not null,
                   vendor_id char(3),
                   rate_code char(3),
                   store_and_fwd_flag char(3),
                   pickup_datetime datetime  not null,
                   dropoff_datetime datetime,
                   passenger_count int,
                   trip_time_in_secs bigint,
                   trip_distance float,
                   pickup_longitude varchar(30),
                   pickup_latitude varchar(30),
                   dropoff_longitude varchar(30),
                   dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12
            )

    - Načtení dat z externích tabulek ve službě Azure blob storage do SQL Data Warehouse

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - Vytvoření ukázkové tabulky dat (NYCTaxi_Sample) a vkládání dat k němu z výběru dotazů SQL pro tabulky cesty a tarif. (Některé kroky tohoto názorného postupu musí používat tento vzorovou tabulkou.)

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

Zeměpisné umístění účtu úložiště má vliv na dobu načítání.

> [!NOTE]
> V závislosti na zeměpisné umístění účtu úložiště objektů blob v privátním, proces kopírování dat z veřejných objektů blob do účtu privátního úložiště může trvat přibližně 15 minut nebo i delší dobu a proces načítání dat z vašeho účtu úložiště do Azure SQL data Warehouse může trvat 20 minut nebo i delší dobu.
>
>

Budete muset rozhodnout, které máte duplicitní zdrojové a cílové soubory.

> [!NOTE]
> Pokud CSV soubory zkopírovány z veřejné blob storage do účtu úložiště objektů blob v privátní již existují ve vašem účtu úložiště objektů blob v privátním, AzCopy zeptá, jestli chcete je přepsat. Pokud nechcete přepsat, vstup **n** po zobrazení výzvy. Pokud chcete přepsat **všechny** z nich, vstup po zobrazení výzvy. Můžete také zadat **y** přepsat soubory .csv jednotlivě.
>
>

![Výstup nástroje AzCopy][21]

Můžete použít vlastní data. Pokud jsou vaše data ve vašem místním počítači v reálné aplikaci, můžete stále použít AzCopy k nahrání místních dat do úložiště objektů blob v Azure privátní. Je potřeba jenom změnit **zdroj** umístění, `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, v příkazu AzCopy soubor skriptu Powershellu k místnímu adresáři, který obsahuje vaše data.

> [!TIP]
> Pokud už vaše data v reálném životě aplikaci ve svém úložišti objektů blob v Azure privátní, můžete přeskočit krok AzCopy ve skriptu prostředí PowerShell a přímo odeslat data do Azure SQL data Warehouse. To bude vyžadovat další úpravy skript, který chcete přizpůsobit formát data.
>
>

Tento skript prostředí Powershell také zpřístupní v informacích o Azure SQL data Warehouse datové soubory příklad zkoumání SQLDW_Explorations.sql SQLDW_Explorations.ipynb a SQLDW_Explorations_Scripts.py tak, že tyto tři soubory jsou připraveni vyzkoušeli okamžitě po dokončení skriptu prostředí PowerShell.

Po úspěšném spuštění, zobrazí se obrazovka podobná níže uvedenému příkladu:

![Výstup skriptu úspěšná spuštění][20]

## <a name="dbexplore"></a>Zkoumání dat a vytváření funkcí ve službě Azure SQL Data Warehouse
V této části provádíme zkoumání a funkce generování dat prostřednictvím spouštění dotazů SQL pro Azure SQL data Warehouse přímo pomocí **Visual Studio Data Tools**. Všechny dotazy SQL, které jsou použity v tomto scénáři najdete v ukázkový skript s názvem *SQLDW_Explorations.sql*. Tento soubor již byl stažen do místního adresáře skript prostředí PowerShell. Můžete také získat z [Githubu](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Ale souboru na Githubu, nemá žádné informace o Azure SQL data Warehouse zapojené do elektrické zásuvky.

Připojte se k vaší Azure SQL data Warehouse pomocí sady Visual Studio s SQL data Warehouse přihlašovací jméno a heslo a otevře **Průzkumník objektů systému SQL** potvrďte databáze a tabulky byly naimportovány. Načíst *SQLDW_Explorations.sql* souboru.

> [!NOTE]
> Chcete-li otevřít editor dotazů Parallel Data Warehouse (PDW), použijte **nový dotaz** příkaz zapnutým vaše PDW **Průzkumník objektů systému SQL**. Standardní editor dotazů SQL PDW nepodporuje.
>
>

Tady je typu dat dělat úlohy generování zkoumání a funkce v této části:

* Prozkoumejte data distribuce několik polí v různých časových oken.
* Prozkoumejte data kvality zeměpisnou šířku a délku pole.
* Generovat popisky klasifikace binární a víc tříd na základě **tip\_částka**.
* Generovat funkce a výpočetní/porovnání vzdálenosti o jízdách.
* Spojení dvou tabulek a extrahovat náhodného vzorku, který se použije k vytvoření modelů.

### <a name="data-import-verification"></a>Ověření importu dat
Tyto dotazy poskytují rychlé ověření počtu řádků a sloupců v tabulkách vyplní dříve pomocí Polybase pro paralelní hromadný import,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Výstup:** Měli byste obdržet 173,179,759 řádků a 14 sloupců.

### <a name="exploration-trip-distribution-by-medallion"></a>Zkoumání: Distribuce latence podle Medailon
Tento příklad dotaz identifikuje medallions (taxislužby čísla), která dokončila více než 100 zkracuje dobu odezvy v rámci určeného časového období. Dotaz je výhodná dělenou tabulku přístupu od náležitého schéma oddílů **vyzvednutí\_data a času**. Dotazování úplnou datovou sadu se také provést pomocí dělené tabulky nebo indexu kontroly.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Výstup:** Dotaz by měl vrátit tabulku s řádky zadání 13,369 medallions (taxi) a číslem o jízdách dokončit v 2013. Poslední sloupec obsahuje počet cest dokončit.

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Zkoumání: Distribuce latence podle Medailon a hack_license
V tomto příkladu identifikuje medallions (taxislužby čísla) a hack_license čísla (ovladače), která dokončena více než 100 zkracuje dobu odezvy v rámci určeného časového období.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Výstup:** Dotaz by měl vrátit tabulku s 13,369 řádky zadání 13,369 car/driver ID, které dokončily další této 100 zkracuje dobu odezvy v 2013. Poslední sloupec obsahuje počet cest dokončit.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Hodnocení kvality dat: Zkontrolujte záznamy s nesprávné délky a šířky
V tomto příkladu prověří, pokud jakýkoli z polí zeměpisná délka a/nebo zeměpisnou šířku buď obsahuje neplatnou hodnotu (stupně radián by měl být od -90 do 90), nebo máte (0, 0) souřadnic.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Výstup:** Dotaz vrátí 837,467 služebních cest, které mají neplatnou zeměpisnou délku a/nebo zeměpisnou šířku pole.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Zkoumání: Šikmý vs. není šikmý zkracuje dobu odezvy distribuce
Tento příklad zjistí počet cest, které byly šikmý vs. číslo, které nebyly šikmý v zadaném časovém období (nebo v celé datové sadě, pokud pokrývající celý rok, jak je zde nastavený). Toto rozdělení odráží distribuce binární označení později použitého pro binární klasifikaci modelování.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Výstup:** Dotaz by měl vrátit následující frekvencí tip pro rok 2013: 90,447,622 šikmý a 82,264,709 not šikmý.

### <a name="exploration-tip-classrange-distribution"></a>Zkoumání: Tip třídy a rozsahu distribuce
Tento příklad vypočítá distribuci tip rozsahů v daném časovém období (nebo v celé datové sadě, pokud pokrývající celý rok). To je distribuce popisek tříd, které se později použijí pro modelování klasifikace víc tříd.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**Výstup:**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Zkoumání: COMPUTE a porovnat vzdálenost o jízdách
Tento příklad převede sbírat míčky a dropoff délky a šířky do SQL zeměpisné oblasti odkazuje, vypočítá vzdálenost o jízdách pomocí SQL zeměpisné oblasti bodů rozdíl a vrací náhodného vzorku výsledky pro porovnání. V příkladu omezuje výsledky na platné souřadnice pouze pomocí dotazu hodnocení kvality dat věnovali výše.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>Vytváření funkcí pomocí funkce SQL
Funkce SQL může být někdy efektivní možnosti pro vytváření funkcí. V tomto názorném postupu jsme definovali funkci SQL pro přímé vzdálenosti mezi vzájemně sbírat míčky a dropoff umístění. Spuštěním následujících skriptů SQL **Visual Studio Data Tools**.

Tady je skript SQL, který definuje funkci vzdálenost.

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

Tady je příklad pro volání této funkce pro generování funkcí v dotazu SQL:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Výstup:** Tento dotaz vytvoří tabulku (s 2,803,538 řádky) s sbírat míčky a dropoff zeměpisná šířka a délka a odpovídající přímé vzdálenosti v mil. Tady jsou výsledky pro první 3 řádky:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Příprava dat pro vytváření modelů
Následující dotaz spojení **nyctaxi\_o jízdách** a **nyctaxi\_tarif** tabulky, generuje binární klasifikační popisek **šikmý**, Popisek klasifikace roc **tip\_třída**a extrahuje ukázku z úplné datové sadě připojené k doméně. Vzorkování se provádí načtením podmnožinu zkracuje dobu odezvy na základě času vyzvednutí.  Tento dotaz můžete zkopírovat a vložit přímo v [Azure Machine Learning Studio](https://studio.azureml.net) [Import dat] [ import-data] modul pro zpracování dat s přímým přístupem v instanci SQL database v Azure. Dotaz vyloučí záznamy s nesprávnou (0, 0) souřadnic.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Až budete připravení přejít k Azure Machine Learning, můžete se buď:

1. Poslední dotaz SQL extrahovat ukázková data a kopírování a vkládání dotaz přímo do uložit [Import dat] [ import-data] modulu ve službě Azure Machine Learning, nebo
2. Zachování vzorky a analýzou dat máte v úmyslu použít pro model vytváření nové tabulky SQL data Warehouse a použít nové tabulky v [Import dat] [ import-data] modulu ve službě Azure Machine Learning. Skript Powershellu v předchozích kroků má to provede za vás. Může číst přímo z této tabulky v modulu, importovat Data.

## <a name="ipnb"></a>Zkoumání dat a vytváření funkcí v IPython notebook
V této části budeme provádět zkoumání dat a vytváření funkce pomocí obou Python a dotazy SQL na SQL data Warehouse, vytvořili dříve. Notebook IPython ukázkové s názvem **SQLDW_Explorations.ipynb** a souboru skriptu Pythonu **SQLDW_Explorations_Scripts.py** byly staženy do místního adresáře. Jsou k dispozici na [Githubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Tyto dva soubory jsou stejné v skriptů v Pythonu. V případě, že nemáte serveru IPython Notebook budete mít k dispozici soubor skriptu Pythonu. Tyto dvě ukázkové soubory jsou navrženy v Pythonu **Python 2.7**.

Azure SQL data Warehouse potřebné informace v ukázce IPython Notebook a souboru skriptu Pythonu stáhnou do místního počítače má byla zapojené do elektrické zásuvky skript prostředí PowerShell již dříve. Jedná se o spustitelný soubor bez nutnosti jakékoli úpravy.

Pokud již jste vytvořili pracovní prostor služby Azure ml, můžete přímo nahrát ukázku IPython Notebook ve službě Azure ml IPython Notebook a spustíte ho. Tady jsou kroky odesílat do služby Azure ml IPython Notebook:

1. Přihlaste se k vašemu pracovnímu prostoru Azure ml, klikněte na tlačítko "Studio" v horní části a klikněte na "Notebooky" na levé straně webové stránky.

    ![Klikněte na tlačítko Studio pak poznámkové BLOKY][22]
2. V levém dolním rohu webové stránky klikněte na tlačítko "NEW" a vyberte "Python 2". Potom zadejte název do poznámkového bloku a klikněte na značku zaškrtnutí k vytvoření nové prázdné IPython Notebook.

    ![Klikněte na nový, pak vyberte Python 2][23]
3. Kliknutím na symbol "Jupyter" v levém horním rohu nový IPython Notebook.

    ![Klikněte na Jupyter symbol][24]
4. Přetáhnout myší ukázka IPython Notebook pro **stromu** stránku služby Azure ml IPython Notebook, a klikněte na **nahrát**. Potom ukázka IPython Notebook nahraje do služby Azure ml IPython Notebook.

    ![Klikněte na tlačítko Odeslat][25]

Chcete-li spustit ukázku soubor Pythonu následující balíčky jsou nutné skriptu IPython Notebook nebo Python. Pokud používáte službu Azure ml IPython Notebook, tyto balíčky se předem nainstalované.

    - pandas
    - numpy
    - matplotlib
    - modul pyodbc
    - PyTables

Doporučené pořadí při sestavování Pokročilá analytická řešení v Azure ml s velkými datovými je následující:

* Přečtěte si v malým vzorkem dat do rámečku data v paměti.
* Provedení některých vizualizací a průzkumy pomocí jen Vzorkovaná data.
* Můžete experimentovat s vytváření funkcí pomocí jen Vzorkovaná data.
* Pro větší zkoumání dat, manipulaci s daty a vytváření funkcí použití Pythonu k vydávat dotazy SQL přímo na SQL data Warehouse.
* Rozhodněte, velikost vzorku být vhodný pro vytváření modelů Azure Machine Learning.

Tady je několik zkoumání dat, vizualizace dat a funkce technické příklady. Další průzkumy dat najdete v ukázce IPython Notebook a ukázkový soubor skriptu Pythonu.

### <a name="initialize-database-credentials"></a>Inicializovat pověření databáze
Inicializace nastavení připojení k databázi v následující proměnné:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Vytvoření připojení k databázi
Tady je připojovací řetězec, který se vytvoří připojení k databázi.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Sestava počet řádků a sloupců v tabulce < nyctaxi_trip >
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Celkový počet řádků = 173179759
* Celkový počet sloupců = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxifare"></a>Sestava počet řádků a sloupců v tabulce < nyctaxi_fare >
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Celkový počet řádků = 173179759
* Celkový počet sloupců = 11

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>Čtení ve vzorku malá data z databáze datového skladu SQL
    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Doba čtení že ukázkové tabulky je 14.096495 sekund.
Počet řádků a sloupců načtení = (1 000, 21).

### <a name="descriptive-statistics"></a>Popisný statistiky
Nyní jste připraveni na zkoumání jen Vzorkovaná data. Začneme s statistikami popisný pro prohlížení **o jízdách\_vzdálenost** (nebo všechna pole, které budete chtít zadat).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Vizualizace: Příklad diagramu pole
Dále podíváme na diagramu pole pro cestu vzdálenost k vizualizaci quantiles.

    df1.boxplot(column='trip_distance',return_type='dict')

![Pole vykreslení výstupu][1]

### <a name="visualization-distribution-plot-example"></a>Vizualizace: Příklad diagramu distribuce
Vykreslení, které vizualizují distribuce a histogram vzdálenosti vzorky o jízdách.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Distribuce vykreslení výstupu][2]

### <a name="visualization-bar-and-line-plots"></a>Vizualizace: Pruhové a spojnicové grafy
V tomto příkladu jsme bin vzdálenost výlet do pěti přihrádek a vizualizaci výsledků binningu.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Jsme můžete vykreslení výše uvedené přihrádky distribuce v pruhu nebo čáry diagramu s:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Panel výstupu vykreslení][3]

a

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Výstup vykreslení čáry][4]

### <a name="visualization-scatterplot-examples"></a>Vizualizace: Příklady diagnostického
Ukážeme korelačního diagramu mezi **o jízdách\_čas\_v\_sekundy** a **o jízdách\_vzdálenost** zobrazíte, pokud se jakákoli korelace

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Diagnostického výstupu vztah mezi časem a vzdálenost][6]

Podobně lze kontrolujeme vztah mezi **míra\_kód** a **o jízdách\_vzdálenost**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Diagnostického výstupu vztah mezi kódem a vzdálenost][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Zkoumání dat na vzorku dat. použití příkazů jazyka SQL v IPython notebook
V této části se podíváme na distribuce dat pomocí vzorky data, která se ukládají do nové tabulky, kterou jsme vytvořili výše. Všimněte si, že podobné průzkumy je možné provádět pomocí původní tabulky.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Zkoumání: Sestava počet řádků a sloupců v tabulce vzorky
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Zkoumání: Přepnutí šikmý nebo není distribuce
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Zkoumání: Tip třídy distribuce
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Zkoumání: Vykreslení distribuce tip třídou
    tip_class_dist['tip_freq'].plot(kind='bar')

![Vykreslení #26][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Zkoumání: Denní distribuční cesty
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Zkoumání: Distribuce výlet za Medailon
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Zkoumání: Distribuce latence podle Medailon a hack licence
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Zkoumání: Distribuce doby odezvy
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Zkoumání: Vzdálenost distribuce latence
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Zkoumání: Distribuce typu platby
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Zkontrolujte poslední formulář natrénuje tabulky
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Vytváření modelů Azure Machine Learning.
Jsme připraveni přejít k vytváření modelů a nasazení modelů v [Azure Machine Learning](https://studio.azureml.net). Data jsou připravená k použití v některém z předpovědi problémy identifikovat dříve, a to:

1. **Binární klasifikace**: Pro předpověď Určuje, jestli tip byla zaplacena cesty.
2. **Klasifikace víc tříd**: K předpovědi rozsahu tip placené podle dříve definovaných tříd.
3. **Úloha regrese**: Odhadnout množství tip placené cesty.

Začněte cvičení modelování, přihlaste se k vaší **Azure Machine Learning** pracovního prostoru. Pokud jste ještě nevytvořili pracovního prostoru machine learning, přečtěte si téma [vytvoření pracovního prostoru Azure Machine Learning studio](../studio/create-workspace.md).

1. Začínáme s Azure Machine Learning, najdete v článku [co je Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net).
3. Na domovskou stránku Studio nabízí celou řadu informací, videa, kurzy, odkazy k odkazu moduly a dalším prostředkům. Další informace o službě Azure Machine Learning [centru dokumentace Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Typické výukového experimentu se skládá z následujících kroků:

1. Vytvoření **+ nová** experimentovat.
2. Načíst data do Azure Machine Learning studio.
3. Předběžné zpracování, transformovat a zpracovávat data podle potřeby.
4. Funkce vygenerujte, podle potřeby.
5. Rozdělení dat do datové sady školení/ověření/testování (nebo máte rozdělování datových sad pro každou).
6. Vyberte jeden nebo více algoritmů strojového učení v závislosti na učení problém vyřešit. Například binární klasifikace klasifikace víc tříd, regrese.
7. Trénování jeden nebo více modelů pomocí trénovací datové sady.
8. Ověření datové sady, natrénované modely pomocí skóre.
9. Vyhodnoťte modely pro výpočet důležité metriky pro výuky.
10. Bez problémů můžete vyladit modely a vybrat nejlepší model nasadit.

V tomto cvičení jsme mít již prozkoumali a analýzou dat ve službě SQL Data Warehouse a rozhodli na velikost vzorku ingestovat v Azure Machine Learning studio. Tady je postup pro sestavení jeden nebo více prediktivní modely:

1. Získat data do aplikace pomocí Azure Machine Learning studio [Import dat] [ import-data] modulu, k dispozici v **datový vstup a výstup** oddílu. Další informace najdete v tématu [Import dat] [ import-data] stránce odkaz na modul.

    ![Azure ML umožňuje importovat Data][17]
2. Vyberte **Azure SQL Database** jako **zdroj dat** v **vlastnosti** panelu.
3. Zadejte název DNS databáze **název databázového serveru** pole. Formát: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Zadejte **název_databáze** v odpovídajícím poli.
5. Zadejte *uživatelské jméno SQL* v **název uživatelského účtu serveru**a *heslo* v **heslo uživatelského účtu serveru**.
7. V **databázový dotaz** upravit textovou oblast, vložte dotaz, který extrahuje nezbytné databázová pole (včetně jakékoli vypočítaná pole, jako jsou štítky) a dolů vzorkuje data, která mají velikost požadovaného vzorku.

Na následujícím obrázku je například binární klasifikace: experiment čtení dat přímo z databáze SQL Data Warehouse (Nezapomeňte nahradit název schématu a názvy tabulek, který jste použili v vašeho návodu nyctaxi_trip názvy tabulek a nyctaxi_fare). Podobně jako experimenty může být vytvořen pro klasifikace víc tříd a regresní problémy.

![Trénování služby Azure ML][10]

> [!IMPORTANT]
> Modelování dat extrakce a vzorkování Příklady dotazů uvedené v předchozích částech, **všechny popisky pro tři cvičení modelování jsou obsažena v dotazu**. Důležitým krokem (povinné) v každém modelování cvičení je **vyloučit** zbytečné popisky pro dva problémy a jakékoli jiné **cílit nevracení**. Například pokud používáte binární klasifikace, použít popisek **šikmý** a vyloučit pole **tip\_třídy**, **tip\_částka**a **celkový\_částka**. Druhá možnost se cílového únikům protože implikují tip placené.
>
> Vyloučit všechny nepotřebných sloupců nebo cílové nevracení, můžete použít [výběr sloupců v datové sadě] [ select-columns] modulu nebo [upravit Metadata][edit-metadata]. Další informace najdete v tématu [výběr sloupců v datové sadě] [ select-columns] a [upravit Metadata] [ edit-metadata] odkazují na stránky.
>
>

## <a name="mldeploy"></a>Nasazení modelů ve službě Azure Machine Learning
Když je model hotový, můžete snadno nasadit ho jako webovou službu přímo z experimentu. Další informace o nasazení webové služby Azure ML najdete v tématu [nasazení webové služby Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).

Pokud chcete nasadit nové webové služby, budete muset:

1. Vytvoření experimentu bodování.
2. Nasazení webové služby.

K vytvoření bodování experiment z **dokončeno** školení experiment, klikněte na tlačítko **vytvořit vyhodnocování EXPERIMENTOVAT** na dolním panelu akcí.

![Bodování Azure][18]

Azure Machine Learning se pokusí vytvořit bodování experiment založené na součástech výukového experimentu. Konkrétně se tyto akce:

1. Uložení naučeného modelu a odeberte moduly trénování modelu.
2. Identifikujte logické **vstupním portem** představující schéma očekávaný vstupní data.
3. Identifikujte logické **výstupní port** představující schéma výstupu očekávané webové služby.

Při vyhodnocování experiment, zkontrolujte ho a proveďte podle potřeby upravte. Typické nastavení je k nahrazení vstupní datové sady a/nebo dotazu s jedním, který vyloučí popisek pole, tyto nebudou k dispozici při volání služby. Je také vhodné zmenšit velikost vstupní datové sady a/nebo dotazu na několik záznamů, právě takové, aby označuje vstupní schéma. Pro výstupní port je společné pro všechny vstupní pole vyloučit a obsahovat jenom **popisky vyhodnocení** a **skóre pravděpodobnosti** ve výstupu pomocí [výběr sloupců v datové sadě] [ select-columns] modulu.

Ukázku vyhodnocování experimentu najdete v následujícím obrázku. Až budete připraveni k nasazení, klikněte na tlačítko **publikovat webovou službu** tlačítko na panelu akcí nižší.

![Služba Azure ML publikovat][11]

## <a name="summary"></a>Souhrn
Na rekapitulace, co jsme udělali v tomto kurzu návodu, vytvoříte Azure prostředí pro datové vědy, pracoval s velkou datovou sadu veřejné, trvá až vědecké zpracování týmových dat, od získání dat pro trénování modelu a potom na nasazení webové služby Azure Machine Learning.

### <a name="license-information"></a>Informace o licenci
Tento ukázkový názorný postup a jeho doprovodném skripty a IPython notebook(s) sdílí Microsoft v rámci licence MIT. Zkontrolujte prosím soubor LICENSE.txt v adresáři ukázkového kódu na Githubu pro další podrobnosti.

## <a name="references"></a>Odkazy
• [Stránce pro stažení cesty taxíkem NYC Andrés Monroy](http://www.andresmh.com/nyctaxitrips/) • [FOILing NYC Taxi Data o jízdách podle Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/) • [NYC taxislužby a Limousine Komise výzkumu a statistiky](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sqldw-walkthrough/azuremltrain.png
[11]: ./media/sqldw-walkthrough/azuremlpublish.png
[12]: ./media/sqldw-walkthrough/ssmsconnect.png
[13]: ./media/sqldw-walkthrough/executescript.png
[14]: ./media/sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/sqldw-walkthrough/bulkimport.png
[17]: ./media/sqldw-walkthrough/amlreader.png
[18]: ./media/sqldw-walkthrough/amlscoring.png
[19]: ./media/sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/sqldw-walkthrough/ps_load_data.png
[21]: ./media/sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
