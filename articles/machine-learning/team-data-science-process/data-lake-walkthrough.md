---
title: 'Škálovatelné vědecké zpracování dat pomocí Azure Data Lake: návod začátku do konce | Microsoft Docs'
description: Jak používat Azure Data Lake udělat zkoumání a binární klasifikace úlohy dat na datovou sadu.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 91a8207f-1e57-4570-b7fc-7c5fa858ffeb
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev
ms.openlocfilehash: 6d0f889e1cc76eced172d66755a0a9275e6b7bdf
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Škálovatelné vědecké zpracování dat pomocí Azure Data Lake: návod začátku do konce
Tento návod ukazuje, jak používat Azure Data Lake a zkoumání dat a binární klasifikace úlohy na ukázku cesty taxíkem NYC jízdenky datová sada k předvídání, zda je tip uhrazeno tarif. Provede vás provede postupem [proces vědecké účely dat Team](http://aka.ms/datascienceprocess), klient server, získávání dat pro modelování školení a pak do nasazení webové služby, který publikuje modelu.

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
[Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) má všechny možnosti potřeba usnadňují datových vědců k ukládání dat všech velikost, tvar a rychlost a ke zpracování dat, pokročilé analýzy a modelování s vysokou strojové učení škálovatelnost nákladově efektivní způsobem.   Platíte na základě na úlohu jenom v případě, že data ve skutečnosti probíhá zpracování. Azure Data Lake Analytics obsahuje U-SQL, jazyk, který smíchá deklarativní charakter jazyka SQL o výrazovou sílu jazyka C# k poskytování škálovatelných distribuovaných možnost dotazu. Umožňuje zpracování nestrukturovaných dat s použitím schématu na čtení, vložení vlastní logiky a uživatelem definovaných funkcí (UDF) a zahrnuje rozšíření povolit podrobné kontrolu nad postup provést ve velkém měřítku. Další informace o filosofie návrhu tříd za U-SQL najdete v tématu [Visual Studio příspěvku na blogu](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Služba Data Lake Analytics je také klíčovou součástí Cortana Analytics Suite a spolupracuje se službami Azure SQL Data Warehouse, Power BI a Data Factory. To vám dává kompletní cloud velkých objemů dat a platformy pokročilou analýzu.

Tento názorný postup začíná popisující, jak nainstalovat požadavky a prostředky, které jsou nutné k dokončení úlohy procesu dat vědecké účely. Pak se popisuje postup zpracování dat pomocí U-SQL a ukazuje, jak používat Python a Hive se ukončí s Azure Machine Learning Studio vytvářet a nasazovat prediktivní modely. 

### <a name="u-sql-and-visual-studio"></a>U-SQL Server a Visual Studio
Tento názorný postup se doporučuje pomocí sady Visual Studio upravit skriptů U-SQL zpracovat datovou sadu. Skriptů U-SQL jsou zde popsané a poskytuje v samostatném souboru. Proces zahrnuje příjem, zkoumat a vzorkování data. Také ukazuje, jak spustit úlohu skripty U-SQL z portálu Azure. Pro data v clusteru HDInsight přidružené k usnadnění vytváření a nasazení modelu binární klasifikace v Azure Machine Learning Studio se vytvoří tabulek Hive.  

### <a name="python"></a>Python
Tento názorný postup obsahuje také oddíl, který ukazuje, jak vytvářet a nasazovat prediktivní model pomocí Azure Machine Learning Studio Python. Poskytuje poznámkového bloku Jupyter pomocí skriptů Python pro kroky v tomto procesu. Poznámkový blok obsahuje kód pro některé další funkce engineering kroky a modely konstrukce například více třídami klasifikace a regresní modelování kromě modelu binární klasifikace podle zde uvedeného. Tato úloha regrese je k předvídání množství tip podle dalších funkcí tip. 

### <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning Studio je umožňuje vytvářet a nasazovat prediktivní modely. To se provádí pomocí dva přístupy: nejdřív se skriptů Pythonu a potom se tabulek Hive v clusteru HDInsight (Hadoop).

### <a name="scripts"></a>Scripts
V tomto názorném postupu jsou uvedeny pouze hlavní kroky. Si můžete stáhnout kompletní **skript U-SQL** a **Poznámkový blok Jupyter** z [Githubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Požadavky
Před zahájením těchto témat, musíte mít následující:

* Předplatné Azure. Pokud není již nemáte, přečtěte si téma [získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Doporučeno] Visual Studio 2013 nebo novější. Pokud jste ještě není jedním z těchto verzí, můžete stáhnout bezplatnou verzi komunity z [Visual Studio Community](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> Místo Visual Studio můžete také použít portál Azure k odesílání dotazů Azure Data Lake. Pokyny o tom, jak udělat, tak i pomocí sady Visual Studio a na portálu v části s názvem **zpracování dat pomocí U-SQL**. 
> 
> 


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Příprava dat vědecké účely prostředí pro Azure Data Lake
Příprava prostředí vědecké účely data v tomto návodu, vytvořte v následujících zdrojích informací:

* Azure Data Lake Store (ADLS) 
* Azure Data Lake Analytics (ADLA)
* Účet služby Azure Blob storage
* Účet Azure Machine Learning Studio
* Nástroje Azure Data Lake pro Visual Studio (doporučeno)

Tato část obsahuje pokyny k vytvoření každý z těchto prostředků. Pokud chcete použít tabulek Hive pomocí Azure Machine Learning, místo Python, k vytvoření modelu, musíte taky ke zřízení clusteru služby HDInsight (Hadoop). V této alternativní postup popsaný v části možnost 2.


> [!NOTE]
> **Azure Data Lake Store** je možné vytvořit buď samostatně nebo při vytváření **Azure Data Lake Analytics** jako výchozí úložiště. Pokyny odkazují pro každý z těchto prostředků vytváření samostatně, ale nemusí být samostatně vytvoření účtu úložiště Data Lake.
>
> 

### <a name="create-an-azure-data-lake-store"></a>Vytvoření Azure Data Lake Store


Vytvoření ADLS z [portál Azure](http://portal.azure.com). Podrobnosti najdete v tématu [vytvoření clusteru HDInsight s Data Lake Store pomocí portálu Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Nezapomeňte nastavit identita AAD clusteru v **DataSource** okno **volitelné konfiguraci** okna popsané existuje. 

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Vytvoření účtu Azure Data Lake Analytics
Vytvoření účtu ADLA z [portál Azure](http://portal.azure.com). Podrobnosti najdete v tématu [kurz: Začínáme s Azure Data Lake Analytics pomocí portálu Azure](../../data-lake-analytics/data-lake-analytics-get-started-portal.md). 

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Vytvoření účtu úložiště objektů Blob v Azure
Vytvoření účtu úložiště objektů Blob v Azure z [portál Azure](http://portal.azure.com). Podrobnosti najdete v tématu vytvořením účtu úložiště v tématu v [účty Azure storage](../../storage/common/storage-create-storage-account.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-account"></a>Nastavit účet Azure Machine Learning Studio
Přihlášení/do Azure Machine Learning Studio z [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) stránky. Klikněte na **začít nyní** tlačítko a pak zvolte "Volného prostoru" nebo "Standardní pracovní prostor". Teď vaše jsou připravené k vytvoření experimenty v Azure ML Studio.  

### <a name="install-azure-data-lake-tools-recommended"></a>Instalace nástroje Azure Data Lake [doporučeno]
Instalace nástroje Azure Data Lake pro vaši verzi sady Visual Studio z [nástrojů Azure Data Lake pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Po úspěšném dokončení instalace, otevřete Visual Studio. Měli byste vidět Data Lake, klikněte v nabídce v horní části. Vašich prostředků Azure mají objevit v levém panelu, když se přihlásíte ke svému účtu Azure.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>Datová sada NYC taxíkem cest
Sada dat použít zde je veřejně dostupné datové sady – [datovou sadu cest taxíkem NYC](http://www.andresmh.com/nyctaxitrips/). Data NYC taxíkem cesty se skládá z přibližně 20 GB komprimovaných souborů CSV (nekomprimovaným ~ 48 GB), zaznamenávání 173 milionů jednotlivých cest a tarify placené pro každou cestu. Každý záznam cestě zahrnuje vyzvednutí a odkládací umístění a časy, číslo licence anonymizovaná hackerský (ovladač) a číslo Medailon (taxi na jedinečné ID). Data obsahuje všechny služebních cest v roku 2013 a je dostupné pro každý měsíc následující dvě datové sady:

'trip_data' CSV obsahuje podrobnosti o cestě, například na počtu cestujících, vyzvednutí a dropoff body, doba trvání cesty a délka cesty. Tady je několik ukázkových záznamů:

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



Trip_fare CSV obsahující podrobnosti o tarif placené pro každou cestu, například typ platby, velikost tarif, příplatek a daně, tipy a mýtné, a celkovou velikost placené. Tady je několik ukázkových záznamů:

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Jedinečný klíč pro připojení k cestě\_dat a cesty\_tarif se skládá z následující tři pole: medailonu, hackerský\_licence a vyzvednutí\_data a času. Nezpracované soubory CSV jsou přístupné z veřejného úložiště Azure blob. Skript U-SQL pro toto připojení je v [připojení k cestě a tarif tabulky](#join) části.

## <a name="process-data-with-u-sql"></a>Zpracování dat pomocí U-SQL
Úlohy zpracování dat v této části zahrnují příjem, kontrolu kvality, prohlížení a vzorkování data. Postup připojení k cestě a tarif tabulky se také zobrazí. Koncová část ukazuje spuštění úlohy pomocí skriptu U-SQL z portálu Azure. Tady jsou odkazy na každou část:

* [Přijímání dat: přečíst data z veřejného objektu blob](#ingest)
* [Kontrolu kvality dat](#quality)
* [Zkoumání dat](#explore)
* [Připojení k cestě a tarif tabulky](#join)
* [Vzorkování dat](#sample)
* [Spuštění úloh U-SQL](#run)

Skriptů U-SQL jsou zde popsané a poskytuje v samostatném souboru. Si můžete stáhnout kompletní **skriptů U-SQL** z [Githubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Spuštění U-SQL, otevřete Visual Studio, klikněte na tlačítko **souboru--> Nový Projekt-->**, zvolte **projekt U-SQL**, název a uložte je do složky.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> Je možné pomocí webu Azure Portal spuštění U-SQL místo sady Visual Studio. Můžete přejít k prostředku Azure Data Lake Analytics na portálu a odesílání dotazů přímo jako ilustrované na následujícím obrázku:
> 
> 

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>Přijímání dat: Přečíst data z veřejného objektu blob
Umístění dat v Azure blob je odkazováno jako **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name** a mohou být extrahovány pomocí **Extractors.Csv()**. Nahraďte váš vlastní název kontejneru a název účtu úložiště v následujících skriptů pro container_name@blob_storage_account_name wasb adresu. Vzhledem k tomu, že názvy souborů jsou ve stejném formátu, je možné použít **cestě\_data_ {\*\}.csv** ke čtení v všechny soubory 12 cesty. 

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

Vzhledem k tomu, že jsou v první řádek záhlaví, musíte odebrat hlavičky a změnit typy sloupců do odpovídající těm, které jsou. Můžete buď uložit zpracovaná data pomocí Azure Data Lake Storage **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ nebo pomocí účtu úložiště objektů Blob v Azure  **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**. 

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

Stejně tak si můžete přečíst tarif datových sad. Klikněte pravým tlačítkem na Azure Data Lake Store, můžete se podívat na vaše data v **portál Azure--> Průzkumníku dat** nebo **Průzkumníka souborů** v sadě Visual Studio. 

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="quality"></a>Kontrolu kvality dat
Po cestě a tarif tabulky byly načteny v, kontrolu kvality dat lze provést následujícím způsobem. Výsledné soubory sdíleného svazku clusteru může být výstup do úložiště objektů Blob v Azure nebo Azure Data Lake Store. 

Získat počet medallions a jedinečné číslo medallions:

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

Najděte tyto medallions, kterým má více než 100 odezvy:

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
Proveďte některé zkoumání dat pomocí následujících skriptů pro lepší pochopení dat.

Najděte distribuci šikmý a vysypávány služebních cest:

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

Najít distribuci tip velikost s oříznutím hodnoty: 0, 5, 10 až 20 odběru.

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

Najděte základní statistické údaje o cestě vzdálenost:

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

Najděte percentily vzdálenost cesty:

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


### <a name="join"></a>Připojení k cestě a tarif tabulky
Služební cestě a tarif tabulek je možné připojit Medailon, hack_license a pickup_time.

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


Pro každou úroveň počtu osobní vypočte se počet záznamů, tip pro průměrnou dobu, odchylku tip velikost, procento šikmý služebních cest.

    // contigency table
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
První, náhodně vyberte 0,1 % dat z připojeného k tabulce:

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

Vrstveného vzorkování pak provedete binární proměnné tip_class:

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
Po dokončení úprav skriptů U-SQL, je možné odeslat na server pomocí účtu Azure Data Lake Analytics. Klikněte na tlačítko **Data Lake**, **odeslat úlohu**, vyberte vaše **účet Analytics**, zvolte **paralelismus**a klikněte na tlačítko **odeslání**  tlačítko.  

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Pokud úloha se úspěšně splněny, zobrazí se stav úlohy pro monitorování v sadě Visual Studio. Po dokončení úlohy můžete i přehráním proces spuštění úlohy a zjistěte problémové místo kroky ke zlepšení efektivity úlohy. Můžete také přejít na portál Azure a zkontrolujte stav úloh U-SQL.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Nyní můžete zkontrolovat výstupní soubory v Azure Blob storage nebo portálu Azure. Použijte vrstveného ukázková data pro naše modelování v dalším kroku.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Vytváření a nasazování modelů v Azure Machine Learning
Dvě možnosti jsou k dispozici pro vás k získání dat do Azure Machine Learning k sestavení a 

* V první možnosti, můžete použít jen Vzorkovaná data, která byla zapsána do objektu Blob Azure (v **dat vzorkování** předchozí krok) a použít Python pro sestavení a nasazení modely z Azure Machine Learning. 
* V druhé možnosti je dotaz na data v Azure Data Lake přímo pomocí dotazů Hive. Tato možnost vyžaduje vytvoření nového clusteru HDInsight nebo použít stávající cluster HDInsight, kde tabulek Hive příkaz NY taxíkem data v Azure Data Lake Storage.  Obě tyto možnosti jsou popsány v následujících částech. 

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Možnost 1: Použití Python k vytváření a nasazování počítače learning modely
K vytváření a nasazování modelů machine learning používá Python, vytvořte poznámkového bloku Jupyter na místním počítači nebo v Azure Machine Learning Studio. Poznámkového bloku Jupyter k dispozici na [Githubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) obsahuje kód úplné chcete prozkoumat, vizualizovat data, funkce technikům, modelování a nasazení. V tomto článku jsou popsané právě modelování a nasazení. 

### <a name="import-python-libraries"></a>Importovat knihovny Python
Aby bylo možné spustit ukázku soubor, následující Python, balíčky jsou nutné skriptu Poznámkový blok Jupyter nebo Python. Pokud používáte službu AzureML Poznámkový blok, byly tyto balíčky předem nainstalovaná.

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
* Přidat názvy sloupců a jednotlivé sloupce
  
        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* Změnit některé sloupce na číselné
  
        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Vytvářet modely machine learning
Zde vytvoříte model binární klasifikace předpovědět, zda je vysypávány cesty, nebo ne. V poznámkovém bloku Jupyter můžete najít další dva modely: více třídami klasifikace a modely regrese.

* Nejdřív je potřeba vytvořit fiktivní proměnné, které lze použít v scikit-další modely
  
        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* Vytvořit data snímku pro modelování
  
        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])
  
        X = data.iloc[:,1:]
        Y = data.tipped
* Trénování a testování 60 40 rozdělení
  
        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* Logistic Regression v sadě školení
  
        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)
  
       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Stanovení skóre testování datové sady
  
        Y_test_pred = logit_fit.predict(X_test)
* Výpočet hodnocení metriky
  
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

### <a name="build-web-service-api-and-consume-it-in-python"></a>Sestavení webového rozhraní API služby a využívat v Pythonu
Chcete zprovoznit strojového učení modelu, jakmile byl vytvořen. Binární logistic model se používá jako příklad sem. Zajistěte, aby scikit-další 0.15.1 je verze v místním počítači. Nemusíte si dělat starosti o to, pokud používáte službu Azure ML studio.

* Najít z Azure ML studio nastavení přihlašovacích údajů pracovního prostoru. V nástroji Azure Machine Learning Studio, klikněte na tlačítko **nastavení** --> **název** --> **autorizace tokeny**. 
  
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
* Získat přihlašovací údaje webové služby
  
        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key
  
        print url
        print api_key
  
        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass
* Volání rozhraní API webové služby. Budete muset počkejte 5 až 10 sekund po předchozím kroku.
  
        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)
  
       ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Možnost 2: Vytvoření a nasazení modely přímo v Azure Machine Learning
Azure Machine Learning Studio můžete číst data přímo z Azure Data Lake Store a pak použije k vytvoření a nasazení modelů. Tento postup používá tabulku Hive, která odkazuje na Azure Data Lake Store. To vyžaduje, aby zřídit samostatný cluster Azure HDInsight, na který se vytvoří tabulku Hive. Následující části vysvětlují, jak to provést. 

### <a name="create-an-hdinsight-linux-cluster"></a>Vytvoření clusteru HDInsight Linux
Vytvoření clusteru HDInsight (Linux) z [portál Azure](http://portal.azure.com). Podrobnosti najdete v tématu **vytvoření clusteru HDInsight s přístupem k Azure Data Lake Store** kapitoly [vytvoření clusteru HDInsight s Data Lake Store pomocí portálu Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Vytvoří tabulku Hive v HDInsight
Teď vytvoříte tabulek Hive, který se má použít v nástroji Azure Machine Learning Studio v clusteru HDInsight pomocí dat uložených v Azure Data Lake Store v předchozím kroku. Přejděte k vytvoření clusteru HDInsight. Klikněte na tlačítko **nastavení** --> **vlastnosti** --> **identita AAD clusteru** --> **přístupu ADLS**, Ujistěte se, že váš účet Azure Data Lake Store je přidaný do seznamu čtení, zápisu a oprávnění pro spouštění. 

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Pak klikněte na tlačítko **řídicí panel** vedle **nastavení** tlačítko a v okně se zobrazí. Klikněte na tlačítko **zobrazení Hive** v pravém horním rohu stránky a jste měli vidět **Editor dotazů**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Vložte následující skripty Hive a vytvořte tabulku. Umístění zdroje dat je v Azure Data Lake Store odkaz tímto způsobem: **adl://data_lake_store_name.azuredatalakestore.net:443/název_složky/název_souboru**.

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


Po dokončení spuštění dotazu byste měli vidět výsledky takto:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Vytváření a nasazování modely v Azure Machine Learning Studio
Nyní jste připraveni k sestavení a nasazení model, který předpovídá, zda je tip placené pomocí Azure Machine Learning. Je připravená k použití v této binární klasifikace vrstveného ukázková data (tip nebo ne) problém. Prediktivní modely pomocí více třídami klasifikace (tip_class) a regrese (tip_amount) můžete také být vytvořené a nasazené se službou Azure Machine Learning Studio, ale tady je zobrazen pouze postup zpracování případu použití modelu binární klasifikace.

1. Získat data do aplikace pomocí Azure ML **importovat Data** modulu, k dispozici v **vstupu a výstupu dat** části. Další informace najdete v tématu [importovat Data modulu](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) stránka s referencemi k.
2. Vyberte **dotaz Hive** jako **zdroj dat** v **vlastnosti** panelu.
3. Vložte následující skript Hive v **databázový dotaz Hive** editoru
   
        select * from nyc_stratified_sample;
4. Zadejte identifikátor URI HDInsight clusteru (lze najít na portálu Azure), přihlašovací údaje Hadoop, umístění výstupních dat a název název / / kontejner klíče účtu úložiště Azure.
   
   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)  

Příklad experimentu čtení binární klasifikace, které data z tabulky Hive je znázorněno na následujícím obrázku:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

Po vytvoření experimentu klikněte na tlačítko **nastavit webové služby** --> **prediktivní webové služby**

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Spustit automaticky vytvořený vyhodnocování experiment, po dokončení, klikněte na tlačítko **nasazení webové služby**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Řídicí panel webové služby krátce zobrazí:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Souhrn
Provedením tohoto návodu jste vytvořili data vědecké účely prostředí pro vytváření škálovatelných začátku do konce řešení v Azure Data Lake. Toto prostředí byl použit k analýze velkých veřejné datové sady, který ji provede kanonický kroky procesu vědecké účely dat, z získávání dat přes model školení a následně do nasazení model jako webovou službu. U-SQL byl použit ke zpracování, prozkoumejte a ukázková data. Python a Hive se použít s Azure Machine Learning Studio vytvářet a nasazovat prediktivní modely.

## <a name="whats-next"></a>Co dále?
Studijní postup pro [tým datové vědy procesu (TDSP)](http://aka.ms/datascienceprocess) obsahuje odkazy na témata s popisem jednotlivých kroků v procesu pokročilou analýzu. Existuje řada návodů uvedeno na [proces vědecké účely dat Team návody](walkthroughs.md) stránky, který předvádí použití služby a prostředky v různých scénářích prediktivní analýzy:

* [Proces Team dat. vědecké účely v akci: pomocí SQL Data Warehouse](sqldw-walkthrough.md)
* [Proces Team dat. vědecké účely v akci: pomocí clusterů systému HDInsight Hadoop](hive-walkthrough.md)
* [Procesu vědecké účely Team dat: SQL Server pomocí](sql-walkthrough.md)
* [Přehled procesu vědecké účely dat pomocí Spark v Azure HDInsight](spark-overview.md)
