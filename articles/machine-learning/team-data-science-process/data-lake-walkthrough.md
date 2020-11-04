---
title: Škálovatelná škála dat pomocí procesu Azure Data Lake – tým pro datové vědy
description: Jak používat Azure Data Lake k provádění úloh zkoumání dat a binární klasifikace pro datovou sadu.
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
ms.openlocfilehash: e6eb0be4d9946907dc5bb2f22b27530a27a37aec
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321254"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Škálovatelný návod pro datovou vědu pomocí Azure Data Lake: ucelený návod
V tomto návodu se dozvíte, jak pomocí Azure Data Lake provádět zkoumání dat a binární klasifikace v ukázce s NYC taxislužby a datovou sadou tarifů, abyste předpovídat, jestli je nebo není Tip placený tarifem. Provede vás kroky [vědeckého procesu pro týmovou analýzu dat](./index.yml), od získání dat až po vyřízení modelu a pak nasazení webové služby, která tento model zveřejňuje.

## <a name="technologies"></a>Technologie

Tyto technologie se používají v tomto průvodci.
* Azure Data Lake Analytics
* U-SQL a Visual studia
* Python
* Azure Machine Learning
* Skripty


### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
U [Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) je potřeba, aby odborníci na data mohli snadno ukládat data libovolné velikosti, tvaru a rychlosti a provádět zpracování dat, pokročilou analýzu a modelování strojového učení s vysokou škálovatelností a nákladově efektivním způsobem.   Platíte za jednotlivé úlohy, jenom když se data skutečně zpracovávají. Azure Data Lake Analytics zahrnuje jazyk U-SQL, což je jazyk, který je smíchán deklarativní charakter SQL s využitím možnosti jazyka C# k zajištění škálovatelné funkce distribuovaných dotazů. Umožňuje zpracovat nestrukturovaná data použitím schématu při čtení, vložením vlastní logiky a uživatelsky definovaných funkcí (UDF) a zahrnuje rozšiřitelnost, která umožňuje detailní kontrolu nad tím, jak se dá provádět ve velkém měřítku. Další informace o návrhu filozofie za U-SQL najdete v příspěvku na [blogu sady Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Data Lake Analytics je také klíčovou součástí sady Cortana Analytics Suite a spolupracuje s Azure synapse Analytics, Power BI a Data Factory. Tato kombinace nabízí kompletní cloudovou platformu pro velké objemy dat a pokročilé analýzy.

Tento návod začíná tím, že popisuje, jak nainstalovat požadavky a prostředky, které jsou potřeba k dokončení úloh vědeckého zpracování dat. Pak popisuje kroky zpracování dat pomocí U-SQL a končí tím, že ukazuje, jak používat Python a podregistr s Azure Machine Learning Studio (Classic) k sestavování a nasazování prediktivních modelů.

### <a name="u-sql-and-visual-studio"></a>U-SQL a Visual studia
Tento návod doporučuje použít sadu Visual Studio pro úpravu skriptů U-SQL pro zpracování datové sady. Tady jsou popsané skripty U-SQL, které jsou k dispozici v samostatném souboru. Proces zahrnuje přijímání, prozkoumávání a vzorkování dat. Také ukazuje, jak spustit skript U-SQL s využitím úlohy z Azure Portal. Tabulky podregistru jsou vytvořeny pro data v přidruženém clusteru HDInsight, aby bylo usnadněno sestavování a nasazení binární klasifikace modelu v Azure Machine Learning Studio.

### <a name="python"></a>Python
Tento návod obsahuje také část, která ukazuje, jak sestavit a nasadit prediktivní model pomocí Pythonu s Azure Machine Learning Studio. Poskytuje Poznámkový blok Jupyter se skripty Pythonu pro kroky v tomto procesu. Poznámkový blok obsahuje kód pro některé další postupy a konstrukce modelů, jako je například klasifikace s více třídami a regresní modelování kromě binárního klasifikačního modelu, který je zde popsaný. Regresní úlohou je předpovědět množství tipů na základě dalších funkcí Tip.

### <a name="azure-machine-learning"></a>Azure Machine Learning 
Azure Machine Learning Studio (Classic) se používá k sestavování a nasazování prediktivních modelů pomocí dvou přístupů: nejdřív pomocí skriptů v Pythonu a potom s tabulkami podregistru v clusteru HDInsight (Hadoop).

