---
title: Škálovatelné datových věd s využitím Azure Data Lake - vědecké zpracování týmových dat
description: Jak provádět data průzkumu a binární klasifikace na datové sadě pomocí Azure Data Lake.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717925"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Škálovatelné vědecké zkoumání dat s Azure Data Lake: návod začátku do konce
Tento návod ukazuje, jak pomocí Azure Data Lake a zkoumání dat a úlohy binární klasifikace na vzorek cesty taxíkem NYC jízdenky datovou sadu, která předpovědět, jestli je tip zaplaceno tarif. Provede vás kroky [vědeckého procesu pro týmovou analýzu dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), od získání dat až po vyřízení modelu a pak nasazení webové služby, která tento model zveřejňuje.

## <a name="technologies"></a>Technologie

Tyto technologie se používají v tomto průvodci.
* Azure Data Lake Analytics
* Jazyk U-SQL a sady Visual Studio
* Python
* Azure Machine Learning
* Scripts


### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
U [Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) je potřeba, aby odborníci na data mohli snadno ukládat data libovolné velikosti, tvaru a rychlosti a provádět zpracování dat, pokročilou analýzu a modelování strojového učení s vysokou škálovatelností a nákladově efektivním způsobem.   Platíte na základě za úlohu pouze v případě, že data ve skutečnosti zpracovávají. Azure Data Lake Analytics zahrnuje U-SQL, jazyka, která spojuje deklarativní charakter jazyka SQL a výrazové Možnosti C# k poskytování škálovatelných distribuovaných možnosti dotazů. Umožňuje zpracování nestrukturovaných dat s použitím schéma při čtení, vložit vlastní logiku a uživatelem definované funkce (UDF) a zahrnuje rozšíření k povolení potřebujete jemněji odstupňované kontroly nad tom, jak spustit ve velkém měřítku. Další informace o návrhu filozofie za U-SQL najdete v příspěvku na [blogu sady Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Služba Data Lake Analytics je také klíčovou součástí Cortana Analytics Suite a spolupracuje se službami Azure SQL Data Warehouse, Power BI a Data Factory. Tato kombinace nabízí kompletní cloudovou platformu pro velké objemy dat a pokročilé analýzy.

Tento názorný postup začíná popisující, jak nainstalovat požadavky a prostředky, které jsou vyžadovány k dokončení procesu úloh datových věd. Pak popisuje kroky zpracování dat pomocí U-SQL a končí tím, že ukazuje, jak používat Python a podregistr s Azure Machine Learning Studio (Classic) k sestavování a nasazování prediktivních modelů.

### <a name="u-sql-and-visual-studio"></a>Jazyk U-SQL a sady Visual Studio
Tento názorný postup doporučuje používat Visual Studio k úpravě skriptů U-SQL ke zpracování datové sady. Skripty U-SQL jsou zde popsané a k dispozici v samostatném souboru. Proces zahrnuje ingestování, zkoumat a vzorkování data. Také ukazuje, jak spustit úlohu skripty U-SQL na webu Azure Portal. Tabulek Hive jsou vytvářeny pro data v clusteru HDInsight přidružené usnadňuje vytváření a nasazování binární klasifikační model v Azure Machine Learning Studio.

### <a name="python"></a>Python
Tento názorný postup obsahuje také oddíl, který ukazuje, jak vytvářet a nasazovat prediktivní model použití Pythonu s Azure Machine Learning Studio. Poznámkový blok Jupyter pomocí skriptů Python poskytuje pokyny v tomto procesu. Poznámkový blok obsahuje kód pro některé další funkce engineering kroky a modely konstrukce například klasifikace víc tříd a modelování kromě binární klasifikační model podle zde uvedeného regrese. Regrese úkolem je předpovědět, částky spropitného založených na jiných funkcích tip.

