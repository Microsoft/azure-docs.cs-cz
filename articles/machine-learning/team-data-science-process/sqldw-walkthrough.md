---
title: Sestavování a nasazování modelu pomocí Azure synapse Analytics – vědecké zpracování týmových dat
description: Sestavte a nasaďte model strojového učení s využitím Azure synapse Analytics s veřejně dostupnou datovou sadou.
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
ms.openlocfilehash: e64b951a8bb96b25a6ef917b4cebe077d6dd6657
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718442"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>Vědecké zpracování týmových dat v akci: používání Azure synapse Analytics
V tomto kurzu Vás provedeme vytvořením a nasazením modelu strojového učení s využitím Azure synapse Analytics pro veřejně dostupnou datovou sadu, která je datovou sadou [NYC taxislužby TRIPS](https://www.andresmh.com/nyctaxitrips/) . Model binární klasifikace vytváří předpověď bez ohledu na to, jestli je pro cestu placené nebo ne.  Mezi modely patří klasifikace s více třídami (bez ohledu na to, zda existuje Tip) a regrese (rozdělení pro placené částky Tip).

Postup následuje po pracovním postupu [TDSP (Team data vědecký proces)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) . Ukážeme, jak nastavit prostředí pro datové vědy, jak načíst data do služby Azure synapse Analytics a jak pomocí Azure synapse Analytics nebo IPython poznámkového bloku prozkoumat funkce dat a inženýrů modelu. Potom ukážeme, jak sestavit a nasadit model s využitím Azure Machine Learning.

## <a name="dataset"></a>Datová sada cest taxislužby pro NYC
Data o jízdách taxislužby NYC se skládá z přibližně 20 GB komprimované soubory CSV (nekomprimovaný ~ 48 GB), záznam 173 milionů jednotlivé trips a tarify placené pro každou cestu. Každý záznam o jízdách zahrnuje sbírat míčky a dropoff umístění a čas, číslo řidičského anonymizované hack (ovladače) a číslo Medailon (jedinečné ID taxislužby.). Data v roce 2013 zahrnuje všechny cesty a je dostupné pro každý měsíc následující dvě datové sady:

1. Soubor **trip_data. csv** obsahuje podrobnosti o cestě, jako je třeba počet cestujících, vyzvednutí a dropoff body, doba trvání cesty a délka cesty. Tady je několik ukázkových záznamů:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Soubor **trip_fare. csv** obsahuje podrobnosti o tarifu placeného za každou cestu, jako je typ platby, částka tarifů, příplatek a daně, tipy a mýtné a celková placená částka. Tady je několik ukázkových záznamů:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

**Jedinečný klíč** , který se používá pro připojení cesty\_data a cesty\_jízdné, se skládá z následujících tří polí:

* medailonu,
* \_licence pro napadení a
* vyzvednutí\_data a času.

## <a name="mltasks"></a>Adresovat tři typy úloh předpovědi
Formuluje tři problémy předpovědi na základě *tipů\_množství* , které ilustrují tři druhy úloh modelování:

1. **Binární klasifikace**: Chcete-li předpovědět, zda byla pro cestu vyplacena hodnota tipu, to znamená, že *Tip\_množství* větší než $0 je kladným příkladem, zatímco *Tip\_hodnota* $0 je negativním příkladem.
2. **Klasifikace více tříd**: pro předpověď rozsahu tipu placeného pro danou cestu. *\_velikost pro Tip* se rozdělí na pět přihrádek nebo tříd:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Regresní úloha**: pro předpověď množství tipu placeného pro cestu.

## <a name="setup"></a>Nastavení prostředí pro Azure Data vědu pro pokročilé analýzy
Nastavení prostředí Azure pro datové vědy, postupujte podle těchto kroků.

**Vytvoření vlastního účtu úložiště Azure Blob**

* Když zřizujete vlastní úložiště objektů BLOB v Azure, vyberte geografickou polohu pro úložiště objektů BLOB v Azure v nebo co nejblíže **střed USA – jih**, což je místo, kde se ukládají data taxislužby NYC. Data se zkopírují pomocí AzCopy z veřejného kontejneru objektů blob storage do kontejneru v účtu úložiště. Čím blíž je službě Azure blob storage na střed USA – Jih, tím rychleji dokončení tohoto úkolu (krok 4).
* Pokud chcete vytvořit vlastní účet Azure Storage, postupujte podle kroků uvedených v části [informace o Azure Storagech účtech](../../storage/common/storage-create-storage-account.md). Nezapomeňte si dělat poznámky na hodnoty pro následující přihlašovací údaje účtu úložiště, jako je budou potřebovat později v tomto názorném postupu.

  * **Název účtu úložiště**
  * **Klíč účtu úložiště**
  * **Název kontejneru** (který má ukládat data do úložiště objektů BLOB v Azure)

**Zřiďte svou instanci Azure synapse Analytics.**
Pokud chcete zřídit instanci Azure synapse Analytics, postupujte podle dokumentace v části [Vytvoření a dotazování Azure SQL Data Warehouse v Azure Portal](../../sql-data-warehouse/create-data-warehouse-portal.md) . Ujistěte se, že jste provedli zápisy následujících přihlašovacích údajů služby Azure synapse Analytics, které budou použity v pozdějších krocích.

* **Název serveru**: \<název serveru >. Database. Windows. NET
* **Název SQLDW (databáze)**
* **Uživatelské jméno**
* **Heslo**

**Nainstalujte Visual Studio a SQL Server Data Tools.** Pokyny najdete v tématu [Začínáme se sadou Visual Studio 2019 pro SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Připojte se k Azure synapse Analytics pomocí sady Visual Studio.** Pokyny najdete v části kroky 1 & 2 v tématu [připojení k Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

> [!NOTE]
> Spusťte následující dotaz SQL v databázi, kterou jste vytvořili ve službě Azure synapse Analytics (místo dotazu, který jste zadali v kroku 3 tématu připojit), **Vytvořte hlavní klíč**.
>
>

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**V rámci vašeho předplatného Azure vytvořte pracovní prostor Azure Machine Learning.** Pokyny najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](../studio/create-workspace.md).

## <a name="getdata"></a>Načtení dat do služby Azure synapse Analytics
Otevřete konzolu příkazového prostředí Windows PowerShell. Spusťte následující příkazy PowerShellu ke stažení ukázkových souborů skriptu SQL, které s vámi sdílíme na GitHubu, do místního adresáře, který zadáte s parametrem *-DestDir*. Hodnotu parametru *-DestDir* můžete změnit na libovolný místní adresář. IF *-DestDir* neexistuje, vytvoří se skript PowerShellu.

> [!NOTE]
> Je možné, že při spuštění následujícího skriptu PowerShellu budete muset **Spustit jako správce** , pokud adresář *DestDir* potřebuje oprávnění správce, aby ho bylo možné vytvořit nebo zapsat do něj zapisovat.
>
>

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Po úspěšném provedení se aktuální pracovní adresář změní na *-DestDir*. Byste měli vidět obrazovka podobná níže uvedenému příkladu:

![Změny v aktuálním pracovním adresáři][19]

Ve *DestDir*spusťte následující skript PowerShellu v režimu správce:

    ./SQLDW_Data_Import.ps1

Při prvním spuštění skriptu PowerShell budete požádáni o zadání informací z Azure synapse Analytics a účtu úložiště Azure Blob. Po dokončení tento skript Powershellu s prvním, přihlašovací údaje je vstup bude mít byl zapsán do konfiguračního souboru SQLDW.conf v aktuální pracovní adresář. Budoucí spuštění tohoto souboru skriptu prostředí PowerShell je možnost číst že všechny potřebné parametry z tohoto konfiguračního souboru. Pokud potřebujete změnit některé parametry, můžete zvolit zadání parametrů na obrazovce po zobrazení výzvy odstraněním tohoto konfiguračního souboru a vložením hodnot parametrů jako výzvy nebo změnou hodnot parametrů úpravou souboru SQLDW. conf v adresáři *DestDir* .

> [!NOTE]
> Aby se zabránilo tomu, že název schématu koliduje s tím, které už existují ve službě Azure Azure synapse Analytics, při čtení parametrů přímo ze souboru SQLDW. conf se do názvu schématu v souboru SQLDW. conf jako výchozí schéma přidá náhodné číslo se třemi číslicemi. název každého spuštění. Skript prostředí PowerShell může vyzve k zadání názvu schématu: může být zadán název na uvážení uživatelů.
>
>

Tento soubor **skriptu PowerShellu** dokončí následující úlohy:

* **Stáhne a nainstaluje AzCopy**, pokud AzCopy ještě není nainstalovaný.

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
* **Zkopíruje data do privátního účtu BLOB Storage** z veřejného objektu BLOB pomocí AzCopy.

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* Pomocí následujících příkazů **načte data do vaší služby Azure synapse Analytics** z vašeho privátního účtu BLOB Storage s využitím LoadDataToSQLDW. SQL.

  * Vytvořte schéma

          EXEC (''CREATE SCHEMA {schemaname};'');
  * Vytvoření přihlašovacích údajů s rozsahem databáze

          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Vytvoření externího zdroje dat pro objekt blob Azure Storage

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

    - Načtení dat z externích tabulek ve službě Azure Blob Storage do služby Azure synapse Analytics

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

    - Vytvoření ukázkové tabulky dat (NYCTaxi_Sample) a vkládání dat k němu z výběru dotazů SQL pro tabulky cesty a tarif. (Některé kroky tohoto návodu musí použít tuto ukázkovou tabulku.)

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
> V závislosti na geografickém umístění vašeho privátního účtu úložiště BLOB může proces kopírování dat z veřejného objektu BLOB na váš privátní účet úložiště trvat přibližně 15 minut nebo i déle a proces načítání dat z vašeho účtu úložiště do Azure. Azure synapse Analytics může trvat 20 minut nebo i déle.
>
>

Budete muset rozhodnout, které máte duplicitní zdrojové a cílové soubory.

> [!NOTE]
> Pokud CSV soubory zkopírovány z veřejné blob storage do účtu úložiště objektů blob v privátní již existují ve vašem účtu úložiště objektů blob v privátním, AzCopy zeptá, jestli chcete je přepsat. Pokud je nechcete přepsat, zadejte **n** po zobrazení výzvy. Pokud chcete **všechny** z nich přepsat, po zobrazení výzvy **Zadejte.** Můžete také zadat **y** a přepsat soubory. csv jednotlivě.
>
>

![Výstup nástroje AzCopy][21]

Můžete použít vlastní data. Pokud jsou vaše data ve vašem místním počítači v reálné aplikaci, můžete stále použít AzCopy k nahrání místních dat do úložiště objektů blob v Azure privátní. V příkazu AzCopy souboru skriptu PowerShellu do místního adresáře, který obsahuje vaše data, stačí změnit **zdrojové** umístění, `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`.

> [!TIP]
> Pokud vaše data už jsou v privátním úložišti objektů BLOB v reálném čase, můžete přeskočit krok AzCopy ve skriptu PowerShellu a přímo nahrát data do Azure Azure synapse Analytics. To bude vyžadovat další úpravy skript, který chcete přizpůsobit formát data.
>
>

Tento skript PowerShellu také připojí informace o analýze Azure synapse do ukázkových souborů pro zkoumání dat SQLDW_Explorations. SQL, SQLDW_Explorations. ipynb a SQLDW_Explorations_Scripts. py, aby byly tyto tři soubory připravené k vyzkoušení. okamžitě po dokončení skriptu PowerShellu.

Po úspěšném spuštění, zobrazí se obrazovka podobná níže uvedenému příkladu:

![Výstup skriptu úspěšná spuštění][20]

## <a name="dbexplore"></a>Zkoumání dat a strojírenství funkcí v Azure synapse Analytics
V této části provádíme zkoumání dat a generování funkcí spuštěním dotazů SQL ve službě Azure synapse Analytics přímo pomocí **nástrojů Visual Studio Data Tools**. Všechny dotazy SQL použité v této části najdete v ukázkovém skriptu s názvem *SQLDW_Explorations. SQL*. Tento soubor již byl stažen do místního adresáře skript prostředí PowerShell. Můžete ho také načíst z [GitHubu](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Ale soubor v GitHubu neobsahuje informace o službě Azure synapse Analytics, které jsou napájené ze sítě.

Připojte se k analýze Azure synapse pomocí sady Visual Studio s přihlašovacím jménem a heslem služby Azure synapse Analytics a otevřete **SQL Průzkumník objektů** a potvrďte, že databáze a tabulky byly naimportovány. Načtěte soubor *SQLDW_Explorations. SQL* .

> [!NOTE]
> Chcete-li otevřít Editor dotazů PDW (Parallel Data Warehouse), použijte příkaz **New Query** v době, kdy je na **serveru SQL Průzkumník objektů**vybrána možnost PDW. Standardní editor dotazů SQL PDW nepodporuje.
>
>

Tady jsou typy úloh zkoumání a vytváření funkcí provedených v této části:

* Prozkoumejte data distribuce několik polí v různých časových oken.
* Prozkoumejte data kvality zeměpisnou šířku a délku pole.
* Vygenerujte binární a mezitřídní popisky klasifikace na základě **\_ho množství tipů**.
* Generovat funkce a výpočetní/porovnání vzdálenosti o jízdách.
* Spojení dvou tabulek a extrahovat náhodného vzorku, který se použije k vytvoření modelů.

### <a name="data-import-verification"></a>Ověření importu dat
Tyto dotazy poskytují rychlé ověření počtu řádků a sloupců v tabulkách vyplní dříve pomocí Polybase pro paralelní hromadný import,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Výstup:** Měli byste získat 173 179 759 řádků a 14 sloupců.

### <a name="exploration-trip-distribution-by-medallion"></a>Zkoumání: Distribuce latence podle Medailon
Tento příklad dotaz identifikuje medallions (taxislužby čísla), která dokončila více než 100 zkracuje dobu odezvy v rámci určeného časového období. Dotaz by měl těžit z přístupu do děleného tabulky, protože je určen schématem oddílu **pickup\_DateTime**. Dotazování úplnou datovou sadu se také provést pomocí dělené tabulky nebo indexu kontroly.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Výstup:** Dotaz by měl vrátit tabulku s řádky, které určují 13 369 medallions (Taxis) a počet cest dokončených v 2013. Poslední sloupec obsahuje počet cest dokončit.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Zkoumání: Distribuce latence podle Medailon a hack_license
V tomto příkladu identifikuje medallions (taxislužby čísla) a hack_license čísla (ovladače), která dokončena více než 100 zkracuje dobu odezvy v rámci určeného časového období.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Výstup:** Dotaz by měl vrátit tabulku s 13 369 řádky s určením identifikátorů 13 369 auta/ovladače, u kterých bylo dokončeno více 100 v 2013. Poslední sloupec obsahuje počet cest dokončit.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Hodnocení kvality dat: Ověřte záznamy s nesprávné délky a šířky
V tomto příkladu prověří, pokud jakýkoli z polí zeměpisná délka a/nebo zeměpisnou šířku buď obsahuje neplatnou hodnotu (stupně radián by měl být od -90 do 90), nebo máte (0, 0) souřadnic.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Výstup:** Dotaz vrátí 837 467 cest s neplatnými poli Zeměpisná délka a/nebo zeměpisná šířka.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Zkoumání: Šikmý vs. není šikmý zkracuje dobu odezvy distribuce
Tento příklad zjistí počet cest, které byly šikmý vs. číslo, které nebyly šikmý v zadaném časovém období (nebo v celé datové sadě, pokud pokrývající celý rok, jak je zde nastavený). Toto rozdělení odráží distribuce binární označení později použitého pro binární klasifikaci modelování.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Výstup:** Dotaz by měl vrátit následující četnosti tipů pro rok 2013:90 447 622 šikmo a 82 264 709 není-šikmo.

### <a name="exploration-tip-classrange-distribution"></a>Průzkumu: Rozdělení třídy a rozsahu Tip
Tento příklad vypočítá distribuci tip rozsahů v daném časovém období (nebo v celé datové sadě, pokud pokrývající celý rok). Tato distribuce tříd Label se bude používat později pro modelování klasifikace s více třídami.

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

**Výkonem**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Zkoumání: Výpočty a porovnat vzdálenost o jízdách
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
Funkce SQL může být někdy efektivní možnosti pro vytváření funkcí. V tomto názorném postupu jsme definovali funkci SQL pro přímé vzdálenosti mezi vzájemně sbírat míčky a dropoff umístění. V **nástrojích Data Tools sady Visual Studio**můžete spustit následující skripty SQL.

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

**Výstup:** Tento dotaz vygeneruje tabulku (s 2 803 538 řádky) s využitím Pickup a dropoff Latitudes a zeměpisná délka a příslušné přímé vzdálenosti v mílích. Tady jsou výsledky pro první tři řádky:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Příprava dat pro vytváření modelů
Následující dotaz se spojí s tabulkami **nyctaxi\_TRIPS** a **nyctaxi\_tarifs** , vygeneruje **binární popisek klasifikace, který je**popsán, jako **\_Tip**klasifikační klasifikace s více třídami a extrahuje vzorek z plné připojené datové sady. Vzorkování se provádí načtením podmnožinu zkracuje dobu odezvy na základě času vyzvednutí.  Tento dotaz se dá zkopírovat a vložit přímo do modulu [Azure Machine Learning Studio (Classic)](https://studio.azureml.net) [Import dat]import[-data] pro příjem přímých dat z instance SQL Database v Azure. Dotaz vyloučí záznamy s nesprávnou (0, 0) souřadnic.

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

1. Uložte konečný dotaz SQL pro extrakci a vzorkování dat a zkopírujte – vložte dotaz přímo do modulu import dat import[-data] v Azure Machine Learning nebo
2. Pochovejte ukázková a inženýrská data, která plánujete použít pro vytváření modelů v nové tabulce Azure synapse Analytics a použijte novou tabulku v modulu [Import dat][-data] v Azure Machine Learning. Skript PowerShellu v předchozím kroku tuto úlohu dokončil za vás. Může číst přímo z této tabulky v modulu, importovat Data.

## <a name="ipnb"></a>Zkoumání dat a strojírenství funkcí v IPython poznámkovém bloku
V této části provedeme zkoumání dat a generování funkcí pomocí dotazů Pythonu a SQL oproti analýze Azure synapse, kterou jste vytvořili dříve. Do místního adresáře se stáhl ukázkový IPython Poznámkový blok s názvem **SQLDW_Explorations. ipynb** a soubor skriptu pythonu **SQLDW_Explorations_Scripts. py** . Jsou také k dispozici na [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Tyto dva soubory jsou stejné v skriptů v Pythonu. V případě, že nemáte serveru IPython Notebook budete mít k dispozici soubor skriptu Pythonu. Tyto dva ukázkové soubory Pythonu jsou navrženy v rámci **Python 2,7**.

V ukázkovém poznámkovém bloku IPython a staženém souboru skriptu Pythonu, který jste stáhli do vašeho místního počítače, jste předtím připojili skript PowerShellu do služby Azure synapse Analytics. Jedná se o spustitelný soubor bez nutnosti jakékoli úpravy.

Pokud jste již nastavili pracovní prostor Azure Machine Learning, můžete přímo odeslat ukázkový Poznámkový blok IPython do služby IPython poznámkového bloku AzureML a spustit ho. Tady je postup, jak odeslat do služby IPython poznámkového bloku AzureML:

1. Přihlaste se ke svému pracovnímu prostoru Azure Machine Learning, v horní části klikněte na **Studio** a na levé straně webové stránky klikněte na **poznámkové bloky** .

    ![Klikněte na tlačítko Studio pak poznámkové BLOKY][22]
2. V levém dolním rohu webové stránky klikněte na **Nový** a vyberte **Python 2**. Potom zadejte název do poznámkového bloku a klikněte na značku zaškrtnutí k vytvoření nové prázdné IPython Notebook.

    ![Klikněte na nový, pak vyberte Python 2][23]
3. V levém horním rohu nového poznámkového bloku IPython klikněte na symbol **Jupyter** .

    ![Klikněte na Jupyter symbol][24]
4. Přetáhněte ukázkový Poznámkový blok IPython na stránku **stromu** služby IPython poznámkového bloku AzureML a klikněte na **nahrát**. Potom ukázka IPython Notebook nahraje do služby Azure ml IPython Notebook.

    ![Klikněte na tlačítko Odeslat][25]

Chcete-li spustit ukázku soubor Pythonu následující balíčky jsou nutné skriptu IPython Notebook nebo Python. Pokud používáte službu Azure ml IPython Notebook, tyto balíčky se předem nainstalované.

- pandas
- numpy
- matplotlib
- modul pyodbc
- PyTables

Při vytváření pokročilých analytických řešení na Azure Machine Learning s velkými objemy dat je tady Doporučené pořadí:

* Přečtěte si v malým vzorkem dat do rámečku data v paměti.
* Provedení některých vizualizací a průzkumy pomocí jen Vzorkovaná data.
* Můžete experimentovat s vytváření funkcí pomocí jen Vzorkovaná data.
* V případě většího zkoumání dat, manipulace s daty a strojírenství funkcí použijte Python k vydávání dotazů SQL přímo proti analýze Azure synapse.
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

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Sestava počet řádků a sloupců v tabulce < nyctaxi_trip >
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

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>Sestava počet řádků a sloupců v tabulce < nyctaxi_fare >
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

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Ukázka malého data z databáze Azure synapse Analytics
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
Nyní jste připraveni na zkoumání jen Vzorkovaná data. Začneme s prohlížením některých popisných statistik pro **cestu\_vzdálenosti** (nebo jakákoli jiná pole, která se rozhodnete zadat).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Vizualizace: Příklad diagramu pole
Dále podíváme na diagramu pole pro cestu vzdálenost k vizualizaci quantiles.

    df1.boxplot(column='trip_distance',return_type='dict')

![Pole vykreslení výstupu][1]

### <a name="visualization-distribution-plot-example"></a>Vizualizace: Příklad diagram distribuce
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
V bodovém grafu si ukážeme **dobu\_času\_v\_sekundách** a **cestě\_** , abyste zjistili, jestli existuje korelace.

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Diagnostického výstupu vztah mezi časem a vzdálenost][6]

Podobně je možné kontrolovat vztah mezi **sazbou\_kódem** a **cestou\_ou vzdálenost**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Diagnostického výstupu vztah mezi kódem a vzdálenost][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Zkoumání dat na vzorku dat. použití příkazů jazyka SQL v IPython notebook
V této části prozkoumáme distribuci dat pomocí ukázkových dat, která jsou trvale vytvořená v nové tabulce, kterou jsme vytvořili výše. Podobné průzkumy je možné provádět pomocí původních tabulek.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Zkoumání: Sestava počet řádků a sloupců v tabulce vzorky
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Zkoumání: Šikmý nebo není přepne distribuce
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Průzkumu: Rozdělení třídy Tip
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Zkoumání: Vykreslení distribuce tip třídou
    tip_class_dist['tip_freq'].plot(kind='bar')

![Vykreslení #26][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Zkoumání: Denní distribuci zkracuje dobu odezvy
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Zkoumání: Distribuční výlet za Medailon
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Průzkumu: Distribuce latence podle Medailon a hack licence
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Průzkumu: Distribuce doby jízdy
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Průzkumu: Distribuce vzdálenost o jízdách
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Průzkumu: Distribuce typu platby
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Zkontrolujte poslední formulář natrénuje tabulky
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Modely sestavení v Azure Machine Learning
Nyní je připraven pokračovat na sestavení modelu a nasazení modelu v [Azure Machine Learning](https://studio.azureml.net). Data jsou připravená k použití v některém z předpovědi problémy identifikovat dříve, a to:

1. **Binární klasifikace**: pro předpověď, zda byl pro cestu zaplacen Tip.
2. **Třída klasifikace s více třídami**: pro předpověď rozsahu zaplaceného tipu podle dříve definovaných tříd.
3. **Regresní úloha**: pro předpověď množství tipu placeného pro cestu.

Pokud chcete začít modelování, přihlaste se k pracovnímu prostoru **Azure Machine Learning (Classic)** . Pokud jste ještě nevytvořili pracovní prostor machine learningu, přečtěte si téma [Vytvoření pracovního prostoru Azure Machine Learning Studio (Classic)](../studio/create-workspace.md).

1. Pokud chcete začít s Azure Machine Learning, přečtěte si téma [co je Azure Machine Learning Studio (Classic)?](../studio/what-is-ml-studio.md)
2. Přihlaste se k [Azure Machine Learning Studio (Classic)](https://studio.azureml.net).
3. Domovská stránka Machine Learning Studio (Classic) poskytuje spoustu informací, videí, kurzů, odkazů na reference k modulům a dalších prostředků. Další informace o Azure Machine Learning najdete v [centru dokumentace Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Typické výukového experimentu se skládá z následujících kroků:

1. Vytvořte a **+ Nový** experiment.
2. Získat data do Azure Machine Learning Studio (Classic).
3. Proveďte předběžné zpracování, transformaci a manipulaci s daty podle potřeby.
4. Funkce vygenerujte, podle potřeby.
5. Rozdělení dat do datové sady školení/ověření/testování (nebo máte rozdělování datových sad pro každou).
6. Vyberte jeden nebo více algoritmů strojového učení v závislosti na učení problém vyřešit. Například binární klasifikace, klasifikace s více třídami, regrese.
7. Trénování jeden nebo více modelů pomocí trénovací datové sady.
8. Ověření datové sady, natrénované modely pomocí skóre.
9. Vyhodnoťte modely pro výpočet důležité metriky pro výuky.
10. Optimalizujte model (y) a vyberte nejlepší model, který chcete nasadit.

V tomto cvičení jsme už prozkoumali a provedli analýzu dat ve službě Azure synapse Analytics a rozhodli jste se, jak na základě velikosti vzorků ingestovat v Azure Machine Learning Studio (Classic). Tady je postup pro sestavení jeden nebo více prediktivní modely:

1. Získat data do Azure Machine Learning Studio (Classic) pomocí modulu [Import dat][-data] , který je k dispozici v části **vstup a výstup dat** . Další informace najdete na referenční stránce [Import dat][-data] Module.

    ![Azure ML umožňuje importovat Data][17]
2. Na panelu **vlastnosti** vyberte možnost **Azure SQL Database** jako **zdroj dat** .
3. Do pole **název databázového serveru** zadejte název DNS databáze. Formát: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Do příslušného pole zadejte **název databáze** .
5. Zadejte *uživatelské jméno SQL* do pole **název uživatelského účtu serveru**a *heslo* v **hesle uživatelského účtu serveru**.
7. V textové oblasti **dotaz do databáze** vložte dotaz, který extrahuje potřebná databázová pole (včetně všech vypočítaných polí, jako jsou popisky), a dolů vyvzorkuje data do požadované velikosti vzorku.

Příkladem binární klasifikace experimentu, který čte data přímo z databáze Azure synapse Analytics, je na následujícím obrázku (Nezapomeňte nahradit názvy tabulek nyctaxi_trip a nyctaxi_fare podle názvu schématu a názvů tabulek, které jste použili v Návod). Podobně jako experimenty může být vytvořen pro klasifikace víc tříd a regresní problémy.

![Trénování služby Azure ML][10]

> [!IMPORTANT]
> V ukázkách dotazů pro extrakci a vzorkování dat modelování, které jsou uvedené v předchozích částech, **jsou v dotazu zahrnuté všechny popisky pro tři cvičení modelování**. Důležitým (vyžadovaným) krokem v každé cvičení modelování je **vyloučení** zbytečných popisků pro ostatní dva problémy a jakékoli jiné **cíle nevracení**. Například při použití binární klasifikace **použijte popisek,** který se zanechal a vyloučí pole **\_třída**, **tip\_množství**a **Celková\_ovaná částka**. Druhá možnost se cílového únikům protože implikují tip placené.
>
> Chcete-li vyloučit nepotřebné sloupce nebo nevrácené cíle, můžete použít modul [Vybrat sloupce v datové sadě][select-columns] nebo [Upravit metadata][edit-metadata]. Další informace najdete v tématu [Výběr sloupců v datové sadě][select-columns] a úpravy odkazů na [metadata][edit-metadata] .
>
>

## <a name="mldeploy"></a>Nasazení modelů v Azure Machine Learning
Když je model hotový, můžete snadno nasadit ho jako webovou službu přímo z experimentu. Další informace o nasazení webových služeb Azure ML najdete v tématu [nasazení webové služby Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).

Pokud chcete nasadit nové webové služby, budete muset:

1. Vytvoření experimentu bodování.
2. Nasazení webové služby.

Pokud chcete vytvořit experiment bodování z **dokončeného** experimentu, klikněte na tlačítko **vytvořit bodování experimentu** v dolním panelu akcí.

![Bodování Azure][18]

Azure Machine Learning se pokusí vytvořit bodování experiment založené na součástech výukového experimentu. Konkrétně se tyto akce:

1. Uložení naučeného modelu a odeberte moduly trénování modelu.
2. Identifikujte logický **vstupní port** , který bude představovat očekávané schéma vstupních dat.
3. Identifikujte logický **výstupní port** , který bude představovat očekávané výstupní schéma webové služby.

Když se vytvoří experiment bodování, zkontrolujte výsledky a podle potřeby proveďte úpravy. Typickou úpravou je nahradit vstupní datovou sadu nebo dotaz s jedním, který vylučuje pole popisku, protože tato pole popisků nebudou při volání služby namapována na schéma. Je také vhodné omezit velikost vstupní datové sady nebo dotaz na několik záznamů, které jsou dostatečné pro indikaci vstupního schématu. V případě výstupního portu je běžné vyloučit všechna vstupní pole a zahrnout do výstupu pouze **popisky** s skóre a jejich **pravděpodobnosti** ve výstupu pomocí modulu [Výběr sloupců v datové sadě][select-columns] .

Ukázku vyhodnocování experimentu najdete v následujícím obrázku. Až budete připraveni k nasazení, klikněte na tlačítko **publikovat webovou službu** na dolním panelu akcí.

![Služba Azure ML publikovat][11]

## <a name="summary"></a>Souhrn
Na rekapitulace, co jsme udělali v tomto kurzu návodu, vytvoříte Azure prostředí pro datové vědy, pracoval s velkou datovou sadu veřejné, trvá až vědecké zpracování týmových dat, od získání dat pro trénování modelu a potom na nasazení webové služby Azure Machine Learning.

### <a name="license-information"></a>Informace o licenci
Tento ukázkový názorný postup a jeho doprovodném skripty a IPython notebook(s) sdílí Microsoft v rámci licence MIT. Další podrobnosti najdete v souboru LICENSE. txt v adresáři ukázkového kódu na GitHubu.

## <a name="references"></a>Odkazy
- [Stránka stahování Andrés Monroy NYC taxislužby TRIPS](https://www.andresmh.com/nyctaxitrips/)
- [FÓLIE NYC data taxislužby na cestách pomocí Chrisho Whongu](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [NYC taxislužby a výzkum a statistika Komise Limousine](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

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
[-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
