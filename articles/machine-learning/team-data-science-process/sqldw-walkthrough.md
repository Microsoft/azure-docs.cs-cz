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
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 59935d2659d27059617059c021fef9b6a2b552e0
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89440197"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>Vědecké zpracování týmových dat v akci: používání Azure synapse Analytics
V tomto kurzu Vás provedeme vytvořením a nasazením modelu strojového učení s využitím Azure synapse Analytics pro veřejně dostupnou datovou sadu, která je datovou sadou [NYC taxislužby TRIPS](https://www.andresmh.com/nyctaxitrips/) . Model binární klasifikace vytváří předpověď bez ohledu na to, jestli je pro cestu placené nebo ne.  Mezi modely patří klasifikace s více třídami (bez ohledu na to, zda existuje Tip) a regrese (rozdělení pro placené částky Tip).

Postup následuje po pracovním postupu [TDSP (Team data vědecký proces)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) . Ukážeme, jak nastavit prostředí pro datové vědy, jak načíst data do služby Azure synapse Analytics a jak pomocí Azure synapse Analytics nebo IPython poznámkového bloku prozkoumat funkce dat a inženýrů modelu. Potom ukážeme, jak sestavit a nasadit model pomocí Azure Machine Learning.

## <a name="the-nyc-taxi-trips-dataset"></a><a name="dataset"></a>Datová sada cest taxislužby pro NYC
Data NYC taxislužby se skládají z přibližně 20 GB komprimovaných souborů CSV (~ 48 GB nekomprimovaných), zaznamenáváním více než 173 000 000 jednotlivých cest a tarifů placených za každou cestu. Každý záznam cesty zahrnuje umístění a časy vyzvednutí a dropoff, číslo licence v anonymního napadení (strojvedoucí) a číslo Medallion (jedinečné ID taxislužby). Data se týkají všech cest v roce 2013 a jsou k dispozici v následujících dvou datových sadách pro každý měsíc:

1. **trip_data.csv** soubor obsahuje podrobnosti o cestě, jako je třeba počet cestujících, vyzvednutí a dropoff body, doba trvání cesty a délka cesty. Tady je několik ukázkových záznamů:

`medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. Soubor **trip_fare.csv** obsahuje podrobnosti o tarifu placeného za každou cestu, jako je typ platby, částka tarifů, příplatek a daně, tipy a mýtné a celková placená částka. Tady je několik ukázkových záznamů:

`medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

**Jedinečný klíč** , který se používá pro připojení k \_ datům cest a služební \_ tarif, se skládá z následujících tří polí:

* medallion,
* licence pro napadení \_ a
* \_Datum a čas vyzvednutí

## <a name="address-three-types-of-prediction-tasks"></a><a name="mltasks"></a>Adresovat tři typy úloh předpovědi
Pro ilustraci tří druhů úloh modelování formuluje tři problémy s předpovědí na základě * \_ výše uvedeného popisu* :

1. **Binární klasifikace**: Chcete-li předpovědět, zda byla pro cestu vyplacena * \_ hodnota* tipu, to znamená, že hodnota tipu větší než $0 je pozitivním příkladem, zatímco je * \_ hodnota tipu* $0 záporná.
2. **Klasifikace více tříd**: pro předpověď rozsahu tipu placeného pro danou cestu. * \_ Velikost tipu* rozdělíme na pět přihrádek nebo tříd:

`Class 0 : tip_amount = $0`

`Class 1 : tip_amount > $0 and tip_amount <= $5`

`Class 2 : tip_amount > $5 and tip_amount <= $10`

`Class 3 : tip_amount > $10 and tip_amount <= $20`

`Class 4 : tip_amount > $20`

3. **Regresní úloha**: pro předpověď množství tipu placeného pro cestu.

## <a name="set-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Nastavení prostředí pro Azure Data vědu pro pokročilé analýzy
Pokud chcete nastavit prostředí pro datové vědy v Azure, postupujte podle těchto kroků.

**Vytvoření vlastního účtu úložiště Azure Blob**

* Když zřizujete vlastní úložiště objektů BLOB v Azure, vyberte geografickou polohu pro úložiště objektů BLOB v Azure v nebo co nejblíže **střed USA – jih**, což je místo, kde se ukládají data taxislužby NYC. Data se zkopírují pomocí AzCopy z veřejného kontejneru úložiště objektů blob do kontejneru ve vlastním účtu úložiště. Čím blíže je úložiště objektů BLOB v Azure Střed USA – jih, tím rychleji se tato úloha (krok 4) dokončí.
* Pokud chcete vytvořit vlastní účet Azure Storage, postupujte podle kroků uvedených v části [informace o Azure Storagech účtech](../../storage/common/storage-create-storage-account.md). Nezapomeňte si dělat poznámky k hodnotám těchto přihlašovacích údajů k účtu úložiště, které budete potřebovat později v tomto návodu.

  * **Název účtu úložiště**
  * **Klíč účtu úložiště**
  * **Název kontejneru** (který má ukládat data do úložiště objektů BLOB v Azure)

**Zřiďte svou instanci Azure synapse Analytics.**
Pokud chcete zřídit instanci Azure synapse Analytics, postupujte podle dokumentace v části [Vytvoření a dotazování analýzy Azure synapse v Azure Portal](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) . Ujistěte se, že jste provedli zápisy následujících přihlašovacích údajů služby Azure synapse Analytics, které budou použity v pozdějších krocích.

* **Název serveru**: \<server Name> . Database.Windows.NET
* **Název SQLDW (databáze)**
* **Uživatelské jméno**
* **Heslo**

**Nainstalujte Visual Studio a SQL Server Data Tools.** Pokyny najdete v tématu [Začínáme se sadou Visual Studio 2019 pro Azure synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Připojte se k Azure synapse Analytics pomocí sady Visual Studio.** Pokyny najdete v krocích 1 & 2 v tématu [připojení k SQL Analytics v Azure synapse Analytics](../../synapse-analytics/sql/connect-overview.md).

> [!NOTE]
> Spusťte následující dotaz SQL v databázi, kterou jste vytvořili ve službě Azure synapse Analytics (místo dotazu, který jste zadali v kroku 3 tématu připojit), **Vytvořte hlavní klíč**.
>
>

```sql
BEGIN TRY
       --Try to create the master key
    CREATE MASTER KEY
END TRY
BEGIN CATCH
       --If the master key exists, do nothing
END CATCH;
```

**V rámci vašeho předplatného Azure vytvořte pracovní prostor Azure Machine Learning.** Pokyny najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](../studio/create-workspace.md).

## <a name="load-the-data-into-azure-synapse-analytics"></a><a name="getdata"></a>Načtení dat do služby Azure synapse Analytics
Otevřete konzolu příkazového řádku prostředí Windows PowerShell. Spusťte následující příkazy PowerShellu ke stažení ukázkových souborů skriptu SQL, které s vámi sdílíme na GitHubu, do místního adresáře, který zadáte s parametrem *-DestDir*. Hodnotu parametru *-DestDir* můžete změnit na libovolný místní adresář. IF *-DestDir* neexistuje, vytvoří se skript PowerShellu.

> [!NOTE]
> Je možné, že při spuštění následujícího skriptu PowerShellu budete muset **Spustit jako správce** , pokud adresář *DestDir* potřebuje oprávnění správce, aby ho bylo možné vytvořit nebo zapsat do něj zapisovat.
>
>

```azurepowershell
$source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
$ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
$wc = New-Object System.Net.WebClient
$wc.DownloadFile($source, $ps1_dest)
.\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'
```

Po úspěšném provedení se aktuální pracovní adresář změní na *-DestDir*. Měli byste vidět obrazovku, jak je znázorněno níže:

![Aktuální pracovní adresáře – změny][19]

Ve *DestDir*spusťte následující skript PowerShellu v režimu správce:

```azurepowershell
./SQLDW_Data_Import.ps1
```

Při prvním spuštění skriptu PowerShell budete požádáni o zadání informací z Azure synapse Analytics a účtu úložiště Azure Blob. Po prvním dokončení tohoto skriptu PowerShellu se zadané přihlašovací údaje zapíší do konfiguračního souboru SQLDW. conf v tomto pracovním adresáři. Budoucí spuštění tohoto souboru skriptu PowerShellu má možnost číst všechny potřebné parametry z tohoto konfiguračního souboru. Pokud potřebujete změnit některé parametry, můžete zvolit zadání parametrů na obrazovce po zobrazení výzvy odstraněním tohoto konfiguračního souboru a vložením hodnot parametrů jako výzvy nebo změnou hodnot parametrů úpravou souboru SQLDW. conf v adresáři *DestDir* .

> [!NOTE]
> Aby se zabránilo tomu, že název schématu koliduje s tím, které už existují ve službě Azure Azure synapse Analytics, při čtení parametrů přímo ze souboru SQLDW. conf se do názvu schématu přidají v souboru SQLDW. conf jako výchozí název schématu pro každé spuštění tři číslice. Skript prostředí PowerShell vás může vyzvat k zadání názvu schématu: jméno může být zadáno podle uvážení uživatele.
>
>

Tento soubor **skriptu PowerShellu** dokončí následující úlohy:

* **Stáhne a nainstaluje AzCopy**, pokud AzCopy ještě není nainstalovaný.

  ```azurepowershell
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
  ```

* **Zkopíruje data do privátního účtu BLOB Storage** z veřejného objektu BLOB pomocí AzCopy.

  ```azurepowershell
  Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
  $start_time = Get-Date
  AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
  $end_time = Get-Date
  $time_span = $end_time - $start_time
  $total_seconds = [math]::Round($time_span.TotalSeconds,2)
  Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
  Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
  ```

* Pomocí následujících příkazů **načte data do vaší služby Azure synapse Analytics** z vašeho privátního účtu BLOB Storage s využitím LoadDataToSQLDW. SQL.

  * Vytvoření schématu

    ```sql
    EXEC (''CREATE SCHEMA {schemaname};'');
    ```

  * Vytvoření databáze s rozsahem pověření

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
    WITH IDENTITY = ''asbkey'' ,
    Secret = ''{StorageAccountKey}''
    ```

  * Vytvoření externího zdroje dat pro objekt blob Azure Storage

    ```sql
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
    ```

  * Vytvoří externí formát souboru pro soubor CSV. Data jsou nekomprimovaná a pole jsou oddělená znakem kanálu.

    ```sql
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
    ```

  * Vytvoření externích tabulek tarifů a cest pro datovou sadu NYC taxislužby ve službě Azure Blob Storage.

    ```sql
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
    ```

    - Načtení dat z externích tabulek ve službě Azure Blob Storage do služby Azure synapse Analytics

      ```sql
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
      ```

    - Vytvořte ukázkovou tabulku dat (NYCTaxi_Sample) a vložte do ní data z výběru dotazů SQL v tabulkách Trip a tarif. (Některé kroky tohoto návodu musí použít tuto ukázkovou tabulku.)

      ```sql
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
      ```

Geografické umístění účtů úložiště ovlivňuje dobu načítání.

> [!NOTE]
> V závislosti na geografickém umístění vašeho privátního účtu BLOB Storage může proces kopírování dat z veřejného objektu BLOB na váš privátní účet úložiště trvat přibližně 15 minut nebo i déle a proces načítání dat z vašeho účtu úložiště do Azure Azure synapse Analytics může trvat 20 minut nebo i delší dobu.
>
>

Budete se muset rozhodnout, co dělat, pokud máte duplicitní zdrojový a cílový soubor.

> [!NOTE]
> Pokud soubory. csv, které se mají zkopírovat z veřejného úložiště objektů blob do privátního účtu úložiště BLOB, už ve vašem privátním účtu BLOB Storage existují, AzCopy se zeptá, jestli je chcete přepsat. Pokud je nechcete přepsat, zadejte **n** po zobrazení výzvy. Pokud chcete **všechny** z nich přepsat, po zobrazení výzvy **Zadejte.** Můžete také zadat **y** a přepsat soubory. csv jednotlivě.
>
>

![Výstup z AzCopy][21]

Můžete používat vlastní data. Pokud jsou vaše data v místním počítači v reálném čase, můžete i nadále používat AzCopy k odesílání místních dat do privátního úložiště objektů BLOB v Azure. Stačí změnit **zdrojové** umístění, `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"` v příkazu AzCopy souboru skriptu PowerShellu do místního adresáře, který obsahuje vaše data.

> [!TIP]
> Pokud vaše data už jsou v privátním úložišti objektů BLOB v reálném čase, můžete přeskočit krok AzCopy ve skriptu PowerShellu a přímo nahrát data do Azure Azure synapse Analytics. To bude vyžadovat další úpravy skriptu pro přizpůsobení formátu vašich dat.
>
>

Tento skript PowerShellu také připojí informace o analýze Azure synapse do ukázkových souborů pro zkoumání dat SQLDW_Explorations. SQL, SQLDW_Explorations. ipynb a SQLDW_Explorations_Scripts. py, aby byly tyto tři soubory připravené k okamžitému vyzkoušení po dokončení skriptu PowerShellu.

Po úspěšném provedení se zobrazí obrazovka podobná této:

![Výstup úspěšného spuštění skriptu][20]

## <a name="data-exploration-and-feature-engineering-in-azure-synapse-analytics"></a><a name="dbexplore"></a>Zkoumání dat a strojírenství funkcí v Azure synapse Analytics
V této části provádíme zkoumání dat a generování funkcí spuštěním dotazů SQL ve službě Azure synapse Analytics přímo pomocí **nástrojů Visual Studio Data Tools**. Všechny dotazy SQL použité v této části najdete v ukázkovém skriptu s názvem *SQLDW_Explorations. SQL*. Skript PowerShellu již tento soubor stáhl do místního adresáře. Můžete ho také načíst z [GitHubu](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Ale soubor v GitHubu neobsahuje informace o službě Azure synapse Analytics, které jsou napájené ze sítě.

Připojte se k analýze Azure synapse pomocí sady Visual Studio s přihlašovacím jménem a heslem služby Azure synapse Analytics a otevřete **SQL Průzkumník objektů** a potvrďte, že databáze a tabulky byly naimportovány. Načtěte soubor *SQLDW_Explorations. SQL* .

> [!NOTE]
> Chcete-li otevřít Editor dotazů PDW (Parallel Data Warehouse), použijte příkaz **New Query** v době, kdy je na **serveru SQL Průzkumník objektů**vybrána možnost PDW. Standardní editor dotazů SQL není podporován nástrojem PDW.
>
>

Tady jsou typy úloh zkoumání a vytváření funkcí provedených v této části:

* Prozkoumejte distribuci dat několika polí v různých časových oknech.
* Prozkoumejte kvalitu dat v polích Zeměpisná délka a zeměpisná šířka.
* Vygenerujte binární a mezitřídní popisky klasifikace na **základě \_ velikosti tipu**.
* Generování funkcí a výpočetních/porovnávacích vzdáleností cest.
* Připojte se k oběma tabulkám a extrahujte náhodný vzorek, který se použije k sestavení modelů.

### <a name="data-import-verification"></a>Ověření importu dat
Tyto dotazy poskytují rychlé ověření počtu řádků a sloupců v tabulkách naplněných pomocí paralelního hromadného importu základní verze.

--Sestava počet řádků v tabulce <nyctaxi_trip> bez kontroly tabulky

   ```sql
   SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
   ```

--Sestava počtu sloupců v tabulce <nyctaxi_trip>

   ```sql
   SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'
   ```

**Výstup:** Měli byste získat 173 179 759 řádků a 14 sloupců.

### <a name="exploration-trip-distribution-by-medallion"></a>Průzkum: distribuce cest pomocí Medallion
Tento ukázkový dotaz identifikuje medallions (taxislužby čísla), které v zadaném časovém období dokončily více než 100 cest. Dotaz by měl těžit z tabulkového přístupu s dělenou výhodou, protože je podmíněné schémam oddílu **pro \_ Datum vyzvednutí**. Dotazování na celou datovou sadu použije také prohledání děleného tabulky nebo indexu.

```sql
SELECT medallion, COUNT(*)
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
GROUP BY medallion
HAVING COUNT(*) > 100
```

**Výstup:** Dotaz by měl vrátit tabulku s řádky, které určují 13 369 medallions (Taxis) a počet cest dokončených v 2013. Poslední sloupec obsahuje počet dokončených cest.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Průzkum: distribuce cest pomocí Medallion a hack_license
Tento příklad identifikuje medallions (čísla taxislužby) a čísla hack_license (ovladačů), která během zadaného časového období dokončila více než 100 cest.

```sql
SELECT medallion, hack_license, COUNT(*)
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
GROUP BY medallion, hack_license
HAVING COUNT(*) > 100
```

**Výstup:** Dotaz by měl vrátit tabulku s 13 369 řádky s určením identifikátorů 13 369 auta/ovladače, u kterých bylo dokončeno více 100 v 2013. Poslední sloupec obsahuje počet dokončených cest.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Vyhodnocení kvality dat: ověření záznamů s nesprávnou zeměpisnou délkou nebo zeměpisnou šířkou
Tento příklad zkoumá, pokud některá pole Zeměpisná délka a/nebo zeměpisná délka buď obsahují neplatnou hodnotu (Radian stupně by měly být mezi-90 a 90), nebo musí mít souřadnice (0, 0).

```sql
SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
OR    (pickup_longitude = '0' AND pickup_latitude = '0')
OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))
```

**Výstup:** Dotaz vrátí 837 467 cest s neplatnými poli Zeměpisná délka a/nebo zeměpisná šířka.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Prozkoumávání: v nestejné distribuci cest
Tento příklad najde počet cest, které byly v zadaném časovém období (nebo v úplné datové sadě) neuvedené, a to tak, aby pokrývaly celý rok, jak je nastaveno zde. Tato distribuce odráží binární distribuci jmenovky, která se má později použít pro modelování binární klasifikace.

```sql
SELECT tipped, COUNT(*) AS tip_freq FROM (
  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
  FROM <schemaname>.<nyctaxi_fare>
  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tipped
```

**Výstup:** Dotaz by měl vrátit následující četnosti tipů pro rok 2013:90 447 622 šikmo a 82 264 709 není-šikmo.

### <a name="exploration-tip-classrange-distribution"></a>Průzkum: distribuce třídy nebo rozsahu tipu
Tento příklad vypočítá distribuci rozsahů tipů v daném časovém období (nebo v úplné datové sadě, pokud pokrývá celý rok). Tato distribuce tříd Label se bude používat později pro modelování klasifikace s více třídami.

```sql
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
```

**Výkonem**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Průzkum: výpočty a porovnání vzdálenosti cest
Tento příklad převede vyzvednutí a dropoff zeměpisnou délku a zeměpisnou šířku na geografické body SQL, vypočítá vzdálenost pro cestu pomocí rozdílů geografických bodů SQL a vrátí náhodný vzorek výsledků pro porovnání. Příklad omezuje výsledky na platné souřadnice pouze pomocí dotazu vyhodnocení kvality dat popsaných výše.

```sql
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
```

### <a name="feature-engineering-using-sql-functions"></a>Technologie funkcí pomocí funkcí SQL
V některých případech může být funkce SQL efektivní možností pro strojírenství funkcí. V tomto návodu jsme definovali funkci SQL pro výpočet přímé vzdálenosti mezi výstupními a dropoff umístěními. V **nástrojích Data Tools sady Visual Studio**můžete spustit následující skripty SQL.

Zde je skript SQL, který definuje funkci Distance.

```sql
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
```

Tady je příklad volání této funkce pro generování funkcí v dotazu SQL:

--Vzorový dotaz pro volání funkce pro vytvoření funkcí

   ```sql
SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
FROM <schemaname>.<nyctaxi_trip>
WHERE datepart("mi",pickup_datetime)=1
AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND pickup_longitude != '0' AND dropoff_longitude != '0'
   ```

**Výstup:** Tento dotaz vygeneruje tabulku (s 2 803 538 řádky) s využitím Pickup a dropoff Latitudes a zeměpisná délka a příslušné přímé vzdálenosti v mílích. Tady jsou výsledky pro první tři řádky:

| (Číslo řádku) | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40,731804 |-74,001083 |40,736622 |-73,988953 |.7169601222 |
| 2 |40,715794 |-74, 010635 |40,725338 |-74,00399 |.7448343721 |
| 3 |40,761456 |-73,999886 |40,766544 |-73,988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Příprava dat pro vytváření modelů
Následující dotaz se spojí s tabulkami **nyctaxi \_ TRIPS** a **nyctaxi \_ tarifs** , vygeneruje **binární popisek klasifikace**, který je na ní popsán, ** \_ třídu tipů**klasifikační klasifikace s více třídami a extrahuje ukázku z plné připojené datové sady. Vzorkování se provádí načtením podmnožiny cest na základě doby vyzvednutí.  Tento dotaz se dá zkopírovat a vložit přímo do modulu [Azure Machine Learning Studio (Classic)](https://studio.azureml.net) [Import dat]import[-data] pro příjem přímých dat z instance SQL Database v Azure. Dotaz vyloučí záznamy s nesprávnými souřadnicemi (0, 0).

```sql
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
```

Až budete připraveni přejít k Azure Machine Learning, může to mít jednu z těchto akcí:

1. Uložte konečný dotaz SQL pro extrakci a vzorkování dat a zkopírujte – vložte dotaz přímo do modulu import dat import[-data] v Azure Machine Learning nebo
2. Pochovejte ukázková a inženýrská data, která plánujete použít pro vytváření modelů v nové tabulce Azure synapse Analytics a použijte novou tabulku v modulu [Import dat][import-data] v Azure Machine Learning. Skript PowerShellu v předchozím kroku tuto úlohu dokončil za vás. Přímo z této tabulky si můžete přečíst v modulu import dat.

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Zkoumání dat a strojírenství funkcí v IPython poznámkovém bloku
V této části provedeme zkoumání dat a generování funkcí pomocí dotazů Pythonu a SQL oproti analýze Azure synapse, kterou jste vytvořili dříve. Do místního adresáře se stáhl ukázkový IPython Poznámkový blok s názvem **SQLDW_Explorations. ipynb** a soubor skriptu pythonu **SQLDW_Explorations_Scripts. py** . Jsou také k dispozici na [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Tyto dva soubory jsou ve skriptech Pythonu stejné. K dispozici je soubor skriptu Pythonu pro případ, že nemáte server IPython Poznámkový blok. Tyto dva ukázkové soubory Pythonu jsou navrženy v rámci **Python 2,7**.

V ukázkovém poznámkovém bloku IPython a staženém souboru skriptu Pythonu, který jste stáhli do vašeho místního počítače, jste předtím připojili skript PowerShellu do služby Azure synapse Analytics. Jsou spustitelné bez jakýchkoli úprav.

Pokud jste již nastavili pracovní prostor Azure Machine Learning, můžete přímo odeslat ukázkový Poznámkový blok IPython do služby IPython poznámkového bloku AzureML a spustit ho. Tady je postup, jak odeslat do služby IPython poznámkového bloku AzureML:

1. Přihlaste se ke svému pracovnímu prostoru Azure Machine Learning, v horní části klikněte na **Studio** a na levé straně webové stránky klikněte na **poznámkové bloky** .

    ![Klikněte na Studio a poznámkové bloky][22]
2. V levém dolním rohu webové stránky klikněte na **Nový** a vyberte **Python 2**. Pak zadejte název poznámkového bloku a kliknutím na značku zaškrtnutí vytvořte nový prázdný Poznámkový blok IPython.

    ![Klikněte na nový a vyberte Python 2.][23]
3. V levém horním rohu nového poznámkového bloku IPython klikněte na symbol **Jupyter** .

    ![Klikněte na symbol Jupyter][24]
4. Přetáhněte ukázkový Poznámkový blok IPython na stránku **stromu** služby IPython poznámkového bloku AzureML a klikněte na **nahrát**. Pak se ukázkový Poznámkový blok IPython nahraje do služby IPython poznámkového bloku AzureML.

    ![Klikněte na nahrát.][25]

Aby bylo možné spustit vzorový Poznámkový blok IPython nebo soubor skriptu Pythonu, jsou potřeba následující balíčky Pythonu. Pokud používáte službu IPython poznámkového bloku služby AzureML, tyto balíčky byly předem nainstalovány.

- pandas
- numpy
- matplotlib
- pyodbc
- PyTables

Při vytváření pokročilých analytických řešení na Azure Machine Learning s velkými objemy dat je tady Doporučené pořadí:

* Přečtěte si malý vzorek dat do datového rámce v paměti.
* Pomocí ukázkových dat provedete některé vizualizace a průzkumy.
* Experimentujte s inženýry funkcí pomocí ukázkových dat.
* V případě většího zkoumání dat, manipulace s daty a strojírenství funkcí použijte Python k vydávání dotazů SQL přímo proti analýze Azure synapse.
* Určete velikost vzorku, která bude vhodná pro Azure Machine Learning vytváření modelů.

Následují některé příklady zkoumání dat, vizualizace dat a techniků funkcí. Další průzkumy dat najdete v ukázkovém poznámkovém bloku IPython a v ukázkovém souboru skriptu Pythonu.

### <a name="initialize-database-credentials"></a>Inicializovat pověření databáze
Nastavení připojení k databázi inicializujte v těchto proměnných:

```sql
SERVER_NAME=<server name>
DATABASE_NAME=<database name>
USERID=<user name>
PASSWORD=<password>
DB_DRIVER = <database driver>
```

### <a name="create-database-connection"></a>Vytvoření připojení k databázi
Toto je připojovací řetězec, který vytváří připojení k databázi.

```sql
CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
conn = pyodbc.connect(CONNECTION_STRING)
```

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Vykázat počet řádků a sloupců v tabulce <nyctaxi_trip>

```sql
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
```

* Celkový počet řádků = 173179759
* Celkový počet sloupců = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>Vykázat počet řádků a sloupců v tabulce <nyctaxi_fare>

```sql
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
```

* Celkový počet řádků = 173179759
* Celkový počet sloupců = 11

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Ukázka malého data z databáze Azure synapse Analytics

```sql
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
```

Doba čtení ukázkové tabulky je 14,096495 sekund.
Počet načtených řádků a sloupců = (1000, 21).

### <a name="descriptive-statistics"></a>Popisné statistiky
Teď jste připraveni prozkoumat data s ukázkami. Začneme s prohlížením některých popisných statistik pro ** \_ vzdálenost na cestách** (nebo na všech ostatních polích, která se rozhodnete zadat).

```sql
df1['trip_distance'].describe()
```

### <a name="visualization-box-plot-example"></a>Vizualizace: příklad vykreslení boxu
V dalším kroku se podíváme na pole, ve kterém se bude dráha cesty vizualizovat quantiles.

```sql
df1.boxplot(column='trip_distance',return_type='dict')
```

![Výstup grafu Box][1]

### <a name="visualization-distribution-plot-example"></a>Vizualizace: příklad vykreslení distribuce
Vykreslení, které vizualizují rozdělení a histogram pro vzorové vzdálenosti cest.

```sql
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
df1['trip_distance'].hist(ax=ax2, bins=100, color='k')
```

![Výstup vykreslení distribuce][2]

### <a name="visualization-bar-and-line-plots"></a>Vizualizace: pruhové a spojnicové pruhy
V tomto příkladu vytvoříme cestu ke vzdálenosti do pěti přihrádek a vizualizujte výsledky binningu.

```sql
trip_dist_bins = [0, 1, 2, 4, 10, 1000]
df1['trip_distance']
trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
trip_dist_bin_id
```

Rozdělení přihrádky můžeme vykreslit v pruhovém nebo spojnicovém grafu pomocí:

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')
```

![Výstup pruhového grafu][3]

a

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')
```

![Výstup vykreslení čáry][4]

### <a name="visualization-scatterplot-examples"></a>Vizualizace: příklady scatterplot
Pro zjištění, zda existuje korelace, zobrazujeme ** \_ v grafu dobu provozu \_ v \_ sekundách** a na **služební \_ dráze** .

```sql
plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])
```

![Scatterplot výstup vztahu mezi časem a vzdáleností][6]

Podobně můžeme kontrolovat vztah mezi ** \_ kódem sazby** a ** \_ délkou cesty**.

```sql
plt.scatter(df1['passenger_count'], df1['trip_distance'])
```

![Scatterplot výstup vztahu mezi kódem a vzdáleností][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Zkoumání dat pro ukázková data pomocí dotazů SQL v poznámkovém bloku IPython
V této části prozkoumáme distribuci dat pomocí ukázkových dat, která jsou trvale vytvořená v nové tabulce, kterou jsme vytvořili výše. Podobné průzkumy je možné provádět pomocí původních tabulek.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Prozkoumávání: vyhlášení počtu řádků a sloupců v ukázkové tabulce

```sql
nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
print 'Number of rows in sample = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
print 'Number of columns in sample = %d' % ncols.iloc[0,0]
```

#### <a name="exploration-tippednot-tripped-distribution"></a>Průzkum: tripá/nedistribuce

```sql
query = '''
SELECT tipped, count(*) AS tip_freq
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY tipped
    '''

    pd.read_sql(query, conn)
```

#### <a name="exploration-tip-class-distribution"></a>Průzkum: distribuce třídy Tip

```sql
query = '''
    SELECT tip_class, count(*) AS tip_freq
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY tip_class
'''

tip_class_dist = pd.read_sql(query, conn)
```

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Průzkum: vykreslení pro distribuci tipů podle třídy

```sql
tip_class_dist['tip_freq'].plot(kind='bar')
```

![#26 vykreslení][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Průzkum: každodenní distribuce cest

```sql
query = '''
    SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY CONVERT(date, dropoff_datetime)
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-per-medallion"></a>Průzkum: distribuce cest na Medallion

```sql
query = '''
    SELECT medallion,count(*) AS c
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY medallion
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Průzkum: distribuce cest pomocí Medallion a licence pro napadení

```sql
query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
pd.read_sql(query,conn)
```

#### <a name="exploration-trip-time-distribution"></a>Průzkum: distribuce doby provozu

```sql
query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distance-distribution"></a>Průzkum: distribuce vzdálenosti cest

```sql
query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
pd.read_sql(query,conn)
```

#### <a name="exploration-payment-type-distribution"></a>Průzkum: rozdělení typu platby

```sql
query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
pd.read_sql(query,conn)
```

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Ověření konečného formuláře tabulky natrénuje

```sql
query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
pd.read_sql(query,conn)
```

## <a name="build-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Modely sestavení v Azure Machine Learning
Nyní je připraven pokračovat na sestavení modelu a nasazení modelu v [Azure Machine Learning](https://studio.azureml.net). Data jsou připravena k použití v některém z dříve zjištěných problémů, konkrétně:

1. **Binární klasifikace**: pro předpověď, zda byl pro cestu zaplacen Tip.
2. **Třída klasifikace s více třídami**: pro předpověď rozsahu zaplaceného tipu podle dříve definovaných tříd.
3. **Regresní úloha**: pro předpověď množství tipu placeného pro cestu.

Pokud chcete začít modelování, přihlaste se k pracovnímu prostoru **Azure Machine Learning (Classic)** . Pokud jste ještě nevytvořili pracovní prostor machine learningu, přečtěte si téma [Vytvoření pracovního prostoru Azure Machine Learning Studio (Classic)](../studio/create-workspace.md).

1. Pokud chcete začít s Azure Machine Learning, přečtěte si téma [co je Azure Machine Learning Studio (Classic)?](../studio/what-is-ml-studio.md)
2. Přihlaste se k [Azure Machine Learning Studio (Classic)](https://studio.azureml.net).
3. Domovská stránka Machine Learning Studio (Classic) poskytuje spoustu informací, videí, kurzů, odkazů na reference k modulům a dalších prostředků. Další informace o Azure Machine Learning najdete v [centru dokumentace Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Typický zkušební experiment se skládá z následujících kroků:

1. Vytvořte a **+ Nový** experiment.
2. Získat data do Azure Machine Learning Studio (Classic).
3. Proveďte předběžné zpracování, transformaci a manipulaci s daty podle potřeby.
4. Podle potřeby generujte funkce.
5. Rozdělit data na datové sady pro školení, ověřování/testování (nebo mít pro každý z nich samostatné datové sady).
6. Vyberte jeden nebo více algoritmů strojového učení v závislosti na problémech s učením při řešení. Například binární klasifikace, klasifikace s více třídami, regrese.
7. Proveďte výuku jednoho nebo více modelů pomocí školicí datové sady.
8. Vyhodnotí datovou sadu ověřování pomocí vycvičených modelů.
9. Vyhodnoťte modely pro výpočet relevantních metrik pro problém s učením.
10. Optimalizujte model (y) a vyberte nejlepší model, který chcete nasadit.

V tomto cvičení jsme už prozkoumali a provedli analýzu dat ve službě Azure synapse Analytics a rozhodli jste se, jak na základě velikosti vzorků ingestovat v Azure Machine Learning Studio (Classic). Zde je postup sestavení jednoho nebo více předpovědí modelů:

1. Získat data do Azure Machine Learning Studio (Classic) pomocí modulu [Import dat][import-data] , který je k dispozici v části **vstup a výstup dat** . Další informace najdete na referenční stránce [Import dat][import-data] Module.

    ![Import dat z Azure ML][17]
2. Na panelu **vlastnosti** vyberte možnost **Azure SQL Database** jako **zdroj dat** .
3. Do pole **název databázového serveru** zadejte název DNS databáze. Formátovat `tcp:<your_virtual_machine_DNS_name>,1433`
4. Do příslušného pole zadejte **název databáze** .
5. Zadejte *uživatelské jméno SQL* do pole **název uživatelského účtu serveru**a *heslo* v **hesle uživatelského účtu serveru**.
7. V textové oblasti **dotaz do databáze** vložte dotaz, který extrahuje potřebná databázová pole (včetně všech vypočítaných polí, jako jsou popisky), a dolů vyvzorkuje data do požadované velikosti vzorku.

Příklad binární klasifikace experimentu, který čte data přímo z databáze Azure synapse Analytics, je na následujícím obrázku (Nezapomeňte nahradit názvy tabulek nyctaxi_trip a nyctaxi_fare podle názvu schématu a názvů tabulek, které jste použili v návodu). Podobné experimenty lze vytvořit pro třídy s více třídami a regresní problémy.

![Výukový program Azure ML][10]

> [!IMPORTANT]
> V ukázkách dotazů pro extrakci a vzorkování dat modelování, které jsou uvedené v předchozích částech, **jsou v dotazu zahrnuté všechny popisky pro tři cvičení modelování**. Důležitým (vyžadovaným) krokem v každé cvičení modelování je **vyloučení** zbytečných popisků pro ostatní dva problémy a jakékoli jiné **cíle nevracení**. Například při použití binární klasifikace **použijte popisek,** který se zanechal a vyloučí pole ** \_ Třída Tip**, ** \_ hodnota tipu**a **celkovou \_ částku**. Tato druhá z nich nevrací cíle, protože implikuje Tip.
>
> Chcete-li vyloučit nepotřebné sloupce nebo nevrácené cíle, můžete použít modul [Vybrat sloupce v datové sadě][select-columns] nebo [Upravit metadata][edit-metadata]. Další informace najdete v tématu [Výběr sloupců v datové sadě][select-columns] a úpravy odkazů na [metadata][edit-metadata] .
>
>

## <a name="deploy-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Nasazení modelů v Azure Machine Learning
Když je model připravený, můžete ho snadno nasadit jako webovou službu přímo z experimentu. Další informace o nasazení webových služeb Azure ML najdete v tématu [nasazení webové služby Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).

K nasazení nové webové služby potřebujete:

1. Vytvořte experiment pro bodování.
2. Nasaďte webovou službu.

Pokud chcete vytvořit experiment bodování z **dokončeného** experimentu, klikněte na tlačítko **vytvořit bodování experimentu** v dolním panelu akcí.

![Bodování Azure][18]

Azure Machine Learning se pokusí vytvořit experiment bodování založený na komponentách zkušebního experimentu. Konkrétně to bude:

1. Uložte školicí model a odeberte moduly pro školení modelů.
2. Identifikujte logický **vstupní port** , který bude představovat očekávané schéma vstupních dat.
3. Identifikujte logický **výstupní port** , který bude představovat očekávané výstupní schéma webové služby.

Když se vytvoří experiment bodování, zkontrolujte výsledky a podle potřeby proveďte úpravy. Typickou úpravou je nahradit vstupní datovou sadu nebo dotaz s jedním, který vylučuje pole popisku, protože tato pole popisků nebudou při volání služby namapována na schéma. Je také vhodné omezit velikost vstupní datové sady nebo dotaz na několik záznamů, které jsou dostatečné pro indikaci vstupního schématu. V případě výstupního portu je běžné vyloučit všechna vstupní pole a zahrnout do výstupu pouze **popisky** s skóre a jejich **pravděpodobnosti** ve výstupu pomocí modulu [Výběr sloupců v datové sadě][select-columns] .

Na následujícím obrázku je k dispozici ukázkový experiment pro bodování. Až budete připraveni k nasazení, klikněte na tlačítko **publikovat webovou službu** na dolním panelu akcí.

![Publikování v Azure ML][11]

## <a name="summary"></a>Shrnutí
Abyste rekapitulacei, co jsme udělali v tomto výukovém kurzu, vytvořili jste prostředí pro datové vědy v Azure, které pracovalo s velkou veřejnou datovou sadou, provedli jsme to prostřednictvím vědeckého procesu pro týmovou práci, veškerého způsobu získávání dat a následného nasazení Azure Machine Learning webové služby.

### <a name="license-information"></a>Informace o licenci
Tento ukázkový návod a příslušné doprovodné skripty a IPython Poznámkový blok sdílí Microsoft v rámci licence MIT. Další podrobnosti najdete v souboru LICENSE.txt v adresáři ukázkového kódu na GitHubu.

## <a name="references"></a>Reference
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
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