### <a name="azure-machine-learning"></a>Azure Machine Learning 
Azure Machine Learning Studio (Classic) se používá k sestavování a nasazování prediktivních modelů pomocí dvou přístupů: nejdřív pomocí skriptů v Pythonu a potom s tabulkami podregistru v clusteru HDInsight (Hadoop).

### <a name="scripts"></a>Scripts
Pouze hlavní kroky jsou popsané v tomto názorném postupu. Úplný **skript U-SQL** si můžete stáhnout a **Jupyter notebook** z [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Předpoklady
Před zahájením těchto témat, musíte mít následující:

* Předplatné Azure. Pokud ho ještě nemáte, přečtěte si téma [získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Doporučuje] Visual Studio 2013 nebo novější. Pokud ještě nemáte nainstalovanou některou z těchto verzí, můžete si stáhnout bezplatnou verzi komunity z [Visual Studio Community](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> Místo sady Visual Studio můžete také použít na webu Azure portal k zadávání dotazů Azure Data Lake. Pokyny k tomu, jak postupovat v rámci sady Visual Studio i na portálu v části s názvem **zpracování dat pomocí u-SQL**.
>
>


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Příprava prostředí pro datové vědy pro službu Azure Data Lake
Příprava prostředí pro datové vědy v tomto návodu, vytvořte následující prostředky:

* Azure Data Lake Storage (ADLS)
* Azure Data Lake Analytics (ADLA)
* Účet služby Azure Blob storage
* Účet Azure Machine Learning Studio (Classic)
* Azure Data Lake Tools pro Visual Studio (doporučeno)

Tato část obsahuje informace o tom, jak vytvořit každý z těchto prostředků. Pokud zvolíte použití tabulek Hive pomocí Azure Machine Learning, namísto Pythonu, vytvoříte model, budete potřebovat ke zřízení clusteru služby HDInsight (Hadoop). Tento alternativní postup popsaný v části Možnosti 2.


> [!NOTE]
> **Azure Data Lake Store** lze vytvořit buď samostatně, nebo při vytváření **Azure Data Lake Analytics** jako výchozího úložiště. Pokyny jsou odkazovány pro každý z těchto prostředků vytváření samostatně, ale nemusí samostatně vytvoření účtu úložiště Data Lake.
>
>

### <a name="create-an-azure-data-lake-storage"></a>Vytvoření Azure Data Lake Storage


Vytvoří ADLS z [Azure Portal](https://portal.azure.com). Podrobnosti najdete v tématu [Vytvoření clusteru HDInsight s Data Lake Store pomocí Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že jste nastavili identitu AAD clusteru v okně **zdroje dat** v **volitelném okně Konfigurace** popsané zde.

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Vytvoření účtu Azure Data Lake Analytics
Vytvořte z [Azure Portal](https://portal.azure.com)účet ADLA. Podrobnosti najdete v tématu [kurz: Začínáme s Azure Data Lake Analytics pomocí Azure Portal](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Vytvoření účtu úložiště objektů Blob v Azure
Vytvořte účet úložiště objektů BLOB v Azure z [Azure Portal](https://portal.azure.com). Podrobnosti najdete v části Vytvoření účtu úložiště v tématu [informace o Azure Storage účtech](../../storage/common/storage-create-storage-account.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-classic-account"></a>Nastavení účtu Azure Machine Learning Studio (Classic)
Zaregistrujte se do Azure Machine Learning Studio (Classic) na stránce [Azure Machine Learning studia](https://azure.microsoft.com/services/machine-learning/) . Klikněte na tlačítko **začít hned** a pak zvolte "bezplatný pracovní prostor" nebo "standardní pracovní prostor". Teď jste připraveni vytvořit experimenty v Azure Machine Learning Studiu.

### <a name="install-azure-data-lake-tools-recommended"></a>Instalace nástrojů Azure Data Lake [doporučuje]
Nainstalujte nástroje Azure Data Lake pro vaši verzi sady Visual Studio z [nástroje Azure Data Lake pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Po dokončení instalace otevřete aplikaci Visual Studio. Měli byste vidět Data Lake kartu v nabídce v horní části. Prostředky Azure se zobrazí v levém panelu po přihlášení k účtu Azure.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>Cesty taxíkem NYC datové sady
Datová sada, která se tady používá, je veřejně dostupná datová sada – [datová sada NYC taxislužby TRIPS](https://www.andresmh.com/nyctaxitrips/). Data o jízdách taxislužby NYC se skládá z přibližně 20 GB komprimované soubory CSV (nekomprimovaný ~ 48 GB), záznam 173 milionů jednotlivé trips a tarify placené pro každou cestu. Každý záznam o jízdách zahrnuje sbírat míčky a dropoff umístění a čas, číslo řidičského anonymizované hack (ovladače) a číslo Medailon (jedinečné ID taxislužby.). Data v roce 2013 zahrnuje všechny cesty a je dostupné pro každý měsíc následující dvě datové sady:

"Trip_data" CSV obsahuje podrobnosti o jízdách, jako je třeba počet cestujících, vyzvednutí a dropoff body, doba trvání cesty a délka cesty. Tady je několik ukázkových záznamů:

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



"trip_fare" CSV obsahuje podrobné informace o tarif placené pro každou cestu, například typ platby, velikost tarif, příplatek za a daní, tipy a mýtné a celkové částky zaplacené. Tady je několik ukázkových záznamů:

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Jedinečný klíč pro připojení cest\_dat a cest\_tarif se skládá z následujících tří polí: Medallion, napadení\_licence a vyzvednutí\_data a času. K nezpracovaným souborům CSV se dá dostat z Azure Storageého objektu BLOB. Skript U-SQL pro toto spojení je v části [připojení a tabulky tarifů](#join) .

## <a name="process-data-with-u-sql"></a>Zpracování dat pomocí U-SQL
Úlohy zpracování dat v této části patří ingestovat, kontrola kvality, zkoumat a vzorkování data. Jak o jízdách a tarif tabulky mají spojit se navíc zobrazí. Koncová část ukazuje spuštění úlohu U-SQL skriptů na webu Azure Portal. Tady jsou odkazy na každém pododdílu:

* [Přijímání dat: čtení dat z veřejného objektu BLOB](#ingest)
* [Kontroly kvality dat](#quality)
* [Zkoumání dat](#explore)
* [Připojit tabulky pro cestu a tarify](#join)
* [Vzorkování dat](#sample)
* [Spuštění úloh U-SQL](#run)

Skripty U-SQL jsou zde popsané a k dispozici v samostatném souboru. Všechny **skripty U-SQL** si můžete stáhnout z [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Chcete-li spustit U-SQL, spusťte aplikaci Visual Studio, klikněte na **soubor--> nový--> projekt**, vyberte **projekt U-SQL**, název a uložte jej do složky.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> Je možné použít ke spuštění U-SQL místo sady Visual Studio na webu Azure Portal. Můžete přejít k prostředku Azure Data Lake Analytics na portálu a odesílání dotazů přímo jako ukázáno na následujícím obrázku:
>
>

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>Přijímání dat: čtení dat z veřejného objektu BLOB

Umístění dat v objektu blob Azure je odkazováno jako **wasb://container\_název\@objektu blob\_storage\_account\_Name.blob.Core.Windows.NET/blob_name** a lze ho extrahovat pomocí **extraktorů. CSV ()** . Použijte název vlastního kontejneru a název účtu úložiště v následujících skriptech pro\_název kontejneru\@BLOB\_Storage\_\_název účtu na adrese wasb. Vzhledem k tomu, že jsou názvy souborů ve stejném formátu, je možné použít **\_dat pro cestu\_\{\*\}. csv** ke čtení ve všech 12 cestách souborů.

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

Protože je v prvním řádku záhlaví, budete muset odebrat záhlaví a změňte typ sloupce na odpovídající značky. Zpracovaná data můžete buď Uložit do Azure Data Lake Storage pomocí **swebhdfs://data_lake_storage_name. azuredatalakestorage. NET/Folder_name/file_name**_ nebo do účtu Azure Blob Storage pomocí **wasb://container_name\@blob_storage_account_name. blob. Core. Windows. NET/blob_name**.

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

Podobně můžete číst v datových sadách tarif. Klikněte pravým tlačítkem na Azure Data Lake Storage, můžete se podívat na data v **Azure Portal--> Průzkumník dat** nebo v **Průzkumníkovi souborů** v sadě Visual Studio.

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="quality"></a>Kontroly kvality dat
Po cesty a tarif tabulky byly načteny v, kontrolu kvality dat lze provést následujícím způsobem. Výsledné soubory CSV můžou být výstupem do služby Azure Blob Storage nebo Azure Data Lake Storage.

Najdete číslo medallions a jedinečného počtu medallions:

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

Vyhledejte tyto medallions, do kterých se více než 100 zkracuje dobu odezvy:

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

Vyhledejte tyto neplatné záznamy z hlediska pickup_longitude:

    ///find those invalid records in terms of pickup_longitude
    @ex_3 =
        SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
        FROM @trip
        WHERE
        pickup_longitude <- 90 OR pickup_longitude > 90;
        OUTPUT @ex_3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
    USING Outputters.Csv();

Najdete chybějící hodnoty pro některé proměnné:

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



### <a name="explore"></a>Zkoumání dat
Proveďte některé zkoumání dat pomocí těchto skriptů pro lepší pochopení dat.

Vyhledejte distribuční šikmý a šikmý zkracuje dobu odezvy:

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

Vyhledejte distribuční tip velikost mezní hodnoty: 0, 5, 10 a 20 dolarech.

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

Najdete základní statistické údaje o jízdách vzdálenosti:

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

Najdete percentily vzdálenost o jízdách:

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


### <a name="join"></a>Připojit tabulky pro cestu a tarify
Medailon, hack_license a pickup_time lze spojit cesty a tarif tabulky.

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


Pro každou úroveň počet cestujících Vypočítejte počet záznamů, tip Průměrná velikost, odchylka velikost špičky, procento šikmý zkracuje dobu odezvy.

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


### <a name="sample"></a>Vzorkování dat
První, náhodně vyberte 0,1 % dat z tabulky připojené k doméně:

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

Pak už si vzorkování podle binární tip_class proměnné:

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


### <a name="run"></a>Spuštění úloh U-SQL
Po úpravě skriptů U-SQL je můžete odeslat na server pomocí účtu Azure Data Lake Analytics. Klikněte na **Data Lake**, **Odeslat úlohu**, vyberte svůj **účet Analytics**, zvolte **paralelismus**a klikněte na tlačítko **Odeslat** .

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Když je úloha úspěšně splněny, zobrazí se stav úlohy pro monitorování v sadě Visual Studio. Po dokončení úlohy můžete dokonce znovu přehrát proces provádění úlohy a zjistit, jaké kroky je potřeba ke zlepšení efektivity úlohy. Může taky přejít na webu Azure portal a zkontrolujte stav úloh U-SQL.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Nyní můžete zkontrolovat výstupních souborů Azure Blob storage nebo Azure portal. Použití vrstveného ukázková data pro naše modelování v dalším kroku.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Vytváření a nasazování modelů ve službě Azure Machine Learning
Dvě možnosti jsou k dispozici pro přetáhnutí dat do Azure Machine Learning k vytváření a

* V první možnosti použijete ukázková data zapsaná do objektu blob Azure (v kroku **vzorkování dat** výše) a pomocí Pythonu sestavíte a nasadíte modely z Azure Machine Learning.
* V druhé možnosti dotazů data ve službě Azure Data Lake přímo pomocí dotazů Hive. Tato možnost vyžaduje vytvoření nového clusteru HDInsight nebo použijte existujícího clusteru HDInsight, kde tabulek Hive přejděte data taxislužby NY ve službě Azure Data Lake Storage.  Obě tyto možnosti jsou popsány v následujících částech.

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Možnost 1: Použití Pythonu k vytvoření a nasazení modelů strojového učení
Pokud chcete sestavovat a nasazovat modely machine learningu pomocí Pythonu, vytvoření poznámkového bloku Jupyter na svém místním počítači nebo v Azure Machine Learning Studio. Jupyter Notebook poskytované na [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) obsahuje úplný kód pro zkoumání, vizualizaci, vytváření funkcí, modelování a nasazení. V tomto článku se vztahují jenom modelování a nasazení.

### <a name="import-python-libraries"></a>Importovat knihovny jazyka Python
Chcete-li spustit ukázku soubor Pythonu následující balíčky jsou nutné skriptu Poznámkový blok Jupyter nebo Python. Pokud používáte službu Azure Machine Learning Poznámkový blok, tyto balíčky byly předem nainstalovány.

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


### <a name="read-in-the-data-from-blob"></a>Pro čtení dat z objektu blob
* Připojovací řetězec

        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* Přečíst jako text

        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)
* Přidat názvy sloupců a oddělení sloupců

        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* Změna některých sloupců na číselný datový typ

        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Vytváření modelů strojového učení
Tady vytvoříte binární klasifikační model k predikci, jestli je nebo není šikmý cesty. Do poznámkového bloku Jupyter můžete najít další dva modely: klasifikace víc tříd a regresních modelů.

* Nejdřív je potřeba vytvořit fiktivního proměnné, které lze použít v scikit-další modely

        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* Vytvoření datového rámce pro modelování

        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])

        X = data.iloc[:,1:]
        Y = data.tipped
* Trénování a testování 60 40 rozdělení

        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* Logistické Regrese v sadě školení

        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)

       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Skóre testovací datové sady

        Y_test_pred = logit_fit.predict(X_test)
* Výpočet metrik

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

### <a name="build-web-service-api-and-consume-it-in-python"></a>Sestavení webového rozhraní API služby a používat ji v Pythonu
Chcete pro zprovoznění modelu strojového učení po byl sestaven. Binární modelu logistické slouží jako příklad. Ujistěte se, že verze scikit-učení v místním počítači je 0.15.1 (Azure Machine Learning Studio je už aspoň v této verzi).

* Vyhledejte přihlašovací údaje k pracovnímu prostoru z nastavení Azure Machine Learning Studio (Classic). V Azure Machine Learning Studio klikněte na **nastavení** --> **název** --> **autorizační tokeny**.

    ![c3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

* Vytvoření webové služby

        @services.publish(workspaceid, auth_token)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)
* Získání přihlašovacích údajů pro webové služby

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

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Možnost 2: Vytvoření a nasazení modelů přímo ve službě Azure Machine Learning
Azure Machine Learning Studio (Classic) mohou číst data přímo z Azure Data Lake Storage a pak je použít k vytvoření a nasazení modelů. Tento přístup používá tabulku podregistru, která odkazuje na Azure Data Lake Storage. Pro tabulku podregistru se musí zřídit samostatný cluster Azure HDInsight. 

### <a name="create-an-hdinsight-linux-cluster"></a>Vytvoření clusteru HDInsight s Linuxem
Vytvořte cluster HDInsight (Linux) z [Azure Portal](https://portal.azure.com). Podrobnosti najdete v části **Vytvoření clusteru HDInsight s přístupem k Azure Data Lake Storage** v tématu [Vytvoření clusteru HDInsight s Data Lake Store pomocí Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Vytvoří tabulku Hive v HDInsight
Nyní vytváříte tabulky podregistru, které se mají použít v Azure Machine Learning Studio (Classic) v clusteru HDInsight pomocí dat uložených v Azure Data Lake Storage v předchozím kroku. Přejděte k vytvoření clusteru HDInsight. Klikněte na **nastavení** --> **vlastnosti** --> **clusteru AAD** --> **přístup k adls**, ujistěte se, že se Váš účet Azure Data Lake Storage přidal v seznamu s právy pro čtení, zápis a spouštění.

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Pak klikněte na **řídicí panel** vedle tlačítka **Nastavení** a zobrazí se okno se stavem. V pravém horním rohu stránky klikněte na **zobrazení podregistru** a měli byste vidět **Editor dotazů**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Vložte následující skriptů Hive k vytvoření tabulky. Umístění zdroje dat je v Azure Data Lake Storage odkaz tímto způsobem: **ADL://data_lake_store_name. azuredatalakestore. NET: 443/Folder_name/file_name**.

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


Po dokončení dotazu byste měli vidět podobné výsledky:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Vytváření a nasazování modelů v Azure Machine Learning Studio
Nyní jste připraveni k sestavení a nasazení modelu pro předpověď, jestli tip jde placenou službou Azure Machine Learning. Vrstveného ukázková data připravená pro použití v tomto binární klasifikace (tip nebo ne) problém. Prediktivní modely pomocí klasifikace víc tříd (tip_class) a regrese (tip_amount) také dají vytvořit a nasadit pomocí Azure Machine Learning Studio, ale tady je zobrazen pouze způsob zpracování případu použití binární klasifikační model.

1. Získat data do Azure Machine Learning Studio (Classic) pomocí modulu **Import dat** , který je k dispozici v části **vstup a výstup dat** . Další informace najdete na referenční stránce [Import datových modulů](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) .
2. Na panelu **vlastnosti** vyberte možnost **dotaz na podregistr** jako **zdroj dat** .
3. Vložte následující skript podregistru do editoru **dotazů databáze podregistru** .

        select * from nyc_stratified_sample;
4. Zadejte identifikátor URI clusteru HDInsight (Tento identifikátor URI najdete v Azure Portal), přihlašovací údaje Hadoop, umístění výstupních dat a název účtu, klíč nebo kontejner pro Azure Storage.

   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)

Příklad čtení binární klasifikace: experiment, data z tabulky Hive je zobrazena na následujícím obrázku:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

Po vytvoření experimentu klikněte na **nastavit webovou službu** --> **prediktivní webovou službu** .

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Spusťte experiment automaticky vytvořeného bodování, po jeho dokončení klikněte na **nasadit webovou službu** .

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Krátce se zobrazí řídicím panelu webové služby:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Souhrn
Po dokončení tohoto návodu jste vytvořili prostředí pro datové vědy pro vytváření škálovatelných komplexních řešení v Azure Data Lake. Toto prostředí byl použit k analýze velkých veřejné datové sady, jejího canonical kroky vědecké zpracování dat, od získání dat prostřednictvím trénování modelu a nasazení modelu jako webové služby. U-SQL bylo použito ke zpracování, prozkoumávání a vzorkování dat. Python a podregistr se použily s Azure Machine Learning Studio (Classic) k vytváření a nasazování prediktivních modelů.

## <a name="whats-next"></a>Co dále?
Postup výuky pro [vědecké zpracování týmových dat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) obsahuje odkazy na témata popisující jednotlivé kroky procesu pokročilé analýzy. Na stránce průvodce [vědeckým zpracováním týmových dat](walkthroughs.md) najdete řadu návodů, které prezentují použití prostředků a služeb v různých scénářích prediktivní analýzy:

* [Vědecké zpracování týmových dat v akci: použití SQL Data Warehouse](sqldw-walkthrough.md)
* [Vědecké zpracování týmových dat v akci: používání clusterů HDInsight Hadoop](hive-walkthrough.md)
* [Vědecké zpracování týmových dat: použití SQL Server](sql-walkthrough.md)
* [Přehled procesu pro datové vědy pomocí Sparku v Azure HDInsight](spark-overview.md)
