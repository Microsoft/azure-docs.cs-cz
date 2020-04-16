---
title: Vytvoření a nasazení modelu pomocí Azure Synapse Analytics – proces vědecké analýzy týmových dat
description: Vytvářejte a nasazujte model strojového učení pomocí Azure Synapse Analytics s veřejně dostupnou datovou sadou.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9c4c1cfdb927cfd2ee607bfe2a951e06c80f9bfb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418537"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>Proces vědecké ho procesu týmových dat v akci: pomocí Azure Synapse Analytics
V tomto kurzu vás provedeme vytvářením a nasazováním modelu strojového učení pomocí Azure Synapse Analytics pro veřejně dostupnou datovou sadu – datovou sadu [NYC Taxi Trips.](https://www.andresmh.com/nyctaxitrips/) Binární klasifikační model vytvořený předpovídá, zda je tip placen za cestu.  Modely zahrnují klasifikaci více tříd (bez ohledu na to, zda je špička) a regrese (rozdělení zaplacených částek tipu).

Postup se řídí pracovním postupem Proces vědecké ho řízení [týmových dat (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) Ukážeme si, jak nastavit prostředí datové vědy, jak načíst data do Azure Synapse Analytics a jak používat Azure Synapse Analytics nebo Poznámkový blok IPython k prozkoumání funkcí dat a inženýrů pro modelování. Pak ukážeme, jak sestavit a nasadit model pomocí Azure Machine Learning.

## <a name="the-nyc-taxi-trips-dataset"></a><a name="dataset"></a>Datový soubor NYC Taxi Trips
NYC Taxi Trip data se skládá z asi 20 GB komprimovaných souborů CSV (~ 48 GB nekomprimované), záznam více než 173 milionů jednotlivých cest a tarify zaplacené za každou cestu. Každý záznam cesty obsahuje místa a časy vyzvednutí a předání, anonymizované číslo licence hacku (řidiče) a číslo medailonu (jedinečné ID taxislužby). Údaje se týkají všech cest v roce 2013 a jsou uvedeny v následujících dvou datových souborech za každý měsíc:

1. Soubor **trip_data.csv** obsahuje podrobnosti o cestě, jako je počet cestujících, místa vyzvednutí a předání, doba trvání cesty a délka cesty. Zde je několik ukázkových záznamů:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Soubor **trip_fare.csv** obsahuje podrobnosti o jízdném zaplacené za každou cestu, jako je typ platby, částka jízdného, příplatek a daně, tipy a mýtné a celková zaplacená částka. Zde je několik ukázkových záznamů:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

**Jedinečný klíč** používaný ke\_spojení\_údajů o cestě a jízdného se skládá z následujících tří polí:

* Medailon
* hack\_licence a
* datum\_vyzvednutí.

## <a name="address-three-types-of-prediction-tasks"></a><a name="mltasks"></a>Adresování tří typů úloh předpovědi
Formulujeme tři predikční problémy na základě *výše tip\_* uznázorněna tři druhy modelování úkolů:

1. **Binární klasifikace**: Chcete-li předpovědět, zda tip byl zaplacen za cestu, to znamená, že *\_tip částka,* která je větší než $0 je pozitivní příklad, zatímco *tip\_částka* 0 kč je negativní příklad.
2. **Klasifikace více tříd**: Předpovědět rozsah tipu placeného za cestu. *Množství spropitného\_* rozdělíme do pěti přihrádek nebo tříd:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Regresní úkol**: Předpovědět množství tipu zaplacené za cestu.

## <a name="set-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Nastavení prostředí Azure pro datové vědy pro pokročilé analýzy
Pokud chcete nastavit prostředí Azure Data Science, postupujte takto.

**Vytvořte si vlastní účet úložiště objektů blob Azure**

* Když zřídíte vlastní úložiště objektů blob Azure, zvolte geografické umístění pro úložiště objektů blob Azure v usa nebo co nejblíže k **jižnímu centrálnímu USA**, což je místo, kde se ukládají data NYC Taxi. Data se zkopírují pomocí AzCopy z kontejneru úložiště veřejných objektů blob do kontejneru ve vašem vlastním účtu úložiště. Čím blíže je vaše úložiště objektů blob Azure k jižnímu centrálnímu USA, tím rychleji se tento úkol (krok 4) dokončí.
* Pokud si chcete vytvořit vlastní účet Azure Storage, postupujte podle pokynů uvedených na webu [O účtech Azure Storage](../../storage/common/storage-create-storage-account.md). Nezapomeňte si udělat poznámky o hodnotách pro následující přihlašovací údaje účtu úložiště, protože budou potřeba později v tomto návodu.

  * **Název účtu úložiště**
  * **Klíč účtu úložiště**
  * **Název kontejneru** (které chcete, aby se data ukládala v úložišti objektů blob Azure)

**Zřídit svou instanci Azure Synapse Analytics.**
Postupujte podle dokumentace na [vytvoření a dotaz na Azure SQL Data Warehouse na webu Azure portal](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) pro zřízení instance Azure Synapse Analytics. Ujistěte se, že provedete zápisy na následující přihlašovací údaje Azure Synapse Analytics, které se použijí v pozdějších krocích.

* **Název**serveru \<: název serveru>.database.windows.net
* **SQLDW (databáze) Název**
* **Username**
* **Heslo**

**Nainstalujte nástroje Visual Studio a SQL Server Data Tools.** Pokyny najdete [v tématu Začínáme s Visual Studio 2019 pro SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Připojte se k Azure Synapse Analytics pomocí Sady Visual Studio.** Pokyny najdete v článku kroky 1 & 2 v [tématu Připojení k SQL Analytics v Azure Synapse Analytics](../../synapse-analytics/sql/connect-overview.md).

> [!NOTE]
> Spusťte následující dotaz SQL v databázi, kterou jste vytvořili ve službě Azure Synapse Analytics (namísto dotazu uvedeného v kroku 3 tématu připojení), a **vytvořte hlavní klíč**.
>
>

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Vytvořte pracovní prostor Azure Machine Learning v rámci předplatného Azure.** Pokyny najdete [v tématu Vytvoření pracovního prostoru Azure Machine Learning](../studio/create-workspace.md).

## <a name="load-the-data-into-azure-synapse-analytics"></a><a name="getdata"></a>Načtení dat do Azure Synapse Analytics
Otevřete konzoli příkazů prostředí Windows PowerShell. Spusťte následující příkazy Prostředí PowerShell a stáhněte si ukázkové soubory skriptů SQL, které s vámi sdílíme na GitHubu, do místního adresáře, který zadáte s parametrem *-DestDir*. Hodnotu parametru *-DestDir* můžete změnit na libovolný místní adresář. Pokud *-DestDir* neexistuje, bude vytvořen skriptem prostředí PowerShell.

> [!NOTE]
> Pokud adresář *DestDir* potřebuje k vytvoření nebo zápisu oprávnění správce, bude pravděpodobně nutné **spustit jako správce** při provádění následujícího skriptu prostředí PowerShell.
>
>

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Po úspěšném spuštění se aktuální pracovní adresář změní na *-DestDir*. Měli byste být schopni vidět obrazovku jako níže:

![Aktuální změny pracovního adresáře][19]

Ve vašem *-DestDir*spusťte v režimu správce následující skript prostředí PowerShell:

    ./SQLDW_Data_Import.ps1

Při prvním spuštění skriptu Prostředí PowerShell budete vyzváni k zadání informací z Azure Synapse Analytics a vašeho účtu úložiště objektů blob Azure. Po dokončení tohoto skriptu prostředí PowerShell je poprvé spuštěno, zadávaná pověření budou zapsána do konfiguračního souboru SQLDW.conf v současném pracovním adresáři. Budoucí spuštění tohoto souboru skriptu prostředí PowerShell má možnost číst všechny potřebné parametry z tohoto konfiguračního souboru. Pokud potřebujete změnit některé parametry, můžete po výzvě zadat parametry na obrazovce odstraněním tohoto konfiguračního souboru a zadáním hodnot parametrů tak, jak byly vyzvány, nebo změnit hodnoty parametrů úpravou souboru SQLDW.conf v adresáři *-DestDir.*

> [!NOTE]
> Aby se zabránilo konfliktu názvu schématu s názvy, které již existují ve vašem Azure Azure Synapse Analytics, při čtení parametrů přímo ze souboru SQLDW.conf, 3 místné náhodné číslo je přidán do názvu schématu ze souboru SQLDW.conf jako výchozí název schématu pro každé spuštění. Skript prostředí PowerShell vás může vyzvat k zadání názvu schématu: název může být zadán podle uvážení uživatele.
>
>

Tento soubor **skriptu prostředí PowerShell** dokončí následující úkoly:

* **Stáhne a nainstaluje AzCopy**, pokud AzCopy ještě není nainstalován

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
* **Zkopíruje data do vašeho účtu úložiště objektů blob** z veřejného objektu blob s AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* **Načte data pomocí Polybase (spuštěním LoadDataToSQLDW.sql) do azure synapse analytics** z vašeho účtu úložiště privátní ho objektů blob pomocí následujících příkazů.

  * Vytvoření schématu

          EXEC (''CREATE SCHEMA {schemaname};'');
  * Vytvoření pověření s vymezeným oborem databáze

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
  * Vytvořte externí formát souboru pro soubor CSV. Data jsou nekomprimovaná a pole jsou oddělena znakem potrubí.

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
  * Vytvořte externí tarify a tabulky výletů pro datové sady taxi NYC v úložišti objektů blob Azure.

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

    - Načítání dat z externích tabulek v úložišti objektů blob Azure do Azure Synapse Analytics

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

    - Vytvořte ukázkovou tabulku dat (NYCTaxi_Sample) a vložte do ní data z výběru dotazů SQL v tabulkách cesty a tarifů. (Některé kroky tohoto návodu je třeba použít tuto ukázkovou tabulku.)

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

Geografická poloha účtů úložiště ovlivňuje dobu načítání.

> [!NOTE]
> V závislosti na geografické poloze vašeho účtu úložiště soukromých objektů blob může proces kopírování dat z veřejného objektu blob do vašeho účtu soukromého úložiště trvat přibližně 15 minut nebo dokonce déle a proces načítání dat z vašeho účtu úložiště do azure Azure Synapse Analytics může trvat 20 minut nebo déle.
>
>

Budete se muset rozhodnout, co dělat, pokud máte duplicitní zdrojové a cílové soubory.

> [!NOTE]
> Pokud soubory .csv, které mají být zkopírovány z veřejného úložiště objektů blob do vašeho účtu úložiště objektů blob, již existují ve vašem účtu úložiště objektů blob, AzCopy se vás zeptá, zda je chcete přepsat. Pokud nechcete přepsat, zadejte **n** po zobrazení výzvy. Pokud chcete přepsat **všechny** z nich, zadejte **po** zobrazení výzvy. Můžete také zadat **y** přepsat .csv soubory jednotlivě.
>
>

![Výstup z AzCopy][21]

Můžete použít vlastní data. Pokud jsou vaše data v místním počítači ve vaší aplikaci v reálném životě, můžete stále používat AzCopy k nahrání místních dat do vašeho privátního úložiště objektů blob Azure. Stačí změnit umístění **Zdroj,** `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, v příkazu AzCopy souboru skriptu Prostředí PowerShell na místní adresář, který obsahuje vaše data.

> [!TIP]
> Pokud jsou vaše data už ve vašem soukromém úložišti objektů blob Azure ve vaší skutečné aplikaci, můžete krok AzCopy ve skriptu PowerShellpřeskočit a přímo nahrát data do Azure Azure Synapse Analytics. To bude vyžadovat další úpravy skriptu přizpůsobit formát dat.
>
>

Tento skript Prostředí PowerShell také připojí informace O Azure Synapse Analytics do ukázkových souborů průzkumu dat SQLDW_Explorations.sql, SQLDW_Explorations.ipynb a SQLDW_Explorations_Scripts.py, aby tyto tři soubory byly připraveny k okamžitému vyzkoušení po dokončení skriptu Prostředí PowerShell.

Po úspěšném provedení uvidíte obrazovku jako níže:

![Výstup úspěšného spuštění skriptu][20]

## <a name="data-exploration-and-feature-engineering-in-azure-synapse-analytics"></a><a name="dbexplore"></a>Zkoumání dat a technické funkce v Azure Synapse Analytics
V této části provádíme zkoumání dat a generování funkcí spuštěním dotazů SQL proti Azure Synapse Analytics přímo pomocí **nástrojů Visual Studio Data Tools**. Všechny dotazy SQL použité v této části naleznete v ukázkovém skriptu s názvem *SQLDW_Explorations.sql*. Tento soubor již byl skriptem prostředí PowerShell stažen do místního adresáře. Můžete také načíst z [GitHubu](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Ale soubor v GitHubu nemá připojené informace Azure Synapse Analytics.

Připojte se k Azure Synapse Analytics pomocí Visual Studia s přihlašovacím jménem a heslem Azure Synapse Analytics a otevřete **PRŮZKUMNÍK SQL Object Explorer** a potvrďte, že databáze a tabulky byly importovány. Načíst soubor *SQLDW_Explorations.sql.*

> [!NOTE]
> Chcete-li otevřít editor dotazů paralelního datového skladu (PDW), použijte příkaz **Nový dotaz,** když je pdw vybrán v **Průzkumníku objektů SQL**. Standardní editor dotazů SQL není podporován PDW.
>
>

Zde jsou typy úloh průzkumu dat a vytváření funkcí prováděných v této části:

* Prozkoumejte rozdělení dat několika polí v různých časových oknech.
* Prozkoumejte kvalitu dat polí zeměpisné délky a zeměpisné šířky.
* Generovat binární a vícetřídní klasifikační popisky na základě **částky špičky\_**.
* Vytvářejte funkce a vypočítejte/porovnejte vzdálenosti jízdy.
* Připojte se ke dvěma tabulkám a extrahujte náhodný vzorek, který bude použit k sestavení modelů.

### <a name="data-import-verification"></a>Ověření importu dat
Tyto dotazy poskytují rychlé ověření počtu řádků a sloupců v tabulkách naplněných dříve pomocí paralelního hromadného importu polybase,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Výstup:** Měli byste získat 173,179,759 řádků a 14 sloupců.

### <a name="exploration-trip-distribution-by-medallion"></a>Průzkum: Rozdělení cesty medailonem
Tento příklad dotazu identifikuje medailonky (čísla taxi), které dokončily více než 100 cest v zadaném časovém období. Dotaz by měl prospěch z přístupu k rozdělené tabulce, protože je podmíněn systémem oddílu **datatime vyzvednutí\_**. Dotazování úplné datové sady bude také využívat rozdělené tabulky nebo index prohledávání.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Výstup:** Dotaz by měl vrátit tabulku s řádky určujícími 13 369 medailonků (taxi) a počet cest dokončených v roce 2013. Poslední sloupec obsahuje počet dokončených cest.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Průzkum: Rozdělení výletů medailonem a hack_license
Tento příklad identifikuje medailonky (čísla taxi) a hack_license čísla (řidiči), které během zadaného časového období dokončily více než 100 cest.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Výstup:** Dotaz by měl vrátit tabulku s 13 369 řádky určujícími 13 369 ID vozidla/ovladače, které v roce 2013 dokončily více než 100 cest. Poslední sloupec obsahuje počet dokončených cest.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Posouzení kvality údajů: Ověření záznamů s nesprávnou zeměpisnou délkou a šířkou
Tento příklad zkoumá, zda některé z polí zeměpisné délky nebo šířky obsahují neplatnou hodnotu (radiánní stupně by měly být mezi -90 a 90) nebo mají souřadnice (0, 0).

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Výstup:** Dotaz vrátí 837 467 cest, které mají neplatná pole zeměpisné délky nebo šířky.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Průzkum: Šikmá vs. nešiponé rozdělení cest
Tento příklad vyhledá počet cest, které byly vychýleny vs. číslo, které nebyly vychýlena v zadaném časovém období (nebo v úplné datové sadě, pokud pokrývá celý rok, jak je zde nastaven). Toto rozdělení odráží binární distribuce popisků, které mají být později použity pro modelování binární klasifikace.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Výstup:** Dotaz by měl vrátit následující frekvence tip pro rok 2013: 90,447,622 sklápěcí a 82,264,709 bez hrotu.

### <a name="exploration-tip-classrange-distribution"></a>Průzkum: Rozložení třídy tipu/rozsahu
Tento příklad vypočítá rozdělení rozsahů špiček v daném časovém období (nebo v úplné datové sadě, pokud pokrývá celý rok). Toto rozdělení tříd popisků bude později použito pro modelování klasifikace více tříd.

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

### <a name="exploration-compute-and-compare-trip-distance"></a>Průzkum: Výpočet a porovnání vzdálenosti jízdy
Tento příklad převede délku a zeměpisnou šířku vyzvednutí a šířky na body geografie SQL, vypočítá vzdálenost cesty pomocí rozdílu bodů zeměpisné polohy SQL a vrátí náhodný vzorek výsledků pro porovnání. Příklad omezuje výsledky na platné souřadnice pouze pomocí dotazu hodnocení kvality dat, který byl popsán dříve.

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

### <a name="feature-engineering-using-sql-functions"></a>Inženýrské funkce pomocí funkcí SQL
Někdy funkce SQL může být efektivní volbou pro funkce inženýrství. V tomto návodu jsme definovali funkci SQL pro výpočet přímé vzdálenosti mezi místy vyzvednutí a odtažení. V **nástrojích Visual Studio Data Tools**můžete spustit následující skripty SQL .

Zde je skript SQL, který definuje funkci vzdálenosti.

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

Zde je příklad volání této funkce pro generování funkcí v dotazu SQL:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Výstup:** Tento dotaz generuje tabulku (s 2,803,538 řádky) s vyzvednutí a dropoff zeměpisné šířky a délky a odpovídající přímé vzdálenosti v mílích. Zde jsou výsledky pro první tři řádky:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | Vzdálenost přímého směru |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Příprava dat pro vytváření modelů
Následující dotaz spojuje **nyctaxi\_výlet** a **\_nyctaxi tarif** tabulky, generuje binární klasifikace popisek **hrot**, více třídy klasifikace popisek **\_tip třídy**a extrahuje vzorek z úplné spojené datové sady. Vzorkování se provádí načtením podmnožiny cest na základě času vyzvednutí.  Tento dotaz lze zkopírovat a pak vložit přímo do [Azure Machine Learning Studio (klasické)](https://studio.azureml.net) Import [dat][importu dat importu dat] modul pro přímé ingestování dat z instance databáze SQL v Azure. Dotaz vyloučí záznamy s nesprávnými souřadnicemi (0, 0).

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

Až budete připraveni přejít na Azure Machine Learning, můžete buď:

1. Uložte konečný dotaz SQL k extrahování a vzorkování dat a zkopírujte a vložte dotaz přímo do importovaného[datového] modulu v Azure Machine Learning nebo
2. Zachovávejte ukázková a navržená data, která chcete použít pro vytváření modelů v nové tabulce Azure Synapse Analytics, a použijte novou tabulku v modulu [Import dat][importu dat] v Azure Machine Learning. Skript prostředí PowerShell v předchozím kroku provedl tuto úlohu za vás. Můžete číst přímo z této tabulky v modulu Importovat data.

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Zkoumání dat a technické funkce v poznámkovém bloku IPython
V této části budeme provádět zkoumání dat a generování funkcí pomocí Pythonu a SQL dotazy proti Azure Synapse Analytics vytvořené dříve. Do místního adresáře byl stažen ukázkový poznámkový blok IPython s názvem **SQLDW_Explorations.ipynb** a soubor skriptu Pythonu **SQLDW_Explorations_Scripts.py.** Jsou také k dispozici na [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Tyto dva soubory jsou identické ve skriptech Pythonu. Soubor skriptu Pythonu je k dispozici v případě, že nemáte server IPython Notebook. Tyto dva ukázkové soubory Pythonu jsou navrženy pod **Python 2.7**.

Potřebné informace Azure Synapse Analytics v ukázkovém poznámkovém bloku IPython a souboru skriptu Pythonu stažené do místního počítače byly dříve připojeny skriptem PowerShell. Jsou spustitelné bez jakýchkoli úprav.

Pokud jste už nastavili pracovní prostor Azure Machine Learning, můžete přímo nahrát ukázkový poznámkový blok IPython do služby Poznámkový blok AzureML IPython a spustit ho. Tady jsou kroky, jak nahrát do služby Poznámkový blok AzureML IPython:

1. Přihlaste se do pracovního prostoru Azure Machine Learning, klikněte nahoře na **Studio a** klikněte na **POZNÁMKOVÉ BLOKY** na levé straně webové stránky.

    ![Klikněte na Studio a pak na POZNÁMKY.][22]
2. Klikněte na **NOVÝ** v levém dolním rohu webové stránky a vyberte **Python 2**. Potom zadejte název poznámkového bloku a kliknutím zaškrtněte a vytvořte nový prázdný poznámkový blok IPython.

    ![Klikněte na NOVÝ a vyberte Python 2][23]
3. Klikněte na symbol **Jupyter** v levém horním rohu nového poznámkového bloku IPython.

    ![Klikněte na symbol Jupyter.][24]
4. Přetáhněte ukázkový poznámkový blok IPython na **stromovou** stránku služby Poznámkový blok AzureML IPython a klikněte na **Nahrát**. Potom ukázkový poznámkový blok IPython se nahraje do služby Poznámkový blok AzureML IPython.

    ![Klikněte na Nahrát][25]

Aby bylo možné spustit ukázkový poznámkový blok IPython nebo soubor skriptu Pythonu, jsou potřeba následující balíčky Pythonu. Pokud používáte službu Poznámkový blok AzureML IPython, byly tyto balíčky předinstalovány.

- Pandy
- numpy
- matplotlib
- pyodbc řekl:
- PyTables

Při vytváření pokročilých analytických řešení v Azure Machine Learning s velkými daty, tady je doporučená sekvence:

* Čtení v malém vzorku dat do datového rámce v paměti.
* Pomocí navzorkovaných dat provádějte některé vizualizace a průzkumy.
* Experimentujte s návrhem funkcí pomocí vzorkovaných dat.
* Pro větší zkoumání dat, manipulaci s daty a technické funkce, použijte Python k vydávání dotazů SQL přímo proti Azure Synapse Analytics.
* Rozhodněte velikost ukázky, která má být vhodná pro sestavení modelu Azure Machine Learning.

Následuje několik příkladů zkoumání dat, vizualizace dat a technické funkce. Další zkoumání dat najdete v ukázkovém poznámkovém bloku IPython a v ukázkovém souboru skriptu Pythonu.

### <a name="initialize-database-credentials"></a>Inicializovat pověření databáze
Inicializovat nastavení připojení k databázi v následujících proměnných:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Vytvořit připojení k databázi
Zde je připojovací řetězec, který vytvoří připojení k databázi.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Počet řádků a sloupců v tabulce <nyctaxi_trip>
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

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>Počet řádků a sloupců v tabulce <nyctaxi_fare>
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

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Čtení malé ukázky dat z azure synapse analytics databáze
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

Čas pro čtení ukázkové tabulky je 14.096495 sekund.
Počet načtených řádků a sloupců = (1000, 21).

### <a name="descriptive-statistics"></a>Popisné statistiky
Nyní jste připraveni prozkoumat vzorkovaná data. Začneme s pohledem na některé popisné statistiky pro **vzdálenost cesty\_** (nebo jakákoli jiná pole, která se rozhodnete zadat).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Vizualizace: Příklad vykreslení rámečku
Dále se podíváme na pole pozemku pro vzdálenost cesty vizualizovat kvantily.

    df1.boxplot(column='trip_distance',return_type='dict')

![Výstup vykreslení rámečku][1]

### <a name="visualization-distribution-plot-example"></a>Vizualizace: Příklad distribučníparcely
Vykreslí, které vizualizují rozdělení a histogram pro vzorkované vzdálenosti cesty.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Výstup distribučního vykreslení][2]

### <a name="visualization-bar-and-line-plots"></a>Vizualizace: Parcely pruhů a čar
V tomto příkladu jsme bin vzdálenost cesty do pěti přihrádek a vizualizovat binning výsledky.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Výše uvedenou distribuci přihrádky můžeme vykreslit v pruhu nebo v řádkovém obrázku s:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Výstup vykreslování pruhů][3]

a

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Výstup vykreslování čáry][4]

### <a name="visualization-scatterplot-examples"></a>Vizualizace: Příklady bodových plotů
Ukazujeme bodový graf mezi **dobou\_\_cesty\_v sekundách** a **vzdáleností,\_** abychom zjistili, zda existuje nějaká korelace

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Bodový graf výstupu vztahu mezi časem a vzdáleností][6]

Podobně můžeme zkontrolovat vztah mezi **kódem sazby\_** a **vzdáleností cesty\_**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Bodový graf výstupu vztahu mezi kódem a vzdáleností][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Zkoumání dat na vzorkovaných datech pomocí dotazů SQL v poznámkovém bloku IPython
V této části zkoumáme distribuce dat pomocí vzorkovaných dat, která jsou trvalá v nové tabulce, kterou jsme vytvořili výše. Podobné průzkumy lze provádět pomocí původních tabulek.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Průzkum: Sestava počtu řádků a sloupců v vzorované tabulce
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Průzkum: Šikmé/nezakopl Distribuce
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Průzkum: Distribuce třídy tip
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Průzkum: Vykreslení rozložení špičky podle třídy
    tip_class_dist['tip_freq'].plot(kind='bar')

![#26 vykreslování][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Průzkum: Denní distribuce výletů
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Průzkum: Rozdělení cesty na medailon
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Průzkum: Výlet distribuce medailon emedilion a hack licence
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Průzkum: Rozložení doby cesty
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Průzkum: Rozdělení vzdálenosti cesty
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Průzkum: Distribuce typu platby
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Ověření konečné podoby tabulky featurized
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="build-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Vytváření modelů v Azure Machine Learning
Teď jsme připraveni pokračovat v modelování vytváření a nasazení modelu v [Azure Machine Learning](https://studio.azureml.net). Data jsou připravena k použití v některém z predikčních problémů zjištěných dříve, a to:

1. **Binární klasifikace**: Chcete-li předpovědět, zda byl za cestu zaplacen tip.
2. **Klasifikace více tříd**: Předpovědět rozsah placeného tipu podle dříve definovaných tříd.
3. **Regresní úkol**: Předpovědět množství tipu zaplacené za cestu.

Chcete-li zahájit modelování cvičení, přihlaste se do pracovního prostoru **Azure Machine Learning (klasické).** Pokud jste ještě nevytvořili pracovní prostor strojového učení, přečtěte si informace [o vytvoření pracovního prostoru Azure Machine Learning Studio (klasické).](../studio/create-workspace.md)

1. Pokud chcete začít s Azure Machine Learning, přečtěte [si, co je Azure Machine Learning Studio (klasické)?](../studio/what-is-ml-studio.md)
2. Přihlaste se do [Azure Machine Learning Studio (klasické)](https://studio.azureml.net).
3. Domovská stránka Machine Learning Studio (klasická) poskytuje nepřeberné množství informací, videí, kurzů, odkazů na odkaz na moduly a dalších zdrojů. Další informace o Azure Machine Learning najdete v [tématu Azure Machine Learning Documentation Center](https://azure.microsoft.com/documentation/services/machine-learning/).

Typický tréninkový experiment se skládá z následujících kroků:

1. Vytvořte experiment **+NEW.**
2. Získejte data do Azure Machine Learning Studio (klasické).
3. Předzpracováním, transformací a manipulací s daty podle potřeby.
4. Podle potřeby vygenerujte funkce.
5. Rozdělte data do trénovacích/validačních/testovacích datových sad (nebo mají samostatné datové sady pro každou z nich).
6. Vyberte jeden nebo více algoritmů strojového učení v závislosti na problému učení, který chcete vyřešit. Například binární klasifikace, klasifikace více tříd, regrese.
7. Trénování jednoho nebo více modelů pomocí trénovací datové sady.
8. Skóre ověřovací datové sady pomocí trénovaného modelu (y).
9. Vyhodnoťte model (y) pro výpočet příslušné metriky pro problém učení.
10. Nalaďte model (y) a vyberte nejlepší model k nasazení.

V tomto cvičení jsme již prozkoumali a navrhli data v Azure Synapse Analytics a rozhodli o velikosti ukázky ingestovat v Azure Machine Learning Studio (klasické). Zde je postup pro sestavení jednoho nebo více modelů předpovědi:

1. Získejte data do Azure Machine Learning Studio (klasické) pomocí modulu [Import dat importu][dat,] který je k dispozici v části **Vstup dat a výstup.** Další informace naleznete v referenční stránce modulu [Import dat][importu dat.]

    ![Data importu Azure ML][17]
2. V panelu **Vlastnosti** vyberte **Azure SQL Database** jako zdroj **dat.**
3. Do pole **název databázového serveru** zadejte název DNS databáze. Formát:`tcp:<your_virtual_machine_DNS_name>,1433`
4. Do odpovídajícího pole zadejte **název databáze.**
5. Zadejte *uživatelské jméno SQL* do názvu **uživatelského účtu serveru**a *heslo* do **hesla uživatelského účtu serveru**.
7. V oblasti úprav textu **databázového dotazu** vložte dotaz, který extrahuje potřebná databázová pole (včetně všech vypočítaných polí, jako jsou popisky), a dolů ukažte data na požadovanou velikost vzorku.

Příklad experimentu binární klasifikace čtení dat přímo z databáze Azure Synapse Analytics je na obrázku níže (nezapomeňte nahradit názvy tabulek nyctaxi_trip a nyctaxi_fare názvem schématu a názvy tabulek, které jste použili v návodu). Podobné experimenty lze sestavit pro vícetřídní klasifikace a regresní problémy.

![Vlak Azure ML][10]

> [!IMPORTANT]
> V příkladech extrakce dat modelování a vzorkování dotazu uvedených v předchozích částech **jsou do dotazu zahrnuty všechny popisky pro tři modelování**cvičení . Důležitým (povinným) krokem v každém z modelovacích cvičení je **vyloučit** zbytečné popisky pro další dva problémy a jakékoli další **úniky cílů**. Například při použití binární klasifikace použijte popisek **s hrotem** a vylučte **\_třídu tip**polí , **částku\_špičky**a **celkovou\_částku**. Ty jsou cílové úniky, protože znamenají tip placené.
>
> Chcete-li vyloučit nepotřebné sloupce nebo nevracení cílů, můžete použít [modul Vybrat sloupce v datové sadě][select-columns] nebo Upravit [metadata][edit-metadata]. Další informace najdete [v tématech Výběr sloupců v datových sadách][select-columns] a [Úpravy referenčních][edit-metadata] stránek metadat.
>
>

## <a name="deploy-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Nasazení modelů v Azure Machine Learning
Když je váš model připraven, můžete jej snadno nasadit jako webovou službu přímo z experimentu. Další informace o nasazení webových služeb Azure ML najdete [v tématu Nasazení webové služby Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).

Chcete-li nasadit novou webovou službu, je třeba:

1. Vytvořte experiment hodnocení.
2. Nasaďte webovou službu.

Chcete-li vytvořit experiment hodnocení z **experimentu dokončeného** tréninku, klepněte na tlačítko **VYTVOŘIT EXPERIMENT Bodování** v dolním panelu akcí.

![Azure vyhodnocování][18]

Azure Machine Learning se pokusí vytvořit vyhodnocovací experiment založený na součástech trénovacího experimentu. Zejména bude:

1. Uložte trénovaný model a odeberte tréninkové moduly modelu.
2. Identifikujte logický **vstupní port** představující schéma očekávaných vstupních dat.
3. Identifikujte logický **výstupní port** představující očekávané schéma výstupu webové služby.

Po vytvoření experimentu bodování zkontrolujte výsledky a podle potřeby proveďte úpravy. Typická úprava je nahradit vstupní datovou sadu nebo dotaz, který vylučuje pole popisků, protože tato pole popisků nebudou při volání služby namapována na schéma. Je také vhodné zmenšit velikost vstupní datové sady nebo dotazu na několik záznamů, stačí k označení vstupního schématu. Pro výstupní port je běžné vyloučit všechna vstupní pole a zahrnout do výstupu pouze **scoredikované popisky** a **pravděpodobnosti s kinem** pomocí modulu [Vybrat sloupce v datové sadě.][select-columns]

Ukázkový bodovací experiment je uveden na obrázku níže. Až budete připraveni k nasazení, klikněte na tlačítko **Publikovat webovou službu** v dolním panelu akcí.

![Publikování Azure ML][11]

## <a name="summary"></a>Souhrn
Chcete-li shrnout, co jsme udělali v tomto kurzu návodu, vytvořili jste prostředí Azure pro datové vědy, pracovali s rozsáhlou veřejnou datovou sadou, provedli ji procesem vědecké správy týmových dat, od získávání dat až po školení modelu a pak nasazení webové služby Azure Machine Learning.

### <a name="license-information"></a>Informace o licenci
Tento ukázkový návod a jeho doprovodné skripty a poznámkové bloky IPython jsou sdíleny společností Microsoft pod licencí MIT. Další podrobnosti najdete v souboru LICENSE.txt v adresáři ukázkového kódu na GitHubu.

## <a name="references"></a>Odkazy
- [Andrés Monroy NYC Taxi Výlety Ke stažení Page](https://www.andresmh.com/nyctaxitrips/)
- [FOILing NYC taxi výlet data Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [NYC Taxi a limuzína Komise výzkum a statistiky](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

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