### <a name="scripts"></a>Skripty
V tomto návodu jsou popsaný pouze hlavní kroky. Úplný **skript U-SQL** si můžete stáhnout a **Jupyter notebook** z [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Předpoklady
Než začnete s těmito tématy, musíte mít následující:

* Předplatné Azure. Pokud ho ještě nemáte, přečtěte si téma [získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Doporučil Visual Studio 2013 nebo novější. Pokud ještě nemáte nainstalovanou některou z těchto verzí, můžete si stáhnout bezplatnou verzi komunity z [Visual Studio Community](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> Místo sady Visual Studio můžete také použít Azure Portal k odeslání Azure Data Lake dotazů. Pokyny k tomu, jak postupovat v rámci sady Visual Studio i na portálu v části s názvem **zpracování dat pomocí u-SQL**.
>
>


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Příprava prostředí pro datové vědy pro Azure Data Lake
Pro přípravu prostředí pro datové vědy pro tento návod vytvořte následující prostředky:

* Azure Data Lake Storage (ADLS)
* Azure Data Lake Analytics (ADLA)
* Účet služby Azure Blob Storage
* Účet Azure Machine Learning Studio (Classic)
* Nástroje Azure Data Lake pro Visual Studio (doporučeno)

V této části najdete pokyny, jak vytvořit každý z těchto prostředků. Pokud se rozhodnete použít tabulky podregistru s Azure Machine Learning místo Pythonu k vytvoření modelu, musíte taky zřídit cluster HDInsight (Hadoop). Tento alternativní postup je popsaný v části možnost 2.


> [!NOTE]
> **Azure Data Lake Store** lze vytvořit buď samostatně, nebo při vytváření **Azure Data Lake Analytics** jako výchozího úložiště. Pokyny pro vytvoření každého z těchto prostředků jsou odkazovány samostatně, ale Data Lake účet úložiště není nutné vytvořit samostatně.
>
>

### <a name="create-an-azure-data-lake-storage"></a>Vytvoření Azure Data Lake Storage


Vytvoří ADLS z [Azure Portal](https://portal.azure.com). Podrobnosti najdete v tématu [Vytvoření clusteru HDInsight s Data Lake Store pomocí Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že jste nastavili identitu AAD clusteru v okně **zdroje dat** v **volitelném okně Konfigurace** popsané zde.

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Vytvoření účtu Azure Data Lake Analytics
Vytvořte z [Azure Portal](https://portal.azure.com)účet ADLA. Podrobnosti najdete v tématu [kurz: Začínáme s Azure Data Lake Analytics pomocí Azure Portal](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Vytvoření účtu služby Azure Blob Storage
Vytvořte účet úložiště objektů BLOB v Azure z [Azure Portal](https://portal.azure.com). Podrobnosti najdete v části Vytvoření účtu úložiště v tématu [informace o Azure Storage účtech](../../storage/common/storage-account-create.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-classic-account"></a>Nastavení účtu Azure Machine Learning Studio (Classic)
Zaregistrujte se do Azure Machine Learning Studio (Classic) na stránce [Azure Machine Learning studia](https://azure.microsoft.com/services/machine-learning/) . Klikněte na tlačítko **začít hned** a pak zvolte "bezplatný pracovní prostor" nebo "standardní pracovní prostor". Teď jste připraveni vytvořit experimenty v Azure Machine Learning Studiu.

### <a name="install-azure-data-lake-tools-recommended"></a>Nainstalovat Azure Data Lake nástroje [Doporučené]
Nainstalujte nástroje Azure Data Lake pro vaši verzi sady Visual Studio z [nástroje Azure Data Lake pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Po dokončení instalace otevřete aplikaci Visual Studio. Měla by se zobrazit karta Data Lake v horní části nabídky. Prostředky Azure by se měly zobrazit na levém panelu, když se přihlásíte k účtu Azure.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>Datová sada cest taxislužby pro NYC
Datová sada, která se tady používá, je veřejně dostupná datová sada – [datová sada NYC taxislužby TRIPS](https://www.andresmh.com/nyctaxitrips/). Data NYC taxislužby se skládají z přibližně 20 GB komprimovaných souborů CSV (~ 48 GB nekomprimovaných), zaznamenáváním více než 173 000 000 jednotlivých cest a tarifů placených za každou cestu. Každý záznam cesty zahrnuje umístění a časy vyzvednutí a dropoff, číslo licence v anonymního napadení (strojvedoucí) a číslo Medallion (jedinečné ID taxislužby). Data se týkají všech cest v roce 2013 a jsou k dispozici v následujících dvou datových sadách pro každý měsíc:

CSV trip_data obsahuje podrobnosti o cestě, jako je třeba počet cestujících, vyzvednutí a dropoff body, doba trvání cesty a délka cesty. Tady je několik ukázkových záznamů:

`medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`
`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

CSV trip_fare obsahuje podrobnosti o tarifu placeného za každou cestu, jako je typ platby, částka tarifů, příplatek a daně, tipy a mýtné a celková placená částka. Tady je několik ukázkových záznamů:

`medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`
`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Jedinečný klíč pro připojení k \_ datům cest a služební \_ tarif se skládá z následujících tří polí: Medallion, oprávnění k napadení \_ a \_ data a času vyzvednutí. K nezpracovaným souborům CSV se dá dostat z Azure Storageého objektu BLOB. Skript U-SQL pro toto spojení je v části [připojení a tabulky tarifů](#join) .

## <a name="process-data-with-u-sql"></a>Zpracování dat pomocí U-SQL
Úlohy zpracování dat popsané v této části zahrnují ingestování, kontrolu kvality, prozkoumávání a vzorkování dat. Zobrazuje se taky tabulka pro cestu k jízdám a tarifům. Poslední část ukazuje spuštění úlohy skript U-SQL z Azure Portal. Tady jsou odkazy na jednotlivé pododdíly:

* [Přijímání dat: čtení dat z veřejného objektu BLOB](#ingest)
* [Kontroly kvality dat](#quality)
* [Zkoumání dat](#explore)
* [Připojit tabulky pro cestu a tarify](#join)
* [Vzorkování dat](#sample)
* [Spuštění úloh U-SQL](#run)

Tady jsou popsané skripty U-SQL, které jsou k dispozici v samostatném souboru. Všechny **skripty U-SQL** si můžete stáhnout z [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Chcete-li spustit U-SQL, spusťte aplikaci Visual Studio, klikněte na **soubor--> nový--> projekt** , vyberte **projekt U-SQL** , název a uložte jej do složky.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> Místo sady Visual Studio je možné použít Azure Portal ke spuštění U-SQL. Na portálu můžete přejít na prostředek Azure Data Lake Analytics a odesílat dotazy přímo, jak je znázorněno na následujícím obrázku:
>
>

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="data-ingestion-read-in-data-from-public-blob"></a><a name="ingest"></a>Přijímání dat: čtení dat z veřejného objektu BLOB

Umístění dat v objektu blob Azure je odkazováno jako **\_ název wasb://Container \@ \_ účtu BLOB Storage \_ \_ name.blob.core.windows.net/blob_name** a lze ho extrahovat pomocí **Extractors.Csv ()**. Název kontejneru pro název kontejneru \_ \@ BLOB \_ Storage \_ \_ v adrese wasb nahraďte názvem vlastního kontejneru a názvem účtu úložiště v následujících skriptech. Vzhledem k tomu, že názvy souborů jsou ve stejném formátu, je možné použít data ze souboru **\_ \_ \{ \* \} . csv** pro čtení všech 12 souborů na cestách.

```sql
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
```

Vzhledem k tomu, že jsou v prvním řádku záhlaví, je nutné odebrat záhlaví a změnit typy sloupců do odpovídajících. Zpracovaná data můžete buď Uložit do Azure Data Lake Storage pomocí **swebhdfs://data_lake_storage_name. azuredatalakestorage. NET/Folder_name/file_name** _ nebo do účtu Azure Blob Storage pomocí  **wasb://container_name \@ blob_storage_account_name. blob. Core. windows. NET/blob_name**.

```sql
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
```

Podobně můžete číst v sadách dat tarifů. Klikněte pravým tlačítkem na Azure Data Lake Storage, můžete se podívat na data v **Azure Portal--> Průzkumník dat** nebo v **Průzkumníkovi souborů** v sadě Visual Studio.

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="data-quality-checks"></a><a name="quality"></a>Kontroly kvality dat
Po načtení tabulek pro cestu a tarify v nástroji je možné kontroly kvality dat provést následujícím způsobem. Výsledné soubory CSV můžou být výstupem do služby Azure Blob Storage nebo Azure Data Lake Storage.

Najděte počet medallions a jedinečné číslo medallions:

```sql
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
```

Najděte ty medallions, které obsahovaly více než 100 cest:

```sql
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
```

Vyhledá tyto neplatné záznamy z podmínek pickup_longitude:

```sql
///find those invalid records in terms of pickup_longitude
@ex_3 =
    SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
    FROM @trip
    WHERE
    pickup_longitude <- 90 OR pickup_longitude > 90;
    OUTPUT @ex_3
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
USING Outputters.Csv();
```

Najít chybějící hodnoty pro některé proměnné:

```sql
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
```

### <a name="data-exploration"></a><a name="explore"></a>Zkoumání dat
Prozkoumáním dat pomocí následujících skriptů získáte lepší porozumění datům.

Najděte distribuci šikmého a nešikmého výletu:

```sql
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
```

Vyhledá distribuci částky tipu pomocí vyjmutých hodnot: 0, 5, 10 a 20 dolarů.

```sql
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
```

Najděte základní statistiku vzdálenosti cest:

```sql
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
```

Najděte percentily vzdálenosti cest:

```sql
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
```

### <a name="join-trip-and-fare-tables"></a><a name="join"></a>Připojit tabulky pro cestu a tarify
Tabulky cest a tarifů je možné spojit pomocí Medallion, hack_license a pickup_time.

```sql
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
```

Pro každou úroveň počtu spolujezdců Vypočítejte počet záznamů, průměrnou velikost hrotu, odchylku množství špičky, procento vyhraných cest.

```sql
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
```

### <a name="data-sampling"></a><a name="sample"></a>Vzorkování dat
Nejprve náhodně vyberte 0,1% dat z připojené tabulky:

```sql
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
```

Pak stratified vzorkování podle binární proměnné tip_class:

```sql
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
```

### <a name="run-u-sql-jobs"></a><a name="run"></a>Spuštění úloh U-SQL
Po úpravě skriptů U-SQL je můžete odeslat na server pomocí účtu Azure Data Lake Analytics. Klikněte na **Data Lake** , **Odeslat úlohu** , vyberte svůj **účet Analytics** , zvolte **paralelismus** a klikněte na tlačítko **Odeslat** .

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Po úspěšném splnění úlohy se stav úlohy zobrazí v aplikaci Visual Studio pro monitorování. Po dokončení úlohy můžete dokonce znovu přehrát proces provádění úlohy a zjistit, jaké kroky je potřeba ke zlepšení efektivity úlohy. Můžete také přejít na Azure Portal a ověřit stav úloh U-SQL.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Nyní můžete zkontrolovat výstupní soubory buď v úložišti objektů BLOB v Azure, nebo v Azure Portal. Použijte ukázková data stratified pro naše modelování v dalším kroku.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Sestavování a nasazování modelů v Azure Machine Learning
K dispozici jsou dvě možnosti, jak načíst data do Azure Machine Learning sestavení a

* V první možnosti použijete ukázková data zapsaná do objektu blob Azure (v kroku **vzorkování dat** výše) a pomocí Pythonu sestavíte a nasadíte modely z Azure Machine Learning.
* Ve druhé možnosti Dotazujte data v Azure Data Lake přímo pomocí dotazu na podregistr. Tato možnost vyžaduje, abyste vytvořili nový cluster HDInsight nebo používali existující cluster HDInsight, ve kterém tabulky podregistru odkazují na NY taxislužby data v Azure Data Lake Storage.  Obě tyto možnosti jsou popsány v následujících částech.

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Možnost 1: použití Pythonu k sestavení a nasazení modelů strojového učení
Pokud chcete vytvářet a nasazovat modely strojového učení pomocí Pythonu, vytvořte Jupyter Notebook v místním počítači nebo v Azure Machine Learning Studio. Jupyter Notebook poskytované na [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) obsahuje úplný kód pro zkoumání, vizualizaci, vytváření funkcí, modelování a nasazení. V tomto článku se zabývá pouze modelováním a nasazením.

### <a name="import-python-libraries"></a>Importovat knihovny Pythonu
Aby bylo možné spustit vzorový Jupyter Notebook nebo soubor skriptu Pythonu, jsou potřeba následující balíčky Pythonu. Pokud používáte službu Azure Machine Learning Poznámkový blok, tyto balíčky byly předem nainstalovány.

```python
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
```

### <a name="read-in-the-data-from-blob"></a>Čtení dat z objektu BLOB
* Connection String (Připojovací řetězec)

  ```text
  CONTAINERNAME = 'test1'
  STORAGEACCOUNTNAME = 'XXXXXXXXX'
  STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
  BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
  blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
  ```

* Číst jako text

  ```text
  t1 = time.time()
  data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
  t2 = time.time()
  print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))
  ```

  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)

* Přidat názvy sloupců a samostatné sloupce

  ```text
  colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
  'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
  'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
  df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
  ```

* Změnit některé sloupce na číselné

  ```text
  cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
  'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
  ,'tipped','tip_class','rownum']
  for col in cols_2_float:
      df1[col] = df1[col].astype(float)
  ```

### <a name="build-machine-learning-models"></a>Sestavování modelů strojového učení
Tady sestavíte binární klasifikační model, který předpovídá, jestli je cesta popracovaná nebo ne. V Jupyter Notebook můžete najít další dva modely: třídy klasifikace s více třídami a regresní modely.

* Nejdřív je potřeba vytvořit fiktivní proměnné, které se dají použít v modelech scikit-učení.

  ```python
  df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
  df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
  ```

* Vytvořit datový rámec pro modelování

  ```python
  cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
  data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])

  X = data.iloc[:,1:]
  Y = data.tipped
    ```

* Školení a testování 60-40 – rozdělení

  ```python
  X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
  ```

* Logistické regrese v sadě školení

  ```python
  model = LogisticRegression()
  logit_fit = model.fit(X_train, Y_train)
  print ('Coefficients: \n', logit_fit.coef_)
  Y_train_pred = logit_fit.predict(X_train)
  ```

    ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)

* Sada dat testování skóre

  ```python
  Y_test_pred = logit_fit.predict(X_test)
  ```

* Vypočítat metriky vyhodnocení

  ```python
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
  ```

    ![c2](./media/data-lake-walkthrough/c2-py-logit-evaluation.PNG)

### <a name="build-web-service-api-and-consume-it-in-python"></a>Sestavení rozhraní API webové služby a jeho využití v Pythonu
Po sestavení je třeba zprovoznění model strojového učení. Jako příklad se používá binární logistický model. Ujistěte se, že verze scikit-učení v místním počítači je 0.15.1 (Azure Machine Learning Studio je už aspoň v této verzi).

* Vyhledejte přihlašovací údaje k pracovnímu prostoru z nastavení Azure Machine Learning Studio (Classic). V Azure Machine Learning Studio klikněte na **Nastavení**  -->  **názvy**  -->  **autorizační tokeny**.

    ![c3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

  ```output
  workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
  auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
  ```

* Vytvořit webovou službu

  ```python
  @services.publish(workspaceid, auth_token)
  @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
  @services.returns(int) #0, or 1
  def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
      inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
      return logit_fit.predict(inputArray)
  ```

* Získat přihlašovací údaje webové služby

  ```python
  url = predictNYCTAXI.service.url
  api_key =  predictNYCTAXI.service.api_key

  print url
  print api_key

  @services.service(url, api_key)
  @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
  @services.returns(float)
  def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
      pass
  ```

* Volání rozhraní API webové služby Obvykle počkejte 5-10 sekund po předchozím kroku.

  ```python
  NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)
  ```

    ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Možnost 2: vytvoření a nasazení modelů přímo v Azure Machine Learning
Azure Machine Learning Studio (Classic) mohou číst data přímo z Azure Data Lake Storage a pak je použít k vytvoření a nasazení modelů. Tento přístup používá tabulku podregistru, která odkazuje na Azure Data Lake Storage. Pro tabulku podregistru se musí zřídit samostatný cluster Azure HDInsight. 

### <a name="create-an-hdinsight-linux-cluster"></a>Vytvoření clusteru HDInsight Linux
Vytvořte cluster HDInsight (Linux) z [Azure Portal](https://portal.azure.com). Podrobnosti najdete v části **Vytvoření clusteru HDInsight s přístupem k Azure Data Lake Storage** v tématu [Vytvoření clusteru HDInsight s Data Lake Store pomocí Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Vytvoření tabulky podregistru v HDInsight
Nyní vytváříte tabulky podregistru, které se mají použít v Azure Machine Learning Studio (Classic) v clusteru HDInsight pomocí dat uložených v Azure Data Lake Storage v předchozím kroku. Přejdete na vytvořený cluster HDInsight. Klikněte na **Nastavení**  -->  **vlastnosti**  -->  **cluster AAD identity**  -->  **adls přístup** , ujistěte se, že se Váš účet Azure Data Lake Storage přidal v seznamu s právy pro čtení, zápis a spouštění.

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Pak klikněte na **řídicí panel** vedle tlačítka **Nastavení** a zobrazí se okno se stavem. V pravém horním rohu stránky klikněte na **zobrazení podregistru** a měli byste vidět **Editor dotazů**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Pokud chcete vytvořit tabulku, vložte ji do následujících skriptů podregistru. Umístění zdroje dat je v Azure Data Lake Storage odkaz tímto způsobem: **ADL://data_lake_store_name. azuredatalakestore. NET: 443/Folder_name/file_name**.

```hiveql
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
```

Po dokončení dotazu byste měli vidět podobné výsledky:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Sestavování a nasazování modelů v Azure Machine Learning Studio
Nyní jste připraveni sestavit a nasadit model, který předpovídá, zda je nebo není u Azure Machine Learning placena Tip. Vzorová data stratified jsou připravená k použití v této binární klasifikaci (Tip nebo ne). Prediktivní modely s použitím třídy Classification (tip_class) a regrese (tip_amount) lze také sestavit a nasadit pomocí Azure Machine Learning Studio, ale zde se zobrazí pouze postup zpracování případu pomocí binárního klasifikačního modelu.

1. Získat data do Azure Machine Learning Studio (Classic) pomocí modulu **Import dat** , který je k dispozici v části **vstup a výstup dat** . Další informace najdete na referenční stránce [Import datových modulů](/azure/machine-learning/studio-module-reference/import-data) .
2. Na panelu **vlastnosti** vyberte možnost **dotaz na podregistr** jako **zdroj dat** .
3. Vložte následující skript podregistru do editoru **dotazů databáze podregistru** .

    ```hiveql
    select * from nyc_stratified_sample;
    ```

4. Zadejte identifikátor URI clusteru HDInsight (Tento identifikátor URI najdete v Azure Portal), přihlašovací údaje Hadoop, umístění výstupních dat a název účtu, klíč nebo kontejner pro Azure Storage.

   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)

Příklad experimentu binární klasifikace, který čte data z tabulky podregistru, je znázorněn na následujícím obrázku:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

Po vytvoření experimentu klikněte na **nastavit**  -->  **prediktivní webovou službu** webové služby.

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Spusťte experiment automaticky vytvořeného bodování, po jeho dokončení klikněte na **nasadit webovou službu** .

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Brzy se zobrazí řídicí panel webové služby:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Souhrn
Po dokončení tohoto návodu jste vytvořili prostředí pro datové vědy pro vytváření škálovatelných komplexních řešení v Azure Data Lake. Toto prostředí se použilo k analýze velkých veřejných datových sad, a to díky kanonickým krokům procesu pro datové vědy, od získání dat prostřednictvím školení modelů a pak k nasazení modelu jako webové služby. U-SQL bylo použito ke zpracování, prozkoumávání a vzorkování dat. Python a podregistr se použily s Azure Machine Learning Studio (Classic) k vytváření a nasazování prediktivních modelů.

## <a name="whats-next"></a>Co dále?
Postup výuky pro [vědecké zpracování týmových dat (TDSP)](./index.yml) obsahuje odkazy na témata popisující jednotlivé kroky procesu pokročilé analýzy. Na stránce průvodce [vědeckým zpracováním týmových dat](walkthroughs.md) najdete řadu návodů, které prezentují použití prostředků a služeb v různých scénářích prediktivní analýzy:

* [Vědecké zpracování týmových dat v akci: používání Azure synapse Analytics](sqldw-walkthrough.md)
* [Vědecké zpracování týmových dat v akci: používání clusterů HDInsight Hadoop](hive-walkthrough.md)
* [Vědecké zpracování týmových dat: použití SQL Server](sql-walkthrough.md)
* [Přehled procesu pro datové vědy pomocí Sparku v Azure HDInsight](spark-overview.md)