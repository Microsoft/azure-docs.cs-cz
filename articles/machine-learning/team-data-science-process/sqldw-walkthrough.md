---
title: 'Proces Team dat. vědecké účely v akci: pomocí SQL Data Warehouse | Microsoft Docs'
description: Proces pokročilou analýzu a technologie v akci
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 88ba8e28-0bd7-49fe-8320-5dfa83b65724
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: bradsev
ms.openlocfilehash: 6566db5f186b92179df3125deaf5ad17c6f9e974
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>Proces Team dat. vědecké účely v akci: pomocí SQL Data Warehouse
V tomto kurzu jsme vás provede procesem vytváření a nasazování modelu strojového učení pomocí SQL datového skladu (SQL DW) pro veřejně dostupné datové sady – [NYC taxíkem cest](http://www.andresmh.com/nyctaxitrips/) datovou sadu. Binární klasifikace model sestavený předpovídá, zda je tip placené cesty a které předpovědi distribuce pro tip částky placené jsou popsány i modely pro více třídami klasifikace a regrese.

Následuje postup [tým datové vědy procesu (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) pracovního postupu. Ukážeme, jak nastavit prostředí datového vědecké účely, jak načíst data do datového skladu SQL a o použití datového skladu SQL nebo IPython poznámkového bloku a prozkoumejte data a pracovníka funkce do modelu. Potom ukážeme, jak vytvořit a nasadit model pomocí Azure Machine Learning.

## <a name="dataset"></a>Datová sada NYC taxíkem cest
Data NYC taxíkem cesty se skládá z přibližně 20GB komprimovaných souborů CSV (nekomprimovaným ~ 48GB), zaznamenávání 173 milionů jednotlivých cest a tarify placené pro každou cestu. Každý záznam cestě zahrnuje vyzvednutí a odkládací umístění a časy, anonymizovaná hackerský (ovladač) číslo licence a číslo Medailon (taxi na jedinečné id). Data obsahuje všechny služebních cest v roku 2013 a je dostupné pro každý měsíc následující dvě datové sady:

1. **Trip_data.csv** soubor obsahuje podrobnosti o cestě, například na počtu cestujících, vyzvednutí a dropoff body, doba trvání cesty a délka cesty. Tady je několik ukázkových záznamů:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. **Trip_fare.csv** soubor obsahuje podrobnosti o tarif placené pro každou cestu, například typ platby, velikost tarif, příplatek a daně, tipy a mýtné, a celkovou velikost placené. Tady je několik ukázkových záznamů:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

**Jedinečný klíč** používá k připojení k cestě\_dat a cesty\_tarif se skládá z následující tři pole:

* medailonu,
* zabezpečení\_licencí a
* vyzvednutí\_data a času.

## <a name="mltasks"></a>Adresa tři typy úloh předpovědi
Jsme formulovali tři předpovědi problémů na základě *tip\_velikost* pro ilustraci tři druhy modelování úlohy:

1. **Binární klasifikace**: K předvídání, jestli tip byl placené cesty, tj. *tip\_velikost* větší než $0 je pozitivní příklad, při *tip\_velikost* $ 0 je záporný příklad.
2. **Více třídami klasifikace**: K předpovědi rozsahu tipu placené pro cestu. Jsme rozdělit *tip\_velikost* do pěti přihrádek nebo třídy:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Úloha regrese**: K předvídání množství tip placené cesty.  

## <a name="setup"></a>Nastavení prostředí vědecké účely dat Azure pro pokročilou analýzu
K nastavení prostředí vědecké zpracování dat Azure, postupujte podle těchto kroků.

**Vytvořit svůj vlastní účet úložiště objektů blob v Azure**

* Při zřizování úložiště objektů blob v Azure, zvolte geografické umístění úložiště objektů blob v Azure v nebo co nejblíže k **jihu USA**, což je, které jsou uložená data NYC taxíkem. Data se zkopírují pomocí AzCopy z úložiště kontejneru veřejného objektu blob do kontejneru v účtu úložiště. Čím bližší služby Azure blob storage je jihu USA, tím rychleji se tento úkol (krok 4) dokončí.
* Při vytváření účtu úložiště Azure, postupujte podle kroků uvedených v [účty Azure storage](../../storage/common/storage-create-storage-account.md). Ujistěte se, chcete-li poznámky u hodnot pro tyto přihlašovací údaje účtu úložiště, jak bude potřeba dále v tomto návodu.
  
  * **Název účtu úložiště**
  * **Klíče účtu úložiště.**
  * **Název kontejneru** (aplikaci, kterou chcete data se neukládají v Azure blob storage)

**Zřízení instance Azure SQL DW.**
Postupujte podle dokumentace v [vytvořit SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) ke zřízení instanci SQL Data Warehouse. Ujistěte se, abyste vytvořili zápisy na následující přihlašovací údaje SQL Data Warehouse, které se použije v dalších krocích.

* **Server Name**: <server Name>.database.windows.net
* **Název SQLDW (databáze)**
* **Uživatelské jméno**
* **Heslo**

**Instalace sady Visual Studio a SQL Server Data Tools.** Pokyny najdete v tématu [instalaci sady Visual Studio 2015 a SSDT (SQL Server Data Tools) pro SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Připojte k vaší datového skladu Azure SQL pomocí sady Visual Studio.** Pokyny najdete v tématu kroky 1 a 2 v [připojit k Azure SQL Data Warehouse pomocí sady Visual Studio](../../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

> [!NOTE]
> Spusťte následující dotaz SQL na databázi, kterou jste vytvořili v SQL Data Warehouse (namísto dotazu zadaný v kroku 3 tématu připojení) na **vytvoření hlavního klíče**.
> 
> 

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Vytvořte pracovní prostor služby Azure Machine Learning v rámci vašeho předplatného Azure.** Pokyny najdete v tématu [vytvořit pracovní prostor služby Azure Machine Learning](../studio/create-workspace.md).

## <a name="getdata"></a>Načítání dat do SQL Data Warehouse
Otevřete konzolu příkazového prostředí Windows PowerShell. Spusťte PowerShell následující příkazy ke stažení v příkladu SQL skriptu soubory, které můžeme sdílet s vámi na Githubu do místního adresáře, který zadáte s parametrem *- DestDir*. Můžete změnit hodnotu parametru *- DestDir* do libovolného místního adresáře. Pokud *- DestDir* neexistuje, vytvoří se skript prostředí PowerShell.

> [!NOTE]
> Možná budete muset **spustit jako správce** při provádění následující skript prostředí PowerShell, pokud vaše *DestDir* directory potřebuje správce oprávnění k vytvoření nebo zápis.
> 
> 

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Po úspěšném provedení změny aktuální pracovní adresář *- DestDir*. Nyní byste měli mít obrazovka jako níže:

![][19]

Ve vašem *- DestDir*, spusťte následující skript prostředí PowerShell v režimu správce:

    ./SQLDW_Data_Import.ps1

Při prvním spuštění skriptu prostředí PowerShell, zobrazí se výzva k vstupní informace z vašeho datového skladu SQL Azure a účtu úložiště objektů blob v Azure. Po dokončení tohoto skriptu prostředí PowerShell s prvním, přihlašovací údaje vstup je bude mít byla zapsána do konfiguračního souboru SQLDW.conf v přítomen pracovní adresář. Budoucí spuštění tento soubor skriptu PowerShell má možnost přečtěte si, že všechny parametry z tohoto konfiguračního souboru. Pokud potřebujete změnit některé parametry, můžete k vstupní parametry na obrazovce po řádku odstranit tento konfigurační soubor a po zobrazení výzvy zadání hodnot parametrů nebo ke změně hodnoty parametru úpravou souboru SQLDW.conf ve vašem *- DestDir* adresáře.

> [!NOTE]
> Aby se zabránilo schématu název je v konfliktu s těmi, které již existují v Azure SQL DW, při čtení parametry přímo ze souboru SQLDW.conf, náhodné číslo 3 číslice je přidat k názvu schématu ze souboru SQLDW.conf jako výchozí název schématu pro každé spuštění. Skript prostředí PowerShell můžete být vyzváni k zadání názvu schématu: uvážení uživatele může být zadán název.
> 
> 

To **skript prostředí PowerShell** souboru dokončí následující úkoly:

* **Stáhne a nainstaluje AzCopy**, pokud ještě není nainstalovaný nástroj AzCopy
  
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
* **Zkopíruje data na účtu úložiště objektů blob privátní** z veřejného objektu blob s AzCopy
  
        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* **Načte data pomocí Polybase (spuštěním LoadDataToSQLDW.sql) pro vaši Azure SQL DW** z vašeho účtu úložiště objektů blob privátní pomocí následujících příkazů.
  
  * Vytvořte schéma
    
          EXEC (''CREATE SCHEMA {schemaname};'');
  * Vytvoření oboru databáze přihlašovacích údajů
    
          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Vytvoření externího zdroje dat pro objekt blob úložiště Azure
    
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
  * Vytvořte formátu externí soubor pro soubor csv. Data nekomprimované a pole jsou oddělená znakem kanálu.
    
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
  * Vytvořte externí tarif a tabulky cesty pro datovou sadu taxíkem NYC v Azure blob storage.
    
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

    - Načtení dat z externí tabulky v Azure blob storage do SQL Data Warehouse

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

    - Vytvoří tabulku ukázkových dat (NYCTaxi_Sample) a vložit data, vyberou dotazů SQL na služební cestě a tarif tabulky. (Některé kroky tohoto názorného postupu musí tato ukázková tabulka.)

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

Geografické umístění účtů úložiště ovlivňuje časů načtení.

> [!NOTE]
> V závislosti na jejich zeměpisné umístění svého účtu úložiště blob privátní proces kopírování dat z veřejného objektu blob na váš účet privátní úložiště může trvat přibližně 15 minut, nebo i déle a proces načítání dat z vašeho účtu úložiště do vaší Azure SQL DW může trvat 20 minut nebo déle.  
> 
> 

Je nutné se rozhodnout co proveďte, pokud máte duplicitní zdrojový a cílový soubor.

> [!NOTE]
> Pokud soubory CSV zkopírovány z veřejného objektu blob úložiště k účtu úložiště objektů blob privátní již existuje v účtu úložiště objektů blob privátní, AzCopy se zeptá, jestli chcete je přepsat. Pokud nechcete je přepsat, vstup **n** po zobrazení výzvy. Pokud chcete přepsat **všechny** z nich, vstup po zobrazení výzvy. Můžete také zadat **y** přepsat soubory .csv jednotlivě.
> 
> 

![Vykreslení #21][21]

Můžete vytvořit svoje vlastní data. Pokud vaše data jsou v místním počítači ve vaší aplikaci reálného života, můžete pořád použít AzCopy k nahrání místní data do vaší privátní Azure blob storage. Budete muset změnit **zdroj** umístění, `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, v příkazu AzCopy souboru skriptu prostředí PowerShell místní adresář, který obsahuje vaše data.

> [!TIP]
> Pokud již vaše data v aplikaci reálného života ve vaší privátní Azure blob storage, můžete přeskočit na krok AzCopy v skriptu prostředí PowerShell a přímo nahrát data do Azure SQL DW. To bude vyžadovat další úpravy skript, který chcete přizpůsobit na formát data.
> 
> 

Tento skript prostředí Powershell také připojuje v Azure SQL DW informace do datových souborů příklad zkoumání SQLDW_Explorations.sql, SQLDW_Explorations.ipynb a SQLDW_Explorations_Scripts.py tak, aby tyto tři soubory jsou připravení vyzkoušeny okamžitě po dokončení skriptu prostředí PowerShell.

Po úspěšném spuštění, zobrazí se obrazovka jako níže:

![][20]

## <a name="dbexplore"></a>Zkoumání dat a funkce analýzy v Azure SQL Data Warehouse
V této části, můžeme provést zkoumání a funkce generování dat provádění dotazů SQL Azure SQL DW přímo pomocí **Data nástroje sady Visual Studio**. Všechny dotazy SQL použít v této části najdete v ukázkový skript s názvem *SQLDW_Explorations.sql*. Tento soubor má již byla stažena do vašeho místního adresáře skript prostředí PowerShell. Můžete také načíst z [Githubu](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Ale soubor v Githubu nemá informace o Azure SQL DW napájený ze sítě.

Připojení k vaší Azure SQL DW pomocí sady Visual Studio s datovým Skladem SQL přihlašovací jméno a heslo a otevře **Průzkumník objektů systému SQL** potvrďte databáze a tabulky byly importovány. Načtení *SQLDW_Explorations.sql* souboru.

> [!NOTE]
> Pokud chcete otevřít editor dotazů paralelní datového skladu (PDW), použijte **nový dotaz** příkaz vaší PDW vybráno v **Průzkumník objektů systému SQL**. Standardní editoru dotazů SQL nepodporuje PDW.
> 
> 

Tady jsou typu dat zkoumání a funkce generování úlohy provádějí v této části:

* Prozkoumejte data distribuce několik polí v různých časových oken.
* Prozkoumejte data quality polí zeměpisné šířky a délky.
* Generovat binární a více třídami klasifikační štítky na základě **tip\_velikost**.
* Generovat funkce a výpočetní nebo porovnat cestě vzdálenosti.
* Spojení dvou tabulek a extrahovat z náhodného vzorku, který bude použit k vytvoření modelů.

### <a name="data-import-verification"></a>Ověření importu dat
Tyto dotazy poskytují rychlý ověření počtu řádků a sloupců v tabulkách vyplněny dříve Polybase na paralelní hromadného importu,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Výstup:** měli byste obdržet 173,179,759 řádků a sloupců 14.

### <a name="exploration-trip-distribution-by-medallion"></a>Zkoumání: Cestě distribuce podle Medailon
Tento příklad dotazu identifikuje medallions (taxi čísla), které byly dokončeny více než 100 služebních cest v rámci určeného časového období. Dotaz by využívat přístup dělenou tabulku vzhledem k tomu, že je podmíněno tím schéma oddílů **vyzvednutí\_data a času**. Dotazování úplné datové sadě také budou používat oddílů tabulky nebo indexu kontroly.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Výstup:** dotaz by měl vrátit tabulku s řádky zadání 13,369 medallions (taxislužby) a číslo cesty dokončit v 2013. Poslední sloupec obsahuje počet služebních cest byla dokončena.

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Zkoumání: Cestě distribuce podle Medailon a hack_license
Tento příklad identifikuje medallions (taxi čísla) a hack_license čísla (ovladače), dokončeno více než 100 služebních cest v rámci určeného časového období.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Výstup:** dotaz by měl vrátit tabulku s 13,369 řádky zadání 13,369 ID car a ovladače, které dokončily více, 100 cest v 2013. Poslední sloupec obsahuje počet služebních cest byla dokončena.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Hodnocení kvality dat: ověřit záznamy s nesprávné délky a šířky
Tento příklad prověří, pokud jakýkoli z pole zeměpisné délky a šířky buď obsahuje neplatnou hodnotu (radián stupňů musí být mezi -90 a 90), nebo (0, 0) souřadnice.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Výstup:** dotaz vrátí 837,467 služebních cest, které mají neplatná pole zeměpisné délky a šířky.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Zkoumání: Vysypávány oproti distribuční není šikmý služebních cest
Tento příklad vyhledá počet cest, které byly vysypávány oproti číslo, které nebyly vysypávány v zadaném časovém období (nebo v úplné datové sady, pokud vztahující se na úplné rok jako je zde nastavený). Toto rozdělení odráží binární popisek distribuci do později použije pro modelování binární klasifikace.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Výstup:** dotaz by měl vrátit následující četnosti tip pro roku 2013: 90,447,622 vysypávány a 82,264,709 vysypávány není.

### <a name="exploration-tip-classrange-distribution"></a>Zkoumání: Distribuce třídy nebo rozsah Tip
Tento příklad vypočítá distribuci rozsahy tip v daném časovém období (nebo pokud vztahující se na úplné rok úplné datové sady). Toto je distribuce popisek třídy, které se později použije pro modelování více třídami klasifikace.

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

### <a name="exploration-compute-and-compare-trip-distance"></a>Zkoumání: Výpočetní a porovnání vzdálenost cesty
Tento příklad převede vyzvednutí a odkládací zeměpisné délky a šířky na SQL geography body, vypočítá pomocí SQL geography body rozdíl vzdálenost cestě a vrátí z náhodného vzorku výsledky pro porovnání. V příkladu omezí výsledky do platná souřadnice jenom pomocí dotazu hodnocení kvality dat popsané výše.

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

### <a name="feature-engineering-using-sql-functions"></a>Funkce inženýrství pomocí funkce SQL
Funkce SQL v některých případech může být efektivní možnost pro funkci inženýrství. V tomto návodu jsme definovali funkce SQL k výpočtu přímé vzdálenost mezi vyzvednutí a dropoff umístění. Spuštěním následujících skriptů SQL v **Data nástroje sady Visual Studio**.

Zde je skript SQL, který definuje funkci vzdálenost.

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

**Výstup:** tento dotaz vygeneruje tabulku (s 2,803,538 řádky) s vyzvednutí a dropoff zeměpisné šířky a stupně zeměpisné délky a odpovídající přímé vzdálenosti v paliva. Zde jsou výsledky pro první 3 řádky:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Příprava dat pro vytváření modelů
Následující dotaz spojení **nyctaxi\_cestě** a **nyctaxi\_tarif** tabulky, vygeneruje štítek binární klasifikace **vysypávány**, Popisek více třída klasifikace **tip\_třída**a extrahuje ukázku z připojeného k úplné datové sadě. Je potřeba vzorkuje načítání podmnožinu služebních cest, na základě výstupní času.  Tento dotaz můžete zkopírovat a vložit přímo v [Azure Machine Learning Studio](https://studio.azureml.net) [importovat Data] [ import-data] modul pro přijímání přímé dat z instance databáze SQL v Azure. Dotaz vyloučí záznamy s nesprávnou (0, 0) souřadnice.

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

Jakmile budete připraveni pokračovat do Azure Machine Learning, můžete se buď:  

1. Poslední dotaz SQL k extrahování a ukázková data a způsobené kopírováním a vkládáním dotaz přímo do uložit [importovat Data] [ import-data] modulu v Azure Machine Learning, nebo
2. Zachovat data jen Vzorkovaná a inženýrství máte v úmyslu použít pro model vytváření v nové tabulce SQL DW a použít nové tabulky v [importovat Data] [ import-data] modulu v Azure Machine Learning. Můžete to bylo dokončeno skript prostředí PowerShell v předchozím kroku. Si můžete přečíst přímo z této tabulky v modulu, importovat Data.

## <a name="ipnb"></a>Zkoumání dat a funkce technikům v poznámkovém bloku IPython
V této části provedeme zkoumání dat a funkce generování pomocí obou Python a dotazy SQL pro SQL DW vytvořili dříve. Ukázka IPython Poznámkový blok s názvem **SQLDW_Explorations.ipynb** a soubor skriptu jazyka Python **SQLDW_Explorations_Scripts.py** byly staženy do vašeho místního adresáře. Jsou k dispozici na [Githubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Tyto dva soubory jsou identické v skriptů Python. Soubor skriptu jazyka Python je které jste získali v případě, že jste k serveru IPython Poznámkový blok. Tyto dva ukázkové soubory jsou navrženy v části Python **Python 2.7**.

Azure SQL DW potřebné informace v ukázce IPython Poznámkový blok a souboru skriptu jazyka Python stahovat do místního počítače je zapojen skript prostředí PowerShell dříve. Jsou spustitelné bez nutnosti jakékoli úpravy.

Pokud již jste vytvořili pracovní prostor služby AzureML, můžete přímo odeslání vzorku IPython Poznámkový blok službě AzureML IPython Poznámkový blok a spustit ho. Zde jsou kroky pro odeslání službě AzureML IPython Poznámkový blok:

1. Přihlaste se do pracovního prostoru AzureML, klikněte na tlačítko "Studio" v horní části a klikněte na "Notebooky" na levé straně webové stránky.
   
    ![Vykreslení #22][22]
2. Klikněte v levém dolním rohu webová stránka "NEW" a vyberte "Python 2". Potom zadejte název do poznámkového bloku a klikněte na tlačítko zaškrtnutí pro vytvoření nové prázdné IPython poznámkového bloku.
   
    ![Vykreslení #23][23]
3. Kliknutím na symbol "Jupyter" v levém horním rohu nový poznámkový blok IPython.
   
    ![Vykreslení #24][24]
4. Přetažení ukázka IPython Poznámkový blok k **stromu** služby AzureML IPython Poznámkový blok a klikněte na tlačítko **nahrát**. Potom ukázka IPython Poznámkový blok, nebude možné odesílat ke službě AzureML IPython poznámkového bloku.
   
    ![Vykreslení #25][25]

Aby bylo možné spustit ukázku soubor, následující Python, balíčky jsou nutné skriptu IPython Poznámkový blok nebo Python. Pokud používáte službu AzureML IPython Poznámkový blok, byly tyto balíčky předem nainstalovaná.

    - pandas
    - numpy
    - matplotlib
    - pyodbc
    - PyTables

Doporučené pořadí při sestavování Pokročilá analytická řešení na AzureML s velkých objemů dat je následující:

* Přečtěte si v malé ukázkové dat do data v paměti rámečku.
* Proveďte některé vizualizace a explorations pomocí jen Vzorkovaná data.
* Experimentujte s inženýrství funkce pomocí jen Vzorkovaná data.
* Pro větší zkoumání dat, manipulaci s daty a funkce technikům používejte jazyk Python vydávat dotazy SQL na přímo pro SQL DW.
* Rozhodněte, velikost vzorku být vhodný pro vytváření modelů Azure Machine Learning.

Tady je několik zkoumání dat, vizualizace dat a funkce technici příklady. Další data explorations naleznete v ukázce IPython Poznámkový blok a ukázkový soubor skriptu jazyka Python.

### <a name="initialize-database-credentials"></a>Inicializace přihlašovací údaje databáze
Inicializace nastavení připojení k databázi v následující proměnné:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Vytvoření připojení k databázi
Tady je připojovací řetězec, který vytvoří připojení k databázi.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Sestava počtu řádků a sloupců v tabulce < nyctaxi_trip >
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

### <a name="report-number-of-rows-and-columns-in-table-nyctaxifare"></a>Sestava počtu řádků a sloupců v tabulce < nyctaxi_fare >
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

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>Pro čtení ve vzorku malá data z databáze SQL datového skladu
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

Čas číst že ukázkové tabulky je 14.096495 sekund.  
Počet řádků a sloupců načíst = (1000, 21).

### <a name="descriptive-statistics"></a>Popisný statistiky
Nyní jste připraveni na zkoumání jen Vzorkovaná data. Začneme s prohlížení statistikami popisný pro **cestě\_vzdálenost** (nebo všechna pole, které určíte).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Vizualizace: Příklad vykreslení pole
Další podíváme na Krabicový pro vzdálenost cesty k vizualizaci quantiles.

    df1.boxplot(column='trip_distance',return_type='dict')

![Vykreslení #1][1]

### <a name="visualization-distribution-plot-example"></a>Vizualizaci: Příklad vykreslení distribuční
Pozemků, které vizualizovat distribuce a histogram vzdálenosti jen Vzorkovaná cesty.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Vykreslení #2][2]

### <a name="visualization-bar-and-line-plots"></a>Vizualizace: Panel a ukazuje zeměpisný řádku
V tomto příkladu jsme bin vzdálenost cesty do pěti přihrádek a vizualizace binning výsledků.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Jsme můžete vykreslení výše uvedené distribuční bin panelu nebo výkresu s řádek:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Vykreslení #3][3]

a

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Vykreslení #4][4]

### <a name="visualization-scatterplot-examples"></a>Vizualizaci: Příklady Scatterplot
Ukážeme bodové vykreslení mezi **cestě\_čas\_v\_sekundy** a **cestě\_vzdálenost** jestli jsou všechny korelace

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Vykreslení #6][6]

Podobně jsme můžete zkontrolovat vztah mezi **míra\_kód** a **cestě\_vzdálenost**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Vykreslení #8][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Zkoumání dat na jen Vzorkovaná data pomocí dotazů SQL v poznámkovém bloku IPython
V této části nám prozkoumat distribuce dat pomocí jen Vzorkovaná data, která je uchován v nové tabulky, kterou jsme vytvořili výše. Všimněte si, že podobné explorations je možné provést pomocí původní tabulky.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Zkoumání: Počet řádků a sloupců v tabulce jen Vzorkovaná sestavy
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Zkoumání: Vysypávány nebo nebyla přerušovačů distribuce
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Zkoumání: Distribuce třída Tip
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Zkoumání: Vykreslení distribuční tip třídou
    tip_class_dist['tip_freq'].plot(kind='bar')

![Vykreslení #26][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Zkoumání: Denní distribuce služebních cest
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Zkoumání: Distribuční cesty za Medailon
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Zkoumání: Cestě distribuce podle Medailon a hackerský licencí
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Zkoumání: Distribuce doby cestě
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Zkoumání: Distribuce vzdálenost cestě
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Zkoumání: Distribuce typ platby
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Ověření konečné formuláře featurized tabulky
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Vytvářet modely v Azure Machine Learning
Snažíme se teď přejít k vytváření modelů a modelu nasazení v [Azure Machine Learning](https://studio.azureml.net). Data jsou připravené k použití v některém z identifikované dříve, a to předpovědi problémy:

1. **Binární klasifikace**: K předvídání, jestli tip byl placené cesty.
2. **Více třídami klasifikace**: K předpovědi rozsahu tipu placené podle dříve definovaných tříd.
3. **Úloha regrese**: K předvídání množství tip placené cesty.  

Pokud chcete začít cvičení modelování, přihlaste se k vaší **Azure Machine Learning** pracovního prostoru. Pokud jste ještě nevytvořili machine learning pracovního prostoru, přečtěte si téma [vytvořit pracovní prostor služby Azure ML](../studio/create-workspace.md).

1. Chcete-li začít s Azure Machine Learning, přečtěte si téma [co je Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net).
3. Na domovskou stránku Studio poskytuje širokou řadu informací, videa, kurzy, odkazů na odkaz na moduly a dalším prostředkům. Další informace o Azure Machine Learning, naleznete [centru dokumentace Azure Machine Learning na](https://azure.microsoft.com/documentation/services/machine-learning/).

Typické výukový experiment sestává z následujících kroků:

1. Vytvoření **+ nový** experiment.
2. Získáte data do Azure ML.
3. Předběžně zpracovat, transformace a manipulovat s daty, podle potřeby.
4. Funkce vygenerujte, podle potřeby.
5. Rozdělení dat do školení, ověření nebo testování datové sady (nebo samostatné datové sady pro každou).
6. Vyberte jeden nebo více algoritmy strojového učení v závislosti na learning problém vyřešit. Například: binární klasifikace, klasifikace více třídami, regresi.
7. Cvičení jednoho nebo více modelů použití školení datové sady.
8. Stanovení skóre datovou sadu ověření pomocí vyškolení modely.
9. Vyhodnoťte modely k výpočtu relevantní metriky pro učení problém.
10. Bez problémů ladit modely a vyberte doporučené modelu pro nasazení.

V tomto cvičení jsme již prozkoumali a analýzou dat v SQL Data Warehouse a ingestování v Azure ML se rozhodli na velikost vzorku. Tady je postup vytvoření jednoho nebo více modelů předpovědi:

1. Získat data do aplikace pomocí Azure ML [importovat Data] [ import-data] modulu, k dispozici v **vstupu a výstupu dat** části. Další informace najdete v tématu [importovat Data] [ import-data] stránka s referencemi modul.
   
    ![Azure ML Import dat][17]
2. Vyberte **Azure SQL Database** jako **zdroj dat** v **vlastnosti** panelu.
3. Zadejte název DNS databáze v **název databázového serveru** pole. Formát: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Zadejte **název databáze** v odpovídajícím poli.
5. Zadejte *uživatelské jméno SQL* v **název uživatelského účtu serveru**a *heslo* v **heslo uživatelského účtu serveru**.
7. V **databázový dotaz** upravit textová oblast, vložte dotaz, který extrahuje pole potřeby databáze (včetně všech počítané pole, jako je popisků) a nižší ukázky data na požadovanou velikost.

V níže uvedeném obrázku je například binární klasifikace experimentů, čtení dat přímo z databáze SQL Data Warehouse (Nezapomeňte nahradit tabulku názvy nyctaxi_trip a nyctaxi_fare tak, že název schématu a názvy tabulek, který jste použili v vaší návod). Podobně jako experimenty konstruovat pro více třídami klasifikaci a regresní problémy.

![Azure ML Train][10]

> [!IMPORTANT]
> V datech modelování extrakce a vzorkuje příklady dotazu zadaný v předchozích částech **všech popisků tři modelování cvičení jsou zahrnuty v dotazu**. Důležitým krokem (povinné) v každé cvičení modelování je **vyloučit** nepotřebné štítky pro dva problémy a všemi ostatními **cíle nevracení**. Například při použití binární klasifikace, použít popisek **vysypávány** a vyloučit pole **tip\_třída**, **tip\_velikost**a **celkový\_velikost**. K tomu jsou cílové nevracení vzhledem k tomu, že implikují tip placené.
> 
> Vyloučit všechny nepotřebné sloupce nebo cíle nevracení, můžete použít [výběr sloupců v datové sadě] [ select-columns] modulu nebo [upravit Metadata][edit-metadata]. Další informace najdete v tématu [výběr sloupců v datové sadě] [ select-columns] a [upravit Metadata] [ edit-metadata] referenční stránky.
> 
> 

## <a name="mldeploy"></a>Nasazení modely v Azure Machine Learning
Pokud váš model je připraven, můžete snadno nasadit ho jako webovou službu přímo z experimentu. Další informace o nasazení webové služby Azure ML najdete v tématu [nasazení webové služby Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).

Chcete-li nasadit novou webovou službu, je potřeba:

1. Vytvoření experimentu vyhodnocování.
2. Nasazení webové služby.

K vytvoření vyhodnocování experimentu z **dokončeno** cvičení experiment, klikněte na tlačítko **vytvořit vyhodnocování EXPERIMENTOVAT** na dolním panelu akcí.

![Výpočet skóre Azure][18]

Azure Machine Learning se pokusí vytvořit vyhodnocování experimentu založené na součástech experimentu školení. Konkrétně se:

1. Uložení naučeného modelu a odeberte moduly školení modelu.
2. Identifikovat logickou **vstupní port** představují schéma očekávané vstupní data.
3. Identifikovat logickou **výstupní port** představují výstupního schématu očekávané webové služby.

Když je vytvořen vyhodnocování experiment, zkontrolujte ji a proveďte podle potřeby upravte. Typické úpravu je nahradit vstupní datové sady nebo dotaz s jedním, který vyloučí pole štítek, jak tyto nebudete mít k dispozici při volání služby. Je také vhodné zmenšit velikost vstupní datové sady a/nebo dotaz na několik záznamů, akorát, aby znamenat vstupní schéma. Na výstupní port je společné pro všechny vstupní pole vyloučit a obsahovat jenom **popisky vyhodnocení** a **skóre pro Magnitudu Pravděpodobnostech** ve výstupu pomocí [výběr sloupců v datové sadě] [ select-columns] modulu.

Ukázka vyhodnocování experimentu najdete v níže uvedeném obrázku. Až bude připravený k nasazení, klikněte na tlačítko **publikování webové služby** tlačítko na dolním panelu akcí.

![Publikování Azure ML][11]

## <a name="summary"></a>Souhrn
K recap, co jsme provedli v tomto kurzu návod, jste vytvořili prostředí vědecké účely dat Azure, práce s velké veřejné datové sady, trvá prostřednictvím Team Data vědecké účely procesu úplně z získávání dat na školení model a pak na nasazení webové služby Azure Machine Learning.

### <a name="license-information"></a>Informace o licenci
Tento návod ukázka a jeho doplňujícími skripty a IPython notebook(s) sdílí Microsoft v rámci licencí MIT. Zkontrolujte prosím soubor LICENSE.txt v adresáři ukázkový kód na Githubu další podrobnosti.

## <a name="references"></a>Odkazy
• [Stránce pro stažení Andrés Monroy NYC taxíkem cest](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC Taxi cestě dat podle Whong Jan](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC taxíkem a Limousine Komise výzkum a statistiky](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

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
