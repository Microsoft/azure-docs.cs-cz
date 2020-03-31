---
title: Škálovatelná datová věda s Azure Data Lake – proces týmové datové vědy
description: Jak používat Azure Data Lake k práci zkoumání dat a binární klasifikace úlohy na datové sady.
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
ms.openlocfilehash: 9409f14b20684afa1a39d45e663ff316f405cc97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717925"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Škálovatelná datová věda s Azure Data Lake: Komplexní návod
Tento návod ukazuje, jak pomocí Azure Data Lake provést zkoumání dat a binární klasifikace úkoly na ukázku nyc taxi výlet a tarif datové sady předpovědět, zda tip se platí jízdné. Provede vás kroky [procesu vědecké správy týmových dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), od začátku do konce, od získávání dat až po modelování školení a potom k nasazení webové služby, která publikuje model.

## <a name="technologies"></a>Technologie

Tyto technologie se používají v tomto návodu.
* Azure Data Lake Analytics
* U-SQL a Visual Studio
* Python
* Azure Machine Learning
* Scripts


### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
[Datové jezero Microsoft Azure](https://azure.microsoft.com/solutions/data-lake/) má všechny funkce potřebné k tomu, aby datoví vědci snadno ukládali data libovolné velikosti, tvaru a rychlosti a prováděli zpracování dat, pokročilou analýzu a modelování strojového učení s vysokou škálovatelností nákladově efektivním způsobem.   Platíte na základě práce, pouze v případě, že jsou data skutečně zpracovávána. Azure Data Lake Analytics zahrnuje U-SQL, jazyk, který spojuje deklarativní povahu SQL s expresivní výkon C# poskytovat škálovatelné možnosti distribuovaných dotazů. Umožňuje zpracovávat nestrukturovaná data použitím schématu při čtení, vložením vlastní logiky a uživatelem definovaných funkcí (UDF) a zahrnuje rozšiřitelnost umožňující jemně odstupňovanou kontrolu nad tím, jak provést ve velkém měřítku. Další informace o filozofii návrhu za U-SQL najdete v [článku Visual Studio blogu](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Data Lake Analytics je také klíčovou součástí Cortana Analytics Suite a spolupracuje s Azure SQL Data Warehouse, Power BI a Data Factory. Tato kombinace vám poskytuje kompletní cloudovou platformu pro velká objem dat a pokročilou analytickou platformu.

Tento návod začíná popisem, jak nainstalovat požadavky a prostředky, které jsou potřebné k dokončení úloh procesu datové vědy. Pak popisuje kroky zpracování dat pomocí U-SQL a na závěr ukazuje, jak používat Python a Hive s Azure Machine Learning Studio (klasické) k sestavení a nasazení prediktivní modely.

### <a name="u-sql-and-visual-studio"></a>U-SQL a Visual Studio
Tento návod doporučuje pomocí sady Visual Studio upravit skripty U-SQL ke zpracování datové sady. U-SQL skripty jsou popsány zde a jsou k dispozici v samostatném souboru. Proces zahrnuje ingestování, zkoumání a vzorkování dat. Také ukazuje, jak spustit úlohu skriptované u u-SQL z portálu Azure. Tabulky hive se vytvářejí pro data v přidruženém clusteru HDInsight, aby se usnadnilo vytváření a nasazování binárního klasifikačního modelu v Azure Machine Learning Studio.

### <a name="python"></a>Python
Tento návod také obsahuje část, která ukazuje, jak vytvořit a nasadit prediktivní model pomocí Pythonu s Azure Machine Learning Studio. Poskytuje poznámkový blok Jupyter se skripty Pythonu pro kroky v tomto procesu. Poznámkový blok obsahuje kód pro některé další kroky technické funkce a konstrukce modelů, jako je například klasifikace více tříd a regresní modelování kromě binárního klasifikačního modelu popsaného zde. Regresní úkol je předpovědět množství tip na základě jiných funkcí tip.

### <a name="azure-machine-learning"></a>Azure Machine Learning 
Azure Machine Learning Studio (classic) se používá k vytváření a nasazování prediktivních modelů pomocí dvou přístupů: nejprve se skripty Pythonu a pak s tabulkami Hive v clusteru HDInsight (Hadoop).

### <a name="scripts"></a>Scripts
V tomto návodu jsou popsány pouze hlavní kroky. Můžete si stáhnout úplný **U-SQL skript** a **Jupyter Notebook** z [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Požadavky
Než začnete tato témata, musíte mít následující:

* Předplatné Azure. Pokud ještě nemáte, najdete v tématu [získat azure bezplatnou zkušební verzi](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Doporučeno] Visual Studio 2013 nebo novější. Pokud ještě nemáte nainstalovanou některou z těchto verzí, můžete si stáhnout bezplatnou verzi komunity z [komunity sady Visual Studio](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> Místo Visual Studia můžete taky použít portál Azure k odesílání dotazů Azure Data Lake. Pokyny jsou k dispozici jak to udělat jak s Visual Studio a na portálu v části s názvem **Zpracování dat s U-SQL**.
>
>


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Příprava prostředí datové vědy pro Azure Data Lake
Chcete-li připravit prostředí datové vědy pro tento návod, vytvořte následující zdroje:

* Azure Úložiště datových jezer (ADLS)
* Azure Data Lake Analytics (ADLA)
* Účet úložiště objektů blob Azure
* Účet Azure Machine Learning Studio (klasický)
* Nástroje datového jezera Azure pro Visual Studio (doporučeno)

Tato část obsahuje pokyny k vytvoření jednotlivých těchto prostředků. Pokud se rozhodnete použít hive tabulky s Azure Machine Learning, místo Pythonu, k vytvoření modelu, musíte také zřídit cluster HDInsight (Hadoop). Tento alternativní postup popsaný v části možnost 2.


> [!NOTE]
> **Azure Data Lake Store** se dá vytvořit buď samostatně, nebo když jako výchozí úložiště vytvoříte Azure Data Lake **Analytics.** Pokyny jsou odkazovány pro vytváření jednotlivých těchto prostředků samostatně, ale účet úložiště datového jezera nemusí být vytvořeny samostatně.
>
>

### <a name="create-an-azure-data-lake-storage"></a>Vytvoření úložiště datových jezer Azure


Vytvořte ADLS z [webu Azure Portal](https://portal.azure.com). Podrobnosti najdete [v tématu Vytvoření clusteru HDInsight s Úložištěm datových jezer pomocí portálu Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Nezapomeňte nastavit identitu Clusteru AAD v okně **DataSource** v okně **Volitelné konfigurace** popsaného v této sadě.

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Vytvoření účtu Azure Data Lake Analytics
Vytvořte účet ADLA z [webu Azure Portal](https://portal.azure.com). Podrobnosti [najdete v tématu Výuka: začínáme s Azure Data Lake Analytics pomocí portálu Azure](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Vytvoření účtu úložiště objektů blob Azure
Vytvořte účet úložiště objektů blob Azure z [webu Azure Portal](https://portal.azure.com). Podrobnosti najdete v části Vytvoření účtu úložiště v [části O účtech Azure Storage](../../storage/common/storage-create-storage-account.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-classic-account"></a>Nastavení účtu Azure Machine Learning Studio (klasické)
Zaregistrujte se nebo do Azure Machine Learning Studio (klasické) ze stránky [studia Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) studio. Klikněte na tlačítko **Začínáme nyní** a pak zvolte "Volný pracovní prostor" nebo "Standardní pracovní prostor". Teď jste připraveni vytvářet experimenty ve studiu Azure Machine Learning.

### <a name="install-azure-data-lake-tools-recommended"></a>Instalace nástrojů Azure Data Lake [Doporučeno]
Nainstalujte nástroje Azure Data Lake Tools pro svou verzi Visual Studia z [Nástrojů datového jezera Azure pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Po dokončení instalace otevřete Visual Studio. V horní části by se měla zobrazit karta Datové jezero v nabídce. Vaše prostředky Azure by se měly zobrazit v levém panelu, když se přihlásíte ke svému účtu Azure.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>Datový soubor NYC Taxi Trips
Zde použitá sada dat je veřejně dostupná datová sada – [datová sada NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/). NYC Taxi Trip data se skládá z asi 20 GB komprimovaných souborů CSV (~ 48 GB nekomprimované), záznam více než 173 milionů jednotlivých cest a tarify zaplacené za každou cestu. Každý záznam cesty obsahuje místa a časy vyzvednutí a předání, anonymizované číslo licence hacku (řidiče) a číslo medailonu (jedinečné ID taxislužby). Údaje se týkají všech cest v roce 2013 a jsou uvedeny v následujících dvou datových souborech za každý měsíc:

CsV "trip_data" obsahuje podrobnosti o jízdě, jako je počet cestujících, místa vyzvednutí a předání, doba trvání cesty a délka cesty. Zde je několik ukázkových záznamů:

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



"trip_fare" CSV obsahuje podrobnosti o jízdném zaplacené za každou cestu, jako je typ platby, částka jízdného, příplatek a daně, tipy a mýtné a celková zaplacená částka. Zde je několik ukázkových záznamů:

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Jedinečný klíč pro\_připojení údajů\_o cestě a jízdného se skládá\_z\_následujících tří polí: medailon, hack licence a vyzvednutí datatime. Nezpracované soubory CSV lze přistupovat z objektu blob úložiště Azure. U-SQL skript pro toto spojení je v [části Připojit se k cestě a tarifu.](#join)

## <a name="process-data-with-u-sql"></a>Zpracování dat pomocí U-SQL
Úlohy zpracování dat znázorněné v této části zahrnují ingestování, kontrolu kvality, zkoumání a vzorkování dat. Jak se připojit k výletu a jízdné tabulky jsou také zobrazeny. Poslední část ukazuje spustit u-SQL skriptované úlohy z portálu Azure. Zde jsou odkazy na každou podsekci:

* [Přičtení dat: čtení v datech z veřejného objektu blob](#ingest)
* [Kontroly kvality údajů](#quality)
* [Zkoumání dat](#explore)
* [Připojte se k jízdě a tarifním stolům](#join)
* [Odběr vzorků dat](#sample)
* [Spuštění úloh U-SQL](#run)

U-SQL skripty jsou popsány zde a jsou k dispozici v samostatném souboru. Můžete si stáhnout úplné **U-SQL skripty** z [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Chcete-li spustit u-SQL, otevřít Visual Studio, klepněte na **soubor --> New --> Project**, zvolte **U-SQL Project**, název a uložte jej do složky.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> Je možné použít portál Azure ke spuštění U-SQL namísto Visual Studia. Můžete přejít na prostředek Azure Data Lake Analytics na portálu a odesílat dotazy přímo, jak je znázorněno na následujícím obrázku:
>
>

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="data-ingestion-read-in-data-from-public-blob"></a><a name="ingest"></a>Ingestování dat: Čtení v datech z veřejného objektu blob

Umístění dat v objektu blob Azure se odkazuje jako **\_wasb://container účet úložiště objektů blob\@\_\_\_název name.blob.core.windows.net/blob_name** a lze extrahovat pomocí **Extractors.Csv()**. Nahraďte svůj vlastní název kontejneru a\_název\@účtu\_\_úložiště\_v následujících skriptech pro název kontejneru název objektu blob název účtu účtu v adrese wasb. Vzhledem k tomu, že názvy souborů jsou ve stejném formátu, je možné použít **data\_\_\{\*\}cesty .csv** ke čtení ve všech 12 souborech cesty.

    ///Read in Trip data
    @trip0 =
        EXTRACT
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

Vzhledem k tomu, že v prvním řádku jsou záhlaví, je třeba odebrat záhlaví a změnit typy sloupců na příslušné. Zpracovaná data můžete uložit do Úložiště datových jezer Azure pomocí **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ nebo do účtu úložiště objektů Blob Azure pomocí **wasb://container_name\@blob_storage_account_name.blob.core.windows.net/blob_name**.

    // change data types
    @trip =
        SELECT
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        DateTime.Parse(pickup_datetime) AS pickup_datetime,
        DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
        Int32.Parse(passenger_count) AS passenger_count,
        Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
        Double.Parse(trip_distance) AS trip_distance,
        (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
        (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
        (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
        (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

    ////output data to ADL
    OUTPUT @trip
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
    USING Outputters.Csv();

    ////Output data to blob
    OUTPUT @trip
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
    USING Outputters.Csv();

Podobně si můžete přečíst v datových sadách tarifů. Klikněte pravým tlačítkem na Azure Data Lake Storage, můžete se podívat na vaše data na **webu Azure Portal --> Průzkumníka dat** nebo **Průzkumníka souborů** v rámci Visual Studia.

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="data-quality-checks"></a><a name="quality"></a>Kontroly kvality údajů
Po přečtení tabulek jízd a tarifů lze kontroly kvality dat provádět následujícím způsobem. Výsledné soubory CSV můžou být výstupní do úložiště objektů blob Azure nebo Azure Data Lake Storage.

Najděte počet medailonů a jedinečný počet medailonů:

    ///check the number of medallions and unique number of medallions
    @trip2 =
        SELECT
        medallion,
        vendor_id,
        pickup_datetime.Month AS pickup_month
        FROM @trip;

    @ex_1 =
        SELECT
        pickup_month,
        COUNT(medallion) AS cnt_medallion,
        COUNT(DISTINCT(medallion)) AS unique_medallion
        FROM @trip2
        GROUP BY pickup_month;
        OUTPUT @ex_1
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
    USING Outputters.Csv();

Najděte ty medailonky, které měly více než 100 cest:

    ///find those medallions that had more than 100 trips
    @ex_2 =
        SELECT medallion,
               COUNT(medallion) AS cnt_medallion
        FROM @trip2
        //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
        GROUP BY medallion
        HAVING COUNT(medallion) > 100;
        OUTPUT @ex_2
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
    USING Outputters.Csv();

Najděte tyto neplatné záznamy z hlediska pickup_longitude:

    ///find those invalid records in terms of pickup_longitude
    @ex_3 =
        SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
        FROM @trip
        WHERE
        pickup_longitude <- 90 OR pickup_longitude > 90;
        OUTPUT @ex_3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
    USING Outputters.Csv();

Najít chybějící hodnoty pro některé proměnné:

    //check missing values
    @res =
        SELECT *,
               (medallion == null? 1 : 0) AS missing_medallion
        FROM @trip;

    @trip_summary6 =
        SELECT
            vendor_id,
        SUM(missing_medallion) AS medallion_empty,
        COUNT(medallion) AS medallion_total,
        COUNT(DISTINCT(medallion)) AS medallion_total_unique
        FROM @res
        GROUP BY vendor_id;
    OUTPUT @trip_summary6
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
    USING Outputters.Csv();



### <a name="data-exploration"></a><a name="explore"></a>Zkoumání dat
Proveďte některé zkoumání dat s následujícími skripty, abyste získali lepší pochopení dat.

Najít rozdělení šikmé a non-hrotové výlety:

    ///tipped vs. not tipped distribution
    @tip_or_not =
        SELECT *,
               (tip_amount > 0 ? 1: 0) AS tipped
        FROM @fare;

    @ex_4 =
        SELECT tipped,
               COUNT(*) AS tip_freq
        FROM @tip_or_not
        GROUP BY tipped;
        OUTPUT @ex_4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
    USING Outputters.Csv();

Najděte rozdělení částky špičky s mezními hodnotami: 0, 5, 10 a 20 dolarů.

    //tip class/range distribution
    @tip_class =
        SELECT *,
               (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
        FROM @fare;
    @ex_5 =
        SELECT tip_class,
               COUNT(*) AS tip_freq
        FROM @tip_class
        GROUP BY tip_class;
        OUTPUT @ex_5
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
    USING Outputters.Csv();

Najít základní statistiky vzdálenosti jízdy:

    // find basic statistics for trip_distance
    @trip_summary4 =
        SELECT
            vendor_id,
            COUNT(*) AS cnt_row,
            MIN(trip_distance) AS min_trip_distance,
            MAX(trip_distance) AS max_trip_distance,
            AVG(trip_distance) AS avg_trip_distance
        FROM @trip
        GROUP BY vendor_id;
    OUTPUT @trip_summary4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
    USING Outputters.Csv();

Najděte percentily vzdálenosti jízdy:

    // find percentiles of trip_distance
    @trip_summary3 =
        SELECT DISTINCT vendor_id AS vendor,
                        PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
        FROM @trip;
       // group by vendor_id;
    OUTPUT @trip_summary3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
    USING Outputters.Csv();


### <a name="join-trip-and-fare-tables"></a><a name="join"></a>Připojte se k jízdě a tarifním stolům
Stoly s výletem a tarify mohou být spojeny medailonem, hack_license a pickup_time.

    //join trip and fare table

    @model_data_full =
    SELECT t.*,
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

    //// output to blob
    OUTPUT @model_data_full
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
    USING Outputters.Csv();

    ////output data to ADL
    OUTPUT @model_data_full
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
    USING Outputters.Csv();


Pro každou úroveň počtu cestujících vypočítejte počet záznamů, průměrnou částku špičky, rozptyl množství hrotu, procento vyklápěcích cest.

    // contingency table
    @trip_summary8 =
        SELECT passenger_count,
               COUNT(*) AS cnt,
               AVG(tip_amount) AS avg_tip_amount,
               VAR(tip_amount) AS var_tip_amount,
               SUM(tipped) AS cnt_tipped,
               (float)SUM(tipped)/COUNT(*) AS pct_tipped
        FROM @model_data_full
        GROUP BY passenger_count;
        OUTPUT @trip_summary8
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
    USING Outputters.Csv();


### <a name="data-sampling"></a><a name="sample"></a>Odběr vzorků dat
Nejprve náhodně vyberte 0,1 % dat ze společné tabulky:

    //random select 1/1000 data for modeling purpose
    @addrownumberres_randomsample =
    SELECT *,
            ROW_NUMBER() OVER() AS rownum
    FROM @model_data_full;

    @model_data_random_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_randomsample
    WHERE rownum % 1000 == 0;

    OUTPUT @model_data_random_sample_1_1000
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
    USING Outputters.Csv();

Pak se stratifikované vzorkování podle binární proměnné tip_class:

    //stratified random select 1/1000 data for modeling purpose
    @addrownumberres_stratifiedsample =
    SELECT *,
            ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
    FROM @model_data_full;

    @model_data_stratified_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_stratifiedsample
    WHERE rownum % 1000 == 0;
    //// output to blob
    OUTPUT @model_data_stratified_sample_1_1000
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv();
    ////output data to ADL
    OUTPUT @model_data_stratified_sample_1_1000
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv();


### <a name="run-u-sql-jobs"></a><a name="run"></a>Spuštění úloh U-SQL
Po úpravě skriptů U-SQL je můžete odeslat na server pomocí účtu Azure Data Lake Analytics. Klikněte na **Datové jezero**, **Odeslat úlohu**, vyberte **svůj účet Analytics**, zvolte **Paralelismus**a klikněte na **tlačítko Odeslat.**

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Pokud je úloha úspěšně splněna, stav úlohy se zobrazí v sadě Visual Studio pro monitorování. Po dokončení úlohy můžete dokonce přehrát proces provádění úloh a zjistit kroky kritického místa ke zlepšení efektivity úlohy. Můžete taky přejít na portál Azure a zkontrolovat stav úloh U-SQL.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Teď můžete zkontrolovat výstupní soubory v úložišti objektů Blob Azure nebo na webu Azure Portal. V dalším kroku použijte stratifikovaná ukázková data pro naše modelování.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Vytváření a nasazování modelů v Azure Machine Learning
K dispozici jsou dvě možnosti pro natáhněte data do Azure Machine Learning k sestavení a

* V první možnosti použijete ukázková data, která byla zapsána do objektu blob Azure (v kroku **vzorkování dat** výše) a pomocí Pythonu můžete vytvářet a nasazovat modely z Azure Machine Learning.
* V druhé možnosti dotaz u dat v Azure Data Lake přímo pomocí dotazu Hive. Tato možnost vyžaduje, abyste vytvořili nový cluster HDInsight nebo použili existující cluster HDInsight, kde tabulky Hive odkazují na data NY Taxi v Úložišti datových jezer Azure.  Obě tyto možnosti jsou popsány v následujících částech.

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Možnost 1: Použití Pythonu k vytváření a nasazování modelů strojového učení
Pokud chcete vytvářet a nasazovat modely strojového učení pomocí Pythonu, vytvořte poznámkový blok Jupyter na místním počítači nebo v Azure Machine Learning Studio. Jupyter notebook k dispozici na [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) obsahuje úplný kód prozkoumat, vizualizovat data, funkce inženýrství, modelování a nasazení. V tomto článku jsou pokryty pouze modelování a nasazení.

### <a name="import-python-libraries"></a>Import knihoven Pythonu
Aby bylo možné spustit ukázkový poznámkový blok Jupyter nebo soubor skriptu Pythonu, jsou potřeba následující balíčky Pythonu. Pokud používáte službu Poznámkový blok Azure Machine Learning, byly tyto balíčky předinstalované.

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random
    import sklearn
    from sklearn.linear_model import LogisticRegression
    from sklearn.cross_validation import train_test_split
    from sklearn import metrics
    from __future__ import division
    from sklearn import linear_model
    from azureml import services


### <a name="read-in-the-data-from-blob"></a>Čtení dat z objektu blob
* Připojovací řetězec

        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* Čtení jako text

        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)
* Přidání názvů sloupců a samostatných sloupců

        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* Změna některých sloupců na číselné

        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Vytváření modelů strojového učení
Zde vytvoříte binární klasifikační model, který předpovídá, zda je cesta nakloněna nebo ne. V Jupyter notebooku najdete další dva modely: vícetřídní klasifikace a regresní modely.

* Nejprve je třeba vytvořit fiktivní proměnné, které lze použít v modelech scikit-learn

        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* Vytvoření datového rámce pro modelování

        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])

        X = data.iloc[:,1:]
        Y = data.tipped
* Školení a testování 60-40 split

        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* Logistická regrese ve výcvikovém setu

        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)

       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Sada dat testování skóre

        Y_test_pred = logit_fit.predict(X_test)
* Vypočítat metriky vyhodnocení

        fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
        print fpr_train, tpr_train, thresholds_train

        fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred)
        print fpr_test, tpr_test, thresholds_test

        #AUC
        print metrics.auc(fpr_train,tpr_train)
        print metrics.auc(fpr_test,tpr_test)

        #Confusion Matrix
        print metrics.confusion_matrix(Y_train,Y_train_pred)
        print metrics.confusion_matrix(Y_test,Y_test_pred)

       ![c2](./media/data-lake-walkthrough/c2-py-logit-evaluation.PNG)

### <a name="build-web-service-api-and-consume-it-in-python"></a>Vytvoření rozhraní API webové služby a jeho využití v Pythonu
Chcete zprovoznit model strojového učení po jeho sestroj. Jako příklad se zde používá binární logistický model. Ujistěte se, že verze scikit-learn ve vašem místním počítači je 0.15.1 (Azure Machine Learning Studio je už alespoň v této verzi).

* Najděte si svoje přihlašovací údaje pracovního prostoru z nastavení Azure Machine Learning Studio (klasické). V Azure Machine Learning Studio klikněte na **Nastavení** --> **tokeny autorizace****názvu** --> .

    ![c3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

* Vytvořit webovou službu

        @services.publish(workspaceid, auth_token)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)
* Získání přihlašovacích údajů webové služby

        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key

        print url
        print api_key

        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass
* Volání rozhraní API webové služby. Obvykle počkejte 5-10 sekund po předchozím kroku.

        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Možnost 2: Vytváření a nasazování modelů přímo v Azure Machine Learning
Azure Machine Learning Studio (classic) můžete číst data přímo z Azure Data Lake Storage a pak se používá k vytváření a nasazování modelů. Tento přístup používá tabulku Hive, která odkazuje na Azure Data Lake Storage. Pro tabulku Hive je potřeba zřídit samostatný cluster Azure HDInsight. 

### <a name="create-an-hdinsight-linux-cluster"></a>Vytvoření linuxového clusteru HDInsight
Vytvořte cluster HDInsight (Linux) z [webu Azure Portal](https://portal.azure.com). Podrobnosti najdete v **tématu Vytvoření clusteru HDInsight s přístupem k úložišti Azure Data Lake Storage** v [tématu Vytvoření clusteru HDInsight s Úložištěm datových jezer pomocí portálu Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)Portal .

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Vytvořit tabulku Hive v HDInsightu
Teď vytvoříte hive tabulky, které se použijí v Azure Machine Learning Studio (klasické) v clusteru HDInsight pomocí dat uložených v Azure Data Lake Storage v předchozím kroku. Přejděte do vytvořeného clusteru HDInsight. Klikněte na **Nastavení** --> **Vlastnosti** --> **clusteru AAD Identity** --> **ADLS Access**, ujistěte se, že váš účet Azure Data Lake Storage je přidán do seznamu s právy pro čtení, zápis a spouštění.

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Pak klikněte na **Řídicí panel** vedle tlačítka **Nastavení** a objeví se okno. V pravém horním rohu stránky klikněte na **Zobrazení úlu** a měl by se zobrazit **Editor dotazů**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Vložte do následujících skriptů Hive a vytvořte tabulku. Umístění zdroje dat je v azure data lake storage odkaz tímto způsobem: **adl://data_lake_store_name.azuredatalakestore.net:443/folder_name/file_name**.

    CREATE EXTERNAL TABLE nyc_stratified_sample
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string,
      payment_type string,
      fare_amount string,
      surcharge string,
      mta_tax string,
      tolls_amount string,
      total_amount string,
      tip_amount string,
      tipped string,
      tip_class string,
      rownum string
      )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';


Po dokončení dotazu byste měli vidět výsledky takto:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Vytváření a nasazování modelů v Azure Machine Learning Studiu
Teď jste připraveni k sestavení a nasazení modelu, který předpovídá, zda se tip platí pomocí Azure Machine Learning. Stratifikovaná ukázková data jsou připravena k použití v tomto problému binární klasifikace (tip or not). Prediktivní modely pomocí klasifikace více tříd (tip_class) a regrese (tip_amount) lze také sestavit a nasadit pomocí Azure Machine Learning Studio, ale tady je zobrazenpouze, jak zacházet s případem pomocí binární klasifikační model.

1. Získejte data do Azure Machine Learning Studio (klasické) pomocí modulu **Import dat,** který je k dispozici v části **Vstup a výstup dat.** Další informace naleznete na referenční stránce [modulu Import dat.](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/)
2. V panelu **Vlastnosti** vyberte **Dotaz na úl** jako zdroj **dat.**
3. Vložit následující skript Hive v editoru **dotazů databáze Hive**

        select * from nyc_stratified_sample;
4. Zadejte identifikátor URI clusteru HDInsight (tento identifikátor URI najdete na webu Azure Portal), přihlašovací údaje Hadoop, umístění výstupních dat a název účtu úložiště Azure/název klíče/kontejneru.

   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)

Příklad experimentu binární klasifikace čtení dat z tabulky Hive je znázorněn na následujícím obrázku:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

Po vytvoření experimentu klepněte na tlačítko Nastavit**prediktivní webovou službu** **webové služby webové služby.** --> 

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Spusťte automaticky vytvořený experiment vyhodnocování, po jeho dokončení klikněte na **Nasadit webovou službu.**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Řídicí panel webové služby se krátce zobrazí:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Souhrn
Dokončením tohoto návodu jste vytvořili prostředí datové vědy pro vytváření škálovatelných komplexních řešení v Azure Data Lake. Toto prostředí bylo použito k analýze velké veřejné datové sady, přičemž ji prostřednictvím kanonické kroky procesu datové vědy, od získávání dat přes model školení a potom nasazení modelu jako webové služby. U-SQL byl použit ke zpracování, prozkoumání a vzorkování dat. Python a Hive se používaly s Azure Machine Learning Studio (klasické) k vytváření a nasazování prediktivních modelů.

## <a name="whats-next"></a>Co dále?
Studijní program pro [proces vědecké ho procesu týmových dat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) obsahuje odkazy na témata popisující každý krok v procesu pokročilé analýzy. Na stránce návodů pro [vědecký proces týmových dat](walkthroughs.md) je rozepsána řada návodů, které ukazují, jak používat prostředky a služby v různých scénářích prediktivní analýzy:

* [Proces vědecké ho procesu týmových dat v akci: pomocí datového skladu SQL](sqldw-walkthrough.md)
* [Proces vědecké ho spoje týmových dat v akci: použití clusterů HDInsight Hadoop](hive-walkthrough.md)
* [Proces vědecké ho procesu týmových dat: pomocí serveru SQL Server](sql-walkthrough.md)
* [Přehled procesu datové vědy pomocí Spark na Azure HDInsight](spark-overview.md)
